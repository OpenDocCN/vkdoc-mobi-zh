# 使用 SpriteBuilder 进行 iOS 游戏开发

版权所有 © 2014，Steffen Itterheim

本作品受版权保护。出版者保留所有权利，无论是涉及材料的全部或部分，特别是翻译、重印、重用插图、朗诵、广播、微缩胶片复制或以任何其他物理方式复制，以及传输或信息存储与检索、电子改编、计算机软件，或现在已知或以后开发的类似或不同方法的权利。在法律保留中豁免的是与评论或学术分析相关的简短摘录，或专门为在计算机系统上输入和执行而提供的材料，仅供作品购买者独家使用。本出版物或其部分的复制仅在出版者所在地现行版权法的规定下允许，且使用许可必须始终从 Springer 获得。使用许可可通过 Copyright Clearance Center 的 RightsLink 获取。违反者将根据相应的版权法被起诉。

ISBN-13 (平装): 978-1-4842-0263-0  
ISBN-13 (电子版): 978-1-4842-0262-3

本书中可能出现的商标名称、标识和图片。我们并非在每个商标名称、标识或图片出现时都使用商标符号，而是仅以编辑方式使用这些名称、标识和图片，以利于商标所有者，并无意侵犯商标权。

本出版物中使用商品名称、商标、服务标志和类似术语，即使未明确标识，也不应被视为就这些术语是否受所有权保护发表意见。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版者均不对任何可能存在的错误或疏漏承担法律责任。出版者对本书所包含的材料不作任何明示或暗示的保证。

董事总经理：Welmoed Spahr  
首席编辑：Michelle Lowman  
开发编辑：Douglas Pundick  
技术审阅：Leland Long 和 Martin Walsh  
编辑委员会：Steve Anglin, Mark Beckner, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss  
协调编辑：Kevin Walter  
文字编辑：Roger LeBlanc  
排版：SPi Global  
索引编制：SPi Global  
插图：SPi Global  
封面设计：Anna Ishchenko

本书通过 Springer Science+Business Media New York（地址：233 Spring Street, 6th Floor, New York, NY 10013）在全球图书贸易中分销。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：orders-ny@springer-sbm.com，或访问 [www.springeronline.com](http://www.springeronline.com)。Apress Media, LLC 是加利福尼亚州的一家有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州的公司。

有关翻译信息，请发送电子邮件至 rights@apress.com，或访问 [www.apress.com](http://www.apress.com)。

Apress 和 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子书版本和许可。如需更多信息，请参考我们的特殊批量销售-电子书许可网页：[www.apress.com/bulk-sales](http://www.apress.com/bulk-sales)。

作者在本书中引用的任何源代码或其他补充材料，读者可在 [www.apress.com](http://www.apress.com) 获取。有关如何找到本书源代码的详细信息，请访问 [www.apress.com/source-code/](http://www.apress.com/source-code/)。

## 目录概览

关于作者  
关于技术审阅者  
致谢  
引言  

![image](img/00004.jpeg) 第一部分：介绍 SpriteBuilder 和 cocos2D-iphone 第三版  
![image](img/00004.jpeg) 第 1 章：引言  
![image](img/00004.jpeg) 第 2 章：打好基础  
![image](img/00004.jpeg) 第 3 章：控制与滚动  
![image](img/00004.jpeg) 第 4 章：物理与碰撞  
![image](img/00004.jpeg) 第 5 章：时间线与触发器  
![image](img/00004.jpeg) 第 6 章：菜单与弹出框  
![image](img/00004.jpeg) 第二部分：使用 SpriteBuilder 着手实践  
![image](img/00004.jpeg) 第 7 章：主场景与游戏状态  
![image](img/00004.jpeg) 第 8 章：选择与解锁关卡  
![image](img/00004.jpeg) 第 9 章：物理关节  
![image](img/00004.jpeg) 第 10 章：软体物理  
![image](img/00004.jpeg) 第三部分：现在你是 SpriteBuilder 专家了！  
![image](img/00004.jpeg) 第 11 章：音频与标签  
![image](img/00004.jpeg) 第 12 章：视觉效果与动画  
![image](img/00004.jpeg) 第 13 章：移植到 Android  
![image](img/00004.jpeg) 第 14 章：调试与最佳实践  

索引

#### 目录

关于作者  
关于技术审阅者  
致谢  
引言  

![image](img/00004.jpeg) 第一部分：介绍 SpriteBuilder 和 cocos2D-iphone 第三版  
![image](img/00004.jpeg) 第 1 章：引言  

本书内容是什么？  
本书适合谁？  
系统要求  
关于 SpriteBuilder  
为什么要使用 SpriteBuilder？  
关于 Cocos2D  
关于 Apportable  
关于程序版本  
关于 Xcode 版本  
关于示例项目  
示例项目对应章节  
关于部署目标  
目录  

第 1 章——引言  
第 2 章——打好基础  
第 3 章——控制与滚动  
第 4 章——物理与碰撞  
第 5 章——时间线与触发器  
第 6 章——菜单与弹出框  
第 7 章——主场景与游戏状态  
第 8 章——选择与解锁关卡  
第 9 章——物理关节  
第 10 章——软体物理  
第 11 章——音频与标签  
第 12 章——视觉效果与动画  
第 13 章——移植到 Android  
第 14 章——调试与最佳实践  
开始之前  

![image](img/00004.jpeg) 第 2 章：打好基础  

创建 SpriteBuilder 项目  
SpriteBuilder 项目的 Xcode 项目  
第一个场景，第一段代码  
创建 GameScene  
检查节点库  
创建按钮



