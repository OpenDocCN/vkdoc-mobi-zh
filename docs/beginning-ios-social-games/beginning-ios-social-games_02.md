# 关于作者



![A978-1-4302-4906-1_BookFrontmatter_Fig1_HTML.jpg](img/A978-1-4302-4906-1_BookFrontmatter_Fig1_HTML.jpg) 凯尔·里希特（Kyle Richter）是 Dragon Forged Software 的创始人，这是一家屡获殊荣的 iOS 和 Mac 开发公司；同时他也是 Empirical Development 的联合创始人，这是一家承接 iOS 开发的外包公司。凯尔于 90 年代初开始编写代码，并始终致力于 Mac 平台。他是《Beginning iOS Game Center and Game Kit: For iPhone, iPad, and iPod touch》以及《iOS Components and Frameworks: Understanding the Advanced Features of the iOS SDK》的作者（对应的购买链接为`http://amzn.to/1fi9Zdt`和`http://amzn.to/19rcIiO`）。凯尔还在`http://KyleRichter.com`上维护着一个关于 iOS 开发社区的博客。他管理着一支由 30 多名全职 iOS 开发者组成的团队，并负责两家开发公司的日常运营。他的早期作品包括首个基于位置的移动端点对点服务、首个 iOS 信用卡处理程序以及首个多人在线 iOS 游戏。凯尔周游世界，发表关于开发与创业的演讲；目前他定居在佛罗里达群岛，与他忠诚的边境牧羊犬一起生活。你可以在 Twitter 上通过@kylerichter 找到他。

## 关于技术审校者

![A978-1-4302-4906-1_BookFrontmatter_Fig2_HTML.jpg](img/A978-1-4302-4906-1_BookFrontmatter_Fig2_HTML.jpg) 马库斯·S·扎拉（Marcus S. Zarra）是位于科罗拉多州的 Empirical Development, LLC 的联合创始人（`http://empiricaldevelopment.com`）。他自 2003 年起从事 Cocoa 软件开发，自 1996 年起从事 Java 软件开发，并从 1985 年开始涉足该行业。目前，马库斯正在为 iOS 和 OS X 开发软件。除了编写代码，他还通过在 Cocoa Is My Girlfriend（`http://www.cimgf.com`）上撰写博文并提供代码示例来帮助其他开发者。马库斯也是《Core Data (2nd edition): Data Storage and Management for iOS, OS X, and iCloud》（`http://pragprog.com/book/mzcd2/core-data`）的作者，以及《Core Animation: Simplified Animation Techniques for Mac and iPhone Development》（`http://amazon.com/Core-Animation-Simplified-Techniques-Development/dp/0321617754/ref=sr_1_1?ie=UTF8&qid=1307226039&sr=8-1`）的合著者。你可以在 Twitter（`https://twitter.com/mzarra`）、App.net（`https://alpha.app.net/mzarra`）以及 StackOverflow（`http://stackoverflow.com/users/10673/marcus-s-zarra`）上找到马库斯。

![A978-1-4302-4906-1_BookFrontmatter_Fig3_HTML.jpg](img/A978-1-4302-4906-1_BookFrontmatter_Fig3_HTML.jpg) 卢卡斯·L·乔丹（Lucas L. Jordan）是一位终身计算机爱好者，多年从事开发者工作，专注于用户界面。他是《JavaFX Special Effects: Taking Java RIA to the Extreme with Animation, Multimedia, and Game Elements》以及《Practical Android Projects》（均由 Apress 出版）的作者。卢卡斯对多种形式的移动应用开发充满兴趣。他近期已辞去日常工作，以 ClayWare, LLC 的名义投身于应用开发事业。了解更多信息请访问`http://claywaregames.com`。

## 简介

移动游戏具有社交属性，并且正日益融入我们的社交生活。Game Center 和 Game Kit 是苹果公司为将社交元素整合到 iOS 游戏中给出的解决方案，使得添加排行榜、成就系统、多人对战和语音聊天等功能变得前所未有的简单。社交网络集成已经无处不在，从汽车到冰箱，为 iOS 游戏添加 Twitter 和 Facebook 支持几乎已成为一项必备功能。如果历史教会了我们什么，那么 AirPlay 和游戏控制器可能是移动游戏领域的下一次巨大飞跃；那些领先于未来一步的人，将占据成功的有利位置。

## 前置要求

本书假设你已具备创建 iOS 应用所需的基本技能和理解能力。同时，也假设你拥有使用 Xcode 5.0 的必要背景知识。本书不会介绍如何定义方法和变量、安装和启动 Xcode，或创建和使用新类。关于这些主题，市面上已有许多优秀的书籍。当你准备好开始使用一些更高级的 Cocoa 技术（如 Game Center 和 Game Kit）时，请确保你已经充分掌握了基础知识，从而能够在不查阅其他参考资料的情况下顺利阅读本书。

