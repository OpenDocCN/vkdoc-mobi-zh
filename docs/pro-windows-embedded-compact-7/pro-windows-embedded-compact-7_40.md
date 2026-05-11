# 硬件中断

硬件中断可分为边沿触发或电平触发。如同世间万物，每种方式各有利弊。边沿触发中断发生在中断请求线上出现脉冲电平跳变时：低电平（0）到高电平（1）的跳变称为上升沿触发，高电平（1）到低电平（0）的跳变则称为下降沿触发。边沿触发中断的主要缺陷在于，噪声可能被误判为中断，或中断完全被遗漏。现代硬件通过中断状态寄存器有效解决了这一问题。精心实现的设备驱动程序应扫描这些寄存器，以确认没有遗漏中断。电平触发中断会将中断请求线切换至有效状态（高电平或低电平），并保持该状态直至接收到切换为非活跃状态的指令。电平触发中断的主要缺陷在于共享中断请求线的处理：若一个低优先级请求持续保持电平有效，即使另一个 I/O 设备具有更高优先级，检测其请求也会变得困难。

Windows Embedded Compact 7 及其前代系统需要提供操作系统服务来处理这些中断，从而使操作系统本身及其进程能够正常运行。物理中断是通过连接至 CPU 的硬件线路传输的信号，OEM 适配层（OAL）的实现会将硬件分配的中断映射为操作系统可理解的逻辑中断，以便中断处理代码正确服务于每个中断。

### 中断架构

Windows Embedded Compact 7 的中断模型将所有中断向量化到内核中。其中断架构通过将中断处理拆分为极快（且“轻量级”）的中断服务例程（ISR）和由设备驱动程序提供、负责处理大部分中断工作量的线程，在简洁性与性能之间寻求平衡。由于在 ISR 中执行扩展处理会延迟对其他中断的服务并可能导致抖动，ISR 仅做极少量工作——确认中断后，便将大部分中断处理工作交由 IST 执行。由于 IST 的调度方式与系统中的其他线程相同，系统会为其分配与所服务中断优先级相匹配的调度优先级。这通过减少抖动的显著提升了实时性能，并简化了内核的锁机制和线程调度。

处理所有中断的内核组件是异常处理程序。当中断发生时，CPU 将控制权转交给内核异常处理程序。异常处理程序随即调用已注册用于处理当前中断的 ISR。ISR 应返回中断的逻辑中断标识符，并将其作为返回值传递给内核。内核会设置与该逻辑中断关联的事件，从而触发中断服务线程（IST）被调度。IST 中的代码负责处理设备中断。IST 在设备管理器的线程上下文中运行，本质上是一个以高优先级运行的普通线程。

### 中断处理流程

图 8-1 的示意图展示了从右向左的时间轴上状态转换的顺序。从硬件触发的中断到达开始，一直持续到中断处理完成并释放被阻塞的中断硬件以处理下一个传入中断。图中标注了以下 5 个步骤：

1. 设备触发已注册的硬件中断
2. 内核捕获异常，调用关联的中断服务例程（ISR）
3. 中断服务例程（ISR）快速处理等待中的中断
4. 驱动程序中的中断服务线程（IST）收到信号，开始处理中断
5. 中断服务线程（IST）完成处理并……（原文此处不完整）

*图 8-1. Windows Embedded Compact 7 中的中断处理流程*

### 中断服务例程 - ISR

WEC 7 中的 ISR 必须短小精悍且执行迅速，因为 ISR 执行时所有中断都会被关闭，它们不能调用任何内核函数，不能使用任何栈内存，并且根据 CPU 架构的不同，甚至可能无法使用全部寄存器。更重要的是，ISR 不能引发任何嵌套异常。ISR 可以向内核返回一个值，通过返回`SYSINTR_NOP`告知内核中断已处理完毕，或通过返回对应的`SYSINTR`来请求内核调度特定的 IST。

基于 WEC 7 的设备可接受以下两类中断服务例程：

- 硬件 OEM 在 OAL 中创建的中断处理程序，为 BSP 设备驱动程序提供中断服务。
- 由 ISV 等根据需要开发的可安装 ISR；但 OEM 必须在 OAL 中提供对链接可安装 ISR 的支持。

### OAL 与中断处理

OEM 适配层（OAL）的实现使操作系统内核能够访问 OEM 的硬件。OAL 实现包含一组超过十几个的函数，用于初始化、映射、启用和禁用中断，并在中断请求值与逻辑 ID 之间进行转换。某些硬件架构（例如 ARM 架构）可能支持由单个 ISR 处理单个中断线，此时 OEM 负责实现名为`OEMInterruptHandler`的单个 ISR。该函数必须针对每个基于 ARM 的 SoC（片上系统）进行专门实现，因为每个 SoC 都拥有自己的一套控制寄存器。清单 8-1 展示了这样一个实现，其中重复的代码已用“......”字符串简写，并移除了性能分析支持。

