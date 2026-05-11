# 第 7 章
## 流设备驱动程序的精髓

流接口设备驱动程序是 Windows Embedded Compact 7（以及早期版本的 Windows CE）中最常开发的设备驱动程序。流设备驱动程序由设备管理器加载和管理，并提供众所周知的统一接口。因此，它允许用户模式应用程序访问流接口设备驱动程序的服务。用户模式应用程序使用文件系统 API（如`CreateFile`）打开流接口设备驱动程序的实例，并使用文件 I/O API 从设备读取或向设备写入数据。流接口可以存在于内核模式设备驱动程序以及用户模式设备驱动程序中，并且在此最新版本的操作系统中也存在于过滤器设备驱动程序中。

### 本章内容

- 流接口设备驱动程序
- 内核模式设备驱动程序
- 过滤器设备驱动程序
- 用户模式设备驱动程序

#### 流接口设备驱动程序

流接口驱动程序是任何暴露一组构成流接口函数的已知入口点的设备驱动程序。流接口并不意味着由此类设备驱动程序控制的硬件设备必须处理流数据（例如串行通信端口）。由于块设备（如存储磁盘）是使用流接口实现的，因此流接口函数被设计为与文件系统 API（如`ReadFile`）的语义强匹配。因此，流接口设备驱动程序通过文件系统间接暴露给应用程序；应用程序通过将设备驱动程序作为文件系统中的特殊文件打开来与驱动程序交互。

设备管理器通过文件系统 API 将应用程序请求转换为实际调用相关设备驱动程序入口点的命令。设备驱动程序封装了将所有必要信息转换为对它所控制的设备执行适当操作所需的全部内容。所有流接口驱动程序，无论是管理内置设备还是可安装设备，无论是在引导时加载还是动态加载，都与其他系统组件具有类似的交互。

图 7-1 显示了管理内置设备的通用流接口驱动程序与系统组件之间的交互。

**图 7-1. 用户模式应用程序与内核模式流接口驱动程序之间的交互**

#### 流接口设备驱动程序的结构

任何流接口设备驱动程序都是一个由设备管理器加载和托管的动态链接库。流接口设备驱动程序必须暴露流接口，这是一组设备管理器可以调用的已知入口点。图 7-2 展示了流接口设备驱动程序结构的可视化。在此可视化中，要么是单片式流接口设备驱动程序，其中流接口嵌入了处理硬件设备的代码；而在分层式流接口设备驱动程序中，可能存在 DDSI 层和 PDD 层中的硬件相关代码。

**图 7-2. 流接口设备驱动程序的结构选项**

#### 流接口入口点



#### 流接口设备驱动程序

任何流接口设备驱动程序都必须提供一组流接口入口点。如果不想实现某个入口点，只需将其实现为空操作存根即可。如果在驱动程序的注册表子项 `Flags` 中指定了 `DEVFLAGS_NAKEDENTRIES`，则入口点名称可以不加修饰；例如 `Open`、`Close` 等。但这并不排除设备驱动程序注册表设置中存在前缀条目。例如，即使入口点名称未加修饰，电池驱动程序的注册表设置仍必须包含前缀。

这些入口点的实现必须通过提供模块定义文件声明为从 DLL 导出。如果使用 C++ 进行开发，则入口点还必须声明为 `extern "C"`，以防止 C++ 编译器对函数名进行重整。下表显示了流接口的入口点。

Windows Embedded Compact 7 中有一些显著变化，因为设备管理器支持异步 I/O 请求。有一个之前不存在的全新入口点，并且有三个入口点接受一个新参数。这个新参数是 I/O 请求包对象的句柄。

##### `XXX_Cancel`

此函数是 Windows Embedded Compact 7 新增的；它取消指定设备所有挂起的异步 I/O 请求。在上述示例中，实现非常简单。无需进行太多清理工作，因为当应用程序调用 `CancelIo` 或 `CancelIoEx` 时，设备管理器会调用 I/O 包管理器来清理所有内容。由于正是 I/O 包管理器创建了 I/O 包并将其添加到其管理的列表中，因此设备驱动程序开发者所需做的只是终止 I/O 请求处理线程，并清理该线程的参数结构。清单 7-4 展示了一个极其简单的实现。

**清单 7-4.** 简单的 `xxx_Cancel` 实现

```c
BOOL TST_Cancel(DWORD dwOpenContext, HANDLE hAsyncHandle)
{
    DWORD dwRet = 0;
    PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT) dwOpenContext;

    // TODO: 添加代码以实现取消 IO：
    TerminateThread(g_hAsyncThread, 0);
    return dwRet;
}
```

##### `XXX_Close`

`XXX_Close` 函数关闭由 `hOpenContext` 标识的设备上下文。它由应用程序调用 `CloseHandle` 来关闭 `CreateFile` 返回的句柄时触发。此函数是通过 `CreateFile` 访问设备的必要条件。清单 7-5 展示了关闭设备驱动程序上下文的示例。如果设备驱动程序可以被打开多个实例，则需要管理实例列表，并从列表中删除并清空正在关闭的实例。

**清单 7-5.** `XXX_Close` 实现示例

```c
BOOL TST_Close(DWORD hOpenContext)
{
    BOOL bRet = TRUE;
    PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT) hOpenContext;

    InterlockedDecrement(&pDevContxt->lOpenCount);
    InterlockedExchange((volatile LONG*)&pDevContxt->bOpenEx, FALSE);

    // TODO: 添加代码以实现关闭实例：
    return bRet;
}
```

##### `XXX_Deinit`

`XXX_Deinit` 函数用于取消初始化设备。其实现应是初始化过程的逆过程。`XXX_Deinit` 由设备管理器调用。此函数是由 `ActivateDeviceEx`、`ActivateDevice` 加载的驱动程序所必需的。清单 7-6 展示了 `XXX_Deinit` 函数的简单实现，它是清单 7-7 中 `XXX_Init` 的逆过程。

