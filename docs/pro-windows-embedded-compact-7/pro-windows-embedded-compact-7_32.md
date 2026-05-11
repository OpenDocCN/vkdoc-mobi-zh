# 第 7 章 ■ 流设备驱动程序的本质

### 关闭和反初始化设备驱动程序

设备驱动程序中可能存在阻塞的线程，这些线程可能因为阻塞而无法释放与句柄或设备实例相关的资源。这会在尝试关闭上下文实例或卸载设备驱动程序时导致竞争条件。

当一个线程正在进行 I/O 操作，而另一个线程调用 `CloseHandle` 时，可能会发生竞争条件。当设备管理器调用 `XXX_Close` 入口点时，驱动程序应唤醒阻塞在句柄上的线程，并释放与该句柄相关的资源。如果设备管理器在另一个线程已调用 `XXX_Close` 之后调用了 I/O 入口点（如 `XXX_IOControl`），设备驱动程序可能会崩溃。

如果设备管理器在一个线程中执行设备驱动程序的 `XXX_Deinit` 函数以尝试卸载设备，而另一个线程同时尝试打开一个句柄，则可能发生另一种竞争条件。在这种情况下，从设备驱动程序的角度来看，`XXX_Deinit` 可能在 `XXX_Open` 之前被调用。这种情况可能发生在长时间且 CPU 负载较高时，或者驱动程序频繁卸载和重新加载时。

##### `XXX_Read`

`XXX_Read` 函数从由打开上下文标识的设备读取数据。根据驱动程序所暴露的设备能力，此函数可能是必需的，也可能不是。请注意，该函数是三个支持异步 I/O 请求的入口点之一。这是 Windows Embedded Compact 7 中引入的新功能，并通过一个新的输入参数（即一个 I/O 数据包的句柄）来实现。示例请参见代码清单 7-5。

##### `XXX_Write`

`XXX_Write` 函数向设备写入数据。根据驱动程序所暴露的设备能力，此函数可能是必需的，也可能不是。请注意，该函数是三个支持异步 I/O 请求的入口点之一。这是 Windows Embedded Compact 7 中引入的新功能，并通过一个新的输入参数（即一个 I/O 数据包的句柄）来实现。示例请参见代码清单 7-10。

*代码清单 7-10. `XXX_Write` 处理异步 I/O 请求的示例*

```
DWORD TST_Write(DWORD hOpenContext,
LPCVOID pSourceBytes,
DWORD NumberOfBytes, HANDLE hAsyncRef)
{
DWORD dwRet = 0;
PTST_DEVCONTXT pDevContxt = (PTST_DEVCONTXT)hOpenContext;
HANDLE hAsyncIO = NULL;
// TODO: 添加实现写入的代码：
if (hAsyncRef)
{
101
[www.it-ebooks.info](http://www.it-ebooks.info/)
第 7 章 ■ 流设备驱动程序的本质
hAsyncIO = CreateAsyncIoHandle(hAsyncRef,
(LPVOID*)pSourceBytes, 0);
}
CeOpenCallerBuffer((PVOID*)(&g_AsyncTestParams.pBufIn),
(PVOID)pSourceBytes,
NumberOfBytes, ARG_O_PTR, TRUE);
g_AsyncTestParams.hAsyncIO = hAsyncIO;
g_AsyncTestParams.dwInLen = NumberOfBytes;
g_hAsyncThread = CreateThread(NULL,163840,
(LPTHREAD_START_ROUTINE)AsyncTestThread,
(LPVOID)&g_AsyncTestParams,
CREATE_SUSPENDED |
STACK_SIZE_PARAM_IS_A_RESERVATION, NULL);
if (g_hAsyncThread == NULL)
{
DEBUGMSG(ZONE_IOCTL,
(_T("TST! 创建线程失败\r\n")));
return FALSE;
}
ResumeThread(g_hAsyncThread);
return dwRet;
}
```

##### `XXX_Seek`

`XXX_Seek` 函数用于移动设备中的数据指针。根据驱动程序所暴露的设备能力，此函数可能是必需的，也可能不是。应用程序通过调用 `SetFilePointer` 来访问此函数。

### 流接口设备驱动程序的加载

流接口设备驱动程序的加载完全依赖于注册表。设备驱动程序的加载过程始于设备管理器读取位于 `HKLM\Drivers\BuiltIn` 的注册表根键，以获取驱动程序加载的根路径。这些注册表项请参见代码清单 7-11。该键下的驱动程序是 `BusEnum.dll`，它是一个作为总线设备驱动程序实现的总线枚举器。然后，设备管理器调用 `ActivateDeviceEx` 来激活总线枚举器。一旦总线枚举器被激活，它会遍历根键下的所有节点，这些节点将由总线枚举器在启动时自动加载。示例请参见代码清单 7-12。这些流设备驱动程序通常被称为“内置”驱动程序。内置驱动程序通常包括其他总线驱动程序，如 PCI、USB 等，这些驱动程序各自管理自己的总线。

*代码清单 7-11. 总线枚举器的注册表项*

```
[HKEY_LOCAL_MACHINE\Drivers]
"RootKey"="Drivers\\BuiltIn"
"ProcName"="udevice.exe"
"ProcVolPrefix"="$udevice"
102
[www.it-ebooks.info](http://www.it-ebooks.info/)
第 7 章 ■ 流设备驱动程序的本质
[HKEY_LOCAL_MACHINE\Drivers\BuiltIn]
"Dll"="BusEnum.dll"
"BusName"="BuiltIn"
"Flags"=dword:8
"BusIoctl"=dword:2a0048
"InterfaceType"=dword:0
"IClass"=multi_sz:
"{B3CC6EBA-5507-4196-8E41-2BF42E4A47C9}=%b",
"{6F40791D-300E-44E4-BC38-E0E63CA8375C}=%b"
```

*代码清单 7-12. 根键下的流设备驱动程序示例*

```
; DemoDrvr 驱动程序
[HKEY_LOCAL_MACHINE\Drivers\BuiltIn\Demodrvr]
"Prefix"="TST"
"DLL"="Demodrvr.DLL"
"SysIntr"=dword:1A
"Irq"=dword:10
"MemBase"=dword:80220000
"MemLen"=dword:100
"Order"=dword:0
"DisplayName"="DemoDrvr 驱动程序"
"Flags"=dword:0
```

加载过程是同步的，因此可能耗时较长，因为设备驱动程序是根据其加载顺序值依次加载的。总线枚举器遍历根键下的设备驱动程序节点，首先加载所有加载顺序值为 0 的驱动程序，然后继续遍历并加载所有加载顺序值为 1 的驱动程序，直到加载完所有标记为需要加载的设备驱动程序。

#### 异步 I/O 请求处理

在最新版本的 Windows CE（即 Windows Embedded Compact 7）中，新增了设备驱动程序对 I/O 请求的支持。在之前版本的 Windows CE 中，设备驱动程序能够满足 I/O



