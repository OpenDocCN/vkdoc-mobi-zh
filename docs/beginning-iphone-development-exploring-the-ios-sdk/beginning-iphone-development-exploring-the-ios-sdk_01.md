# 初探 iPhone 开发

探索 iOS SDK

第七版

![image](img/FM-00.jpg)

大卫·马克  
杰克·纳丁  
金·托普利  
弗雷德里克·奥尔森  
杰夫·拉马什

![image](img/FM-01.jpg)

**初探 iPhone 开发**

版权 © 2014 大卫·马克、杰克·纳丁、金·托普利、弗雷德里克·奥尔森和杰夫·拉马什

本作品受版权保护。出版商保留所有权利，涉及全部或部分材料，特别是翻译、重印、重用插图、朗诵、广播、微缩胶片复制或以任何其他物理方式复制、传输或信息存储与检索、电子改编、计算机软件，或目前已知或此后开发的任何相似或不同方法的权利。此法律保留的例外情况包括与评论或学术分析相关的简短摘录，或专门为输入到计算机系统并执行而提供的材料，仅供购买者个人使用。未经出版商许可，不得以任何形式复制本出版物或其部分内容，许可必须始终从 Springer 获取。可通过 RightsLink 在版权清算中心获得使用许可。违反者将根据相关版权法被起诉。

ISBN-13（平装本）：978-1-4842-0200-5  
ISBN-13（电子版）：978-1-4842-0199-2

本书中可能出现商标名称、徽标和图像。我们不每次使用商标名称、徽标或图像时都使用商标符号，仅以编辑方式并为了商标所有者的利益使用这些名称、徽标和图像，无意侵犯商标。

本出版物中使用的商品名称、商标、服务标记和类似术语，即使未被标识为如此，也不应被视为对它们是否受所有权保护的看法。

尽管本书中的建议和信息在出版时被认为是真实和准确的，但作者、编辑或出版商均不对任何可能出现的错误或遗漏承担法律责任。出版商对书中所含内容不作任何明示或暗示的担保。

总经理：Welmoed Spahr  
首席编辑：Steve Anglin  
开发编辑：Matthew Moodie  
技术审阅人：Felipe Laso Marsetti  
编辑委员会：Steve Anglin、Gary Cornell、Louise Corrigan、Jonathan Gennick、Robert Hutchinson、Michelle Lowman、James Markham、Matthew Moodie、Jeff Olson、Jeffrey Pepper、Douglas Pundick、Ben Renow-Clarke、Gwenan Spearing、Matt Wade、Steve Weiss  
协调编辑：Anamika Panchoo  
文字编辑：Kimberly Burton  
排版：SPi Global  
索引编制：SPi Global  
美工：SPi Global  
封面设计：Anna Ishchenko

全球图书贸易由 Springer Science+Business Media New York 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

如需翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 和 friends of ED 的书籍可批量购买，用于学术、企业或促销用途。大多数图书还提供电子书版本和许可证。有关更多信息，请参考我们的特别批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在本文中引用的任何源代码或其他补充材料均可在 `www.apress.com/9781484202005` 上获取。有关如何找到本书源代码的详细信息，请访问 `www.apress.com/source-code/`。

本书谨献给史蒂夫·乔布斯。我们继续受他的精神和远见所激励。

——大卫·马克和杰克·纳丁

献给我的母亲和父亲，他们为我买了第一台电脑。

——弗雷德里克·奥尔森

献给我的父母和我的姐姐杰基，她之前从未有过一本专门献给她的书。

——金·托普利

## 内容一览

关于作者

关于技术审阅人

![image](img/sq1.jpg) 第 1 章：欢迎来到丛林

![image](img/sq1.jpg) 第 2 章：取悦提基众神

![image](img/sq1.jpg) 第 3 章：处理基本交互

![image](img/sq1.jpg) 第 4 章：更多用户界面乐趣

![image](img/sq1.jpg) 第 5 章：旋转与自适应布局

![image](img/sq1.jpg) 第 6 章：多视图应用程序

![image](img/sq1.jpg) 第 7 章：标签栏与选择器

![image](img/sq1.jpg) 第 8 章：表格视图入门

![image](img/sq1.jpg) 第 9 章：导航控制器与表格视图

![image](img/sq1.jpg) 第 10 章：集合视图

![image](img/sq1.jpg) 第 11 章：使用分割视图和弹出框

![image](img/sq1.jpg) 第 12 章：应用程序设置与用户默认值

![image](img/sq1.jpg) 第 13 章：基本数据持久化

