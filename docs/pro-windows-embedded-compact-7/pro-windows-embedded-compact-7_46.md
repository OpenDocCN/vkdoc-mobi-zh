# 微型端口驱动程序入口点

这是一组大多数由微型端口驱动程序要求实现的入口点，以便 NDIS 框架能够调用它们来执行框架本身或代表 NDIS 协议驱动程序所需的各种任务。

微型端口驱动程序必须实现这些入口点以确保稳定运行。

##### `DriverEntry`

这是 NDIS 设备驱动程序（`NDIS.DLL`）被设备管理器加载并初始化后，由 NDIS 调用的第一个函数。该函数至少必须设置微型端口驱动程序的特征结构 `NDIS_MINIPORT_DRIVER_CHARACTERISTICS`，然后通过调用 `NdisMRegisterMiniportDriver` 向 NDIS 注册驱动程序。实现 `DriverEntry` 的示例如清单 10-1 所示。

```
NDIS_HANDLE g_NdisMiniportDriverHandle = NULL;
NDIS_HANDLE g_MiniportDriverContext = NULL;

NDIS_STATUS DriverEntry(IN PDRIVER_OBJECT DriverObject, IN PUNICODE_STRING RegistryPath)
{
    NDIS_STATUS Status;
    NDIS_MINIPORT_DRIVER_CHARACTERISTICS MPChar;

    // 填写微型端口特征结构
    NdisZeroMemory(&MPChar, sizeof(MPChar));

    MPChar.Header.Type = NDIS_OBJECT_TYPE_MINIPORT_DRIVER_CHARACTERISTICS;
    MPChar.Header.Size = sizeof(NDIS_MINIPORT_DRIVER_CHARACTERISTICS);
    MPChar.Header.Revision = NDIS_MINIPORT_DRIVER_CHARACTERISTICS_REVISION_1;

    MPChar.MajorNdisVersion = MP_NDIS_MAJOR_VERSION;
    MPChar.MinorNdisVersion = MP_NDIS_MINOR_VERSION;
    MPChar.MajorDriverVersion = NIC_MAJOR_DRIVER_VERSION;
    MPChar.MinorDriverVersion = NIC_MINOR_DRIVER_VERISON;

    MPChar.SetOptionsHandler = MPSetOptions;
    MPChar.InitializeHandlerEx = MPInitialize;
    MPChar.HaltHandlerEx = MPHalt;
    MPChar.UnloadHandler = MPUnload;
    MPChar.PauseHandler = MPPause;
    MPChar.RestartHandler = MPRestart;
    MPChar.OidRequestHandler = MPOidRequest;
    MPChar.DevicePnPEventNotifyHandler = MPPnPEventNotify;
    MPChar.ShutdownHandlerEx = MPShutdown;
    MPChar.CheckForHangHandlerEx = MPCheckForHang;
    MPChar.ResetHandlerEx = MPReset;

    Status = NdisMRegisterMiniportDriver(DriverObject, RegistryPath,
                                        (PNDIS_HANDLE)g_MiniportDriverContext,
                                        &MPChar, &g_NdisMiniportDriverHandle);
    return Status;
}
```

##### `MiniportSetOptions`

目前，在 Windows Embedded Compact 7 版本的 NDIS 中，不支持微型端口可选特征或烟囱卸载功能。因此，此函数的实现应不执行任何操作，仅返回 `NDIS_STATUS_SUCCESS` 值。

##### `MiniportInitializeEx`

在调用 `DriverEntry` 成功后，NDIS 框架会调用此函数。在清单 10-1 的示例中，该入口点被命名为 `MPInitialize`，因此 NDIS 将调用此函数。

此函数用于初始化硬件，并向 NDIS 框架注册微型端口适配器的属性和中断特征。清单 10-2 是一个特定于 Windows Embedded Compact 7 的示例实现，已移除调试消息和其他调试处理。部分代码调用了初始化硬件和分配内存的本地函数。请注意，大多数代码用于设置 NDIS 框架结构，并调用 NDIS 函数来设置属性和中断等。

