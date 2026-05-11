# Swift OS X 编程入门（零基础适用）

## 华莱士·王（Wallace Wang）

作者在本书中提及的任何源代码或其他补充材料，读者可在[`www.apress.com/9781484212349`](http://www.apress.com/9781484212349)获取。关于如何查找本书源代码的详细信息，请访问[`www.apress.com/source-code/`](http://www.apress.com/source-code/)。

ISBN 978-1-4842-1234-9  
e-ISBN 978-1-4842-1233-2  
DOI 10.1007/978-1-4842-1233-2  

© Apress 2015

**Swift OS X 编程入门（零基础适用）**

总经理：韦尔莫德·斯帕尔（Welmoed Spahr）  
主编：路易丝·科里根（Louise Corrigan）  
技术审校：布鲁斯·韦德（Bruce Wade）  
编辑委员会：史蒂夫·安格林（Steve Anglin）、路易丝·科里根（Louise Corrigan）、乔纳森·根尼克（Jonathan Gennick）、罗伯特·哈钦森（Robert Hutchinson）、米歇尔·洛曼（Michelle Lowman）、詹姆斯·马克姆（James Markham）、苏珊·麦克德莫特（Susan McDermott）、马修·穆迪（Matthew Moodie）、杰弗里·佩珀（Jeffrey Pepper）、道格拉斯·庞迪克（Douglas Pundick）、本·雷诺-克拉克（Ben Renow-Clarke）、格温南·斯皮林（Gwenan Spearing）、史蒂夫·韦斯（Steve Weiss）  
协调编辑：马克·鲍尔斯（Mark Powers）  
文字编辑：凯伦·詹姆森（Karen Jameson）  
排版：SPi Global  
索引编制：SPi Global  
插画：SPi Global  
封面摄影：马丁·弗鲁姆（Martijn Vroom）

如需了解翻译相关信息，请发送电子邮件至`rights@apress.com`，或访问[`www.apress.com`](http://www.apress.com/)。Apress 及朋友 of ED 系列图书可批量购买用于学术、企业或推广用途。大多数图书也提供电子版及许可证。如需更多信息，请参考我们的批量销售与电子版许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本作品受版权保护。出版方保留所有权利，无论整体或部分内容，具体包括翻译权、重印权、插图复用权、朗诵权、广播权、缩微胶片或任何其他物理形式的复制权，以及信息存储与检索、电子改编、计算机软件或目前已知或未来开发的类似或不类似方法的传输权。法律保留的例外情况包括：与评论、学术分析相关的简短摘录，或专门为在计算机系统上输入和执行而提供的材料（仅供购买者个人使用）。未经出版方所在地现行版权法规定及斯普林格（Springer）许可，不得复制本出版物或其部分内容。使用许可可通过版权清算中心（Copyright Clearance Center）的 RightsLink 获取。违反相关版权法将受到起诉。

本书中可能出现商标名称、标识及图像。本书仅在编辑性描述中使用这些名称、标识及图像，以维护商标所有者权益，并无意侵犯商标权。文中使用的商号、商标、服务标志及类似术语，即使未明确标注，也不应视为对其是否受专有权利保护的意见表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版方对可能存在的任何错误或遗漏不承担法律责任。出版方对本书内容不作任何明示或暗示的保证。  

本书通过施普林格科学与商业媒体纽约公司（Springer Science+Business Media New York）在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是加利福尼亚州有限责任公司，其唯一成员（所有者）为施普林格科学与商业媒体金融公司（SSBM Finance Inc），后者为特拉华州公司。

谨以此书献给所有一直想学习如何在 Macintosh 上编程的人。只要你愿意花时间并相信自己，你就能学会任何想学的东西。

> “答应我，永远记住：你比自己以为的更勇敢、更坚强、更聪明。”  
> ——克里斯托弗·罗宾对小熊维尼说的话

## 引言



## 为什么要学习 Swift 并为 Macintosh 编程？

无论你是一个完全零基础、想要踏入编程世界的新手，一位对编程有所了解但渴望深入学习的人，还是一位精通其他编程语言但不熟悉 Macintosh 编程的资深程序员，这本书都适合你。无论你的技能水平如何，本书都将帮助每个人理解如何使用苹果最新的编程语言 `Swift` 来为 Macintosh 创建 OS X 程序。

现在你可能会想，为什么要学习 `Swift`，又为什么要为 Macintosh 编程？答案很简单。

首先，`Swift` 是苹果最新的编程语言，旨在比以往更快、更简单、更可靠地创建 OS X 和 iOS 程序。以前，你必须使用 `Objective-C` 来创建 OS X 和 iOS 应用。虽然 `Objective-C` 功能强大，但它更难学习；读写起来更复杂；而且由于其复杂性，更容易在程序中引入错误或缺陷。

另一方面，`Swift` 与 `Objective-C` 同样强大（实际上，你很快就会看到它更强大），更容易学习，读写起来也简单得多，同时还能最大程度地减少常见的编程错误。`Swift` 为你提供了 `Objective-C` 的所有优点，而没有任何缺点。此外，`Swift` 还提供了 `Objective-C` 所不具备的特性，这使得 `Swift` 在现在和将来都是更值得学习和使用的编程语言。由于 `Swift` 是苹果的官方编程语言，你可以肯定学习 `Swift` 将在现在和未来带来更大的机遇。

其次，你可能会想为什么要学习创建 Macintosh 程序？毕竟，当前的热门趋势是学习为 iPhone、iPad 和 Apple Watch 创建 iOS 应用。如果你计划开发软件，你肯定想使用 `Swift` 来创建 iOS 应用。

然而，学习 `Swift` 意味着理解以下内容：

- 编程原理，特别是面向对象编程
- `Swift` 编程语言的语法
- `Xcode` 的特性
- 构成每一个 OS X 和 iOS 程序基础的苹果软件开发框架（称为 `Cocoa`）
- 用户界面设计的原则

听起来是不是要学很多东西？别担心。我们将一步步地讲解每个过程，这样你就不会感到迷茫。关键在于，要创建 OS X 程序和 iOS 应用，你需要学习多个主题，但创建 iOS 应用会带来额外的挑战。

例如，一个 iOS 应用除了要适应用户将 iPhone 或 iPad 向左、向右、上下颠倒或正向放置时的变化外，还需要响应单指、双指、滑动、摇晃和运动等触摸手势。

相比之下，Macintosh 程序只需要响应键盘和鼠标输入。这意味着 OS X 程序创建和理解起来要简单得多，这也意味着学习用 `Swift` 创建 OS X 程序远比为 iOS 应用学习 `Swift` 要容易。

最棒的是，其原理是完全相同的。你在创建 OS X 程序时学到的技能，与创建 iOS 应用所需的技能完全一样。区别在于，创建 OS X 程序比创建 iOS 应用要容易得多，不那么令人困惑，也远没有那么令人生畏。

如果你一开始就尝试创建 iOS 应用，就像在你还不知道如何在水中屏住呼吸时就试图游过英吉利海峡一样。

你不想不必要地让自己受挫。这就是为什么通过先学习 OS X 编程来掌握 iOS 应用编程的原理要容易得多。一旦你熟悉了 OS X 编程，你会发现将你的编程技能迁移到创建 iOS 应用上是非常简单的。通过学习用 `Swift` 创建 OS X 程序，你将学到用 `Swift` 创建 iOS 应用所需的一切知识，同时你还将懂得如何创建 OS X 程序，从而也能开拓日益增长的 Macintosh 市场。

### 追随有利可图的编程趋势

新计算机平台的出现，总是为程序员带来了一个有利可图的时期。在 80 年代早期，最热门的平台是 Apple II 计算机。如果你想通过编写程序赚钱，你就为 Apple II 电脑用户编写程序来销售，就像当时的 MBA 研究生 Dan Bricklin 所做的那样，他编写了第一个电子表格程序 `VisiCalc`。

随后，在 80 年代中期，随着 IBM PC 和 MS-DOS 的出现，下一个重大的计算平台转变发生了。许多人凭借 IBM PC 发家致富，包括比尔·盖茨和微软，后者从一家小型初创公司一跃成为全球最占主导地位的计算机公司。IBM PC 造就了成百上千的百万富翁，其中包括宝洁公司前营销总监 Scott Cook，他开发了广受欢迎的理财程序 `Quicken`。

当微软从 MS-DOS 转向 Windows，并为 IBM PC 带来了友好的图形用户界面时，它帮助迎来了下一个计算机平台。编写 Windows 程序再次成为程序员和非程序员通过编写和销售自己的 Windows 程序来发家致富的首要途径。微软利用向 Windows 的转型，发布了多款仅在 Windows 上运行的程序，如 Outlook、Access 和 Excel，这些程序已成为商业世界的固定组成部分。

如今，世界正转向由运行 OS X 和 iOS 的苹果产品组成的新计算平台。成千上万像你一样的人，正渴望开始编写程序，以利用 Macintosh 不断增长的市场份额，以及 iPhone 和 iPad 在智能手机和平板电脑领域的统治地位，还有 Apple Watch 在可穿戴计算机市场中的机会。

除了经验丰富的开发者之外，其他领域的业余爱好者、发烧友和专业人士也对编写自己特定领域的游戏、实用工具和商业软件感兴趣。

许多程序员从对编程一无所知，到通过创建 iPhone/iPad 应用或 Macintosh 程序每天赚取数千美元。随着 Macintosh、iPhone、iPad 以及现在的 Apple Watch 在全球范围内持续获得市场份额，越来越多的人将使用其中一种或多种产品，这为你增加了潜在的市场。

这一切都意味着，现在正是你开始学习如何为你的 Macintosh 编程的绝佳时机，因为你越早理解 Macintosh 编程的基础知识，就能越早开始创建你自己的 Macintosh 程序以及 iPhone/iPad/Apple Watch 应用。


### 本书的阅读预期

无论您是编程新手，还是来自其他编程环境、经验丰富的开发者，本书都将尽量减少技术术语，重点帮助您理解做什么以及为何这么做。

如果您想快速入门并学习使用 Swift 编程的基础知识，本书正适合您。如果您已经是经验丰富的 Windows 程序员，并希望开始为 Macintosh 编写程序，本书将特别有助于您快速掌握基础知识。

如果您以前从未编写过程序，或者您已经熟悉编程但不熟悉 Macintosh 编程，那么本书适合您。即使您对 Macintosh 编程经验丰富，您仍然会发现本书是一本方便的参考书，能帮助您实现特定目标，而无需翻阅多本书籍来寻找答案。

您不会学到创建超级复杂程序所需的全部知识，但您将学到足够入门的知识，能熟练使用 `Xcode`，并且能够更有信心、更深入地理解其他编程书籍。听起来不错吧？如果是这样，那么请翻到下一页，让我们开始吧。

**注意**

本书中的所有代码均使用 Swift 2 在 `Xcode 7` 中测试过。如果您使用的是 `Xcode 6`，本书中的部分功能将无法运行。

### 致谢

感谢 APress 的所有优秀同仁，感谢他们给我机会撰写关于 Swift 的书籍，这是我目前为 `Macintosh`、`iPhone`、`iPad` 和 `Apple Watch` 编程时最喜欢的新语言。

另外还要感谢 Dane Henderson 和 Elizabeth Lee（[`www.echoludo.com`](http://www.echoludo.com)），他们与我一起在 `KNSJ.org` 的广播节目“来自地下的笔记”（[`http://notesfromtheu.com`](http://notesfromtheu.com)）中分享电波。还要感谢 Chris Clobber、Diane Jean、Ikaika Patria 和 Robert Weems，让我在 `KNSJ` 的另一个名为“当面笑”（[`http://www.laughinyourfaceradio.com`](http://www.laughinyourfaceradio.com)）的广播节目中分享他们的友谊和疯狂，该节目将喜剧与政治行动和评论结合在一起。

特别要提到 Michael Montijo 和他不屈不挠的精神，在过去十五年里，他每月都会从凤凰城开车到洛杉矶与好莱坞高管会面。他的卡通节目创意“Mikey 的生活”和“Pachuko 男孩”总有一天会登上电视屏幕，因为他尽管面临重重障碍，也从未放弃。

还要感谢我的妻子 Cassandra 和儿子 Jordan，他们忍受了一个塞满电子设备、活人反而少见的家。最后感谢我的猫咪 Oscar 和 Mayer，它们总在一天中最不方便的时候踩过键盘、撞翻笔记本电脑，并啃咬电源线。



