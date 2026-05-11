# 第 8 章 设备驱动 I/O 与中断

开发设备驱动时的最佳实践是将这些值设置在注册表中，如清单 8-8 中的注册表项示例所示。请注意，此示例描述了 x86 平台上的设备驱动，其中`IoBase`表示 I/O 空间地址。如果这些设置用于内存映射 I/O 的设备驱动，则应使用`MemBase`和`MemLen`设置值。

*清单 8-8. 演示设备驱动的注册表项*

```
; DemoDrvr driver
[HKEY_LOCAL_MACHINE\Drivers\BuiltIn\Demodrvr]
"Prefix"="DMO"
"DLL"="Demodrvr.DLL"
"SysIntr"=dword:20
"Irq"=dword:13
"IoBase"=dword:F0
"IoLen"=dword:16
"Order"=dword:0
"DisplayName"="DemoDrvr driver"
"IsrDll"="giisr.dll"
"IsrHandler"="ISRHandler"
"Flags"=dword:0
"IClass"="{A32942B7-920C-486b-B0E6-92A702A99B35}"
```

### 端口映射 I/O



端口映射 I/O 使用一组专门的 CPU 指令来执行 I/O 操作。这种技术常见于 x86 架构，具体体现为`IN`和`OUT`指令，它们能够对外围 I/O 设备读取或写入 1 到 4 个字节。外围 I/O 设备的地址空间与系统内存空间是分离的，这是通过在 CPU 物理接口上增加一个额外的引脚来实现的。由于 I/O 的地址空间与主内存隔离，这种方式也被称为隔离 I/O。

内核模式设备驱动程序开发者应使用`ceddk.h`中声明的一组函数来读写端口映射 I/O 端口。例如，`READ_PORT_UCHAR`函数可以从外围设备的一个字节宽端口读取一个字节。

清单 8-9 展示了`ceddk.h`中的声明。`ddk_io`库中的实现很简单，演示了如何使用端口映射 I/O 的特殊指令，见清单 8-10。

*清单 8-9. READ_PORT_UCHAR 的声明*

```
NTKERNELAPI
UCHAR
READ_PORT_UCHAR(
__in volatile const UCHAR * const Port
);
```

*清单 8-10. ddk_io 库中 READ_PORT_UCHAR 的实现*

```
UCHAR
READ_PORT_UCHAR(
volatile const UCHAR * const Port
)
{
#if defined(x86)
__asm {
mov dx, word ptr Port
in al, dx
}
#else
return *(volatile UCHAR * const)Port;
#endif
}
```

关于如何使用这些宏的示例，请参见清单 8-11，它是一段设备驱动程序实现的一部分，该驱动从基于 eBox3300 的设备的 GPIO 端口 1 读取数据。

*清单 8-11. 使用 WRITE_PORT_UCHAR 和 READ_PORT_UCHAR 宏*

```
UCHAR chVal = 0;

// 将 GPIO 端口 1 设置为输入方向
WRITE_PORT_UCHAR(0x2E, 0xF3);
WRITE_PORT_UCHAR(0x2F, 0xFF);

// 从 GPIO 端口 1 读取数据
WRITE_PORT_UCHAR(0x2E, 0xF4);
chVal = READ_PORT_UCHAR(0x2F);
```

以下是 CEDDK 支持的端口映射 I/O 端口访问函数表：

- `READ_PORT_BUFFER_UCHAR` - 此函数从指定的端口 I/O 地址读取多个字节到缓冲区。
- `READ_PORT_BUFFER_ULONG` - 此函数从指定的端口地址读取多个 `ULONG` 值到缓冲区。
- `READ_PORT_BUFFER_USHORT` - 此函数从指定的端口地址读取多个 `USHORT` 值到缓冲区。
- `READ_PORT_UCHAR` - 此函数从指定的端口地址读取一个字节。
- `READ_PORT_ULONG` - 此函数从指定的端口地址读取一个 `ULONG` 值。
- `READ_PORT_USHORT` - 此函数从指定的端口地址读取一个 `USHORT` 值。
- `WRITE_PORT_BUFFER_UCHAR` - 此函数将缓冲区中的多个字节写入到指定端口。
- `WRITE_PORT_BUFFER_ULONG` - 此函数将缓冲区中的多个 `ULONG` 值写入到指定的端口地址。
- `WRITE_PORT_BUFFER_USHORT` - 此函数将缓冲区中的多个 `USHORT` 值写入到指定的端口地址。
- `WRITE_PORT_UCHAR` - 此函数向指定的端口地址写入一个字节。
- `WRITE_PORT_ULONG` - 此函数向指定的端口地址写入一个 `ULONG` 值。
- `WRITE_PORT_USHORT` - 此函数向指定的端口地址写入一个 `USHORT` 值。

### 内存映射 I/O

在内存映射 I/O 中，外围设备的寄存器是系统地址空间的一部分，其访问方式与访问任何内存位置相同，并且成为内核虚拟地址空间的一部分。外围设备的一组寄存器的基地址由硬件映射地址表示。在开发时，我们必须将其转换为内核空间的虚拟内存基地址。

