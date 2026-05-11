# 第 8 章 设备驱动 I/O 与中断

为了兼顾优先级和执行速度，链中最高中断请求级别的可安装 ISR 应首先安装。

### 嵌套 ISR

由于 Windows Embedded Compact 7 是一个 RTOS，它需要在最高中断请求级别的中断被触发后尽快为其提供服务，以防止丢失或延迟，因此内核采用中断嵌套。嵌套中断允许更高中断请求级别的中断抢占较低中断请求级别的 IRQ。内核采取以下步骤来处理嵌套中断：

- 内核禁用与当前 ISR 调用所触发的 IRQ 相同或更低 IRQ 级别的所有其他 IRQ。
- 如果在当前 ISR 完成处理之前到达了更高 IRQ 级别，内核会保存当前 ISR 的状态（状态仅包含 ISR 中支持使用的寄存器，以节省时间）。
- 内核调用该 IRQ 级别的 ISR 来处理新的请求。
- 内核恢复原始 ISR 的状态并继续处理。

### 中断服务线程 (IST)

IST 是一个线程，当 ISR 返回相关的`SYSINTR`时，该线程被调度。一旦完成中断处理，它会通知内核重新启用与该 IST 相关的 I/O 设备上的物理中断，并阻塞等待，直到需要处理下一个中断。

创建和启动 IST 的典型步骤如下：

- 创建一个事件
- 将事件与对应的`SYSINTR`关联
- 创建 IST 本身
- 设置 IST 的优先级
- 可选地设置 IST 的时间片

清单 8-5 显示了由设备驱动向导生成的用于创建和启动中断服务线程的示例代码。

*清单 8-5. 创建 IST 的示例实现*

```
DWORD CreateInterruptServiceThread(PDMO_DEVCONTXT pDevCntxt)
{
    pDevCntxt->hIstEvent = CreateEvent(NULL, FALSE, FALSE, NULL);
    if (pDevCntxt->hIstEvent == NULL)
    {
        DEBUGMSG(ZONE_INIT,
            (_T("DMO!IST Create: Event creation failed\r\n")));
        return -1;
    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

