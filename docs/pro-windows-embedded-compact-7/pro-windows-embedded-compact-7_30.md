# 流设备驱动程序实现

### 设备上下文初始化

```c
pDevContxt->dwIstPriority = 75;
pDevContxt->dwIstPriority = 75;
pDevContxt->dwIstQuantum = 50;
```

```c
// 从注册表设置系统中断 ID
pDevContxt->dwSysIntr = IsrInfo.dwSysintr;
```

```c
// 创建 IST 过程（调用设备驱动程序本地函数）
dwRet = CreateInterruptServiceThread((PTST_DEVCONTXT)pDevContxt);
if (dwRet != 0)
{
    FreePhysMem(pDevContxt);
    return 0;
}
```

```c
// 所有操作成功，返回设备上下文指针
dwRet = (DWORD)pDevContxt;
return dwRet;
```

##### `XXX_IOControl`

`XXX_IOControl` 函数用于向设备发送命令，或处理文件系统 API 无法访问的设备驱动程序特定功能。根据驱动程序暴露的设备能力，可能需要也可能不需要实现此函数。当应用程序调用设备管理器暴露的`DeviceIoControl`时，会触发此函数的调用。请注意，此函数是支持异步 I/O 请求的三个入口点之一。这是 Windows Embedded Compact 7 中引入的新功能，并通过一个指向 I/O 包句柄的新输入参数得以实现。清单 7-8 展示了`XXX_IOControl`函数的一个简单实现示例，其中包含一个设备驱动程序私有 IO 控制码。

*清单 7-8. XXX_IOControl 函数简单实现示例*

```c
PINPUT pInput = g_pBufIn;
...
BOOL TST_IOControl(DWORD hOpenContext, DWORD dwCode, PBYTE pBufIn, DWORD dwLenIn, PBYTE
pBufOut, DWORD dwLenOut, PDWORD pdwActualOut, HANDLE hAsyncRef)
{
    PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT)hOpenContext;
    BOOL bRet = TRUE;
    DWORD dwErr = 0;
    HRESULT hr = E_WASNEVERSET;

    // TODO: 添加实现 IOCTL 码的代码：

    switch (dwCode)
    {
        case IOCTL_DEMODRVR_FOO1:
            hr = CeOpenCallerBuffer((PVOID*)&g_pBufIn,
                                    (PVOID)pBufIn,
                                    dwLenIn, ARG_I_PTR, FALSE);
            // 如有需要则进入临界区，否则请删除此行
            EnterCriticalSection(&pDevContxt->CriticalSection);
            // 添加实现代码
            LeaveCriticalSection(&pDevContxt->CriticalSection);
            hr = CeCloseCallerBuffer((PVOID) pBufIn, (PVOID)pBufIn, dwLenIn, ARG_I_PTR);
            break;

        default:
            break;
    }

    // 传递适当的响应代码
    SetLastError(dwErr);
    if ((dwErr != ERROR_SUCCESS) && (dwErr != ERROR_IO_PENDING))
    {
        bRet = FALSE;
    }
    else
    {
        bRet = TRUE;
    }

    return bRet;
}
```

##### `XXX_Open`

`XXX_Open` 函数用于打开一个设备应用相关上下文，以进行读取、写入或两者操作。当应用程序调用`CreateFile`获取设备句柄时，会间接调用此函数。在此函数中，您可以决定是否允许多个应用程序同时访问设备驱动程序服务，还是仅允许单个应用程序访问。如果允许多个应用程序访问设备驱动程序服务，则需要为每个打开上下文实现维护代码，以确保每个应用程序都能获得与其上下文相对应的适当服务。要通过`CreateFile`访问设备，必须实现此函数。清单 7-9 演示了限制设备驱动程序仅允许单个打开上下文的实现方式。

*清单 7-9. 限制打开上下文的 XXX_Open 示例*

```c
DWORD TST_Open(DWORD hDeviceContext, DWORD AccessCode,
               DWORD ShareMode)
{
    DWORD dwRet = DRVR_INVALID_HANDLE;
    PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT)hDeviceContext;

    if (pDevContxt == NULL)
    {
        SetLastError(ERROR_INVALID_HANDLE);
        return dwRet;
    }

    InterlockedIncrement(&pDevContxt->lOpenCount);
    EnterCriticalSection(&pDevContxt->CriticalSection);

    // 以下代码防止打开多个实例
    if (pDevContxt->bOpenEx == TRUE)
    {
        if (pDevContxt->lOpenCount > 0)
        {
            DEBUGMSG(ZONE_OPEN,
```