这是通过调用 `MmMapIoSpace` 来实现的。调用此函数可将寄存器组的物理基地址映射到一个非分页的、直接映射到设备的内核虚拟地址空间。

设备驱动程序开发者应在设备驱动初始化代码中调用此函数，以便在 `HalTranslateBusAddress` 表明总线的设备内存范围可映射到系统内存地址时，为设备驱动程序的内存范围获取一个虚拟地址。清单 8-12 演示了设备驱动程序初始化代码中的一个代码片段。

请注意清单 8-10 中的实现，对于非 x86 平台，实现仅仅是读取一个内存地址。对内存映射 I/O 寄存器的读写自然由 `READ_REGISTER_UCHAR` 来处理，因为它的实现直接且简单，如清单 8-13 所示。

*清单 8-12. 将物理地址空间映射到虚拟地址空间的示例代码*

```
ULONG unIoSpace = 1;

// 获取硬件 I/O 地址 - 初始化为内存映射 IO 空间
pDevContxt->ioPhysicalBase.LowPart = WinInfo.memWindows[0].dwBase;
pDevContxt->ioPhysicalBase.HighPart = 0;

pDevContxt->dwIoRange = WinInfo.memWindows[0].dwLen;

if (HalTranslateBusAddress(
(INTERFACE_TYPE)pDevContxt->dwInterfaceType,
WinInfo.dwBusNumber,
pDevContxt->ioPhysicalBase,
&unIoSpace,
&pDevContxt->ioPhysicalBase))
{
// 判断是内存映射 IO 还是 IO 空间。
if (!unIoSpace)
{
pDevContxt->bIoMemMapped = TRUE;
if ((pDevContxt->dwIoAddr = (DWORD)MmMapIoSpace(
pDevContxt->ioPhysicalBase,
pDevContxt->dwIoRange, FALSE)) == NULL)
{
DEBUGMSG(ZONE_INIT,
(_T("DMO!Error mapping IO Ports failure\r\n")));
FreePhysMem(pDevContxt);
// 你可能需要在此处禁用 IO 设备
return dwRet;
}
}
else
{
pDevContxt->bIoMemMapped = FALSE;
pDevContxt->IoAddr = pDevContxt->ioPhysicalBase.LowPart;
}
}
else
{
DEBUGMSG(ZONE_INIT,
(_T("DMO!Error HalTranslateBusAddress call failure\r\n")));
FreePhysMem(pDevContxt);
// 你可能需要在此处禁用 IO 设备
return dwRet;
}
```

*清单 8-13. ddk_io 库中 READ_REGISTER_UCHAR 的实现*

```
UCHAR
READ_REGISTER_UCHAR(
volatile const UCHAR * const Register
)
{
return (*(volatile UCHAR * const)Register);
}
```

CEDDK 实现了一组类似的函数，用于对内存映射 I/O 寄存器进行快速高效的访问。

- `READ_REGISTER_BUFFER_UCHAR` - 此函数从指定的寄存器内存地址读取多个字节到缓冲区。
- `READ_REGISTER_BUFFER_ULONG` - 此函数从指定的寄存器地址读取多个 `ULONG` 值到缓冲区。
- `READ_REGISTER_BUFFER_USHORT` - 此函数从指定的寄存器地址读取多个 `USHORT` 值到缓冲区。
- `READ_REGISTER_UCHAR` - 此函数从指定的寄存器地址读取一个字节。
- `READ_REGISTER_ULONG` - 此函数从指定的寄存器地址读取一个 `ULONG` 值。
- `READ_REGISTER_USHORT` - 此函数从指定的寄存器地址读取一个 `USHORT` 值。
- `WRITE_REGISTER_BUFFER_UCHAR` - 此函数将缓冲区中的多个字节写入到指定的寄存器。
- `WRITE_REGISTER_BUFFER_ULONG` - 此函数将缓冲区中的多个 `ULONG` 值写入到指定的寄存器。
- `WRITE_REGISTER_BUFFER_USHORT` - 此函数将缓冲区中的多个 `USHORT` 值写入到指定的寄存器。
- `WRITE_REGISTER_UCHAR` - 此函数向指定的地址写入一个字节。
- `WRITE_REGISTER_ULONG` - 此函数向指定的地址写入一个 `ULONG` 值。
- `WRITE_REGISTER_USHORT` - 此函数向指定的地址写入一个 `USHORT` 值。

### 章节总结



### 现代实时系统与中断处理

现代实时系统已从轮询输入转变为等待输入到达。这之所以成为可能，是因为硬件已大幅进步，从输入到达时刻到处理完成时刻之间的处理延迟已显著降低。如今的实时系统能够实现极小的中断延迟，更重要的是，这些系统能将抖动控制在可接受的水平。

本章讨论了 Windows Embedded Compact 7 的中断模型，该模型与 Windows CE 6.0 基本相同，并且与 Windows CE 5.0 等早期版本非常相似。一个由内核异常处理程序触发的、代码量极少的快速 ISR，以及由一个普通线程执行的延迟中断处理，该线程在处理完中断后应释放中断。这种线程可以被赋予与它所关联的中断请求级别相对应的调度优先级，从而实现一致且良好的实时性能。

