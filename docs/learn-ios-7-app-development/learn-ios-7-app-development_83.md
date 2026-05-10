# 练习

你可能注意到了上一个 Pigeon 版本的一个缺陷——为了巧妙回避，我让你在“设置”应用中更改“同步位置”设置之前，先在 Xcode 中停止应用。既然你现在了解了应用状态，问题应该很明显了。

Pigeon 仅在首次启动时检查`kPreferencesLocationsInCloud`的值。如果 Pigeon 用户切换到“设置”应用，更改了“同步位置”设置，然后立即返回 Pigeon，Pigeon 可能仍在运行。它会被移到后台状态并暂停一会儿，但用户返回时会重新激活。问题是 Pigeon 不会再次检查`kPreferencesLocationsInCloud`的值，因此不会知道它已经改变。

解决这个问题有几种方法。一种是在应用委托方法`-applicationWillEnterForeground:`中添加代码。我选择的解决方案是监听`NSUserDefaults`发布的`NSUserDefaultsDidChangeNotification`。请记住，设置捆绑包中的值会更改你应用的用户默认设置，你可以通过通知中心监听这些更改。

你可以在`Learn iOS Development Projects` ➤ `Ch 18` ➤ `Pigeon E1`文件夹中找到我对这个问题的解决方案。看看你是否能想到第三种——非常相似但更有针对性的——解决方案。（提示：阅读`-applicationWillEnterForeground:`方法的文档。）