除了基本要求外，Game Center 及其他涉及的框架还大量使用了块（blocks），这是 Objective-C 中一个相对较新的编程概念。如果你尚未使用过块，我们建议你阅读苹果公司的相关指南，你可以通过访问`http://developer.apple.com`并搜索“blocks”来找到它。同时，你也应能熟练运用 Objective-C 2.0 版本引入的所有特性。

## 本书结构

当你开始阅读本书时，你会发现各章节基本上是独立的。我们已尽力使每一章都能独立于其他章节进行阅读。如果你对 Game Center 或 Game Kit 尚无任何经验，强烈建议你从第一章和第二章开始阅读，然后再跳读其他章节，因为这两章将为你提供在开发环境中启动并运行 Game Center 和 Game Kit 的基础信息。最后四章是基于第三章的示例代码构建的，但并不会在已有产品的基础上持续迭代。

每一章都围绕着一个简单的 iOS 示例游戏展开，该游戏在第一章中引入。从头到尾跟随本书学习，将引导你完成创建一个功能完备的、集成了 Game Center 和 Game Kit 的 iOS 游戏的全过程。此外，每一章都会构建一个 Game Center Manager 类，该类被设计为可在你的所有项目中重用。第 12 章和第 13 章将向你介绍 Social Framework，以及如何为示例游戏添加 Twitter 和 Facebook 支持。第 14 章和第 15 章则涵盖了 AirPlay 和游戏控制器。

如果你已有常规 iOS 开发或 Game Center 的背景知识，并正在寻找特定技术的帮助，本书的每一章都将引导你深入了解其所涉及的技术，并提供示例，展示如何将该技术应用到你的软件中。

## 源代码

本书中项目的源代码可在`www.apress.com/source-code/`获取。源代码同时提供 ARC（自动引用计数）和非 ARC 格式。本书正文中使用的代码示例是非 ARC 格式的，这是因为添加 ARC 支持比删除它更容易。尽管 ARC 正迅速成为标准，但仍有一些开发者、管理者和项目出于种种原因，尚未准备好使用 ARC。


## 所需软件、材料与设备

要开发 iOS 软件——更具体地说是基于 Game Center 和 Game Kit 的 iOS 软件——你首先需要一台搭载 OSX 10.8 (Mountain Lion) 或更新版本操作系统的 Intel 架构 Mac 电脑。虽然你可以在 10.6 版本上进行开发，但它将无法支持最新版本的 Xcode。你还需要一份 Xcode 副本，可从 Mac App Store 或 [`http://developer.apple.com`](http://developer.apple.com/) 免费下载。本书针对 iOS 7 编写；由于它是在用户从 iOS 6 向 iOS 7 迁移期间发布的，因此也适用于 iOS 6。除非文中另有说明，所有代码均兼容 iOS 6。

除了软件和硬件要求外，你还需要一个由 Apple 提供的 iOS 开发者账户。该账户可让你在设备上构建和测试软件，并将完成的成品发布到 App Store。开发者账户每年的费用为 99 美元，你可以在 [`http://developer.apple.com/iPhone`](http://developer.apple.com/iPhone) 购买。

## 致谢

没有许多人的支持与帮助，本书的编写将无法完成。翻阅任何技术书籍的致谢部分都会发现，虽然作者可能只有一位，但像这样一本技术书籍的出版需要数十人的共同努力。首先，我要感谢 Langille Design 的 Jordan Langille，他在百忙之中抽出时间为本书的示例程序提供了图形素材。

我还要感谢 Joe Keeley。Joe 帮我分担了 Dragon Forged Software 和 Empirical Development 的许多工作，让我能有时间撰写本书。如果没有 Joe，我将无法从日常工作中脱身，甚至连一个章节都写不出来。

此外，我还要感谢 Marcus Zarra，他多次搁置自己的生活（这比我愿意承认的次数还要多）来为本书提供技术审阅意见。Marcus 本人也是一位经验丰富的作者，深知技术书籍的撰写工作有多繁重，却毫不犹豫地答应审阅我的书稿。我也很感激 Lucas Jordan，他在最后关头抽出时间帮助本书完稿。同时，我要感谢 Free Time Studios 的 Nathan Eror，他抽出时间为本书撰写序言。

最后但同样重要的是，我要感谢整个社区。在我的一生中，从未遇到过如此支持、如此杰出的群体。从 Cocoaheads 和 NSCoders 聚会，到各种会议和论坛，每个人始终都展现着最高水准。人们常说，在 Apple 开发者社区中，两个相互竞争的开发者也能成为朋友，互相分享代码和秘密。每当我陷入看似无法解决的问题时，总有人伸出援手帮助我渡过难关。在我多年的开发经历和全球旅途中，我从未遇到过像 Apple 开发者社区这样出色的群体。如果没有他们，我或许永远无法发布我的第一个应用。



