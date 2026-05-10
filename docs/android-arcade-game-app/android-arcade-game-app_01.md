# Android 街机游戏应用

![image](img/FM-sq.jpg)

J. F. DiMarzio

![image](img/FM-00.jpg)

**Android 街机游戏应用**

版权所有 © 2012 J. F. DiMarzio

本作品受版权保护。出版商保留所有权利，无论是整体还是部分材料，具体包括翻译、重印、插图再利用、朗诵、广播、微缩胶卷复制或任何其他物理方式，以及传输或信息存储与检索、电子改编、计算机软件的权利，或使用目前已知或未来开发的相关或不同方法。此法律保留的例外情况包括与评论或学术分析相关的简短摘录，或专门为输入并执行于计算机系统而提供的材料，仅供作品购买者独家使用。本出版物或其部分的复制仅在出版商所在地现行版权法的规定下允许，且使用许可必须始终从 Springer 获取。使用许可可通过 Copyright Clearance Center 的 RightsLink 获得。违规行为将根据相应版权法受到起诉。

ISBN-13（平装本）：978-1-4302-4545-2

ISBN-13（电子版）：978-1-4302-4546-9

本书中可能出现商标名称、标识和图像。我们未对每个商标名称、标识或图像使用商标符号，仅以编辑方式使用这些名称、标识和图像，以利于商标所有者，且无意侵犯商标权。

Android 机器人（01 / Android Robot）的图像复制自 Google 创建并共享的作品，并按照 Creative Commons 3.0 署名许可中描述的条款使用。Android 及所有基于 Android 和 Google 的标志是美国及其他国家/地区 Google, Inc. 的商标或注册商标。Apress Media, L.L.C. 与 Google, Inc. 无关联，本书编写未获得 Google, Inc. 的认可。

本出版物中使用商品名称、商标、服务标志及类似术语，即使未标明为商标，也不应被视为对它们是否受专有权利保护的意见表达。

尽管本书中的建议和信息在出版之日被认为是真实准确的，但作者、编辑或出版商对可能存在的任何错误或遗漏不承担任何法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

总裁兼出版人：Paul Manning

首席编辑：Steve Anglin

开发编辑：Tom Welsh

技术审稿人：Tony Hillerson

编辑委员会：Steve Anglin, Ewan Buckingham, Gary Cornell, Louise Corrigan, Morgan Ertel, Jonathan Gennick, Jonathan Hassell, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Tom Welsh

协调编辑：Katie Sullivan

文字编辑：Kimberly Burton

排版：SPi Global

索引制作：SPi Global

美工：SPi Global

封面设计：Anna Ishchenko

全球图书贸易发行由 Springer Science+Business Media New York 负责，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。

关于翻译事宜，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 及 friends of ED 书籍可批量购买用于学术、企业或促销用途。大多数书名也提供电子书版本和许可证。更多信息，请参考我们的特殊批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在此文本中引用的任何源代码或其他补充材料可在 `www.apress.com` 上获取。有关如何找到本书源代码的详细信息，请访问 `www.apress.com/source-code/`。

感谢 Suzannah、Christian、Sophia 和 Giovanni 始终如一的陪伴。

—J. F. DiMarzio

## 目录速览

关于作者

关于技术审稿人

关于游戏图形设计师

致谢

![image](img/sq.jpg)第 1 章：Android 游戏开发入门

![image](img/sq.jpg)第 2 章：什么是街机游戏？

![image](img/sq.jpg)第 3 章：创建菜单

![image](img/sq.jpg)第 4 章：绘制背景

![image](img/sq.jpg)第 5 章：创建玩家角色与障碍物

![image](img/sq.jpg)第 6 章：碰撞检测

![image](img/sq.jpg)第 7 章：计分

![image](img/sq.jpg)第 8 章：添加新关卡

索引

## 目录

关于作者

关于技术审稿人

关于游戏图形设计师

致谢

![images](img/sq.jpg) 第 1 章：Android 游戏开发入门

应具备的知识

你将学到什么

游戏简史

Android 的引入

Android 游戏编程

小结

![images](img/sq.jpg) 第 2 章：什么是街机游戏？

街机风格游戏起源何处？

你的游戏：监狱逃亡

本书内容

第 3 章：创建菜单

第 4 章：绘制背景

第 5 章：创建玩家挡板和砖块

第 6 章：碰撞检测与游戏内物理

第 7 章：计分

第 8 章：添加更多关卡

小结

![images](img/sq.jpg) 第 3 章：创建菜单

开始之前

创建启动画面和主菜单

PrisonbreakActivity

PBMainMenu

小结

![images](img/sq.jpg) 第 4 章：绘制背景

开始游戏

创建 SurfaceView 和渲染器

创建背景类

绘制背景

小结

![images](img/sq.jpg) 第 5 章：创建玩家角色与障碍物

开始之前

创建玩家挡板类

创建砖块类

创建 PBBall 类

PBRow 和 PBWall



