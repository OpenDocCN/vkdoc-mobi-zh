# 第 20 章 ■ 模型-视图-控制器模式

• `PDF Kit`是一个用于嵌入、显示和创建 PDF 图像及文档的强大框架。如果你曾经将图标从侧边栏或程序坞拖出并看到过“噗”的动画效果，该动画实际上是一个多帧的 PDF 文档。

• `Quartz Composer`是一个执行高级、实时图像过滤、合成和变换的框架。它支持插件架构，可实现无限的效果。

可以在《Cocoa 绘画指南》[⁶]、《WebKit Objective-C 编程指南》[⁷]和《Quartz Composer 编程指南》[⁸]中阅读关于这些以及许多其他高级绘图主题的更多内容。

你现在已经理解了视图对象功能的一半。视图对象是应用程序和用户之间的中介。它们显示应用程序的内容，同时也会转换用户的操作。视图对象必须感知用户在做什么（按键、移动鼠标），对其进行解释，并将其转化为要发送给控制器对象的操作消息。接下来的几节描述了视图对象如何参与事件处理。

### 文档模型

正如你将在接下来的几节中发现的，对象的组织方式定义了你的应用程序如何响应事件，并影响类的设计。理解基于文档的应用程序中的对象组织方式非常重要。基于文档的应用程序是一种在窗口中打开数据文件内容，通常允许你操作其内容，并将结果保存到新的或现有文档文件中的应用程序。`TicTacToe`项目就是一个基于文档的应用程序。你可以将游戏保存为`tictactoe`文档，打开旧的游戏，并恢复到之前保存的游戏。

基于文档的应用程序中的关键对象如图 20-13 所示。

***图 20-13.** 基于文档的应用程序对象* [⁶] [⁷] [⁸]

[⁶]: Apple Inc., *Cocoa Drawing Guide*, http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDrawingGuide/, 2009.
[⁷]: Apple Inc., *WebKit Objective-C Programming Guide*, http://developer.apple.com/documentation/Cocoa/Conceptual/DisplayWebContent/, 2008.
[⁸]: Apple Inc., *Quartz Composer Programming Guide*, http://developer.apple.com/documentation/GraphicsImaging/Conceptual/QuartzComposer/, 2008.

[www.it-ebooks.info](http://www.it-ebooks.info/)