此外，为了增强实时性能，ISR 的执行可以被内核抢占，以允许更高中断请求级别的 ISR 在执行返回之前先执行完成。这被称为**嵌套中断**。这确保了关键 I/O 设备的中断能够比非关键设备的中断得到更优先的服务。

I/O 设备资源的访问方式有两种：要么将这些资源映射到系统内存，要么通过特殊的 CPU 指令进行访问（例如 x86 架构所支持的，将资源映射到特定的 I/O 内存范围）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 9 章：设备 I/O 控制处理

应用程序通过文件系统 API 来访问流设备驱动程序。这实际上限制了设备驱动程序的接口能力。文件系统接口允许开发者执行的操作仅限于读取、写入或查找。这对于真正的文件 I/O 来说是可行的，但流设备驱动程序虽然被视为文件，却截然不同。为了允许设备驱动程序传达其对其控制的外围设备执行的自定义操作，创建了一种特殊的操作模式。这种方法并非 Windows Embedded Compact 7 或 Windows CE 所独有，它是 Win32 API 的一部分，并且自 1979 年以来一直是 UNIX 及类 UNIX 系统的一部分。它允许流设备驱动程序通过调用 `DeviceIoControl` 函数来向应用程序传递自定义操作。该函数由设备管理器实现并公开，因此可以从用户模式应用程序内部调用。

### 本章内容

- 什么是 IOCTL
- 添加设备 IOCTL
- 处理设备 IOCTL

### 什么是 IOCTL

`IOCTL` 字面意思是输入输出控制。流设备驱动程序可以被视为代表物理设备的设备文件。物理设备用于输入、输出或两者兼有，因此内核中的设备驱动程序必须有一些机制来与用户模式进程进行通信。由于使用文件系统 API 将流设备驱动程序作为文件打开，意味着我们可以使用文件系统 API `WriteFile` 将数据从用户模式应用程序发送到外围设备进行输出。然而，如果用户模式应用程序需要与同一个外围设备通信，目的不是为了发送数据，而是为了获取设备状态，会发生什么？这就是 `IOCTL` 中“控制”部分的用武之地。它确实为用户模式应用程序提供了一种通过编程方式直接控制外围设备的方法。

那么，从技术角度来看，`IOCTL` 到底是什么？`IOCTL` 是一个 32 位值，分为四个字段，构成一个唯一的系统 IOCTL：

- **A. Kcholi** **P**，**《Windows Embedded Compact 7 专业版》** © Abraham Kcholi 2011

[www.it-ebooks.info](http://www.it-ebooks.info/)

#### 设备类型 | 功能代码 | 请求 | 访问 | 方法

```
31                    16  14             2   0
```



##### 设备类型

-   **设备类型** - 这是 IOCTL 所属的设备类型。可通过设置 `Common` 位由用户自定义。此类型必须与设备对象的设备类型匹配。Windows Embedded Compact 特定的设备类型包括：
    - `FILE_DEVICE_HAL`
    - `FILE_DEVICE_CONSOLE`
    - `FILE_DEVICE_PSL`
    - `FILE_DEVICE_SERVICE`
-   **所需访问权限** - 这是设备所需的访问权限，例如 `FILE_READ_DATA`、`FILE_WRITE_DATA` 等。
-   **功能代码** - 这是系统或用户根据自定义位定义的功能代码。
-   **方法** - 这是用于 Windows Embedded Compact 的数据传输方法，仅应使用 `METHOD_BUFFERED`。

##### 内核 IOCTL

内核，如同内核模式设备驱动程序一样，提供了一种通过内核 API `KernelIoControl` 从内核模式组件发起控制请求的方法。由于 I/O 控制意味着对硬件的控制，该调用实际上会调用一个由 OEM 提供的函数 `OEMIoControl`，该函数是实现这些 IOCTL 的真正函数。请记住，这是基于 Windows Embedded Compact 设备并非通用设备，而是基于大多是唯一的硬件组合这一前提。因此，只有 OEM 才能对其硬件提供一致的 I/O 控制。

`OEMIoControl` 实现了 Microsoft 定义的一系列标准 IOCTL。这些 IOCTL 提供了在启动过程的关键点回调 OAL、从 OAL 检索系统信息、管理中断标识符等机制。OEM 可以扩展 IOCTL 接口，以实现其认为适合系统高级操作的任何其他自定义功能。例如，OAL 有时用于管理对共享系统范围资源（如 GPIO）的访问。

Windows Embedded Compact 7 平台生成器在其平台公共源码中提供了 `OEMIoControl` 的实现。清单 9-1 展示了平台公共源码中提供的 `OEMIoControl` 函数的实现。该函数由调用 `KernelIoControl` 的内核设备驱动程序调用。此函数的唯一目的是允许 OEM 设备驱动程序与内核模式代码进行通信。

[www.it-ebooks.info](http://www.it-ebooks.info/)

