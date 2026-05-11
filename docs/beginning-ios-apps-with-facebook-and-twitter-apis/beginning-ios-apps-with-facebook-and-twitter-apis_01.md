# 使用 Facebook 与 Twitter API 开发 iOS 应用：适用于 iPhone、iPad 和 iPod touch

版权所有 © 2011 Chris Dannen、Christopher White

保留所有权利。未经版权所有者及出版人事先书面许可，不得以任何形式或通过任何手段（电子或机械，包括影印、录音或任何信息存储检索系统）复制或传播本作品的任何部分。

ISBN-13（平装版）：978-1-4302-3542-2

ISBN-13（电子版）：978-1-4302-3543-9

本书中可能出现商标名称、标识和图像。对于商标名称、标识或图像，我们仅在编辑中使用并为了商标所有者的利益，无意侵犯商标权，因此不于每处出现时都使用商标符号。

本出版物中使用的商品名称、商标、服务标识及类似术语，即使未明确标识，也不应视为对其是否受所有权保护的立场表达。

总裁兼出版人：Paul Manning  
首席编辑：Steve Anglin  
开发编辑：Tom Welsh  
技术审稿人：Ryan Petrich  
编辑委员会：Steve Anglin、Mark Beckner、Ewan Buckingham、Gary Cornell、Jonathan Gennick、Jonathan Hassell、Michelle Lowman、James Markham、Matthew Moodie、Jeff Olson、Jeffrey Pepper、Frank Pohlmann、Douglas Pundick、Ben Renow-Clarke、Dominic Shakeshaft、Matt Wade、Tom Welsh  
协调编辑：Kelly Moritz  
文字编辑：Patrick Meader  
排版：MacPS, LLC  
索引编制：John Collin  
插画师：April Milne  
封面设计师：Anna Ishchenko

本书通过 Springer Science+Business Media, LLC. 在全球图书贸易中分销，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：`orders-ny@springer-sbm.com`，或访问 [`www.springeronline.com`](http://www.springeronline.com)。

有关翻译信息，请发送电子邮件至 rights@apress.com，或访问 [www.apress.com](http://www.apress.com)。

Apress 及 friends of ED 的书籍可批量购买用于学术、企业或促销用途。大多数图书也提供电子版及许可证。如需更多信息，请参阅我们的特殊批量销售——电子书授权网页：[`www.apress.com/bulk-sales`](http://www.apress.com/bulk-sales)。

本书中的信息按“原样”提供，不提供任何保证。尽管在编写过程中已采取一切预防措施，但作者和 Apress 均不对因使用本书信息而直接或间接导致的任何损失或损害，承担任何责任。

本书的源代码可供读者在 [`www.apress.com`](http://www.apress.com) 和 [`https://github.com/chrisdannen/Apress_iOSFacebookTwitter`](https://github.com/chrisdannen/Apress_iOSFacebookTwitter) 获取。成功下载代码需要回答与本书相关的问题。

## 内容一览

目录  
关于作者  
关于技术审稿人  
致谢  
前言  

第 1 章：社交图谱能为你的应用带来什么  
第 2 章：隐私，隐私，还是隐私  
第 3 章：选择你的武器！  
第 4 章：搭建开发环境  
第 5 章：安全使用 OAuth 与账户  
第 6 章：让你的应用准备好社交消息功能  
第 7 章：访问人物、地点、对象及关系  
第 8 章：POST 操作、数据建模与离线处理  
第 9 章：使用位置感知与流数据  
第 10 章：使用开源工具及其他实用资源  
第 11 章：你能（以及不能）构建的应用类型  
第 12 章：社交 iOS 应用的 UI 设计与体验指南  
第 13 章：Twitter UI 设计  
第 14 章：Facebook UI 设计  
索引



## 目录

目录概览

关于作者

关于技术审校

致谢

前言

第 1 章：社交图谱能为你的应用做什么

本书的用途是什么？

你需要准备什么

你应该了解什么

你将学到什么

了解社交图谱

简要用例

API 与服务简述

Facebook

Twitter

iOS 上的社交图谱

小结

第 2 章：隐私，隐私，还是隐私

旧有方式

热点问题的简要历史

Facebook 的过往记录

Twitter 的过往记录

OAuth 如何改变一切

新标准的出现

用户“想要”什么

教育你的用户

关于信息流的说明

遇到安全漏洞时该怎么做

小结

第 3 章：选择你的武器！

它们有什么用？

Facebook

Twitter

开始使用 Facebook 强大的开发者工具

使用 Facebook API

Twitter 的“不那么强大”（但依然很棒！）工具

使用 MGTwitterEngine

小结

第 4 章：环境搭建

Git 搞定一切

Github.com

安装 Git

你好，Facebook

创建项目

你好，Twitter

创建项目

接下来，关于安全性

第 5 章：使用 OAuth 和账户安全地工作

OAuth 的全方位解析

OAuth 的工作原理

Facebook 中的 OAuth

使用 Facebook 单点登录

Twitter 中的 OAuth

创建 Twitter 应用

更多内容

第 6 章：让你的应用准备好进行社交消息传递

Facebook Graph API 简介

来自朋友们的一点帮助

分页获取 Graph 响应

底层原理：FBRequest 类

Twitter API 简介



