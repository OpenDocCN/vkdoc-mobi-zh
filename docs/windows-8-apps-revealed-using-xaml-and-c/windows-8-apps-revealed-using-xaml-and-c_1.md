# Windows 8 应用揭秘

使用 XAML 和 C#

Adam Freeman

**Apress 出版社**

**Windows 8 应用揭秘：使用 XAML 和 C#**

版权所有 © 2012 由 Adam Freeman 持有

本作品受版权保护。Apress 出版社保留所有权利，无论是全部还是部分材料，具体包括翻译权、转载权、插图复用权、朗诵权、广播权、以缩微胶片或任何其他物理方式复制权、传输或信息存储与检索权、电子改编权、计算机软件权，或使用目前已知或未来开发的其他类似或不类似的方法。法律保留的例外情况包括与评论或学术分析相关的简短摘录，或专门为在计算机系统上输入和执行而提供的材料，仅供作品购买者独享。未经出版社所在地现行版权法许可，不得复制本出版物或其部分内容，且使用许可必须始终从 Springer 获取。使用许可可通过版权清算中心的 `RightsLink` 获取。违反者将依据相应版权法受到起诉。

ISBN-13（平装）：978-1-4302-5034-0

ISBN-13（电子版）：978-1-4302-5035-7

本书中可能出现的商标名称、标识和图片。我们不随意使用商标符号，而是仅以编辑方式使用这些名称、标识和图片，以惠及商标所有者，且无意侵犯商标权。

本出版物中使用商品名、商标、服务标志和类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的看法表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本材料内容不作任何明示或默示的保证。

Apress 总裁兼出版人：Paul Manning

首席编辑：Ewan Buckingham

技术审阅：Fabio Claudio Ferracchiati

Apress 编辑委员会：Steve Anglin, Ewan Buckingham, Gary Cornell, Louise Corrigan, Morgan Ertel, Jonathan Gennick, Jonathan Hassell, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Tom Welsh

协调编辑：Mark Powers

文字编辑：Kim Wimpsett

排版：SPi Global

索引编制：SPi Global

美术设计：SPi Global

封面设计：Anna Ishchenko

全球图书贸易分销由 Springer Science+Business Media New York 负责，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。

如需翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 及 friends of ED 图书可批量购买用于学术、企业或推广用途。大多数图书也提供电子版和许可证。更多信息，请参考我们的特殊批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在本文中引用的任何源代码或其他补充材料，读者可在 `www.apress.com/9781430250340` 获取。关于如何找到图书源代码的详细信息，请访问 `www.apress.com/source-code`。

*谨以此书献给我亲爱的妻子 Jacqui Griffyth。*

—Adam Freeman

## 内容概览

- 关于作者
- 关于技术审阅者



## 目录

关于作者  
关于技术评审  
致谢

![images](img/sq1.jpg) 第 1 章：入门指南  
关于本书  
阅读本书前需要了解什么？  
本书需要哪些软件？  
本书的结构是怎样的？  
关于示例 Windows 应用的更多信息  
本书代码量很大吗？  
启动与运行  
创建项目  
探索`App.xaml`文件  
探索`MainPage.xaml`文件  
探索`StandardStyles.xaml`文件  
探索`Package.appxmanifest`文件  
极简 XAML 概述  
使用 Visual Studio 设计图面  
在 XAML 中配置控件  
在代码中配置控件  
运行和调试 Windows 应用  
在模拟器中运行 Windows 应用  
总结

![images](img/sq1.jpg) 第 2 章：数据、绑定与页面  
添加视图模型  
添加主布局  
编写代码  
添加资源字典  
编写 XAML  
运行应用  
将其他页面插入布局  
动态插入页面至布局  
在页面间切换  
实现嵌入式页面  
总结

![images](img/sq1.jpg) 第 3 章：应用栏、浮出控件与导航  
添加应用栏  
声明应用栏  
适配预定义的应用栏按钮  
创建自定义应用栏按钮样式  
实现应用栏按钮操作  
创建浮出控件  
创建用户控件  
编写用户控件代码  
将浮出控件添加到应用  
创建更复杂的浮出控件  
在 Windows 应用内导航  
创建包装器  
创建其他视图  
测试导航  
总结

![images](img/sq1.jpg) 第 4 章：视图与磁贴  
支持视图  
在代码中响应视图变化



## 目录

响应 XAML 中的视图变更

