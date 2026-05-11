# 排版后的文档

要从 Bridge Control 应用切换到其设置，你需要进入主屏幕，启动设置应用，找到 Bridge Control 条目并选择它。这需要很多步骤。这非常繁琐，以至于许多应用选择包含自己的设置界面，而不是让用户经历所有这些步骤。如果能够直接将用户带到设置应用中你的设置界面，那岂不是更好？从 iOS 8 开始，你可以做到这一点。还记得我们在图 12-30 中添加到`SecondViewController`的“打开设置应用”按钮吗？我们在视图控制器中将其连接到`settingButtonClicked:`方法，但该方法中没有放入任何代码。现在让我们修复这个问题。添加以下以粗体显示的代码：

```
- (IBAction)settingsButtonClicked:(id)sender {
    [[UIApplication sharedApplication]
          openURL:[NSURL URLWithString:UIApplicationOpenSettingsURLString]];
}
```

这段代码使用存储在外部常量`UIApplicationOpenSettingsURLString`（其值实际上是`app-settings:`）中的系统定义 URL，从我们的视图控制器直接启动设置应用。运行应用，切换到第二个标签页，点击“打开设置应用”按钮——你将被直接带到我们的设置界面，即图 12-3 中所示的那个。这是一个很大的改进。但不幸的是，没有快速返回的方法——要返回到我们的应用，你必须再次经过主屏幕。也许这会在未来的 iOS 版本中得到修复。

## 传送我吧，斯科蒂

此时，你应该对设置应用和用户默认机制有了非常扎实的理解。你知道如何向应用添加设置包，以及如何为应用的偏好设置构建视图层次结构。你还学习了如何使用`NSUserDefaults`读写偏好设置，以及如何让用户从应用内部更改偏好设置。你甚至有机会在 Xcode 中使用新的项目模板。在应用偏好设置方面，现在你应该能够处理任何问题了。

在下一章中，我们将向你展示如何在应用退出后保留数据。准备好了吗？让我们开始吧！