> **实现说明** 采用 do-while 块是为了便于维护，因为跳出该块比从每个失败条件返回更容易。

```
NDIS_STATUS MPInitialize(IN NDIS_HANDLE MiniportAdapterHandle,
                         IN NDIS_HANDLE MiniportDriverContext,
                         IN PNDIS_MINIPORT_INIT_PARAMETERS MiniportInitParameters)
{
    NDIS_STATUS Status = NDIS_STATUS_SUCCESS;
```


```c
NDIS_MINIPORT_INTERRUPT_CHARACTERISTICS Interrupt;
NDIS_MINIPORT_ADAPTER_REGISTRATION_ATTRIBUTES RegistrationAttributes;
NDIS_MINIPORT_ADAPTER_GENERAL_ATTRIBUTES GeneralAttributes;
NDIS_PNP_CAPABILITIES PowerManagementCapabilities;
NDIS_TIMER_CHARACTERISTICS Timer;

PMP_ADAPTER Adapter = NULL; // 用于抽象适配器的本地结构体
ULONG ulInfoLen;

do
{
    // 分配 MP_ADAPTER 结构体（本地函数）
    Status = MpAllocAdapterBlock(&Adapter, MiniportAdapterHandle);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        break;
    }

    Adapter->AdapterHandle = MiniportAdapterHandle;

    NdisZeroMemory(&RegistrationAttributes,
        sizeof(NDIS_MINIPORT_ADAPTER_REGISTRATION_ATTRIBUTES));
    NdisZeroMemory(&GeneralAttributes,
        sizeof(NDIS_MINIPORT_ADAPTER_GENERAL_ATTRIBUTES));

    // 设置注册属性
    RegistrationAttributes.Header.Type =
        NDIS_OBJECT_TYPE_MINIPORT_ADAPTER_REGISTRATION_ATTRIBUTES;
    RegistrationAttributes.Header.Revision =
        NDIS_MINIPORT_ADAPTER_REGISTRATION_ATTRIBUTES_REVISION_1;
    RegistrationAttributes.Header.Size =
        sizeof(NDIS_MINIPORT_ADAPTER_REGISTRATION_ATTRIBUTES);
    RegistrationAttributes.MiniportAdapterContext = (NDIS_HANDLE)Adapter;
    RegistrationAttributes.AttributeFlags =
        NDIS_MINIPORT_ATTRIBUTES_HARDWARE_DEVICE |
        NDIS_MINIPORT_ATTRIBUTES_BUS_MASTER;
    RegistrationAttributes.CheckForHangTimeInSeconds = 2;
    RegistrationAttributes.InterfaceType =
        NIC_INTERFACE_TYPE;

    Status =
        NdisMSetMiniportAttributes(MiniportAdapterHandle,
            (PNDIS_MINIPORT_ADAPTER_ATTRIBUTES)&RegistrationAttributes);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        break;
    }

    // 读取注册表参数（本地函数）
    Status = NICReadRegParameters(Adapter);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        break;
    }

    // 查找物理适配器（本地函数）
    Status = MpFindAdapter(Adapter,
        MiniportInitParameters->AllocatedResources);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        break;
    }

    // 将总线相关 IO 范围映射到系统 IO 空间
    Status = NdisMRegisterIoPortRange(
        (PVOID *)&Adapter->PortOffset,
        Adapter->AdapterHandle,
        Adapter->IoBaseAddress,
        Adapter->IoRange);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        NdisWriteErrorLogEntry(Adapter->AdapterHandle,
            NDIS_ERROR_CODE_BAD_IO_BASE_ADDRESS, 0);
        break;
    }

    // 从 NIC 读取其他信息，例如 MAC 地址
    // （本地函数）
    Status = NICReadAdapterInfo(Adapter);
    if (Status != NDIS_STATUS_SUCCESS)
    {
        break;
    }

    // 设置通用属性
    GeneralAttributes.Header.Type =
        NDIS_OBJECT_TYPE_MINIPORT_ADAPTER_GENERAL_ATTRIBUTES;
    GeneralAttributes.Header.Revision =
        NDIS_MINIPORT_ADAPTER_GENERAL_ATTRIBUTES_REVISION_1;
    GeneralAttributes.Header.Size =
        sizeof(NDIS_MINIPORT_ADAPTER_GENERAL_ATTRIBUTES);
    GeneralAttributes.MediaType = NIC_MEDIA_TYPE;
    GeneralAttributes.MtuSize = NIC_MAX_PACKET_SIZE - NIC_HEADER_SIZE;
    GeneralAttributes.MaxXmitLinkSpeed = NIC_MEDIA_MAX_SPEED;
    GeneralAttributes.MaxRcvLinkSpeed = NIC_MEDIA_MAX_SPEED;
    GeneralAttributes.XmitLinkSpeed = NDIS_LINK_SPEED_UNKNOWN;
    GeneralAttributes.RcvLinkSpeed = NDIS_LINK_SPEED_UNKNOWN;
    GeneralAttributes.MediaConnectState = MediaConnectStateUnknown;
    GeneralAttributes.MediaDuplexState = MediaDuplexStateUnknown;
    GeneralAttributes.LookaheadSize = NIC_MAX_PACKET_SIZE –
        NIC_HEADER_SIZE;

    MPFillPoMgmtCaps(Adapter, &PowerManagementCapabilities,
        &Status, &ulInfoLen);
    if (Status == NDIS_STATUS_SUCCESS)
    {
        GeneralAttributes.PowerManagementCapabilities =
            &PowerManagementCapabilities;
    }
    else
    {
        GeneralAttributes.PowerManagementCapabilities = NULL;
    }
```

