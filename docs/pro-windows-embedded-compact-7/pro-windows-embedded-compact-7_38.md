# 第 7 章：流设备驱动程序精髓

### 用户模式设备驱动程序

用户模式设备驱动程序在用户模式进程的上下文中运行，因此被限制无法调用仅内核可用的 API。用户模式设备驱动程序被加载到一个称为 `udevice.exe` 的用户模式设备驱动程序宿主进程中。这使驱动程序与系统内核隔离，以牺牲性能为代价提高了整体稳定性。基本上，用户模式设备驱动程序是普通的流设备驱动程序，其标志注册表设置中添加了 `DEVFLAGS_LOAD_AS_USERMODE` 标志值。用户模式设备驱动程序无法访问内核模式内存，因此它们能执行的指针和异步内存访问的类型受到限制。

图 7-11 展示了用户模式设备驱动程序如何与设备管理器交互的框图。

*图 7-11. 用户模式设备驱动程序架构*

#### 用户模式设备驱动程序的限制

用户模式设备驱动程序不应使用嵌入式指针。用户模式设备驱动程序反射器将执行必要的封送处理，以便在函数调用中对指针参数进行解引用。然而，反射器无法封送数据结构中的嵌入式指针，因此用户模式驱动程序必须执行必要的封送处理，这很困难。例如，如果驱动程序被内核模式组件调用，嵌入式指针将指向用户模式驱动程序无法访问的内核地址。针对此问题的可移植解决方案是确保所有数据以不包含指针的单一扁平结构传递给驱动程序。

用户模式驱动程序不应设计为对客户端缓冲区实现异步内存访问。内核中的反射器将在同步调用期间封送指针参数，但无法为异步访问封送缓冲区。用户模式驱动程序可以自行封送嵌入式指针以供异步使用，但前提是这些指针指向用户地址空间。

#### 实现用户模式设备驱动程序

实现用户模式设备驱动程序与实现内核模式流设备驱动程序并无不同。主要关注点是记住刚刚讨论的限制并决定用户模式宿主。用户模式设备驱动程序由用户模式进程 `Udevice.exe` 托管；但是，您可以决定是将设备驱动程序托管在自己的宿主中，还是托管在包含一组用户模式设备驱动程序的宿主中。一旦做出此决定，设备管理器将您的设备驱动程序视为用户模式设备驱动程序的唯一方法是将“Flags”键值设置为包含 `DEVFLAGS_LOAD_AS_USERPROC`。

清单 7-19 中的注册表设置示例适用于将托管在其自己的 `Udevice` 进程中的用户模式设备驱动程序。而清单 7-20 中的注册表设置示例适用于将托管在共享 `Udevice` 进程中的用户模式设备驱动程序。后者的 `"ProcGroup"` 键表示它将由注册为进程组 3 的 `Udevice` 进程托管，该进程组由 Microsoft 在注册表中定义（参见清单 7-21）。

**清单 7-19. 由自己的私有 Udevice 进程托管的用户模式设备驱动程序的注册表设置**

```ini
; Umdemodrvr 驱动程序
[HKEY_LOCAL_MACHINE\Drivers\BuiltIn\Umdemodrvr]
"Prefix"="UMD"
"DLL"="Umdemodrvr.DLL"
"Order"=dword:5
"DisplayName"="Umdemodrvr 驱动程序"
"Flags"=dword:10
```

**清单 7-20. 由共享 Udevice 进程托管的用户模式设备驱动程序的注册表设置**

```ini
; Umdemodrvr2 驱动程序
[HKEY_LOCAL_MACHINE\Drivers\BuiltIn\Umdemodrvr2]
"Prefix"="UMD"
"DLL"="Umdemodrvr2.DLL"
"Order"=dword:8
"DisplayName"="Umdemodrvr2 驱动程序"
"Flags"=dword:10
"ProcGroup"=dword:3
```

