# 第 9 章 ■ 设备 I/O 控制处理

*清单 9-1. **OEMIoControl** 的通用实现*

```
BOOL OEMIoControl(DWORD code, VOID *pInBuffer, DWORD inSize,
                  VOID *pOutBuffer, DWORD outSize, DWORD *pOutSize)
{
    BOOL rc = FALSE;
    UINT32 i;

    OALMSG(OAL_IOCTL&&OAL_FUNC,
           (L"+OEMIoControl(0x%x, 0x%x, %d, 0x%x, %d, 0x%x)\r\n", code, pInBuffer,
            inSize, pOutBuffer, outSize, pOutSize));

    // 当调用 IOCTL_HAL_POSTINIT 时
    // 初始化 g_ioctlState.cs。
    // 此时，内核已启动并已准备好处理
    // 临界区初始化。
    if (!g_ioctlState.postInit && code == IOCTL_HAL_POSTINIT)
    {
        // 初始化临界区
        InitializeCriticalSection(&g_ioctlState.cs);
        g_ioctlState.postInit = TRUE;
    }

    // 在 IOCTL 表中搜索请求的代码。
    for (i = 0; g_oalIoCtlTable[i].pfnHandler != NULL; i++)
    {
        if (g_oalIoCtlTable[i].code == code) break;
    }

    // 指示不支持的代码
    if (g_oalIoCtlTable[i].pfnHandler == NULL)
    {
        NKSetLastError(ERROR_NOT_SUPPORTED);
        OALMSG(OAL_IOCTL,
               (L"OEMIoControl: Unsupported Code 0x%x – device 0x%04x func %d\r\n",
                code, code >> 16, (code >> 2)&0x0FFF));
        goto cleanUp;
    }

    // 如果需要则进入临界区（在 postinit 之后且无标志）
    if (g_ioctlState.postInit &&
        (g_oalIoCtlTable[i].flags & OAL_IOCTL_FLAG_NOCS) == 0)
    {
        rc = DoOEMIoControlWithCS(i, code, pInBuffer, inSize,
                                  pOutBuffer, outSize, pOutSize);
    }
    else
    {
        rc = g_oalIoCtlTable[i].pfnHandler(code, pInBuffer,
                                           inSize,
                                           pOutBuffer, outSize,
                                           (UINT32 *)pOutSize);
    }

cleanUp:
    OALMSG(OAL_IOCTL&&OAL_FUNC,
           (L"-OEMIoControl(rc = %d)\r\n", rc));
    return rc;
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)



Windows Embedded Compact 7 的一项新功能是模板板级支持包（BSP），它位于`PLATFORM`根目录下。第一个教程提供了示例入门代码，用于为特定 BSP 实现 OEM IOCTL 的编码。列表 9-2 展示了此实现的示例代码。

*列表 9-2\. `%PLATFORMROOT%\BSPTemplate\tutorial\1\oal\oallib\ioctl.c`的内容*

```c
#include <windows.h>
// This contains IOCTL codes needed by oal_ioctl_tab.h
#include <pkfuncs.h>
// This is the platform\common ioctl header.
#include <oal_ioctl.h>
#include <oemglobal.h>

// --------------------------------------------------------------
// OEMIoControl: REQUIRED
//
// This function provides a generic I/O control code (IOCTL) for
// OEM-supplied information.
//
// OEMIoControl is supplied by a platform\common library in this
// tutorial, so we should not implement it here in the BSP.
// --------------------------------------------------------------
// g_oalIoCtlTable: REQUIRED
//
// The platform\common implementation requires that we define
// this variable describing which IOCTLs we support.
//
const OAL_IOCTL_HANDLER g_oalIoCtlTable[] = {
// Required Termination
{ 0, 0, NULL }
};