```c
// 不因获取电源管理功能失败而导致调用失败
Status = NDIS_STATUS_SUCCESS;

GeneralAttributes.MacOptions = NDIS_MAC_OPTION_COPY_LOOKAHEAD_DATA |
                              NDIS_MAC_OPTION_TRANSFERS_NOT_PEND |
                              NDIS_MAC_OPTION_NO_LOOPBACK;

GeneralAttributes.SupportedPacketFilters = NDIS_PACKET_TYPE_DIRECTED
                                        | NDIS_PACKET_TYPE_MULTICAST
                                        | NDIS_PACKET_TYPE_ALL_MULTICAST
                                        | NDIS_PACKET_TYPE_BROADCAST;

GeneralAttributes.MaxMulticastListSize = NIC_MAX_MCAST_LIST;
GeneralAttributes.MacAddressLength = ETH_LENGTH_OF_ADDRESS;

NdisMoveMemory(GeneralAttributes.PermanentMacAddress,
               Adapter->PermanentAddress,
               ETH_LENGTH_OF_ADDRESS);

NdisMoveMemory(GeneralAttributes.CurrentMacAddress,
               Adapter->CurrentAddress,
               ETH_LENGTH_OF_ADDRESS);

GeneralAttributes.PhysicalMediumType = NdisPhysicalMediumUnspecified;
GeneralAttributes.RecvScaleCapabilities = NULL;

// 典型以太网适配器的 NET_IF_ACCESS_BROADCAST
GeneralAttributes.AccessType = NET_IF_ACCESS_BROADCAST;

// 典型以太网适配器的 NET_IF_DIRECTION_SENDRECEIVE
GeneralAttributes.DirectionType = NET_IF_DIRECTION_SENDRECEIVE;

// 典型以太网适配器的 NET_IF_CONNECTION_DEDICATED
GeneralAttributes.ConnectionType = NET_IF_CONNECTION_DEDICATED;

// 典型以太网适配器的 IF_TYPE_ETHERNET_CSMACD（无论速率如何）
GeneralAttributes.IfType = IF_TYPE_ETHERNET_CSMACD;

// 如果是物理适配器，则按 RFC 2665 设置为 TRUE
GeneralAttributes.IfConnectorPresent = TRUE;

GeneralAttributes.SupportedStatistics = NDIS_STATISTICS_XMIT_OK_SUPPORTED
                                      | NDIS_STATISTICS_RCV_OK_SUPPORTED
                                      | NDIS_STATISTICS_XMIT_ERROR_SUPPORTED
                                      | NDIS_STATISTICS_RCV_ERROR_SUPPORTED
                                      | NDIS_STATISTICS_RCV_CRC_ERROR_SUPPORTED
                                      | NDIS_STATISTICS_RCV_NO_BUFFER_SUPPORTED
                                      | NDIS_STATISTICS_TRANSMIT_QUEUE_LENGTH_SUPPORTED
                                      | NDIS_STATISTICS_GEN_STATISTICS_SUPPORTED;

GeneralAttributes.SupportedOidList = NICSupportedOids;
GeneralAttributes.SupportedOidListLength = sizeof(NICSupportedOids);

Status = NdisMSetMiniportAttributes(MiniportAdapterHandle,
                                    (PNDIS_MINIPORT_ADAPTER_ATTRIBUTES)&GeneralAttributes);

// 分配所有其他内存块，包括共享内存（本地函数）
Status = NICAllocAdapterMemory(Adapter);
if (Status != NDIS_STATUS_SUCCESS)
{
    break;
}

// 初始化发送数据结构（本地函数）
NICInitSend(Adapter);

// 初始化接收数据结构（本地函数）
Status = NICInitRecv(Adapter);
if (Status != NDIS_STATUS_SUCCESS)
{
    break;
}

// 将总线相关寄存器映射到虚拟系统空间
Status = NdisMMapIoSpace((PVOID *)&(Adapter->CSRAddress),
                         Adapter->AdapterHandle,
                         Adapter->MemPhysAddress,
                         NIC_MAP_IOSPACE_LENGTH);

if (Status != NDIS_STATUS_SUCCESS)
{
    NdisWriteErrorLogEntry(Adapter->AdapterHandle,
                           NDIS_ERROR_CODE_RESOURCE_CONFLICT,
                           1,
                           ERRLOG_MAP_IO_SPACE);
    break;
}

// 在此处尽快禁用中断（本地函数）
NICDisableInterrupt(Adapter);

// 注册中断
// 嵌入的 NDIS 中断结构已在适配器结构内清零
NdisZeroMemory(&Interrupt,
               sizeof(NDIS_MINIPORT_INTERRUPT_CHARACTERISTICS));

Interrupt.Header.Type = NDIS_OBJECT_TYPE_MINIPORT_INTERRUPT;
Interrupt.Header.Revision = NDIS_MINIPORT_INTERRUPT_REVISION_1;
Interrupt.Header.Size = sizeof(NDIS_MINIPORT_INTERRUPT_CHARACTERISTICS);
Interrupt.InterruptHandler = MPIsr;
Interrupt.InterruptDpcHandler = MPHandleInterrupt;
Interrupt.MsiSupported = FALSE;
Interrupt.MsiSyncWithAllMessages = FALSE;
```