**清单 7-21. 定义为进程组 3 的共享 Udevice 进程的注册表设置**

```ini
; 在默认 chamber 中运行、普通提升权限、用于微软驱动程序的 udevice.exe
#define PROCGROUP_DRIVER_MSFT_DEFAULT 3
[HKEY_LOCAL_MACHINE\Drivers\ProcGroup_0003]
"ProcName"="udevice.exe"
"ProcVolPrefix"="$udevice"
```

然而，您也可以以相同方式创建自己的进程组，例如进程组 4：`[HKEY_LOCAL_MACHINE\Drivers\ProcGroup_0004]`。

---

#### 加载和初始化用户模式设备驱动程序

加载用户模式设备驱动程序的过程与任何流设备驱动程序加载过程的开始方式相同。

图 7-12 有助于解释加载用户模式设备驱动程序的过程。它显示了顶部调用栈中的加载过程进展，包括反射器的创建；底部调用栈中是驱动程序初始化过程的进展，直到用户模式设备驱动程序的 `xxx_Init` 入口点被调用。

请注意，图中的红色方块实际上在 `BusEnum` 调用 `ActivateDeviceEx` 加载设备驱动程序时就开始了这个过程。第一步是调用 `LoadLib`，这会触发要么找到一个正在运行的 `Udevice` 进程（如果我们的设备驱动程序要在共享宿主中运行），要么创建一个新的 `Udevice` 宿主进程。然后，此进程为我们的设备驱动程序创建一个新的反射器服务（此过程发生在图中顶部区块第 5 行调用 `CreateReflector` 时）。反射器最终加载设备驱动程序 DLL。如果加载成功，反射器的 `Init` 函数会触发对设备驱动程序 `xxx_Init` 入口点的调用。如果设备驱动程序的 `Init` 函数没有失败，则该设备驱动程序将处于活动状态并在宿主 `Udevice` 进程中运行。

*图 7-12. 用户模式设备驱动程序的加载过程*



Windows CE Device Driver Wizard 的选项之一是创建一个快速启动用户模式设备驱动程序。清单 7-22 展示了此类代码的示例。为了演示用户模式设备驱动程序显示 GUI 的能力，我特意为此驱动程序添加了一个私有 `IOCTL`。请注意，在`UMD_Init`中分配内存是使用`VirtualAlloc`而不是`AllocPhysMem`。另外，请注意使用了`PAGE_NOCACHE`。清单 7-23 显示了一个简单的应用程序，它打开设备驱动程序并调用此特定`IOCTL`来显示一个简单的消息框。

该调用的结果显示在图 7-13 中。

*清单 7-22. 用户模式驱动程序中两个入口点的示例代码*

