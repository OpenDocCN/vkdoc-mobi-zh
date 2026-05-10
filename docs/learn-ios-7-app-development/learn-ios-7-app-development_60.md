# 杂项与收尾

还有少量代码改动，有些显而易见，有些则不那么明显。这些改动将使你的应用最终成为一款双人游戏。我将在此处汇总这些改动。如果你正在按照本节内容修改单人版 `SunTouch`，请以此为指南，从 `Learn iOS Development Projects` → `Ch 14` → `SunTouch-4` 文件夹中的源文件中定位并复制更新后的代码。

- `STSun.h`
  - 添加一个布尔型 `localPlayer` 属性。该属性用于确定哪个玩家捕获了太阳。
- `STGame.h`
  - 添加一个 `readonly` 的 `opponentScore` 属性，用于计算对手玩家的分数。
  - `-willCaptureSunAtIndex:gameTime:` 方法重命名为 `-willCaptureSunAtIndex:gameTime:localPlayer:`。当太阳被本地玩家捕获时，新增的 `localPlayer` 参数为 `YES`。
  - 定义一个 `kGameInfoOpponent` 键，用于标识打击通知的来源。
- `STGame.m`
  - 添加一个合成的 `readonly` 布尔型属性 `twoPlayer`。如果这是双人游戏，该属性为 `YES`。
  - 在 `-weightAtTime:` 方法中，如果 `twoPlayer` 为 `YES`，则将权重值加倍。双人游戏中捕获太阳的分数翻倍。
  - 实现 `-opponentScore` 的 getter 方法。`-score` 和 `-opponentScore` 方法均使用新的 `-scoreForLocalPlayer:` 方法，该方法可为任一玩家计算分数。
  - 新增 `-startMultiPlayerWithMatch:started:` 方法。该方法将替代 `-startSinglePlayer`，用于启动双人游戏。
  - 修改 `-strike:radius:inView:` 方法，使其在发送 `-willCaptureSunAtIndex:gameTime:localPlayer:` 时为 `localPlayer` 参数传递 `YES`。
  - `-willCaptureSunAtIndex:gameTime:localPlayer:` 方法设置将被捕获的太阳的 `localPlayer` 属性。这决定了被捕获太阳的图像以及获得分数的玩家。
- `STGameViewController.h`
  - 添加一个布尔型 `twoPlayer` 属性。当用户启动双人游戏时，该属性设为 `YES`。
- `STGameViewController.m`
  - 在 `-viewDidLoad` 方法中，使用 `twoPlayer` 属性来配置单人或双人游戏视图。对于单人游戏，`opponentGameView` 将被移除（删除），因为它未被使用，并且本地游戏视图的 `opaque` 属性设为 `YES`。对于双人游戏，两个游戏视图均被使用，本地游戏视图的 `opaque` 属性设为 `NO`，允许其拥有透明区域，从而显示背后的对手游戏视图。
  - 在 `-finishGame` 方法中，`twoPlayer` 属性用于选择游戏结束时的提示信息。双人游戏的提示会告知本地玩家是获胜（还是落败），以及双方的分数。双人游戏的分数会被提交到 `kTwoPlayerLeaderboardID` 排行榜。
- `STMainViewController.m`
  - 在 `-prepareForSegue:sender:` 方法中，游戏视图控制器的 `twoPlayer` 属性在呈现之前，会根据 segue 的标识符（`"singlePlayer"` 或 `"twoPlayer"`）被设为 `YES` 或 `NO`。将此属性设为 `YES` 将触发一系列事件，从而创建并运行双人游戏。
- `Main_iPhone.storyboard` / `Main_iPad.storyboard`
  - 从“双人游戏”按钮创建一个模态转场（modal segue）连接到游戏视图控制器。将该转场的标识符属性设为 `twoPlayer`。

至此，你的双人游戏前端部分已完成。接下来是网络通信部分，它将使你的游戏与另一台 iOS 设备上的另一位玩家连接起来。

## 匹配机制

实时的点对点游戏通信大致可分为两个阶段：匹配机制和实时通信。到目前为止，匹配机制是最复杂的，所以 `GameKit` 能替你完成这部分工作真是太好了。

匹配机制是指在另一台 iOS 设备上发现你的应用的另一个实例并与之建立连接的过程。它对你的应用设计最大的影响在于，它从根本上改变了游戏的启动方式。在单人版中，`STGameViewController` 创建一个 `STGame` 对象并向其发送 `-startSinglePlayer` 消息，游戏便立即开始。

启动双人版则是一个多步骤过程：

`STGameViewController` 创建一个 `GKMatchRequest` 对象。  
该匹配请求用于创建并呈现一个 `GKMatchmakerViewController`。  
应用等待 `GKMatchmakerViewController` 定位并连接到另一位玩家。  
如果成功，`-matchmakerViewController:didFindMatch:` 委托方法会创建一个 `STGame` 对象并向其发送 `-startMultiplayerWithMatch:` 消息。  
`STGame` 对象向远端应用发送“游戏开始”数据。当它接收到来自远端应用的相应“游戏开始”数据时，游戏开始。

接下来的几个小节将把这些代码添加到你的应用中。一旦匹配代码就位，你将进入实际通信代码的编写。



