# 第 8 章 设备驱动 I/O 与中断

*清单 8-6. 骨架 IST 的示例实现*

```
DWORD DMOInterruptServiceThread(LPVOID lpParameter)
{
    PDMO_DEVCONTXT pParam = (PDMO_DEVCONTXT)lpParameter;
    while(WaitForSingleObject(pParam->hIstEvent, INFINITE))
    {
        if (pParam->bShutDown)
        {
            ExitThread(0);
        }

        // TODO: Write code to process interrupt

        // Complete interrupt and reanable interrupt
        InterruptDone(pParam->dwSysIntr);
    }
    return 0;
}
```

### I/O 内存映射

中断处理的一个重要部分是检索导致中断触发的数据。外围 I/O 设备提供一组端口或寄存器，通过这些端口或寄存器可以检索数据。

访问这些端口意味着我们需要某种方式来寻址这些端口或寄存器，并进行读取或写入操作。寻址外围 I/O 设备上的这些位置是本节的讨论主题。根据架构的不同，CPU 与外围 I/O 设备之间执行 I/O 的两种方法是：内存映射 I/O 和端口映射 I/O。

I/O 基地址和长度信息对于开发设备驱动至关重要。I/O 基地址是外围设备第一个寄存器所在的地址，无论是内存映射还是 I/O 空间映射。长度则更为人所知，是外围设备占用的寄存器范围。

例如，PC 上经典的 COM1 端口（记住它是 I/O 空间）的基地址为`0x3F8`，长度为 8，因为其范围为`0x3F8`到`0x3FF`。长度始终以字节表示。因此，如果一个外围设备可以用清单 8-7 中的结构来描述，它的长度将为 32 字节。

*清单 8-7. 一个演示外围设备 'C' 结构描述*

```
typedef struct
{
    volatile UINT32 REG0; // offset 0x0000, REG0
    volatile UINT32 Reserved[3];
    volatile UINT32 REG1; // offset 0x0010, REG1
    volatile UINT32 REG2; // offset 0x0014, REG2
    volatile UINT32 REG3; // offset 0x0018, REG3
    volatile UINT32 REG4; // offset 0x001C, REG4
} DEMO_REGS *PDEMO_REGS;
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

