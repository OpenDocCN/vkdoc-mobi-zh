# Pro Android 3

版权所有 © 2011 作者：Satya Komatineni, Dave MacLean, 和 Sayed Y. Hashimi

保留所有权利。未经版权所有者及出版人事先书面许可，不得以任何形式或通过任何电子或机械方式（包括影印、录音或任何信息存储与检索系统）复制或传播本作品中的任何部分。

ISBN-13 (平装): 978-1-4302-3222-3

ISBN-13 (电子版): 978-1-4302-3223-0

本书中可能出现商标名称、标识和图像。对于出现的商标名称、标识或图像，我们并非每次均使用商标符号，而仅在编辑风格上使用这些名称、标识和图像，以利于商标所有者，无意侵犯商标权。NFC Forum 及 NFC Forum 标识是近场通信论坛的商标。

本出版物中使用的商品名称、商标、服务标记及类似术语，即使未明确标识，也不应被视为对其是否受专有权利保护的看法表达。

总裁与出版人：Paul Manning  
首席编辑：Matthew Moodie  
技术审校：Dylan Phillips  
编辑委员会：Steve Anglin, Mark Beckner, Ewan Buckingham, Gary Cornell, Jonathan Gennick, Jonathan Hassell, Michelle Lowman, Matthew Moodie, Jeffrey Pepper, Frank Pohlmann, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Matt Wade, Tom Welsh  
协调编辑：Corbin Collins  
文字编辑：Heather Lang, Tracy Brown, Mary Behr  
排版：MacPS, LLC  
索引制作：BIM Indexing & Proofreading Services  
插画师：April Milne  
封面设计：Anna Ishchenko

本书在全球图书贸易中由 Springer Science+Business Media, LLC. 发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问：[`www.springeronline.com`](http://www.springeronline.com)。

如需了解翻译相关信息，请发送电子邮件至：`rights@apress.com`，或访问：[`www.apress.com`](http://www.apress.com)。

Apress 及 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子书版本和许可。欲了解更多信息，请参阅我们的特殊批量销售——电子书许可网页：[`www.apress.com/info/bulksales`](http://www.apress.com/info/bulksales)。

本书中的信息按“原样”提供，不提供任何担保。尽管在编写本书时已采取所有预防措施，但作者及 Apress 对因使用本书所含信息而直接或间接导致的任何损失或损害，不对任何个人或实体承担任何责任。

本书的源代码可供读者在 [`www.apress.com`](http://www.apress.com) 获取。

*献给我的兄弟 Hari，生活对他未曾眷顾。*

–*Satya Komatineni*

*献给我的妻子 Rosie 和儿子 Mike，感谢他们的支持；没有你们，我无法完成此书。也献给我的狗 Max，感谢它长时间陪伴在我脚边。*

–*Dave MacLean*

*献给我的儿子 Sayed-Adieb。*

–*Sayed Y. Hashimi*

## 内容速览

目录

前言

关于作者

关于技术审校

致谢

序言

第 1 章：Android 计算平台介绍

第 2 章：搭建开发环境

第 3 章：理解 Android 资源

第 4 章：理解内容提供者

第 5 章：理解 Intent

第 6 章：构建用户界面及使用控件

第 7 章：使用菜单

第 8 章：使用对话框

第 9 章：使用偏好设置与保存状态

第 10 章：探究安全性与权限

第 11 章：构建与使用服务

第 12 章：探究包

第 13 章：探究处理器

第 14 章：广播接收器与长期运行的服务

第 15 章：探究闹钟管理器

第 16 章：探究 2D 动画

第 17 章：探究地图与基于位置的服务

第 18 章：使用电话 API

第 19 章：理解媒体框架

第 20 章：使用 OpenGL 进行 3D 图形编程

第 21 章：探究实时文件夹

第 22 章：主屏幕小部件

第 23 章：Android 搜索

第 24 章：探究文字转语音

第 25 章：触摸屏

第 26 章：使用传感器

第 27 章：探究联系人 API

第 28 章：部署你的应用：Android 市场及其他

第 29 章：面向平板电脑及更多设备的 Fragment

第 30 章：探究 ActionBar

第 31 章：3.0 版本的其他主题

索引


好的，作为一名高级文档工程师和翻译员，我将严格按照您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


`/path/to/xxx`目录，找到`xxx.json`。

在表达式`cvar = avar + bvar`中，加法运算符（`+`）将`avar`与`bvar`相加，得到它们的和`cvar`

在`List.of(arg0, arg1, arg2)`中，`List`接口的工厂方法`of()`接受一系列的元素，返回包含它们的只读列表。

之后我们这样调用`cmd`命令：`cmd arg0 arg1 arg2`。

```
if (condVar > someVal) {console.log("xxx")}
```

---

## 目录

内容一览

前言

关于作者

关于技术审校

致谢

序言

## 第 1 章：初识 Android 计算平台

新个人计算机的新平台

Android 早期历史

深入 Dalvik VM

理解 Android 软件栈

使用 Android SDK 开发最终用户应用

Android 模拟器

Android UI

Android 基础组件

高级 UI 概念

Android 服务组件

Android 媒体与电话组件

Android Java 包

利用 Android 源代码

本书中的示例项目

小结

## 第 2 章：搭建开发环境

搭建开发环境

下载 JDK 6

下载 Eclipse 3.6

下载 Android SDK

工具窗口

安装 Android 开发工具 (ADT)

学习基础组件

视图 (View)

活动 (Activity)

意图 (Intent)

内容提供者 (Content Provider)

服务 (Service)

AndroidManifest.xml

Android 虚拟设备

Hello World!

Android 虚拟设备

探索 Android 应用的结构

分析记事本应用

加载并运行记事本应用

剖析应用

检查应用生命周期

调试你的应用

启动模拟器

严格模式 (StrictMode)

参考资料

小结

## 第 3 章：理解 Android 资源

理解资源

字符串资源

布局资源

资源引用语法

为后续使用定义你自己的资源 ID

编译与未编译的 Android 资源

列举关键 Android 资源