其他硬件，例如 x86 架构（其中断由可编程中断控制器 PIC 处理），则为多个中断配备了多个 ISR。在这种情况下，OAL 不会实现`OALIntrMapInit`函数。不过，查看为 x86 平台提供的实际 ISR——`PeRPISR`（所有基于 x86 的设备通用）是很有意义的。清单 8-2 展示了移除了性能分析和 ILTiming 支持后的 x86 ISR。

*清单 8-1. OMAP 35xx 平台的 OEMInterruptHandler 实现*

```
UINT32 OEMInterruptHandler(UINT32 ra)
{
    UINT32 irq = OAL_INTR_IRQ_UNDEFINED;
    UINT32 sysIntr = SYSINTR_NOP;
    UINT32 mask,status;

    if (g_oalILT.active) g_oalILT.interrupts++;

    // 获取等待处理的中断
    irq = INREG32(&s_intr.pICLRegs->INTC_SIR_IRQ);

    if (irq == IRQ_GPIO1_MPU)
    {
        // 将状态与已启用中断的 GPIO 进行掩码操作，确保仅处理由新中断生成的中断
        status = INREG32(&s_intr.pGPIORegs[0]->IRQSTATUS1);
        status &= INREG32(&s_intr.pGPIORegs[0]->IRQENABLE1);
        for (irq = IRQ_GPIO_0, mask = 1; mask != 0; mask <<= 1, irq++)
        {
            if ((mask & status) != 0) break;
        }
        OUTPORT32(&s_intr.pGPIORegs[0]->IRQSTATUS1, mask);
        OUTPORT32(&s_intr.pGPIORegs[0]->IRQSTATUS2, mask);
        OUTPORT32(&s_intr.pGPIORegs[0]->CLEARIRQENABLE1, mask);
        OUTPORT32(&s_intr.pGPIORegs[0]->CLEARWAKEUPENA, mask);
        OEMEnableIOPadWakeup((irq - IRQ_GPIO_0), FALSE);
    }
    else if (irq == IRQ_GPIO2_MPU)
    {
        status = INREG32(&s_intr.pGPIORegs[1]->IRQSTATUS1);
        status &= INREG32(&s_intr.pGPIORegs[1]->IRQENABLE1);
        for (irq = IRQ_GPIO_32, mask = 1; mask != 0; mask <<= 1, irq++)
        {
            if ((mask & status) != 0) break;
        }
        OUTPORT32(&s_intr.pGPIORegs[1]->IRQSTATUS1, mask);
        OUTPORT32(&s_intr.pGPIORegs[1]->IRQSTATUS2, mask);
    }
}
```



`OUTPORT32(&s_intr.pGPIORegs[1]->CLEARIRQENABLE1, mask);` `OUTPORT32(&s_intr.pGPIORegs[1]->CLEARWAKEUPENA, mask);` `OEMEnableIOPadWakeup((irq - IRQ_GPIO_0), FALSE);`