退出对齐视图

使用磁贴和锁屏提醒

改进静态磁贴

创建动态磁贴

更新宽磁贴

应用锁屏提醒

总结

![images](img/sq1.jpg) 第 5 章：应用生命周期与合约

处理应用生命周期

修正 Visual Studio 事件代码

模拟生命周期事件

测试生命周期事件

添加后台活动

扩展视图模型

显示位置数据

声明应用功能

控制后台任务

实现合约

声明对合约的支持

实现搜索功能

响应搜索生命周期事件

测试搜索合约

总结

索引

## 关于作者

![image](img/9781430250340_unFig-01.jpg)

**亚当·弗里曼**是一位经验丰富的 IT 专业人士，曾在多家公司担任高级职务，最近一次是在一家全球银行担任首席技术官和首席运营官。现已退休，他致力于写作和跑步。

**他即将出版的其他著作包括：**

- Pro Windows 8 Development in HTML5 and JavaScript
- Pro ASP.NET MVC 4

**他的其他著作包括：**

- *The Definitive Guide to HTML5* — *Pro ASP.NET 4 in C# 2010 4th Edition*
- *Applied ASP.NET 4 in Context* — *Pro ASP.NET 4 in VB 2010 3rd Edition*
- *Pro ASP.NET MVC 3 Framework 3rd Edition* — *Pro LINQ*
- *Pro jQuery* — *Pro .NET 4 Parallel Programming in C#*
- *Introducing Visual C# 2010* — *Visual C# 2010 Recipes*

## 关于技术审校

**法比奥·克劳迪奥·费拉基亚蒂** 是一位资深顾问和高级分析师/开发者，专注于微软技术。他就职于 Brain Force（`www.brainforce.com`）的意大利分公司（`www.brainforce.it`）。他是微软认证解决方案开发专家（.NET）、微软认证应用程序开发专家（.NET）、微软认证专家，同时也是多产的技术作者和审校者。在过去十年中，他为意大利及国际杂志撰稿，并合著了十多本关于各种计算机主题的书籍。

## 致谢

我要感谢 Apress 出版社的每一位成员，感谢他们为本书的出版付出的辛勤努力。特别感谢 Mark Powers 让我保持正轨，以及 Ewan Buckingham 对本书的委托和编辑。我还要感谢我的技术审校法比奥，他的努力使本书比原本好了很多。

### 第 1 章：入门

![image](img/frontdot.jpg)

Windows 应用商店应用是 Microsoft Windows 8 的重要补充，为桌面、平板电脑和智能手机提供了一个统一且一致的编程和交互模型基石。应用的用户体验与以往各代 Windows 应用截然不同：Windows 应用商店应用采用全屏显示，并推崇一种简洁、直接、无干扰的风格。

Windows 应用商店应用完全不同于以往版本的 Windows。它拥有全新的 API、新的交互控件，以及管理应用生命周期的截然不同的方法。

Windows 应用商店应用可以使用多种语言进行开发，包括 JavaScript、Visual Basic、C++，以及本书主题 —— C#。Windows 8 在现有基础之上，让开发者能够利用他们已有的 C# 和 XAML 经验来构建丰富的应用，并集成到更广泛的 Windows 平台中。本书将为您开启 Windows 应用商店应用世界的关键入门之旅；阅读本书后，您将理解如何使用定义核心 Windows 应用商店应用体验的控件和功能。

![image](img/sq.jpg) **注意** —— 微软使用了 *Windows Store App* 这个术语，我觉得它有些别扭，因此无法在整本书中使用它。取而代之，我将使用 *Windows 应用*，并且经常简称为 *应用*。我会让您自行在脑海中根据需要插入官方的微软名称。

## 关于本书

本书面向希望抢先一步为 Windows 8 创建应用的资深 C# 开发者。我将解释您需要快速上手并提升应用开发技术和知识所需的概念和技巧。

## 阅读本书前需要了解什么？

您需要对 C# 有良好的理解，并且理想情况下，还需要了解 XAML。如果您曾从事 WPF 或 Silverlight 项目，那么您将拥有足够的 XAML 知识来构建 Windows 应用。如果您之前没有接触过 XAML，也不必担心；您可以边学边用，并且我将在本章稍后部分提供一个非常简短的概述帮助您入门。在用到主要 XAML 概念时，我会进行说明。

## 阅读本书需要什么软件？

