# 学习 Objective-C：适用于 iOS 和 OS X

版权所有 © 2012 斯科特·克纳斯特、瓦卡尔·马利克、马克·达林普尔

本作品受版权保护。出版商保留所有权利，无论涉及全部还是部分材料，具体包括翻译权、重印权、插图再利用权、朗诵权、广播权、微缩胶片复制权或任何其他物理形式的复制权，以及信息存储与检索的传输权、电子改编权、计算机软件权，或现在已知或以后开发的任何类似或不同方法的权利。以下情况不在此法律保留范围内：与评论或学术分析相关的简短摘录，或为进入计算机系统并执行而专门提供的材料，仅供作品购买者独家使用。本出版物或其部分的复制仅允许在出版商所在地现行版权法的规定下进行，且使用许可必须始终从 Springer 获取。使用许可可通过版权清算中心的 `RightsLink` 获取。违反者将根据相应版权法被起诉。

ISBN-13（平装）：978-1-4302-4188-1

ISBN-13（电子版）：978-1-4302-4189-8

本书中可能出现商标名称、标志和图像。我们没有在每次出现商标名称、标志或图像时使用商标符号，而是仅以编辑方式使用这些名称、标志和图像，以维护商标所有者的利益，并无意侵犯商标。

本出版物中使用商品名称、商标、服务标志及类似术语，即使它们未被标识为商标，也不应被视为对其是否受所有权保护的看法。

尽管本书中的建议和信息在出版时被认为是真实和准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书所含内容不作任何明示或暗示的保证。

总裁兼出版商：保罗·曼宁

首席编辑：史蒂夫·安格林

开发编辑：格温南·斯皮林、马修·穆迪

技术审阅者：尼克·韦尼克

编辑委员会：史蒂夫·安格林、伊万·白金汉、加里·康奈尔、路易丝·科里根、摩根·埃特尔、乔纳森·根尼克、乔纳森·哈塞尔、罗伯特·哈钦森、米歇尔·洛曼、詹姆斯·马克汉姆、马修·穆迪、杰夫·奥尔森、杰弗里·佩珀、道格拉斯·庞迪克、本·雷诺-克拉克、多米尼克·谢克斯哈夫特、格温南·斯皮林、马特·韦德、汤姆·韦尔什

协调编辑：布伦特·杜比

文字编辑：希瑟·朗

排版：SPi Global

索引制作：SPi Global

插图：SPi Global

封面设计：安娜·伊什琴科

全球图书贸易分销由 Springer Science+Business Media New York 负责，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：orders-ny@springer-sbm.com，或访问 [www.springeronline.com](http://www.springeronline.com)。

有关翻译信息，请发送电子邮件至 rights@apress.com，或访问 [www.apress.com](http://www.apress.com)。

Apress 和 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子版版本和许可。如需更多信息，请参考我们的特殊批量销售–电子书许可网页：[www.apress.com/bulk-sales](http://www.apress.com/bulk-sales)。

作者在本文件中引用的任何源代码或其他补充材料可供读者在 [www.apress.com](http://www.apress.com) 获取。有关如何找到本书源代码的详细信息，请访问 [www.apress.com/source-code/](http://www.apress.com/source-code/)。

此书献给我的家人——当然！

–斯科特

献给我的父母 M. 萨利姆和卡尔苏姆·A·马利克，他们在我所有的努力中始终给予无条件的鼓励、支持和帮助。

–瓦卡尔

## 内容概览

前言

关于作者

关于技术审阅者

致谢

如何使用本书

![image](img/9781430241881_square_fmt.jpg) 第 1 章：你好

![image](img/9781430241881_square_fmt.jpg) 第 2 章：C 语言的扩展

![image](img/9781430241881_square_fmt.jpg) 第 3 章：面向对象编程简介

![image](img/9781430241881_square_fmt.jpg) 第 4 章：继承

![image](img/9781430241881_square_fmt.jpg) 第 5 章：组合

![image](img/9781430241881_square_fmt.jpg) 第 6 章：源文件组织

![image](img/9781430241881_square_fmt.jpg) 第 7 章：更多关于 Xcode 的内容

![image](img/9781430241881_square_fmt.jpg) 第 8 章：Foundation Kit 快速浏览

![image](img/9781430241881_square_fmt.jpg) 第 9 章：内存管理

![image](img/9781430241881_square_fmt.jpg) 第 10 章：对象初始化

![image](img/9781430241881_square_fmt.jpg) 第 11 章：属性

![image](img/9781430241881_square_fmt.jpg) 第 12 章：类别

![image](img/9781430241881_square_fmt.jpg) 第 13 章：协议

![image](img/9781430241881_square_fmt.jpg) 第 14 章：块与并发

![image](img/9781430241881_square_fmt.jpg) 第 15 章：UIKit 简介

![image](img/9781430241881_square_fmt.jpg) 第 16 章：Application Kit 简介

![image](img/9781430241881_square_fmt.jpg) 第 17 章：文件加载与保存

![image](img/9781430241881_square_fmt.jpg) 第 18 章：键值编码

![image](img/9781430241881_square_fmt.jpg) 第 19 章：使用静态分析器

![image](img/9781430241881_square_fmt.jpg) 第 20 章：NSPredicate

![image](img/9781430241881_square_fmt.jpg) 附录 A

索引

## 目录

前言

关于作者

关于技术审阅者

致谢

如何使用本书

![image](img/9781430241881_square_fmt.jpg) 第 1 章：你好

开始之前

昨日成就未来之处

接下来内容

准备工作

总结

![image](img/9781430241881_square_fmt.jpg) 第 2 章：C 语言的扩展

最简单的 Objective-C 程序

构建 Hello Objective-C

分解 Hello Objective-C

这个奇怪的 `#import` 东西



框架简介

NSLog() 与 @"strings"

你是布尔类型吗？

强大的 BOOL 实战

总结

![image](img/9781430241881_square_fmt.jpg) 第 3 章：面向对象编程入门

一切皆间接

变量与间接

通过文件名实现间接

在面向对象编程中使用间接

过程式编程

实现面向对象

术语小课堂

Objective-C 中的 OOP

@interface 部分

@implementation 部分

实例化对象

扩展 Shapes-Object

总结

![image](img/9781430241881_square_fmt.jpg) 第 4 章：继承

为什么要用继承？

继承语法

术语小课堂

继承如何运作

方法调度

实例变量

重写方法

调用 super！

总结

![image](img/9781430241881_square_fmt.jpg) 第 5 章：组合

什么是组合？

聊聊汽车

为 NSLog() 定制

访问器方法

设置引擎

设置轮胎

跟踪汽车的变化

扩展 CarParts

组合还是继承？

总结

![image](img/9781430241881_square_fmt.jpg) 第 6 章：源文件组织

分离接口与实现

在 Xcode 中创建新文件

拆分 Car 类

使用跨文件依赖

按需重新编译

让 Car 跑起来。

导入与继承

总结

![image](img/9781430241881_square_fmt.jpg) 第 7 章：更多关于 Xcode 的内容

一统天下的窗口

更改公司名称

编辑器使用技巧与诀窍

借助 Xcode 编写代码

缩进（美化打印）

自动补全功能

括号自动配对

批量编辑

在代码中导航。

聚焦你的精力

导航栏已打开

获取信息

调试

消灭小 Bug

Xcode 的调试器



## 目录

- 微妙的符号隐喻
- 开始调试！
- 先睹为快
- 速查表
- 小结