```
else if (irq == IRQ_GPIO3_MPU)
{
.....
}
else if (irq == IRQ_GPIO4_MPU)
{
.....
}
else if (irq == IRQ_GPIO5_MPU)
{
.....
}
else if (irq == IRQ_GPIO6_MPU)
{
.....
}
else if (irq < 32)
{
SETPORT32(&s_intr.pICLRegs->INTC_MIR0, 1 << irq);
}
else if (irq < 64)
{
SETPORT32(&s_intr.pICLRegs->INTC_MIR1, 1 << (irq - 32));
}
else if (irq < 96)
{
SETPORT32(&s_intr.pICLRegs->INTC_MIR2, 1 << (irq - 64));
}
// 确认中断
OUTREG32(&s_intr.pICLRegs->INTC_CONTROL, IC_CNTL_NEW_IRQ);

// 检查是否为定时器中断
if (irq == g_oalTimerIrq)
{
     if (g_oalILT.active)
          g_oalILT.interrupts--;

     // 调用定时器中断处理函数
     sysIntr = OALTimerIntrHandler();

     // 重新启用中断
     OALIntrDoneIrqs(1, &irq);
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 设备驱动 I/O 与中断

```
else if (irq == g_oalPrcmIrq)
{
     // 调用 PRCM 中断处理函数
     sysIntr = OALPrcmIntrHandler();

     // 重新启用中断
     if (sysIntr == SYSINTR_NOP)
          OALIntrDoneIrqs(1, &irq);
}
else if (irq == g_oalSmartReflex1)
{
     // 调用 PRCM 中断处理函数
     sysIntr = OALSmartReflex1Intr();

     // 重新启用中断
     if (sysIntr == SYSINTR_NOP)
          OALIntrDoneIrqs(1, &irq);
}
else if (irq == g_oalSmartReflex2)
{
.....
}
else if (irq != OAL_INTR_IRQ_UNDEFINED)
{
     // 我们不假设中断共享，使用静态映射
     sysIntr = OALIntrTranslateIrq(irq);
}
return sysIntr;
}
```

*列表 8-2. x86 ISR 函数的 PeRPISR 实现*

```
static ULONG PeRPISR()
{
     ULONG ulRet = SYSINTR_NOP;
     UCHAR ucCurrentInterrupt;

     ucCurrentInterrupt = PICGetCurrentInterrupt();

     if (ucCurrentInterrupt == INTR_TIMER0)
     {
          if (PProfileInterrupt)
          {
               ulRet= PProfileInterrupt();
          }

          if (!PProfileInterrupt || ulRet == SYSINTR_RESCHED)
          {
               if (sgTimeAdvance)
               {
                    CurMSec += g_dwBSPMsPerIntr;
                    CurTicks.QuadPart += g_dwOALTimerCount;
               }

               if ((int) (CurMSec - dwReschedTime) >= 0)
                    ulRet = SYSINTR_RESCHED;
          }

          // 检查是否请求了重启
          if (dwRebootAddress)
          {
               RebootHandler();
          }

     }
     else if (ucCurrentInterrupt == INTR_RTC)
     {
          // 检查是否为闹钟中断
          UCHAR cStatusC = CMOS_Read( RTC_STATUS_C);

          if((cStatusC & (RTC_SRC_IRQ|RTC_SRC_AS)) == (RTC_SRC_IRQ|RTC_SRC_AS))
               ulRet = SYSINTR_RTC_ALARM;
     }
     else if (ucCurrentInterrupt <= INTR_MAXIMUM)
     {
          // 屏蔽中断源
          PICEnableInterrupt(ucCurrentInterrupt, FALSE);

          // 我们有一个物理中断 ID，但需要返回一个
          // SYSINTR_ID。调用中断链以查看是否有任何
          // 已安装的 ISR 处理此中断
          ulRet = NKCallIntChain(ucCurrentInterrupt);

          // 已安装的 ISR 未声明此 IRQ；将其转换为
          // SYSINTR
          if (ulRet == SYSINTR_CHAIN)
          {
               ulRet = OALIntrTranslateIrq (ucCurrentInterrupt);
               if (!OALIsInterruptEnabled(ulRet))
               {
                    // 此默认 IST 未初始化
                    ulRet = (ULONG)SYSINTR_UNDEFINED;
               }
          }

          if (ulRet == SYSINTR_NOP)
          {
               // 如果是 SYSINTR_NOP，表示已由安装的 ISR 声明，但
               // 无需进一步操作
               PICEnableInterrupt(ucCurrentInterrupt, TRUE);
          }
          else if (ulRet == SYSINTR_UNDEFINED ||
               !NKIsSysIntrValid(ulRet))
          {
               // 如果是 SYSINTR_UNDEFINED，忽略
               // 如果 SysIntr 从未初始化，忽略
               OALMSGS(OAL_WARN&&OAL_INTR,
                    (L"IRQ %d 上出现虚假中断\r\n",
                    ucCurrentInterrupt));
               ulRet = OALMarkUnknownIRQ(ucCurrentInterrupt);
          }
     }
}
```



#### OEMIndicateIntSource 的实现

`OEMIndicateIntSource(ulRet);`

```c
// 在发出 EOI 之前禁用中断，这样如果中断源尚未被正确服务，
// 我们将自旋尝试服务该中断，而不是在此处无限嵌套直到堆栈溢出。
// 在 Virtual Server 2005 下运行时，在添加此 cli 之前，
// 我们发现定时器中断在 EOI 后立即中断，导致致命的嵌套中断。
//
__asm {
    cli
}

if (ucCurrentInterrupt > 7 || ucCurrentInterrupt == -2)
{
    __asm {
        mov al, 020h ; 非特定 EOI
        out 0A0h, al
    }
}

__asm {
    mov al, 020h ; 非特定 EOI
    out 020h, al
}