```c
Status = NdisMRegisterInterruptEx(Adapter->AdapterHandle,
                                  Adapter,
                                  &Interrupt,
                                  &Adapter->NdisInterruptHandle);
if (Status != NDIS_STATUS_SUCCESS)
{
    NdisWriteErrorLogEntry(Adapter->AdapterHandle,
                           NDIS_ERROR_CODE_INTERRUPT_CONNECT, 0);
    break;
}
Adapter->Flags |= F_MP_ADAPTER_INTERRUPT_IN_USE;

// 测试适配器硬件（本地函数）
Status = NICSelfTest(Adapter);
if (Status != NDIS_STATUS_SUCCESS)
{
    break;
}

// 初始化硬件并进行全面配置（本地函数）
Status = NICInitializeAdapter(Adapter);
if (Status != NDIS_STATUS_SUCCESS)
{
    break;
}

// 初始状态为暂停
Adapter->AdapterState = NicPaused;
// 设置链路检测标志
Adapter->Flags |= F_MP_ADAPTER_LINK_DETECTION;
// 递增引用计数，以便停止处理程序等待
NdisInterlockedIncrement(&(Adapter)->RefCount);

// 启用中断（本地函数）
NICEnableInterrupt(Adapter);

// 最小化初始化时间
NdisZeroMemory(&Timer,
               sizeof(NDIS_TIMER_CHARACTERISTICS));

Timer.Header.Type =
    NDIS_OBJECT_TYPE_TIMER_CHARACTERISTICS;
Timer.Header.Revision =
    NDIS_TIMER_CHARACTERISTICS_REVISION_1;
Timer.Header.Size = sizeof(NDIS_TIMER_CHARACTERISTICS);
Timer.AllocationTag = NIC_TAG;
// 用于延迟链路协商的本地定时器函数
Timer.TimerFunction = MpLinkDetectionDpc;
Timer.FunctionContext = Adapter;

Status = NdisAllocateTimerObject(Adapter->AdapterHandle,
                                 &Timer,
                                 &Adapter->LinkDetectionTimerHandle);

if (Status != NDIS_STATUS_SUCCESS)
{
    break;
}

liDueTime.QuadPart = NIC_LINK_DETECTION_DELAY;
isTimerAlreadyInQueue = NdisSetTimerObject(
    Adapter->LinkDetectionTimerHandle, liDueTime, 0, NULL);
ASSERT(!isTimerAlreadyInQueue);

} while (FALSE); // do while 代码块结束

if (Adapter && (Status != NDIS_STATUS_SUCCESS))
{
    // 如果失败，撤消所有操作
    NdisInterlockedDecrement(&(Adapter)->RefCount);
    MpFreeAdapter(Adapter);
}
return Status;
```