**清单 7-6.** `XXX_Deinit` 函数的简单实现

```c
BOOL TST_Deinit(DWORD hDeviceContext)
{
    BOOL bRet = TRUE;
    PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT) hDeviceContext;

    // TODO: 添加代码以实现取消初始化：
    // 销毁 IST 过程并释放 DMA 缓冲区
    DestroyInterruptServiceThread(pDevContxt);
    DMADeInitialize(0, pDevContxt);
    FreePhysMem(pDevContxt);
    return bRet;
}
```

##### `XXX_Init`



`XXX_Init`函数初始化一个设备。它由设备管理器在加载设备驱动程序时调用。

该函数的实现不会影响设备驱动程序的性能，因为它仅在加载时执行一次。受控硬件和驱动程序内部信息的所有初始化，以及中断服务例程、中断服务线程和内存访问方法（如 DMA）的设置，都应在此函数中执行。由`ActivateDeviceEx`、`ActivateDevice`或`RegisterDevice`加载的驱动程序需要此函数。清单 7-7 展示了一个初始化函数实现的综合示例。

*清单 7-7. `XXX_Init`函数实现的综合示例*

```
DWORD TST_Init(LPCTSTR pContext, LPCVOID lpvBusContext)
{
    HKEY hConfig;
    DWORD dwRet = 0;
    BOOL bRc = FALSE;
    DWORD dwStatus;
    DDKWINDOWINFO WinInfo;
    ULONG unIoSpace = 1;
    DDKISRINFO IsrInfo;
    PTST_DEVCONTXT pDevContxt = NULL;

    // 分配设备实例上下文结构
    DWORD dwPhAdd;
    pDevContxt = (PTST_DEVCONTXT)AllocPhysMem(sizeof(TST_DEVCONTXT),
        PAGE_READWRITE, 0, 0, &dwPhAdd);
    if (pDevContxt != NULL)
    {
        InitializeCriticalSection(&pDevContxt->CriticalSection);
        pDevContxt->hDriverMan = (HANDLE)GetCurrentProcessId();
    }
    else
    {
        DEBUGMSG(ZONE_INIT, (_T("TST!内存不足\r\n")));
        return dwRet;
    }

    // 打开活动设备注册表键以检索激活
    // 信息
    DEBUGMSG(ZONE_INIT, (_T("TST!开始 TST_Init\r\n")));
    if (hConfig = OpenDeviceKey(pContext))
    {
        // 读取窗口信息
        WinInfo.cbSize = sizeof(WinInfo);
        dwStatus = DDKReg_GetWindowInfo(hConfig, &WinInfo);
        if (dwStatus != ERROR_SUCCESS)
        {
            DEBUGMSG(ZONE_INIT, (_T("TST!获取窗口信息时出错\r\n")));
            FreePhysMem(pDevContxt);
            return dwRet;
        }
        pDevContxt->dwBusNumber = WinInfo.dwBusNumber;
        pDevContxt->dwInterfaceType = WinInfo.dwInterfaceType;

        // 读取 ISR 信息
        IsrInfo.cbSize = sizeof(IsrInfo);
        dwStatus = DDKReg_GetIsrInfo(hConfig, &IsrInfo);
        if (dwStatus != ERROR_SUCCESS)
        {
            DEBUGMSG(ZONE_INIT,
                (_T("TST!获取 ISR 信息时出错\r\n")));
            FreePhysMem(pDevContxt);
            return dwRet;
        }
    }
    else
    {
        DEBUGMSG(ZONE_INIT,
            (_T("TST!无法打开活动设备键\r\n")));
        FreePhysMem(pDevContxt);
        return dwRet;
    }

    RegCloseKey(hConfig);

    // 获取硬件 I/O 地址 - 初始化为内存映射
    // IO 空间
    pDevContxt->ioPhysicalBase.LowPart = WinInfo.memWindows[0].dwBase;
    pDevContxt->ioPhysicalBase.HighPart = 0;
    pDevContxt->dwIoRange = WinInfo.memWindows[0].dwLen;
    if (HalTranslateBusAddress(
            (INTERFACE_TYPE)pDevContxt->dwInterfaceType,
            WinInfo.dwBusNumber,
            pDevContxt->ioPhysicalBase, &unIoSpace,
            &pDevContxt->ioPhysicalBase))
    {
        // 这是内存映射 IO，不在 IO 空间中
        if (!unIoSpace)
        {
            pDevContxt->bIoMemMapped = TRUE;
            if ((pDevContxt->dwIoAddr =
                (DWORD)MmMapIoSpace(pDevContxt->ioPhysicalBase,
                pDevContxt->dwIoRange, FALSE)) == NULL)
            {
                DEBUGMSG(ZONE_INIT,
                    (_T("TST!映射 IO 端口失败\r\n")));
                FreePhysMem(pDevContxt);
                // 你可能需要在此处禁用 IO 设备
                return dwRet;
            }
            else
            {
                pDevContxt->bIoMemMapped = FALSE;
                pDevContxt->pIoAddr =
                    (UCHAR)pDevContxt->ioPhysicalBase.LowPart;
            }
        }
    }
    else
    {
        DEBUGMSG(ZONE_INIT,
            (_T("TST!HalTranslateBusAddress 调用失败\r\n")));
        FreePhysMem(pDevContxt);
        // 你可能需要在此处禁用 IO 设备
        return dwRet;
    }

    bRc = DMAInitialize(0, pDevContxt); // 一个设备驱动程序
    // 本地函数
    if (!bRc)
    {
        FreePhysMem(pDevContxt);
        return 0;
    }
}
```