return ulRet;
}
```

### 可安装 ISR

顾名思义，可安装 ISR 可以在运行中的设备上安装，而无需在 OAL 中实现相关代码。这使得 ISV 无需与 OEM 协作即可将 ISR 添加到系统中。此外，可安装 ISR 允许多个设备共享单个中断请求线上的中断。这意味着 OEM 必须在 OAL 代码中实现对 IRQ 的支持，使得可安装 ISR 能够挂接到该 IRQ。

清单 8-3 展示了设备驱动程序如何在其 `xxx_Init` 入口点中安装注册表设置中指定的 ISR。

*清单 8-3. 安装 ISR 示例代码*

```c
// 我们确保从 DDKReg_GetIsrInfo 获得了有效结果
if (IsrInfo.szIsrDll[0] != 0)
{
    if (IsrInfo.szIsrHandler[0] != 0 &&
        IsrInfo.dwIrq == IRQ_UNSPECIFIED &&
        IsrInfo.dwSysintr == SYSINTR_UNDEFINED)
    {
        DEBUGMSG(ZONE_INIT,
            (_T("TMP!可安装 ISR 设置损坏或丢失\r\n")));
        FreePhysMem(pDevContxt);
        return dwRet;
    }
    else
    {
        pDevContxt->hInstISR =
            LoadIntChainHandler(IsrInfo.szIsrDll,
                                IsrInfo.szIsrHandler,
                                (BYTE)IsrInfo.dwIrq);
        if (!pDevContxt->hInstISR)
        {
            DEBUGMSG(ZONE_INIT,
                (_T("TMP!安装 ISR 处理程序失败\r\n")));
            FreePhysMem(pDevContxt);
            return dwRet;
        }
        else
        {
            GIISR_INFO Info;
            PHYSICAL_ADDRESS PortAddress =
                pDevContxt->ioPhysicalBase;
            pDevContxt->dwSysIntr = IsrInfo.dwSysintr;

            // 设置 ISR 处理程序
            Info.SysIntr = IsrInfo.dwSysintr;
            Info.CheckPort = TRUE;
            Info.PortIsIO = pDevContxt->bIoMemMapped;
            Info.UseMaskReg = FALSE;
            Info.PortAddr = pDevContxt->pIoAddr;
            Info.PortSize = sizeof(DWORD);
            Info.MaskAddr = 0x0000;

            if (!KernelLibIoControl(pDevContxt->hInstISR,
                                    IOCTL_GIISR_INFO, &Info, sizeof(Info), NULL, 0, NULL))
            {
                DEBUGMSG(ZONE_ERROR,
                    (_T("TMP!KernelLibIoControl 调用失败\r\n")));
            }
        }
    }
}
```

`LoadIntChainHandler` 主要负责加载 ISR 的 DLL，调用必需的 `CreateInstance` 函数，并将该 ISR 添加到 ISR 列表的末尾。大多数情况下，`%WINCEROOT%\public\COMMON\oak\drivers\giisr` 中实现的通用可安装 ISR 足以识别触发设备并返回正确的 `SYSINTR`。如果你需要扩展可安装 ISR 的功能，而不仅仅是返回正确的 `SYSINTR`，你可以实现自己的 ISR 处理程序。该处理程序必须在 DLL 中实现，并暴露四个函数：

- `ISRHandler`——实际的 ISR
- `CreateInstance`——返回一个引用该 ISR 特定实例的值
- `DestroyInstance`——通过 `CreateInstance` 返回的索引从列表中移除实例
- `IOControl`——处理任何通过 `KernelLibIOControl` 进行的 IST 调用

下面的清单 8-4 是一个极其简单的可安装 ISR 示例。

*清单 8-4. 过于简化的可安装 ISR 处理程序*

```c
DWORD ISRHandler(DWORD InstanceIndex)
{
    BYTE Value;
    Value = READ_PORT_UCHAR((PUCHAR)PortAddress);
    // 如果中断位被设置，则返回对应的 SYSINTR
    if ( Value & 0x01 )
    {
        return SYSINTR_DEMO;
    }
    else
    {
```



`return SYSINTR_CHAIN;`

`}`

`}`

ISR 处理程序使用端口 I/O 调用来检查设备的状态。你可能需要更复杂的实现，此类 ISR 的示例可在`%WINCEROOT%\public\COMMON\oak\drivers\serial\isr16550`中找到。如果设备不是中断源，返回值将是`SYSINTR_CHAIN`。此返回值告知`NKChainIntr`函数我们的设备不是中断源，并且应评估链中的其他 ISR。如果 ISR 返回有效的`SYSINTR`，则`NKChainIntr`将立即返回，而不会调用列表上的任何其他 ISR。这提供了优先级排序。第一个加载的可安装 ISR 被最先加载到列表上，即最高中断请求级别，后续的可安装 ISR 随后被添加到列表的底部。

[www.it-ebooks.info](http://www.it-ebooks.info/)

