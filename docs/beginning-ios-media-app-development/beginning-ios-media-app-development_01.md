# 开始 iOS 媒体应用开发

*版权所有 © 2014 Ahmed Bakir*

本作品受版权保护。出版社保留所有权利，无论是全部还是部分材料，特别包括翻译权、重印权、插图再利用权、朗诵权、广播权、缩微胶片复制权或任何其他物理形式的复制权，以及信息传输或存储检索权、电子改编权、计算机软件权，或使用目前已知或未来开发的类似或不同方法。法律保留的例外情况包括与评论或学术分析相关的简短摘录，或专门为在计算机系统上输入和执行而提供的材料，仅供作品购买者独家使用。仅可根据出版社所在地现行版权法的规定复制本出版物或其部分内容，且必须始终从 Springer 获得使用许可。使用许可可通过 Copyright Clearance Center 的 RightsLink 获取。违反者将根据相应版权法被起诉。

ISBN-13（平装）：978-1-4302-5083-8  
ISBN-13（电子版）：978-1-4302-5084-5

本书中可能出现商标名称、标识和图像。我们不以每次出现商标名称、标识或图像时都使用商标符号的方式，而是仅以编辑方式并为了商标所有者的利益使用这些名称、标识和图像，无意侵犯商标权。

本书中使用的商品名称、商标、服务标志和类似术语，即使未明确标识，也不应被视为对其是否受所有权保护的表述。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑及出版社均不对可能存在的任何错误或遗漏承担法律责任。出版社对本书所含材料不作任何明示或暗示的担保。

总经理：Welmoed Spahr  
首席编辑：Michelle Lowman  
发展编辑：Russell Jones  
技术审校：Gheorghe Chesler  
编委会：Steve Anglin, Mark Beckner, Gary Cornell, Louise Corrigan, Jim DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Matthew Moodie, Jeff Olson, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Matt Wade, Steve Weiss  
协调编辑：Kevin Walter  
文字编辑：Sharon Wilkey  
排版：SPi Global  
索引编制：SPi Global  
美术设计：SPi Global  
封面设计：Anna Ishchenko  
摄影：Gheorghe Chesler  
图形设计：Rafael Rodriguez

本书通过 Springer Science+Business Media New York 在全球图书贸易中分销，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是一家加利福尼亚州有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc（SSBM Finance Inc）。SSBM Finance Inc 是一家特拉华州公司。

有关翻译信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 及 friends of ED 书籍可批量购买用于学术、企业或促销用途。大多数书籍也提供电子版和许可证。更多信息，请参阅我们的特别批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

作者在本文中引用的任何源代码或其他补充材料均可供读者在 `www.apress.com` 获取。关于如何找到本书源代码的详细信息，请访问 `www.apress.com/source-code/`。



谨以此书献给我的父亲穆罕默德·巴克尔。在我四岁那年，他带回了家里的第一台电脑，并始终鼓励我去探索它，哪怕我在两天后就把它弄坏了。