## MiniportHaltEx

NDIS 框架在微端口适配器被移除时调用微端口驱动程序的 `MiniportHaltEx` 函数，以停止硬件并释放资源。由于这可能在任意时刻发生，因此应考虑检查是否为意外硬件移除并相应处理。清单 10-3 展示了微端口驱动程序停止函数的一种可能实现示例。

*清单 10-3. MiniportHaltEx 函数实现示例*

```c
VOID MPHalt(IN NDIS_HANDLE MiniportAdapterContext,
            IN NDIS_HALT_ACTION HaltAction)
{
    LONG Count;

    PMP_ADAPTER Adapter = (PMP_ADAPTER)MiniportAdapterContext;

    if (HaltAction == NdisHaltDeviceSurpriseRemoved)
    {
        // 例如，您可能需要确保停止操作不会依赖硬件产生中断来完成挂起操作。
    }

    ASSERT(Adapter->AdapterState == NicPaused);
    Adapter->Flags |= F_MP_ADAPTER_HALT_IN_PROGRESS;

    // 调用关闭处理程序以禁用中断并关闭硬件
    MPShutdown(MiniportAdapterContext, NdisShutdownPowerOff);

    // 注销中断，使中断不再被处理
    if ((Adapter->Flags & F_MP_ADAPTER_INTERRUPT_IN_USE) != 0)
    {
        NdisMDeregisterInterruptEx(Adapter->NdisInterruptHandle);
        Adapter->Flags &= ~ F_MP_ADAPTER_INTERRUPT_IN_USE;
    }

    NdisCancelTimerObject(Adapter->LinkDetectionTimerHandle);

    // 递减在 MPInitialize 中递增的引用计数
}
```


