# `Count = NdisInterlockedDecrement(&(Adapter)->RefCount);`

```c
// 非零的引用计数可能意味着存在
// 未完成的异步共享内存分配，或者
// MPHandleInterrupt 尚未完成
if (Count)
{
    while (TRUE)
    {
        if (NdisWaitEvent(&Adapter->ExitEvent, 2000))
        {
            break;
        }
    }
}

// 重置 PHY 收发器芯片。
ResetPHYceiver(Adapter);

// 释放整个适配器对象，包括共享内存
// 结构。
MpFreeAdapter(Adapter);
}
```

##### `MiniportDriverUnload`

该函数的实现需要在系统完成驱动卸载操作前释放资源，如清单 10-4 所示，其逻辑极其简单。

*清单 10-4. `MiniportDriverUnload` 函数实现示例*

```c
VOID MPUnload(IN PDRIVER_OBJECT DriverObject)
{
    // 注销微端口驱动程序
    NdisMDeregisterMiniportDriver(g_NdisMiniportDriverHandle);
}
```

##### `MiniportPause`

该函数被调用来停止通过微端口适配器的网络数据流。它主要设置适配器的状态，释放并完成 `SendWaitQueue` 中待处理的发送数据包。随后递减接收计数，如果接收计数为零，则将适配器状态设置为已暂停并返回成功；否则返回 `STATUS_PENDING`，以通知框架仍有接收数据存在。

清单 10-5 是一个暂停微端口适配器的示例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 网络驱动程序接口规范与网络设备驱动程序

*清单 10-5. `MiniportPause` 函数实现示例*

```c
NDIS_STATUS MPPause(IN NDIS_HANDLE MiniportAdapterContext,
                    IN PNDIS_MINIPORT_PAUSE_PARAMETERS MiniportPauseParameters)
{
    PMP_ADAPTER Adapter = (PMP_ADAPTER)MiniportAdapterContext;
    NDIS_STATUS Status;
    LONG Count;

    ASSERT(Adapter->AdapterState == NicRunning);

    NdisAcquireSpinLock(&Adapter->RcvLock);
    Adapter->AdapterState = NicPausing;
    NdisReleaseSpinLock(&Adapter->RcvLock);

    // 完成所有待处理的发送
    NdisAcquireSpinLock(&Adapter->SendLock);
    MpFreeQueuedSendNetBufferLists(Adapter);
    NdisReleaseSpinLock(&Adapter->SendLock);

    NdisAcquireSpinLock(&Adapter->RcvLock);
    Count = --(Adapter->RcvRefCount);
    if (Count == 0)
    {
        Adapter->AdapterState = NicPaused;
        Status = NDIS_STATUS_SUCCESS;
    }
    else
    {
        Status = NDIS_STATUS_PENDING;
    }
    NdisReleaseSpinLock(&Adapter->RcvLock);

    return Status;
}
```

##### `MiniportRestart`

该函数被调用来重新启动已暂停的微端口适配器。此函数非常简单，主要处理一些重启属性，递增接收计数并将状态设置为运行中。清单 10-6 是一个重启已暂停微端口适配器的示例。

*清单 10-6. `MiniportRestart` 函数实现示例*