148
[www.it-ebooks.info](http://www.it-ebooks.info/)
第 9 章 ■ 设备 I/O 控制处理

// --------------------------------------------------------------
// OEMKDIoctl: OPTIONAL
//
// This function supports requests from the kernel debugger.
//
BOOL OEMKDIoctl(DWORD dwCode, LPVOID pBuf, DWORD cbSize)
{
// Fill in IOCTL code here.
return TRUE;
}

// --------------------------------------------------------------
// OEMIsProcessorFeaturePresent: OPTIONAL
//
// This function provides information about processor features
// and support.
//
BOOL OEMIsProcessorFeaturePresent(DWORD dwProcessorFeature)
{
// Fill in processor feature code here.
return TRUE;
}
```

在 ebox3300 BSP 中可以找到实现此代码的示例，它演示了 OEM 如何将 BSP 特定函数关联到各种预定义的 IOCTL，以及针对`IOCTL_HAL_POSTINIT`的自定义处理程序。列表 9-3 是一个简化版本，我省略了一些对理解示例代码不重要的部分，但保留了大部分注释。请注意其中加粗并带下划线的代码行，它将 IOCTL 连接到稍后在代码中实现的函数。

*列表 9-3\. `%PLATFORM%\ebox3300\src\oal\oallib\ioctl.c`的部分内容*

```c
#include <windows.h>
#include <pkfuncs.h>
#include <oal.h>
// This is the platform\common ioctl headers.
#include <oal_intr.h>
#include <oal_io.h>
#include <oal_ioctl.h>
#include <x86ioctl.h>

// Cache Information for Vortex86DX (A9121) rev. D and
// Vortex86MX/DX-II chips
//
const CacheInfo Vortex86MXCacheInfo =
{
……
……
};

149
[www.it-ebooks.info](http://www.it-ebooks.info/)
第 9 章 ■ 设备 I/O 控制处理

// Declaration of x86 common libraries in platform\common
void RTCPostInit();

// forward declarations of custom IOCTL handler functions
BOOL BSPIoCtlPostInit(
    UINT32 code, VOID *lpInBuf, UINT32 nInBufSize,
    VOID *lpOutBuf, UINT32 nOutBufSize, UINT32 *lpBytesReturned
);

BOOL BSPIoCtlHalGetCacheInfo (
    UINT32 code, VOID *lpInBuf, UINT32 nInBufSize,
    VOID *lpOutBuf, UINT32 nOutBufSize, UINT32 *lpBytesReturned
);

// g_pPlatformManufacturer: REQUIRED
// The platform\common implementation requires that we define
// this variable for device info ioctl handler.
LPCWSTR g_pPlatformManufacturer = L"DMP"; // OEM Name

// g_pPlatformName: REQUIRED
// The platform\common implementation requires that we define
// this variable for device info ioctl handler.
LPCWSTR g_pPlatformName = L"VDX"; // Platform Name

// g_oalIoCtlPlatformType: REQUIRED
// The platform\common implementation requires that we define
```



### 文件头部注释与宏定义

```c
// 此变量用于设备信息的 IOCTL 处理器。
LPCWSTR g_oalIoCtlPlatformType = L"VDX"; // 可通过 IOCTL 更改

// g_oalIoCtlPlatformOEM：必需
// 平台/通用实现要求我们定义此变量，用于设备信息的 IOCTL 处理器。
LPCWSTR g_oalIoCtlPlatformOEM = L"VDX"; // 常量，不应更改

// g_pszDfltProcessorName：必需
// 平台/通用实现要求我们定义此变量，用于处理器信息的 IOCTL 处理器。
LPCWSTR g_pszDfltProcessorName = L"Vortex86DX";

static BOOL g_fPostInit;
```

### `OEMIoControl`：必需函数

`OEMIoControl` 提供通用的 I/O 控制码（IOCTL），用于 OEM 提供的信息。

`OEMIoControl` 由平台/通用库提供，因此我们不应在 BSP 中实现它。

---

### `g_oalIoCtlTable`：必需数组

平台/通用实现要求我们定义此变量，描述我们支持的 IOCTL。

```c
const OAL_IOCTL_HANDLER g_oalIoCtlTable[] = {
    { IOCTL_HAL_REQUEST_SYSINTR, 0, OALIoCtlHalRequestSysIntr},
    { IOCTL_HAL_RELEASE_SYSINTR, 0, OALIoCtlHalReleaseSysIntr },
    { IOCTL_HAL_REQUEST_IRQ, 0, OALIoCtlHalRequestIrq},
    { IOCTL_HAL_DDK_CALL, 0, OALIoCtlHalDdkCall},
    { IOCTL_HAL_DISABLE_WAKE, 0, x86PowerIoctl},
    { IOCTL_HAL_ENABLE_WAKE, 0, x86PowerIoctl},
    { IOCTL_HAL_GET_WAKE_SOURCE, 0, x86PowerIoctl},
    { IOCTL_HAL_PRESUSPEND, 0, x86PowerIoctl},
    { IOCTL_HAL_GET_POWER_DISPOSITION, 0, x86IoCtlGetPowerDisposition},
    { IOCTL_HAL_GET_CACHE_INFO, 0, BSPIoCtlHalGetCacheInfo},
    { IOCTL_HAL_GET_DEVICEID, 0, OALIoCtlHalGetDeviceId},
    { IOCTL_HAL_GET_DEVICE_INFO, 0, OALIoCtlHalGetDeviceInfo},
    { IOCTL_HAL_GET_UUID, 0, OALIoCtlHalGetUUID},
    { IOCTL_HAL_GET_RANDOM_SEED, 0, OALIoCtlHalGetRandomSeed},
    { IOCTL_PROCESSOR_INFORMATION, 0, x86IoCtlProcessorInfo},
    { IOCTL_HAL_INIT_RTC, 0, x86IoCtlHalInitRTC},
    { IOCTL_HAL_REBOOT, 0, x86IoCtlHalReboot},
    { IOCTL_HAL_ILTIMING, 0, x86IoCtllTiming},
    { IOCTL_HAL_POSTINIT, 0, BSPIoCtlPostInit},
    { IOCTL_HAL_INITREGISTRY, 0, x86IoCtlHalInitRegistry},

    // 必需的终止符
    { 0, 0, NULL}
};
```

### `BSPIoCtlPostInit`：自定义函数

`IOCTL_HAL_POSTINIT` 由内核调用，在 OAL 中实现，为 OEM 在其他进程启动前提供最后一次执行操作的机会。

此处理程序在此处初始化 RTC 通用库的关键部分。

```c
BOOL BSPIoCtlPostInit (
    UINT32 code, VOID *lpInBuf, UINT32 nInBufSize,
    VOID *lpOutBuf, UINT32 nOutBufSize, UINT32 *lpBytesReturned
)
{
    if (lpBytesReturned) {
        *lpBytesReturned = 0;
    }

    RTCPostInit();

    g_fPostInit = TRUE;

    return TRUE;
}
```

---

### 添加设备特定 IOCTL

DDK 为开发者提供宏 `CTL_CODE`，用于定义系统唯一的 IOCTL。所有开发者都应使用此宏定义 IOCTL，以避免不同开发者定义的值重叠。该宏将四个字段设置到一个 32 位值中：

```c
#define CTL_CODE( DeviceType, Function, Method, Access) ( \
    ((DeviceType) << 16) | ((Access) << 14) | ((Function) << 2) | (Method))
```

清单 9-4 是一个使用 `CTL_CODE` 为演示设备驱动程序定义 IOCTL 的示例。

**清单 9-4. 定义 IOCTL**

```c
#ifndef __DEMODRVRSDK_H__
#define __DEMODRVRSDK_H__

#ifdef __cplusplus
extern "C"
{
#endif

#include <winioctl.h>

//
```


```c
// 设备 IO 控制码定义：我们定义 2048 为基值，因为 0-2047 保留给微软使用
//
#define IOCTLDEVTYPE_DEVICE_DEMODRVR 2048
/////////////////////////////////////////////////////////////////
//
// 特定驱动程序 IO 控制码
//
//
// IOCTL_DEMODRVR_FOO1
//
#define IOCTL_DEMODRVR_FOO1 \
    CTL_CODE(IOCTLDEVTYPE_DEVICE_DEMODRVR, 0x100, METHOD_BUFFERED, FILE_ANY_ACCESS)

//
// IOCTL_DEMODRVR_FOO2
//
#define IOCTL_DEMODRVR_FOO2 \
    CTL_CODE(IOCTLDEVTYPE_DEVICE_DEMODRVR, 0x101, METHOD_BUFFERED, FILE_ANY_ACCESS)

#endif /* __DEMODRVRSDK_H__ */
```

清单 9-4 中的文件示例由设备驱动程序向导生成，输入所需的`IOCTL`功能代码文本，并选择所需的访问权限，如图 9-1 所示。该头文件与设备驱动程序实现的其他文件分离，以便分发给应用程序开发人员，用于控制特定设备驱动程序的代码中。

*图 9-1. 定义 IOCTL 并将其添加到设备驱动程序定义中*

### 处理设备特定的 IOCTL

假设清单 9-4 中定义的`IOCTL_DEMODRVR_FOO1`处理某些异步 I/O 操作。实现此功能必须编码在流设备驱动程序的`xxx_IOControl`入口点内。清单 9-5 是由设备驱动程序向导生成的骨架代码；它允许开发人员向代码中添加特定于设备驱动程序的功能，以处理异步 I/O。

*清单 9-5. xxx_IOControl 入口点的骨架实现*

```c
BOOL DMO_IOControl(DWORD hOpenContext, DWORD dwCode, PBYTE pBufIn,
    DWORD dwLenIn, PBYTE pBufOut, DWORD dwLenOut, PDWORD pdwActualOut, HANDLE hAsyncRef)
{
    PDMO_DEVCONTXT pDevContxt = (PDMO_DEVCONTXT)hOpenContext;
    BOOL bRet = TRUE;
    DWORD dwErr = 0;

    switch (dwCode)
    {
    case IOCTL_DEMODRVR_FOO1:
        // 如果需要则进入临界区，否则删除
        // EnterCriticalSection(
        // &pDevContxt->CriticalSection);
        // 添加实现代码
        bRet = Foo1(hAsyncRef, pDevContxt);

        if (!bRet)
        {
            // 处理错误
        }

        // LeaveCriticalSection(
        // &pDevContxt->CriticalSection);
        break;

    ...
    ...
    ...

    default:
        break;
    }
    return bRet;
}
```

清单 9-5 中加粗并带下划线的代码行实际提供了所需功能。`Foo1`函数是一个内部函数，如清单 9-6 所示。然而，您也可以直接将此函数中包含的代码插入到 IOCTL 的 case 处理中。但是，此`IOControl`入口点可能会变得难以跟踪和维护。

*清单 9-6. Foo1 的示例实现*

```c
BOOL Foo1(HANDLE _hAsyncRef, PDMO_DEVCONTXT _pDevContxt)
{
    HANDLE hAsyncIO = NULL;

    if (_hAsyncRef)
    {
        hAsyncIO = CreateAsyncIoHandle(
            hAsyncRef, (LPVOID*)pBufIn, 0);
    }

    CeOpenCallerBuffer(
        (PVOID*)(&g_AsyncParams.pBufIn),
        (PVOID)pBufIn,
        dwLenIn, ARG_O_PTR, TRUE);

    EnterCriticalSection(&_pDevContxt->CriticalSection);
    g_AsyncParams.hAsyncIO = hAsyncIO;
    g_AsyncParams.dwInLen = dwLenIn;
    LeaveCriticalSection(&_pDevContxt->CriticalSection);

    g_hAsyncThread = CreateThread(NULL, 163840,
        (LPTHREAD_START_ROUTINE)AsyncThread,
        (LPVOID)&g_AsyncParams,
        CREATE_SUSPENDED | STACK_SIZE_PARAM_IS_A_RESERVATION, NULL);

    if (g_hAsyncThread == NULL)
    {
        DEBUGMSG(ZONE_IOCTL,
            (_T("DMO! AsyncThread: Failed to create thread\r\n")));
        return FALSE;
    }

    ResumeThread(g_hAsyncThread);
    return TRUE;
}
```

### 电源管理支持

一个使用 IOCTL 提供自定义设备控制的好例子是支持电源管理的设备驱动程序处理的电源管理 I/O 控制。电源管理支持由特定 IOCTL 提供，电源管理器调用这些 IOCTL 来检索设备特定功能的信息——`IOCTL_POWER_CAPABILITIES`。电源管理器调用`IOCTL_POWER_GET`来检索设备电源状态。电源管理器调用`IOCTL_POWER_SET`来请求将设备电源状态从一种状态更改为另一种状态。早期版本的 Windows CE 还有一个 IOCTL，在 Windows Embedded Compact 7 中已弃用——`IOCTL_POWER_QUERY`。清单 9-7 是前三个电源管理 IOCTL 的典型实现。

*清单 9-7. 电源管理 IOCTL 实现*

```c
case IOCTL_POWER_CAPABILITIES:
    // 向电源管理器报告此信息。
    if (pBufOut != NULL &&
        dwLenOut >= sizeof(POWER_CAPABILITIES) &&
        pdwActualOut != NULL)
    {
        __try {
            PPOWER_CAPABILITIES ppc =
                (PPOWER_CAPABILITIES) pBufOut;
            memset(ppc, 0, sizeof(POWER_CAPABILITIES));
            ppc->DeviceDx = 0x11; // 支持 D0, D4
            ppc->WakeFromDx = 0x00; // 无唤醒能力
            ppc->InrushDx = 0x00; // 无浪涌要求
            ppc->Power[D0] = 600; // 0.6W
            ppc->Power[D1] = (DWORD) PwrDeviceUnspecified;
            ppc->Power[D2] = (DWORD) PwrDeviceUnspecified;
            ppc->Power[D3] = (DWORD) PwrDeviceUnspecified;
            ppc->Power[D4] = 0;
            ppc->Latency[D0] = 0;
            ppc->Latency[D1] = (DWORD) PwrDeviceUnspecified;
            ppc->Latency[D2] = (DWORD) PwrDeviceUnspecified;
            ppc->Latency[D3] = (DWORD) PwrDeviceUnspecified;
            ppc->Latency[D4] = 0;
            ppc->Flags = 0;
            *pdwActualOut = sizeof(POWER_CAPABILITIES);
            dwErr = ERROR_SUCCESS;
        }
        __except(EXCEPTION_EXECUTE_HANDLER)
        {
            DEBUGMSG(ZONE_IOCTL,
                (_T("DMO!Exception in ioctl.\r\n")));
        }
    }
    break;

case IOCTL_POWER_SET:
    if (pBufOut != NULL &&
        dwLenOut >= sizeof(POWER_CAPABILITIES) &&
        pdwActualOut != NULL)
    {
        EnterCriticalSection(&pDevContxt->CriticalSection);
        __try {
            CEDEVICE_POWER_STATE NewDx =
                *(PCEDEVICE_POWER_STATE)pBufOut;
            if(VALID_DX(NewDx))
            {
                *(PCEDEVICE_POWER_STATE) pBufOut = NewDx;
                *pdwActualOut = sizeof(CEDEVICE_POWER_STATE);
                pDevContxt->psPowerState = NewDx;
                dwErr = ERROR_SUCCESS;
            }
            DEBUGMSG(ZONE_IOCTL,
                (_T("DMO!IOCTL_POWER_SET %u %s passing back %u\r\n"),
                NewDx, dwErr == ERROR_SUCCESS ? _T("succeeded") :
                _T("failed"), pDevContxt->psPowerState));
        }
        __except(EXCEPTION_EXECUTE_HANDLER)
        {
            DEBUGMSG(ZONE_IOCTL,
                (_T("DMO!Exception in ioctl.\r\n")));
        }
        LeaveCriticalSection(&pDevContxt->CriticalSection);
    }
    break;

case IOCTL_POWER_GET:
    if (pBufOut != NULL &&
        dwLenOut >= sizeof(POWER_CAPABILITIES) &&
        pdwActualOut != NULL)
    {
        EnterCriticalSection(&pDevContxt->CriticalSection);
        __try {
            CEDEVICE_POWER_STATE NewDx =
                *(PCEDEVICE_POWER_STATE) pBufOut;
            *(PCEDEVICE_POWER_STATE) pBufOut =
                pDevContxt->psPowerState;
            *pdwActualOut = sizeof(CEDEVICE_POWER_STATE);
            dwErr = ERROR_SUCCESS;
            DEBUGMSG(ZONE_IOCTL,
                (_T("DMO!IOCTL_POWER_GET %s passing back %u\r\n"),
                dwErr == ERROR_SUCCESS ? _T("succeeded") :
                _T("failed"), pDevContxt->psPowerState));
        }
        __except(EXCEPTION_EXECUTE_HANDLER)
        {
            DEBUGMSG(ZONE_IOCTL,
                (_T("DMO!Exception in ioctl.\r\n")));
        }
        LeaveCriticalSection(&pDevContxt->CriticalSection);
    }
    break;
```

### 章节总结


I/O 控制是一种允许用户模式应用程序向内核模式设备驱动程序发送自定义请求的机制。用户模式设备驱动程序也是如此。设备驱动程序的实现决定了它可以处理哪些 IOCTL。微软为各种设备驱动程序类别预定义了 IOCTL，因此如果您正在编写例如存储磁盘设备驱动程序这样的驱动程序，检查这些预定义的 IOCTL 是一个好习惯，因为您必须实现这些预定义的 IOCTL。您可以为您正在实现的设备驱动程序创建自定义的 I/O 控制代码，但请记住提供一个定义这些自定义 IOCTL 的头文件，以便应用程序开发者能够编译使用这些代码的程序。

`Device Manager` 公开了一个特殊函数 `DeviceIoControl`，供应用程序调用并传递 IOCTL 代码以及缓冲区，这些缓冲区可用于输入或输出，或者如果自定义 IOCTL 需要，也可以同时用于输入和输出。在 Windows Embedded Compact 7 中，此函数接受一个 `OVERLAPPED` 结构，以便您的设备驱动程序实现可以为这类 I/O 请求提供异步 I/O 支持。

---

## 第 10 章：网络驱动程序接口规范与网络设备驱动程序

网络驱动程序接口规范（NDIS）是网络驱动程序架构的规范，它允许诸如 TCP/IP、Native ATM、IPX 和 NetBEUI 等传输协议与底层网络适配器进行通信。该规范的实现是一个用于开发网络设备驱动程序的应用程序框架。网络设备驱动程序为基于 Windows Embedded Compact 7 的设备提供对局域网和广域网的访问。基于 Windows Embedded Compact 的通信架构支持使用网络驱动程序接口规范（NDIS）框架实现的网络设备驱动程序，以及远程 NDIS（RNDIS）、NDISWAN、令牌环、Wi-Fi、IrDA 和蓝牙等技术。

本章内容：
- OSI 模型
- NDIS 作为编程框架
- NDIS 微端口驱动程序

#### 概述

网络允许大量计算机通过某种物理通信线路相互传输数据，当然我也听说过无线通信。显然这会带来一些障碍，因为大量数据会造成通信拥堵。一台计算机如何筛选数据，使其只接收发送给它的数据呢？这些问题大多通过分组交换网络解决了，即把数据分成小块，称为数据包，然后单独发送。

历史上，最大的进展发生在 20 世纪 80 年代初期。由美国国防部高级研究计划局（ARPA）资助，ARPANET 不断发展，而更广为人知的 ARPA 互联网协议 TCP/IP 已成为标准。图 10-1 显示了 TCP/IP 协议流程，其中网络接口层负责与物理网络进行帧交互，互联网层将数据包封装成互联网数据报并处理路由。传输层提供计算机之间的通信会话，应用层提供 API 供应用程序连接到传输层。

*图 10-1. TCP/IP*

这仍然给顶层和底层带来了沉重的实现负担，这促使了 ISO 开放系统互连（OSI）模型的形成（与 TCP/IP 的对比见图 10-2）。

*图 10-2. 与 TCP/IP 相关的 OSI 模型*

OSI 模型是 Windows Embedded Compact 7 网络架构的基础。

### NDIS

网络驱动程序接口规范（NDIS）框架将网络硬件从网络驱动程序中抽象出来。NDIS 还规定了分层网络驱动程序之间的标准接口，从而将管理硬件的底层驱动程序从上层驱动程序（如网络传输协议）中抽象出来。NDIS 维护着网络驱动程序的状态信息和参数，包括指向函数的指针、句柄、用于链接的参数块以及各种系统值。图 10-3 展示了 NDIS 框架与使用它的网络驱动程序之间的关系。

*图 10-3. NDIS 框架与网络驱动程序*

### 网络设备驱动程序的层次

在所有 Windows 操作系统版本中，网络驱动程序实现了紧邻物理通信介质之上的四个层次，如图 10-4 所示。

*图 10-4. 与 OSI 模型相关的网络设备驱动程序*

#### 物理层

此层负责在物理介质上管理和接收、发送未经处理的原始比特流。物理层由网络接口卡（NIC）、其收发器以及 NIC 所连接的介质实现。

#### 数据链路层

数据链路层由两个子层组成：

- **LLC** - 逻辑链路控制
  - LLC 子层提供数据帧从一个节点到另一个节点的无差错传输。
- **MAC** - 介质访问控制
  - MAC 子层管理对物理层的访问，检查帧错误，并管理接收帧的地址识别。

#### 网络层

此层根据以下因素确定数据应采用的物理路径：
- 网络状况
- 服务优先级

其他因素包括路由、流量控制、帧碎片整理与重组、逻辑地址到物理地址的映射以及使用计费。

#### 传输层

此层确保消息按顺序、无差错地传送，且无丢失或重复。

### NDIS 微端口驱动程序

微端口驱动程序实现数据链路层的下边缘。它处理管理网络适配器所需的硬件特定操作。物理 NIC 类型会影响微端口驱动程序适配器。

- **总线主控 DMA NIC** - 微端口驱动程序通常支持多数据包发送和接收。
- **从属 DMA NIC** - 微端口驱动程序使用系统 DMA 控制器来管理数据包数据与网络之间的传输。
- **编程 I/O NIC** - 微端口驱动程序使用 NDIS 函数逐字节地将传出帧移动到设备寄存器，或执行其他类型的操作，然后使设备发送数据。如今这种网卡已经非常罕见，您也极不可能实现此类驱动程序。
- **板载共享内存 NIC** - 微端口驱动程序必须将 NIC 的共享内存映射到主机内存，然后将传出数据包复制到 NIC 内存。它将传入帧从 NIC 内存复制到上层协议驱动程序提供的缓冲区中。

NDIS 微端口 NIC 驱动程序通过 NDIS 框架（参见图 10-3）与其 NIC 和更高级别的驱动程序通信。NDIS 框架函数导出一整套函数，通常以 `Ndis` 为前缀，这些函数封装了微端口需要调用的所有操作系统函数。反过来，NDIS 微端口驱动程序必须导出一系列入口点，NDIS 框架会调用这些入口点，用于其自身目的，或代表更高级别的驱动程序访问微端口。

微端口驱动程序维护有关其能力和状态的信息，以及它所控制的 NIC 的信息。对象标识符（OID）是系统定义的常量，用于标识每种信息类型。

#### NDIS 微端口驱动程序函数



