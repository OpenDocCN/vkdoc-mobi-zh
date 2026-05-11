# 调试设备驱动程序

`WCHAR MyString[24] = {0};`

`EXTSTACKTRACE stcktr[2];`

`Frames = StackTrace(0, 0, 0, stcktr, 2);`

`dprintf("Stack frames \n");`

`for (int i = 0; i < 2; i++)`
```
{
    dprintf("Stack frame pointer %08x \n",
            stcktr[i].FramePointer);
    dprintf("Stack frame instruction pointer %08x \n",
            stcktr[i].ProgramCounter);
}
```

`dprintf("Return address %08x \n",`
       `stcktr[i].ReturnAddress);`

*清单 11-8. 同一扩展 DLL 的示例模块定义文件*

```
LIBRARY "DemoDbgExt"

EXPORTS
    WinDbgExtensionDllInit
    WinDbgExtensionDllShutdown
    ExtensionApiVersion
    demo
```

*图 11-11. 调用 !demo 扩展命令*

#### 远程工具

随 Platform Builder 7 安装的远程工具基于远程工具框架，它是一个 .NET 类库（DLL）。除了 Microsoft 随 Platform Builder 提供的远程工具外，一个有趣的功能是，你可以利用该框架设计和开发满足特定需求的插件和远程工具。本节专门介绍如何使用远程工具框架创建一个简单的远程工具，用于显示设备上所有活动的设备驱动程序。

远程工具框架通过核心连接基础设施与设备建立通信。

要创建你自己的远程工具，可以使用位于 `<新项目\其他语言\Visual C#\远程工具框架>` 下的 Visual Studio 2008 远程工具框架插件向导，如图 11-12 所示。

从这里开始，如果你选择创建一个托管设备端的远程工具，过程会非常简单。你只需实现设备端工具的功能，并处理桌面 shell 上的显示即可。

*图 11-12. 创建新的远程工具项目*

此处的远程工具示例会返回目标设备上所有当前活动设备驱动程序的列表。

#### 设备端实现

向导创建了一个名为 `sync.cs` 的文件，其中包含一个示例，用于检索设备的当前时间并将其发送回桌面。这意味着需要实现的基础设施代码非常少。清单 11-9 演示了一个函数的代码，该函数实现了检索活动设备驱动程序，并使用该信息将其发送回去。

*清单 11-9. 设备端远程工具功能示例*

```
static private void DispatchActiveDrivers()
{
    RegistryKey rk = Registry.LocalMachine;
    RegistryKey rkDrvActv = rk.OpenSubKey("Drivers\\Active");

    uint nCount = (uint)rkDrvActv.SubKeyCount;
    m_commandPacket.AddParameterDWORD(nCount);

    foreach (string subKeyName in rkDrvActv.GetSubKeyNames())
    {
        RegistryKey tempKey = rkDrvActv.OpenSubKey(subKeyName);
        string strVal = string.Empty;
        strVal = strVal + subKeyName + "::";

        foreach (string valueName in tempKey.GetValueNames())
        {
            if (valueName.CompareTo("Name") == 0)
            {
                strVal = strVal + ";" +
                         tempKey.GetValue(valueName).ToString();
            }
            else if (valueName.CompareTo("Key") == 0)
            {
                strVal = strVal + ";" +
                         tempKey.GetValue(valueName).ToString();
            }
        }

        m_commandPacket.AddParameterString(strVal);
        strVal = string.Empty;
    }
}

/// <summary>
/// 接收到的命令数据包
/// </summary>
/// <param name="sender">事件源</param>
/// <param name="e">事件参数</param>
private static void CommandTransport_CommandPacketReceived(
    object sender,
    CommandPacketEventArgs e)
{
    // TODO: 添加其他命令
    switch (e.CommandPacketIn.CommandId)
    {
        case 1:
            // 该命令数据包不附带任何参数。
            m_commandPacket = new CommandPacket();
    }
}
```