![image](img/sq1.jpg) 第 14 章：文档与 iCloud

![image](img/sq1.jpg) 第 15 章：Grand Central Dispatch、后台处理与你

![image](img/sq1.jpg) 第 16 章：使用 Core Graphics 绘图

![image](img/sq1.jpg) 第 17 章：Sprite Kit 入门

![image](img/sq1.jpg) 第 18 章：点击、触摸与手势

![image](img/sq1.jpg) 第 19 章：我在哪里？使用 Core Location 和 Map Kit 定位

![image](img/sq1.jpg) 第 20 章：哇！陀螺仪与加速度计！

![image](img/sq1.jpg) 第 21 章：相机与照片库

![image](img/sq1.jpg) 第 22 章：应用程序本地化

索引

## 目录

关于作者

关于技术审阅人

![image](img/sq1.jpg) 第 1 章：欢迎来到丛林

### 本书内容

### 你需要什么

## 开发者选项

### 你需要了解什么

### iOS 开发有何不同？

#### 只有一个活跃应用程序

#### 只有一个窗口

#### 访问受限

#### 响应时间有限

#### 屏幕尺寸有限

#### 系统资源有限

#### 没有垃圾回收，但是……

#### 一些新特性

#### 不同的方法

### 本书内容

### 本次更新有哪些新内容？



准备好了吗？

![image](img/sq1.jpg) 第 2 章：安抚 Tiki 之神

在 Xcode 中设置项目

Xcode 项目窗口

深入了解我们的项目

认识 Xcode 的 Interface Builder

文件格式

故事板

库

向视图添加标签

更改属性

iPhone 的精修：收尾工作

启动屏幕

本章小结

![image](img/sq1.jpg) 第 3 章：处理基本交互

模型-视图-控制器范式

创建我们的项目

查看视图控制器

理解 Outlets 和 Actions

清理视图控制器

设计用户界面

动手尝试

预览布局

添加一些样式

查看应用程序委托

本章小结

![image](img/sq1.jpg) 第 4 章：更多用户界面乐趣

满屏的控件

活动、静态和被动控件

创建应用程序

实现图像视图和文本字段

添加图像视图

调整图像视图大小

设置视图属性

添加文本字段

添加约束

创建并连接 Outlets

关闭键盘

点击“完成”时关闭键盘

触摸背景以关闭键盘

添加滑块和标签

添加更多约束

创建并连接 Actions 和 Outlets

实现 Action 方法

实现开关、按钮和分段控件

添加两个带标签的开关

连接并创建 Outlets 和 Actions

实现开关的 Actions

美化按钮

可拉伸图像

控件状态

连接并创建按钮的 Outlets 和 Actions

实现分段控件的 Action

实现操作表和警告框

显示操作表

显示警告框

冲过终点线

![image](img/sq1.jpg) 第 5 章：旋转与自适应布局

旋转的机制

点、像素与 Retina 显示屏

处理旋转

选择视图方向



应用层面支持的朝向

按控制器控制旋转支持

使用约束设计界面

覆盖默认约束

全宽标签

创建自适应布局

重构应用程序

尺寸分类

尺寸分类与故事板

创建 iPhone 横屏布局

添加 iPad 布局

旋转至此结束

![image](img/sq1.jpg) 第 6 章：多视图应用程序

多视图应用的常见类型

多视图应用的架构

根控制器

内容视图剖析

构建视图切换器

重命名视图控制器

添加内容视图控制器

修改 `SwitchingViewController.m`

使用工具栏构建视图

将工具栏按钮链接到视图控制器

编写根视图控制器

实现内容视图

动画过渡

切换关闭

![image](img/sq1.jpg) 第 7 章：标签栏与选择器

选择器应用程序

委托与数据源

创建选择器应用程序

创建视图控制器

创建标签栏控制器

首次测试运行

实现日期选择器

实现单组件选择器

构建视图

将控制器实现为数据源和委托

实现多组件选择器

声明插座和动作

构建视图

实现控制器

实现依赖组件

使用自定义选择器创建简单游戏

编写控制器头文件

构建视图

实现控制器

最终细节

最终旋转

![image](img/sq1.jpg) 第 8 章：表格视图简介

表格视图基础

表格视图与表格视图单元格

分组表与普通表

实现一个简单表格

设计视图

编写控制器

添加图像

使用表格视图单元格样式

设置缩进级别

处理行选择

更改字体大小与行高

自定义表格视图单元格

向表格视图单元格添加子视图



