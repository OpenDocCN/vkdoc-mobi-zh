# 高级`GKSession`交互

有时，你可能想要更深入地介入`GKSession`对象，而不只是我们之前看到的初始配置。表 6-3 列出了可用于自定义`GKSession`对象的可用属性。

表 6-3. `GKSession`对象上的可用属性

| 属性名称 | 描述 |
| --- | --- |
| `available` | `available`属性用于判断你的对等设备是否对其他正在寻找连接的对等设备可见。通常，此属性会通过对等选择器为你设置，但你也自己设置。当此布尔值设置为`YES`后，你将继续保持与已经连接的任何对等设备的连接，但不会接收到新的连接请求。 |
| `delegate` | `GKSession`的`delegate`属性将在下一节中详细讨论。 |
| `disconnectTimeout` | `disconnectTimeout`属性是超时时间（以秒为单位），即你的会话在断开无响应的对等设备之前等待的时间。Apple 为此值提供了一个默认值，经过测试，该值似乎在 20 到 30 秒之间。文档中未透露确切数值。 |
| `displayName` | `displayName`属性是一个只读的`NSString`，代表对等设备的人类可读名称；此属性应通过你的 GUI 显示给其他用户。`displayName`属性与 iTunes 中显示的设备名称相同。 |
| `peerID` | `peerID`属性是一个只读字符串，用于向其他设备标识你的会话对等设备。该值是唯一的。`peerID`值不应直接显示给用户。 |
| `sessionID` | `sessionID`属性由配置为服务器的会话用来向其他对等设备广播自身，以及由配置为客户端的会话用来搜索兼容的服务器。`sessionID`应为经过批准的 Bonjour 服务类型的简短名称。 |
| `sessionMode` | `sessionMode`属性是用于查找其他对等设备的会话。此属性是只读的，在首次初始化新会话时进行配置。它有三个可能的值：`GKSessionModeServer`、`GKSessionModeClient`和`GKSessionModePeer`。 |



## 点对点选择器委托

在前面的章节中，我们看到使用 `GKPeerPickerController` 能免费获得许多功能。然而，在接受了来自其他用户的连接后，我们的应用还不知道该如何继续。

在本节中，我们将讨论 `GKPeerPickerControllerDelegate`。点对点选择器会调用此委托来创建新的会话对象，并用于处理正常网络连接过程中发生的状态变化。

`GKPeerPickerControllerDelegate` 有三个主要职责，你需要实现并响应它们。它需要创建会话、响应新的连接消息，以及处理用户取消点对点选择器的情况。

首先，我们来看 `peerPickerController:didSelectConnectionType:`。这个委托方法会告知我们用户选择了哪种连接类型。你可能还记得在之前的章节中，我们使用过 `connectionTypeMask`。如果你为用户提供了选择通过局域网还是蓝牙连接的选项，此委托方法将返回所选结果。如果用户选择在线连接，你应该关闭点对点选择器控制器，并显示你自己的局域网连接视图；以下是实现该行为的代码示例。

```
- (void)peerPickerController:(GKPeerPickerController*)picker
didSelectConnectionType:(GKPeerPickerConnectionType)type
{
    if (type == GKPeerPickerConnectionTypeOnline)
    {
        [picker dismiss];
        [picker release];
        // 在此处显示你自己的用户界面。
    }
}
```

`GKPeerPickerControllerDelegate` 还负责返回一个 `GKSession`。当你的点对点选择器需要一个会话对象时，它会调用 `peerPickerController:sessionForConnectionType:`。如果你实现了这个方法，你必须创建一个或返回一个现有的 `GKSession`。

**注意**

如果在 `peerPickerController:sessionForConnectionType:` 中创建新的 `GKSession`，那么你创建的会话必须将其自身声明为 `GKSessionModePeer`。如果你不需要实现 `peerPickerController:sessionForConnectionType:`，并且该会话使用的是蓝牙协议，则点对点控制器会使用默认的 `sessionID` 和 `displayName` 参数分配一个新的会话。如果你正在创建新的局域网连接，则需要实现 `peerPickerController:sessionForConnectionType:` 并返回所需的 `GKSession`。

你还需要实现 `peerPickerControllerDidCancel:`。尽管这个方法是可选的，但 Game Kit 期望它被实现。此方法返回后，点对点选择器将自动关闭，因此你不需要添加代码来处理此事件。

在使用 `GKPeerPickerControllerDelegate` 时，我们需要实现的最后一个委托方法是 `peerPickerController:didConnectPeer:toSession:`。每当有新的对等方连接到会话时，就会调用此方法。

```
- (void)peerPickerController:(GKPeerPickerController*)picker
didConnectPeer:(NSString*)peerID
toSession:(GKSession*)session
{
    currentSession = session;
    // 保存会话和 peerID
    [self dismissModalViewControllerAnimated:YES];
}
```

当此方法被调用时，我们想要保留对会话和 `peerID` 的引用。我们将在接下来的章节中，开始处理双向发送数据时用到它们。一旦对等方成功连接，你的应用应该接管会话的所有权，并关闭点对点选择器。

图 6-11 展示了一个已进入连接状态并将其可用性标志设置为 NO 的对等 iPhone 设备的视图。

![A978-1-4302-4906-1_6_Fig11_HTML.jpg](img/A978-1-4302-4906-1_6_Fig11_HTML.jpg)

图 6-11.

名为 Miranda 的设备已将其可用性状态设置为 false。

**注意**

尽管 `peerPickerController:didConnectPeer:toSession:` 是一个可选的协议方法，但在你完全启用 Game Kit 网络功能之前，你需要实现它。

## 总结

在本章中，你学习了如何使用 Game Kit 网络功能，通过局域网（Wi-Fi）或蓝牙将两台设备连接在一起。在上一章中，我们介绍了匹配和邀请功能。通过这两种技术，我们完全涵盖了在 iOS 平台上使用 Game Center 和 Game Kit 技术查找并连接对等方的所有可用方法。

我们还研究了一些真实的 iOS 应用案例，这些应用受益于 Game Kit 网络集成，并且你已经看到，只需投入少量时间，就能轻松地将你的应用提升到更高的品质层级。

此外，我们深入探讨了点对点选择器的工作原理，包括如何通过蓝牙或局域网请求新的会话，以及如何连接对等方。我们还涵盖了让你的应用快速启动并运行这些系统所需的全部委托和控制器开销。

在下一章中，我们将讨论如何为移动设备设计网络，首先概述网络类型，并包括移动网络设计的注意事项。在第 8 章中，我们将把所有已建立连接的对等方联系起来，并让它们开始彼此通信。