```
DWORD UMD_Init(LPCTSTR pContext, LPCVOID lpvBusContext)
{
    HKEY hConfig;
    DWORD dwRet = 0;
    BOOL bRc = FALSE;
    DWORD dwStatus;
    DDKWINDOWINFO WinInfo;
    PUMD_DEVCONTXT pDevContxt;

    // Allocate device instance context structure
    pDevContxt = (PUMD_DEVCONTXT)VirtualAlloc(NULL,
               sizeof(PUMD_DEVCONTXT), MEM_COMMIT,
               PAGE_NOCACHE |
               PAGE_EXECUTE_READWRITE);
    if (pDevContxt != NULL)
    {
        InitializeCriticalSection(&pDevContxt->CriticalSection);
        pDevContxt->hDriverMan = (HANDLE)GetCurrentProcessId();
    }
    else
    {
        DEBUGMSG(ZONE_INIT, (_T("UMD!not enough memory\r\n")));
        return dwRet;
    }

    // Open active device registry key to retrieve activation
    // info
    DEBUGMSG(ZONE_INIT, (_T("UMD!Starting UMD_Init\r\n")));
    if (hConfig = OpenDeviceKey(pContext))
    {
        // Read window information
        WinInfo.cbSize = sizeof(WinInfo);
        dwStatus = DDKReg_GetWindowInfo(hConfig, &WinInfo);
        if (dwStatus != ERROR_SUCCESS)
        {
            DEBUGMSG(ZONE_INIT,
                     (_T("UMD!Error Getting window information\r\n")));
            VirtualFree(pDevContxt, sizeof(UMD_DEVCONTXT),
                        MEM_DECOMMIT);
            return dwRet;
        }
        pDevContxt->dwBusNumber = WinInfo.dwBusNumber;
        pDevContxt->dwInterfaceType = WinInfo.dwInterfaceType;
    }
    else
    {
        DEBUGMSG(ZONE_INIT,
                 (_T("UMD!Failed to open active device key\r\n")));
        VirtualFree(pDevContxt, sizeof(UMD_DEVCONTXT),
                    MEM_DECOMMIT);
        return dwRet;
    }
    RegCloseKey(hConfig);

    // We got here if all succeded so return the device context
    // pointer
    dwRet = (DWORD)pDevContxt;
    return dwRet;
}
....
....
BOOL UMD_IOControl(DWORD hOpenContext, DWORD dwCode,
                   PBYTE pBufIn,
                   DWORD dwLenIn, PBYTE pBufOut, DWORD dwLenOut,
                   PDWORD pdwActualOut, HANDLE hAsyncRef)
{
    PUMD_DEVCONTXT pDevContxt = (PUMD_DEVCONTXT)hOpenContext;
    BOOL bRet = TRUE;
    DWORD dwErr = 0;

    // TODO: Add code to implement IOCTL codes:
    switch (dwCode)
    {
        case IOCTL_UMDEMODRVR_FOO1:
            // Enter critical section if needed otherwise erase
            // EnterCriticalSection(
            // &pDevContxt->CriticalSection);
            // Add implementation code
            // LeaveCriticalSection(
            // &pDevContxt->CriticalSection);
            MessageBox(NULL,
                       _T("User Driver Message Box"),
                       _T("User Mode Device Driver"), MB_OK);
            break;

        default:
            break;
    }
}
```

*清单 7-23. 调用用户模式设备驱动程序显示消息框的应用程序示例代码*

```
int _tmain(int argc, TCHAR *argv[], TCHAR *envp[])
{
    BOOL bRet = FALSE;
    HANDLE hTstDrvr = INVALID_HANDLE_VALUE;

    // Try open an instance of TestDrvr
    hTstDrvr = CreateFile(L"UMD1:",
                          GENERIC_READ | GENERIC_WRITE,0,
                          NULL,OPEN_EXISTING,0,NULL);
    if (INVALID_HANDLE_VALUE == hTstDrvr) {
        DWORD bdw = GetLastError();
        return FALSE;
    }

    bRet = DeviceIoControl(hTstDrvr,IOCTL_UMDEMODRVR_FOO1,
                           NULL, 0, NULL, 0, NULL, NULL);

    return 0;
}
```

*图 7-13. 显示消息框的用户模式设备驱动程序*

### 章节总结

流设备驱动程序一直是 Windows CE 在其存在期间的中流砥柱。早期版本的 Windows CE（直至 Windows CE 6.0）与 Windows CE 6.0 及其后继者 Windows Embedded Compact 7 之间的主要区别在于，在旧版本中，由于架构原因，流设备驱动程序是用户模式驱动程序。然而，在最后两个版本中，流设备驱动程序可以驻留在内核空间或用户空间中，因此有了内核模式流设备驱动程序和用户模式流设备驱动程序。前者提供了更好的性能，后者则提供了健壮性，特别是对于可安装的设备驱动程序。最新版本的 Windows CE 设备管理器使我们能够开发支持异步 I/O 请求处理的设备驱动程序。设备管理器允许为各种功能创建筛选器驱动程序。

