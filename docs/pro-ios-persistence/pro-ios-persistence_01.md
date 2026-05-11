# iOS 持久化高级编程

## 使用 Core Data

![FM-00.jpg](img/FM-00.jpg)

Michael Privat  
Robert Warner

![FM-01.jpg](img/FM-01.jpg)

**iOS 持久化高级编程**

版权所有 © 2014 Michael Privat 与 Robert Warner

本作品受版权保护。出版者保留所有权利，无论是全部还是部分内容，尤其是翻译权、重印权、插图再利用权、朗诵权、广播权、缩微胶卷复制权或以任何其他物理形式的复制权，以及信息存储与检索的传输权、电子改编权、计算机软件改编权，或使用目前已知或未来开发的相似或不同方法学。法律保留的例外情况包括：与评论或学术分析相关的简短摘录，或为专门供作品购买者独家使用而输入计算机系统并执行的特定材料。未经出版者许可，不得以任何形式复制本出版物或其部分内容，除非符合出版者所在地现行版权法的规定，且使用许可必须始终从 Springer 获取。使用许可可通过 Copyright Clearance Center 的 RightsLink 获得。违反者将根据相应版权法被追究法律责任。

ISBN-13（平装版）：978-1-4302-6028-8  
ISBN-13（电子版）：978-1-4302-6029-5

本书中可能出现的商标名、标识和图片。我们不在此类商标名、标识或图片每次出现时均使用商标符号，而是仅以编辑方式使用这些名称、标识和图片，以维护商标所有者的利益，且无意侵犯其商标权。

本出版物中使用的商品名、商标、服务标记及类似术语，即使未被明确标识，也不应被视为对其是否受专有权利保护的判断依据。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版者均不对可能存在的错误或疏漏承担任何法律责任。出版者不对本书所含内容作任何明示或暗示的保证。

董事总经理：Welmoed Spahr  
首席编辑：Steve Anglin  
开发编辑：Matthew Moodie  
技术审校：Ethan Mateja 和 Ron Natalie  
编辑委员会：Steve Anglin, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss  
协调编辑：Jill Balzano  
文字编辑：Lori Jacobs  
排版：SPi Global  
索引编制：SPi Global  
美术设计：SPi Global  
封面设计：Anna Ishchenko

本书通过全球图书贸易由 Springer Science+Business Media New York 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚有限责任公司，其唯一成员（所有者）为 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

如需了解翻译事宜，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 及 friends of ED 的图书可批量购买用于学术、企业或促销用途。大部分图书也提供电子书版本和许可证。更多信息请参阅我们的特殊批量销售 – 电子书授权网页：`www.apress.com/bulk-sales`。

作者在文中引用的任何源代码或其他补充材料，读者可在 `www.apress.com/9781430260288` 获取。有关如何查找图书源代码的详细信息，请访问 `www.apress.com/source-code/`。

谨以此书献给我挚爱的妻子 Kelly，以及我的孩子 Matthieu 和 Chloé。



—迈克尔·普里瓦特

谨以此书献给我美丽的妻子雪莉，以及我们出色的孩子们：泰森、雅各布、马洛里、卡米和莱拉。

—罗布·华纳

## 目录概览

作者简介

技术审阅者简介

致谢

引言

![image](img/sq1.jpg) 第 1 章：初探 Core Data

![image](img/sq1.jpg) 第 2 章：构建数据模型

![image](img/sq1.jpg) 第 3 章：高级查询

![image](img/sq1.jpg) 第 4 章：关注数据质量

![image](img/sq1.jpg) 第 5 章：与用户界面集成

![image](img/sq1.jpg) 第 6 章：数据版本控制与迁移

![image](img/sq1.jpg) 第 7 章：数据转换与加密

![image](img/sq1.jpg) 第 8 章：与服务交互：iCloud 和 Dropbox

![image](img/sq1.jpg) 第 9 章：优化性能、内存使用和多线程

索引

## 目录

作者简介

