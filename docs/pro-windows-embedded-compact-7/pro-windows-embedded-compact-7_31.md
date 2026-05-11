# 第 7 章 ■ 流设备驱动程序的本质

`**(_T("TST!已存在打开的实例。\r\n")));`

`}`

`}`

[www.it-ebooks.info](http://www.it-ebooks.info/)

`**else**`

`**{**`

`**pDevContxt->bOpenEx = TRUE;**`

`**// TODO: 添加实现打开实例的代码：**`

`**dwRet = (DWORD)pDevContxt;**`

`**}`

`**LeaveCriticalSection(&pDevContxt->CriticalSection);**`

`**if (dwRet == DRVR_INVALID_HANDLE) // 未打开**`

`**{**`

`**InterlockedDecrement(&pDevContxt->lOpenCount);**`

`**}`

`**return dwRet;**`

`}`

##### `XXX_PowerDown`

`XXX_PowerDown` 的实现是可选的。该函数用于关闭设备的电源。它仅适用于那些可以在软件控制下关闭的设备。当系统不支持 API 调用并以单线程模式运行时，非电源管理型驱动程序会通过 `XXX_PowerDown` 入口点挂起。因此，该函数不应调用任何其他函数，并应尽快返回。

##### `XXX_PowerUp`

`XXX_PowerUp` 的实现是可选的。该函数用于恢复设备的供电。此函数在系统恢复时调用，此时系统仍以单线程模式运行。内核通过调用 `XXX_PowerUp` 入口点来恢复非电源管理型驱动程序。

##### `XXX_PreClose`

`XXX_PreClose` 的实现是可选的；然而，如果其他线程中正在进行冗长的异步 I/O 操作，则该函数极为重要。此函数将正在关闭的句柄标记为无效，并唤醒所有阻塞的线程。相关说明请参见“关闭和反初始化设备驱动程序”一节中的注释。

##### `XXX_PreDeinit`

`XXX_PreDeinit` 函数通过唤醒所有阻塞的线程，为卸载设备驱动程序做准备，以便在最终调用 `XXX_Deinit` 之前释放相关资源。如果实现了 `XXX_PreClose` 函数，则必须实现此函数。

[www.it-ebooks.info](http://www.it-ebooks.info/)