开发 Windows 应用需要两样东西：一台运行 Windows 8 的 PC 和 Visual Studio 2012。如果您认真对待 Windows 应用开发，则需要购买一份 Windows 8 副本；但如果您只是出于好奇，可以从微软获得 90 天试用版，地址为 `http://msdn.microsoft.com/en-us/evalcenter`。点击“Windows 与平台开发”部分中的“发布”链接，即可找到所需内容。

您需要创建一个微软账户才能获取评估版，但无论如何，您都需要一个账户才能注册为开发者。微软账户是免费创建的，如果您还没有账户，请按照相关说明进行操作。

![image](img/sq.jpg) **提示** —— 您可以在虚拟机中运行 Windows 8，但为了获得最佳效果，我建议将操作系统直接安装到 PC 上。

Visual Studio 2012 是微软的开发环境。好消息是，微软免费提供一款基础版本的 Visual Studio，我在本书中使用的正是这个版本。它有一个朗朗上口的名字：Visual Studio 2012 Express for Windows 8，您可以从 `www.microsoft.com/visualstudio/11/en-us/products/express` 下载安装程序。提供了多种版本，每种版本可用于开发特定类型的应用程序。对于应用开发，您需要 Visual Studio Express 2012 for Windows 8。不同版本的名称非常相似，容易混淆，请务必下载正确的版本。

免费版本不具备某些付费版本 Visual Studio 中包含的所有测试和集成功能，但您不需要这些功能来创建 Windows 应用。在其他方面，免费版本的 Visual Studio 功能齐全，能满足您的所有需求。

![image](img/sq.jpg) **提示** —— 如果您拥有付费版本的 Visual Studio 2012，不用担心。您不会用到该版本的所有功能，但本书中的所有说明和示例都能毫无问题地运行，也无需修改。



### 安装 Visual Studio

像安装其他任何应用程序一样安装 `Visual Studio`。虽然该软件是免费的，但您需要激活它，这需要用到您之前创建的 `Microsoft` 账户。完成激活流程后，您会得到一个可用于激活 `Visual Studio` 的代码。您还需要一个开发者许可证，这也是免费的。首次启动 `Visual Studio 2012` 时，系统会提示您获取许可证；这个过程只需一秒钟，并且同样需要您之前创建的 `Microsoft` 账户。

![image](img/sq.jpg) **注意**  尽管 `Visual Studio` 和 `Microsoft` 账户是免费的，但如果您想将应用发布到 `Windows Store`，则需要付费。`Microsoft` 目前对个人用户每年收费 49 美元，对公司用户每年收费 99 美元，但如果您订阅了 `MSDN` 等 `Microsoft` 软件包，或许可以免费发布。

## 本书结构是怎样的？

我专注于构建 `Windows` 应用的关键技术和特性。您已经知道如何编写 `C#`，我不会浪费时间教您已经掌握的内容。本书旨在将您的 `C#` 和 `XAML` 开发经验迁移到 `Windows` 应用世界，这意味着要专注于那些使 `Windows` 应用与众不同且特别的部分。

我采用了一种较为宽松的方式来混合各个主题。除了每章的主题之外，您还会找到一些必要的背景信息，用以解释某些功能为何重要以及为何应该实现它们。阅读完本书后，您将了解如何构建一个能正确集成到 `Windows 8` 中，并且能呈现出与使用其他语言（如 `C++` 或 `JavaScript`）编写的应用相一致的用户体验的 `Windows` 应用。

这是一本让您开始为 `Windows 8` 开发应用的入门书。它并非全面的教程；因此，我将重点放在了构成应用主要构建模块的那些主题上。还有很多信息我无法塞进这本薄薄的书里。如果您想更全面地了解 `Windows` 应用开发，`Apress` 已经出版了 Jesse Liberty 的 *Pro Windows 8 Development with XAML and C#*。此外，如果您想使用更多面向 Web 的技术来构建应用，`Apress` 将会出版我的 *Pro Windows 8 Development with HTML5* 和 `JavaScript` 两本书。

以下各节总结了本书的章节内容。

### 第 1 章：入门

也就是本章。除了介绍本书之外，我还向您展示如何为本书通篇使用的示例应用创建 `Visual Studio` 项目。我简要概述了 `XAML`，带您鸟瞰了应用开发项目中的重要文件，向您展示了如何在 `Visual Studio` 模拟器中运行应用，并解释了如何使用调试器。

