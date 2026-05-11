# 代码示例与设备驱动类型讨论

`HANDLE hMsgq = INVALID_HANDLE_VALUE;`
`HANDLE hNotfcn = INVALID_HANDLE_VALUE;`
`MSGQUEUEOPTIONS msgopts;`
`DEVDETAIL DeveDtls;`
`DWORD flags = MSGQUEUE_MSGALERT;`
`DWORD size = 0, dwLastError;`
`BOOL bReadMsg = FALSE;`

`memset(&DeveDtls, 0, sizeof(DeveDtls));`
`memset(&msgopts, 0, sizeof(msgopts));`
`msgopts.dwSize = sizeof(msgopts);`
`msgopts.dwFlags = 0;`
`msgopts.dwMaxMessages = 0;`
`msgopts.cbMaxMessage = sizeof(DeveDtls);`
`msgopts.bReadAccess = TRUE;`

`hMsgq = CreateMsgQueue(NULL, &msgopts);`

`if (hMsgq == INVALID_HANDLE_VALUE)`
`{`
`printf("Failed to create message queue.\n");`
`return 0;`
`}`

`hNotfcn = RequestDeviceNotifications(NULL, hMsgq, TRUE);`

`if (hNotfcn == INVALID_HANDLE_VALUE)`
`{`
`printf("Failed to register for TST notifications.\n");`
`return 0;`
`}`

`bReadMsg = ReadMsgQueue(hMsgq, &DeveDtls, sizeof(DeveDtls),`
`&size,`
`INFINITE, // 等待直到消息出现`
`&flags);`

`if (bReadMsg == FALSE)`
`{`
`dwLastError = GetLastError();`
`}`
`else`
`{`
`// 如果设备接口出现，执行所需操作`
`}`

## 通过`WM_DEVICECHANGE`通知

这种处理设备管理器通知的方法有一些缺点。首先，消息的到达是不可预测的。Windows 消息队列（与点对点消息队列不同）由 GWES 处理，像任何 Windows 消息一样到达窗口过程。另一个限制是，以这种方式处理通知的应用程序必须是实现窗口过程的 Windows 应用程序。你的设备可能根本没有图形用户界面，并且设备可能不支持实现 Windows 应用程序。以下清单 6-4 中的实现示例检查`WM_DEVICECHANGE`消息是否因所需设备接口的出现而到达：

**清单 6-4. 通过 Windows 消息队列接收设备通知的示例代码**

`PDEV_BROADCAST_HDR pHdr;`
`PDEV_BROADCAST_DEVICEINTERFACE pDevInf;`
`GUID guid = DEVCLASS_STREAM_GUID; // 或任何相关的设备接口 GUID`
`:`
`:`
`case WM_DEVICECHANGE:`
`{`
`if ( DBT_DEVICEARRIVAL == wParam ||`
`DBT_DEVICEREMOVECOMPLETE == wParam )`
`{`
`pHdr = (PDEV_BROADCAST_HDR)lParam;`
`if ( pHdr->dbch_devicetype == DBT_DEVTYP_DEVICEINTERFACE)`
`{`
`pDevInf = (PDEV_BROADCAST_DEVICEINTERFACE)pHdr;`
`if (pDevInf->dbcc_classguid == guid)`
`{`
`// 执行计划的操作`
`}`
`}`
`}`
`}`

`DEV_BROADCAST_DEVICEINTERFACE`结构包含所有必需的信息，定义于`C:\WINCE700\public\COMMON\sdk\inc\dbt.h`。

**图 6-2. Testdrvr 设备驱动程序的注册表“IClass”值**

### 章节总结

Windows Embedded Compact 7 中的设备驱动程序，与之前版本的 Windows CE 一样，可以通过多种方式进行分类。可以根据加载它们的系统组件、它们的用途、它们所在的内存空间或它们的实现方式进行分类。原生设备驱动程序由图形窗口和事件系统（GWES）加载，而所有其他设备驱动程序由设备管理器加载。原生设备驱动程序由 GWES 访问，应用程序不能直接访问。

此外，出于这些原因，原生设备驱动程序暴露一组 GWES 调用的函数。所有其他设备驱动程序必须统一暴露流设备接口，以便设备管理器可以代表需要其服务的应用程序访问它们。所有由 GWES 加载的原生设备驱动程序因此驻留在内核中，而由设备管理器加载的流设备驱动程序既可以驻留在内核中，也可以驻留在由设备管理器启动的特殊用户模式宿主中。

在本章中，我们讨论了与设备驱动程序类型和设备驱动程序通知相关的主题，包括：

- 原生设备驱动程序
- 流设备驱动程序
- 单片式和分层式设备驱动程序实现
- 设备接口类
- 设备接口通知

---

