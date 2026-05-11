# Xcode 故障排除指南

Magno Urbano

作者在本文中引用的任何源代码或其他补充材料，读者可在 [`www.apress.com/9781484215616`](http://www.apress.com/9781484215616) 获取。关于如何查找本书源代码的详细信息，请访问 [`www.apress.com/source-code/`](http://www.apress.com/source-code/)。读者也可以在 SpringerLink 上每章的“补充材料”部分获取源代码。

ISBN 978-1-4842-1561-6  
电子书 ISBN 978-1-4842-1560-9  
DOI 10.1007/978-1-4842-1560-9  
© Apress 2015

**Xcode 故障排除指南**  
常务董事：Welmoed Spahr  
首席编辑：Michelle Lowman  
技术审校：Charles Cruz  
编委会：Steve Anglin, Louise Corrigan, James T. DeWolf, Jonathan Gennick, Robert Hutchinson, Michelle Lowman, James Markham, Susan McDermott, Matthew Moodie, Jeffrey Pepper, Douglas Pundick, Ben Renow-Clarke, Gwenan Spearing, Steve Weiss  
协调编辑：Kevin Walter  
排版：SPi Global  
索引编制：SPi Global  
插图制作：SPi Global  
封面图片：Martijn Vroom

如需了解翻译事宜，请发送电子邮件至 `rights@apress.com`，或访问 [`www.apress.com`](http://www.apress.com/)。Apress 及 friends of ED 的书籍可批量购买，用于学术、企业或促销用途。大多数图书也提供电子书版本和许可证。更多信息，请参考我们的特别批量销售 – 电子书许可网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本作品受版权保护。出版商保留所有权利，无论涉及材料的全部或部分，具体包括翻译、重印、使用插图、朗诵、广播、微缩胶片复制或以任何其他物理方式复制，以及电子改编、计算机软件或类似或不同的方法进行传输或信息存储与检索，无论这些方法目前已知或未来开发。法律保留的例外情况包括：与评论或学术分析相关的简短摘录，或专门为输入并运行于计算机系统而提供的材料，仅供购买者独家使用。未经出版商所在地现行版权法许可，不得复制本出版物或其任何部分，且使用许可必须始终从 Springer 获取。许可可通过 Copyright Clearance Center 的 RightsLink 获得。违反者将根据相应版权法被追究责任。

本书中可能出现商标名称、标识和图像。我们并非在每次出现商标名称、标识或图像时都使用商标符号，而是仅以编辑方式使用这些名称、标识和图像，以维护商标所有者的权益，且无意侵犯商标权。本出版物中使用的商品名称、商标、服务标志及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的任何意见表达。

尽管本书中的建议和信息在出版时被认为是真实准确的，但作者、编辑和出版商均不对可能出现的任何错误或遗漏承担法律责任。出版商对本书内容不作任何明示或暗示的保证。

本书通过 Springer Science+Business Media New York 在全球图书贸易中发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。Apress Media, LLC 是加利福尼亚州的有限责任公司，其唯一成员（所有者）为 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州的公司。

## 前言

`Xcode 故障排除指南` 是一本面向那些为 iOS 或 OS X 创建应用的人的指南。本书包含了针对棘手问题的解决方案——任何使用 Xcode 的开发人员迟早都会遇到的问题。

Xcode 是 Apple 为开发 iOS 和 OSX 应用而创建的官方工具。然而，该工具的局限性、一些错误以及其令人费解的错误信息，有时使得使用 Xcode 变得非常困难。

更糟糕的是，一些关键的文档不完整、含糊不清或难以解读。

为了让工作更轻松，作者首先为自己记录下所有这些问题的笔记及其相应的解决方案。一周又一周，一月又一月，多年的笔记积累下来，`Xcode 故障排除指南` 就此诞生。

`Xcode 故障排除指南` 还向您展示了如何自动化一些重复性任务，以及如何解决与 iOS 或 OS X 开发相关但位于 Xcode 外部的问题。

本书中的代码兼容 iOS 9 和 OS X 10.11 “El Capitan”。因此，本书中展示的 Swift 代码兼容 Swift 2.0。

我们希望本书能帮助您解决在开发过程中遇到的一些难题。其中一些问题有多种解决方案，我们已尽力为每种情况添加了所有已知选项。因此，本书并非每个解决方案的最终指南。我们只是尽我们所能。

如果您想为本书未来版本建议添加某个主题，请随时直接联系作者：`troubleshooting-xcode@addfone.com`。