```c
NDIS_STATUS MPRestart(IN NDIS_HANDLE MiniportAdapterContext,
                      IN PNDIS_MINIPORT_RESTART_PARAMETERS MiniportRestartParameters)
{
    PMP_ADAPTER Adapter = (PMP_ADAPTER)MiniportAdapterContext;
    PNDIS_RESTART_ATTRIBUTES NdisRestartAttributes;
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 网络驱动程序接口规范与网络设备驱动程序

```c
    PNDIS_RESTART_GENERAL_ATTRIBUTES NdisGeneralAttributes;

    NdisRestartAttributes =
        MiniportRestartParameters->RestartAttributes;

    // 如果 NdisRestartAttributes 不为 NULL，则微端口可以
    // 修改通用属性，并在末尾添加新的媒体特定信息
    // 属性。否则，NDIS 框架因其他原因重新启动
    // 微端口。微端口驱动程序不应尝试修改/添加属性
    if (NdisRestartAttributes != NULL)
    {
        ASSERT(NdisRestartAttributes->Oid ==
               OID_GEN_MINIPORT_RESTART_ATTRIBUTES);
        NdisGeneralAttributes =
            (PNDIS_RESTART_GENERAL_ATTRIBUTES)
            NdisRestartAttributes->Data;
        // 检查是否需要更改任何属性，也许
        // 更改当前的 MAC 地址，或添加媒体特定
```


```c
// info attributes.
}
NdisAcquireSpinLock(&Adapter->RcvLock);
Adapter->RcvRefCount++;
Adapter->AdapterState = NicRunning;
NdisReleaseSpinLock(&Adapter->RcvLock);
return NDIS_STATUS_SUCCESS;
}
```

`MiniportOidRequest`

该函数被调用，用于处理向无连接微型端口驱动程序查询或设置信息的 OID（NDIS 对象标识符）请求。唯一需要注意点是：处理 OID 请求时，应确保没有待处理请求，且适配器未被移除或重置。否则可以正常处理该请求。清单 10-7 展示了如何处理 OID 请求的示例。请注意，`NDIS_REQUEST_TYPE` 枚举中关于请求类型的帮助信息，因为其中不少类型已被标记为过时。

*清单 10-7. MiniportOidRequest 函数实现示例*

```c
NDIS_STATUS MPOidRequest(IN NDIS_HANDLE MiniportAdapterContext,
                          IN PNDIS_OID_REQUEST NdisRequest)
{
    PMP_ADAPTER Adapter = (PMP_ADAPTER)MiniportAdapterContext;
    NDIS_STATUS Status = NDIS_STATUS_SUCCESS;
    NDIS_REQUEST_TYPE RequestType;

    // 如果重置正在进行中，应中止请求
    NdisAcquireSpinLock(&Adapter->Lock);
    // 若有待处理请求，则断言。
    ASSERT(Adapter->PendingRequest == NULL);

    if (Adapter->Flags &
        (F_MP_ADAPTER_RESET_IN_PROGRESS |
         F_MP_ADAPTER_REMOVE_IN_PROGRESS))
    {
        NdisReleaseSpinLock(&Adapter->Lock);
        return NDIS_STATUS_REQUEST_ABORTED;
    }

    NdisReleaseSpinLock(&Adapter->Lock);

    RequestType = NdisRequest->RequestType;

    switch (RequestType)
    {
    case NdisRequestMethod:
        Status = MpMethodRequest(Adapter, NdisRequest);
        break;

    case NdisRequestSetInformation:
        Status = MpSetInformation(Adapter, NdisRequest);
        break;

    case NdisRequestQueryInformation:
    case NdisRequestQueryStatistics:
        Status = MpQueryInformation(Adapter, NdisRequest);
        break;

    default:
        // 后续入口点可能会被所有请求使用
        Status = NDIS_STATUS_NOT_SUPPORTED;
        break;
    }

    return Status;
}
```

`MiniportDevicePnPEventNotify`

该函数被调用，用于向适配器微型端口驱动程序通知即插即用（PnP）事件。清单 10-8 中的实现示例非常简单，仅输出指示发生了何种事件的消息。

*清单 10-8. MiniportDevicePnPEventNotify 函数实现示例*

```c
VOID MPPnPEventNotify(IN NDIS_HANDLE MiniportAdapterContext,
                       IN PNET_DEVICE_PNP_EVENT NetDevicePnPEvent)
{
    PMP_ADAPTER Adapter = (PMP_ADAPTER)MiniportAdapterContext;
    NDIS_DEVICE_PNP_EVENT PnPEvent =
        NetDevicePnPEvent->DevicePnPEvent;
    PVOID InformationBuffer =
        NetDevicePnPEvent->InformationBuffer;
    ULONG InformationBufferLength =
        NetDevicePnPEvent->InformationBufferLength;

    switch (PnPEvent)
    {
    case NdisDevicePnPEventQueryRemoved:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventQueryRemoved\n"));
        break;

    case NdisDevicePnPEventRemoved:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventRemoved\n"));
        break;

    case NdisDevicePnPEventSurpriseRemoved:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventSurpriseRemoved\n"));
        break;

    case NdisDevicePnPEventQueryStopped:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventQueryStopped\n"));
        break;

    case NdisDevicePnPEventStopped:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventStopped\n"));
        break;

    case NdisDevicePnPEventPowerProfileChanged:
        DEBUGMSG(ZONE_WARN,
            ("MPPnPEventNotify: NdisDevicePnPEventPowerProfileChanged\n"));
        break;

    default:
        DEBUGMSG(ZONE_ERROR,
            ("MPPnPEventNotify: 未知的 PnP 事件 %x \n", PnPEvent));
        break;
    }
}
```


[www.it-ebooks.info](http://www.it-ebooks.info/)

