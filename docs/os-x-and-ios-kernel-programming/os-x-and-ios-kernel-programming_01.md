# OS X 与 iOS 内核编程

版权所有 © 2011，Ole Henry Halvorsen 与 Douglas Clarke

本作品受版权保护。出版商保留所有权利，无论涉及全部或部分材料，具体包括翻译权、重印权、插图复用权、引述权、广播权、微缩胶片或其他任何物理形式的复制权，以及信息存储与检索的传输权、电子改编权、计算机软件权，或目前已知及未来开发的任何类似或不同方法的权利。法律保留的豁免情形包括：与评论或学术分析相关的简短摘录，或专门为输入计算机系统并执行而提供的材料（仅供作品购买者专用）。未经出版商所在地现行版权法许可，不得复制本出版物或其任何部分，且使用许可必须始终从 Springer 获取。使用许可可通过 Copyright Clearance Center 的 RightsLink 获取。违反者将根据相应版权法被追究刑事责任。

ISBN-13（平装本）：978-1-4302-3536-1  
ISBN-13（电子版）：978-1-4302-3537-8

本书中可能出现商标名称、标识和图像。我们并非在每次出现商标名称、标识或图像时都使用商标符号，而仅以编辑方式使用名称、标识和图像，以维护商标所有人的权益，无意侵犯商标权。

本出版物中使用的商品名称、商标、服务标记及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的观点表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版商对可能存在的任何错误或遗漏不承担任何法律责任。出版商对本书所载内容不作任何明示或暗示的担保。

总裁与出版人：Paul Manning  
首席编辑：James Markham  
技术审稿人：Phil Jordan 与 Graham Lee  
编辑委员会：Steve Anglin, Mark Beckner, Ewan Buckingham, Gary Cornell, Morgan Ertel, Jonathan Gennick, Jonathan Hassell, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Tom Welsh  
协调编辑：Debra Kelly  
文字编辑：Scribendi Inc. 与 Kim Wimpsett  
排版：Bytheway Publishing Services  
索引编制：SPI Global  
插图制作：SPI Global  
封面设计：Anna Ishchenko

本书通过 Springer Science+Business Media New York 在全球图书贸易中分销，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问：`www.springeronline.com`。

有关翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。Apress 及 friends of ED 书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子版及许可证。如需更多信息，请参阅我们的特殊批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在本文中引用的任何源代码或其他补充材料，读者可在 `www.apress.com` 获取。有关如何查找本书源代码的详细信息，请访问 `www.apress.com/source-code/`。

*谨以此书献给我的妻子兼挚友 Jennifer，以及我的孩子 Desmund 和 Isabel。*  
*——Ole Henry Halvorsen*

*献给我的父母，他们从小鼓励我对计算机的兴趣。*  
*——Douglas Clarke*

## 目录速览

- 关于作者
- 关于技术审稿人
- 致谢
- 引言
- 第 1 章：操作系统基础
- 第 2 章：Mac OS X 与 iOS
- 第 3 章：Xcode 与内核开发环境
- 第 4 章：I/O Kit 框架
- 第 5 章：从应用程序与驱动程序交互
- 第 6 章：内存管理
- 第 7 章：同步与线程
- 第 8 章：通用串行总线
- 第 9 章：PCI Express 与 Thunderbolt
- 第 10 章：电源管理
- 第 11 章：串行端口驱动程序
- 第 12 章：音频驱动程序
- 第 13 章：网络
- 第 14 章：存储系统
- 第 15 章：用户空间 USB 驱动程序
- 第 16 章：调试
- 第 17 章：高级内核编程
- 第 18 章：部署
- 索引



