# iOS 应用性能优化专业指南

版权所有 © 2011 年，Khang Vo

保留所有权利。未经版权所有者及出版人事先书面许可，不得以任何形式或任何方式（电子或机械，包括影印、录制，或通过任何信息存储或检索系统）复制或传播本作品的任何部分。

ISBN-13（平装）：978-1-4302-3717-4

ISBN-13（电子版）：978-1-4302-3718-1

本书中可能出现商标名称、标识和图像。对于商标名称、标识或图像的每次出现，我们并非使用商标符号，而是仅以编辑方式使用这些名称、标识和图像，以维护商标所有者的利益，且无意侵犯商标权。

本出版物中使用的商品名称、商标、服务标记及类似术语，即使未被标识为注册商标，也不应被视为对其是否受专有权利保护的表达意见。

总裁兼出版人：Paul Manning
主编辑：Tom Welsh
技术审校：Evan Coyne Maloney
编辑委员会：Steve Anglin、Mark Beckner、Ewan Buckingham、Gary Cornell、Morgan Ertel、Jonathan Gennick、Jonathan Hassell、Robert Hutchinson、Michelle Lowman、James Markham、Matthew Moodie、Jeff Olson、Jeffrey Pepper、Douglas Pundick、Ben Renow-Clarke、Dominic Shakeshaft、Gwenan Spearing、Matt Wade、Tom Welsh
协调编辑：Corbin Collins
文字编辑：Mary Behr
排版：MacPS, LLC
索引编制：SPi Global
美工：SPi Global
封面设计：Anna Ishchenko

本书通过 Springer Science+Business Media, LLC.（地址：233 Spring Street, 6th Floor, New York, NY 10013）在全球图书贸易中分销。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 `www.springeronline.com`。

如需了解翻译相关信息，请发送电子邮件至 `rights@apress.com`，或访问 `www.apress.com`。

Apress 和 friends of ED 的书籍可批量购买用于学术、公司或推广用途。大多数图书也提供电子版版本和许可证。如需了解更多信息，请参考我们的批量销售–电子书许可网页：`www.apress.com/bulk-sales`。

本书中的信息按“原样”发布，不提供任何担保。尽管在编写过程中已采取所有预防措施，但作者及 Apress 均不对因使用本书中的信息而直接或间接导致的任何损失或损害承担任何责任。

作者在本书中引用的任何源代码或其他补充材料，读者可在 `www.apress.com` 获取。有关如何找到本书源代码的详细信息，请访问 `http://www.apress.com/source-code/`。

*献给我的女友 Ngan Huynh，感谢她的爱、巨大的鼓励与支持。*

## 目录概览

- 目录
- 关于作者
- 关于技术审校
- 致谢
- 前言
- 第 1 章：iOS 性能优化简介
- 第 2 章：使用工具对你的应用进行基准测试：模拟器与真实设备测试
- 第 3 章：提高并优化 UITableView 性能
- 第 4 章：使用图像与数据缓存技术提升应用性能
- 第 5 章：使用算法与数据结构调优应用
- 第 6 章：使用多线程技术改善并行数据访问
- 第 7 章：优化内存使用以获得更佳性能
- 第 8 章：集成多线程与高效内存使用以提升多任务应用性能
- 第 9 章：使用原生 C/C++ 提升性能
- 第 10 章：比较 Android 与 Windows Phone 性能问题
- 索引



