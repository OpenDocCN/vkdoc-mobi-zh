# 第九章：在 SQLite 中搜索记录

## 搜索应用

### 创建 SQLite 数据库

### 创建 iOS/SQLite 项目

## 运行应用

## 小结



■ 第 10 章：使用多个数据库

`ATTACH` 语句

`DETACH` 语句

多数据库限制

执行连接

Swift 中的 Attach 与 Detach

项目

`ViewController`

构建用户界面

运行应用程序

本章小结

■ 第 11 章：备份 SQLite 数据库

SQLite 备份方法概述

创建备份副本

备份内存中的 SQLite 数据库

备份磁盘上的 SQLite 数据库

备份应用程序

`ViewController`

构建用户界面

本章小结

■ 第 12 章：分析 SQLite 数据库

`Analyze` 语句

`sqldiff` 工具

`sqlite3_analyzer` 工具

本章小结

索引

Kevin Languedoc 拥有超过 20 年的数据库和软件开发经验，曾使用过桌面、客户端/服务器、大型机、Web/云、物联网和移动设备等多个主流数据库系统。他将自己在数据库开发方面的专业知识应用于 iOS（iPhone 和 iPad）平台以及 SQLite。他毕业于魁北克大学蒙特利尔分校，获得计算机科学学位，并在麦吉尔大学获得市场营销管理学位。他曾为企业级客户开发过 50 多款 iPhone 和 iPad 应用，客户规模各异。他在金融、银行服务与保险、零售与分销、能源、运输以及制药等多个行业都有过从业经验。他为本书带来了丰富的实践经验，并提供了大量的代码示例和可运行的应用。

Massimo Nardone 在安全、Web/移动开发、云计算和 IT 架构领域拥有超过 22 年的经验。他真正热爱的 IT 领域是安全和 Android。他在 Android、Perl、PHP、Java、VB、Python、C/C++和 MySQL 方面的编程和教学经验超过 20 年。他拥有意大利萨莱诺大学的计算机科学硕士学位。多年来，他担任过项目经理、软件工程师、研究工程师、首席安全架构师、信息安全经理、PCI/SCADA 审计师以及 IT 安全/云/SCADA 高级首席架构师等职务。他的技术技能包括安全、Android、云、Java、MySQL、Drupal、Cobol、Perl、Web 和移动开发、MongoDB、D3、Joomla、Couchbase、C/C++、WebGL、Python、Pro Rails、Django CMS、Jekyll、Scratch 等。他目前担任 Cargotec Oyj 的首席信息安全官（CISO）。他还曾在赫尔辛基理工大学（阿尔托大学）网络实验室担任访问讲师和习题指导老师。他拥有四项国际专利（涉及 PKI、SIP、SAML 和代理领域）。Massimo 已为多家出版公司审阅过 40 多本 IT 书籍，并且是《Pro Android Games》（Apress, 2015）的合著者。本书献给 Antti Jalonen 和他的家人，他们总是在我需要的时候给予支持。

一本书的完成绝非一人之功，而是团队协作的成果。尽管作者的姓名出现在封面之上，但要使项目成功，离不开幕后众多人士的支持与努力。首先，我要感谢我的妻子 Louisa，以及我的孩子 Patrick 和 Megan，感谢他们给予我写作本书的支持、空间和理解。在 Apress 出版社，我衷心感谢 Steve Anglin 给我这次机会，也感谢 Jessica Vakili，她总是耐心且乐于助人，确保了项目的顺利进行。我还要感谢 Aaron Black 在组织这个项目、组建这个优秀团队方面的支持，团队成员还包括 Jim Markham、Massimo Nardone 和 Eric Levasseur，他们提供了出色的见解，打磨了书中的瑕疵，使本书大放异彩。感谢 Dhaneesh Kumar 设计封面。对于在 Apress 参与此项目的所有其他人，我谨对你们帮助我完成项目表示谢意。

致以我最诚挚的感谢，
——Kevin

我作为一名软件开发者已经工作了 20 多年。我 IT 职业生涯的核心主题一直是数据库开发，因此我希望将这段经验带到这本书中。多年来，我曾使用过许多流行的数据库系统，如 Oracle、MSSQL 和 DB2，更不用说一些老牌系统如 Borland Dbase4、Lotus Approach、Superbase95，当然还有 Lotus Domino。然而，SQLite 给我留下了尤为深刻的印象。这项技术的多功能性令人难以置信。例如，你可以在 Windows PC 上创建一个 SQLite 数据库，然后将其不加修改地运行在 OSX、Linux、Android、JavaScript，当然还有 iOS 上。