技术审阅者简介

致谢

引言

![image](img/sq1.jpg) 第 1 章：初探 Core Data

什么是 Core Data？

Core Data 组件

创建新的 Core Data 项目

创建项目

初识 Core Data 组件

初始化 Core Data 组件

创建托管对象模型

添加一些对象

查看数据

将 Core Data 添加到现有项目

创建一个不使用 Core Data 的应用程序

添加 Core Data 框架

添加托管对象模型

添加并初始化 Core Data 栈

创建对象模型

向 PersistenceApp 添加对象

总结

![image](img/sq1.jpg) 第 2 章：构建数据模型

设计数据库

关系型数据库规范化

构建简单模型

实体

属性

键值编码

生成类

关系

存储与检索数据

插入新的托管对象

检索托管对象

谓词

获取属性

通知

创建时的通知

获取时的通知

变更时的通知

注册观察者

接收通知

结论

![image](img/sq1.jpg) 第 3 章：高级查询



构建词表

创建应用程序

构建数据模型

创建用户界面

加载并分析词表

获取计数

显示统计信息

查询关系

理解谓词和表达式

查看你的 SQL 查询

创建单值表达式

创建集合表达式

使用不同的谓词类型比较表达式

使用不同的比较修饰符

使用不同的选项

总结：使用复合谓词

聚合

使用子查询

排序

本章小结

![图片](img/sq1.jpg) 第 4 章：关注数据质量

种子数据

使用种子存储

在后续版本中更新种子数据

撤销与重做

撤销组

限制撤销栈

禁用撤销追踪

向 BookStore 添加撤销功能

试验撤销组

处理错误

处理 Core Data 操作错误

处理验证错误

在 BookStore 中处理验证错误

实现验证错误处理例程

本章小结

![图片](img/sq1.jpg) 第 5 章：与用户界面集成

使用 NSFetchedResultController 显示表格数据

创建获取结果控制器

创建获取结果控制器代理

存储图片

向 CoreDump 添加截图

验证外部存储

在表格中搜索获取结果

添加搜索控制器

检索搜索结果

显示搜索结果

结论

![图片](img/sq1.jpg) 第 6 章：数据版本控制与迁移

版本控制

轻量级迁移

迁移简单变更

重命名实体和属性

使用映射模型

理解实体映射

理解属性映射

创建需要映射模型的新模型版本

创建映射模型

迁移数据

运行你的迁移

自定义迁移

确保需要迁移

设置迁移管理器



运行迁移

总结

![image](img/sq1.jpg) 第 7 章：转换与加密数据

使用 Transformable 类型

创建项目并添加 Core Data

创建 Taps 实体

创建视图并显示 Taps 数据

等等，我的颜色是怎么转换的？

加密数据

创建项目并建立 Core Data 模型

执行加密与解密操作

设置用于编辑笔记的详情视图

提示输入并验证密码

Transformable 的替代方案：数据保护

总结

![image](img/sq1.jpg) 第 8 章：与云端服务集成：iCloud 和 Dropbox

集成 iCloud

使用 iCloud 的 Ubiquity 容器

同步内容

集成 Dropbox Datastore API

设置 API 访问权限

创建一个与 Dropbox 配合使用的空白应用

配置回调 URL

使用 Datastore API 链接到 Dropbox

使用 Dropbox API 创建和同步数据

将 Core Data 与 Dropbox Datastore API 结合使用：ParcelKit

添加 Dropbox 和 ParcelKit 所需的框架

将 DropboxEvents 与 Dropbox 集成

结论

![image](img/sq1.jpg) 第 9 章：调优性能、内存使用与多线程

调优性能

构建用于测试的应用程序

运行你的第一个测试

故障机制（Faulting）

预取

缓存

过期缓存

唯一性（Uniquing）

使用更好的谓词提升性能

分析性能

处理多线程

线程安全

拜托，这能出什么岔子？

多余的用户界面

在另一个线程上初始化堆栈

总结

索引

