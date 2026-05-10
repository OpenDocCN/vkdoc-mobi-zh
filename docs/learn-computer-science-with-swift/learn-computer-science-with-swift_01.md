# 使用 Swift 学习计算机科学

**计算概念、编程范式、数据管理，以及基于 Swift 和 Playgrounds 的现代组件架构**

Jesse Feiler

本书作者所引用的任何源代码或其他补充材料，读者均可通过本书的产品页面在 GitHub 上获取，该页面位于 [`www.apress.com/978-1-4842-3065-7`](http://www.apress.com/978-1-4842-3065-7)。获取更多详细信息，请访问 [`http://www.apress.com/source-code`](http://www.apress.com/source-code)。

ISBN 978-1-4842-3065-7  
e-ISBN 978-1-4842-3066-4  
[`doi.org/10.1007/978-1-4842-3066-4`](https://doi.org/10.1007/978-1-4842-3066-4)  
美国国会图书馆控制号：2017962300

© Jesse Feiler 2018

本作品受版权保护。出版商保留所有权利，无论是涉及材料的整体还是部分，特别是翻译、重印、重用插图、朗诵、广播、以缩微胶片或任何其他物理方式进行复制，以及电子信息存储与检索、电子改编、计算机软件，或使用目前已知或未来开发的任何相似或不同方法的权利。

本书中可能出现商标名称、徽标和图像。对于出现的商标名称、徽标和图像，我们没有在每个商标名称、徽标或图像旁加注商标符号，而是仅以编辑方式使用它们，以维护商标所有者的权益，并无意侵犯商标权。

本出版物中使用的商号、商标、服务标志和类似术语，即使未被标识为此类术语，也不应被视为对其是否受专有权利保护的看法表达。

尽管本书中的建议和信息在出版时被认为是真实和准确的，但作者、编辑和出版商均不对可能存在的任何错误或遗漏承担法律责任。出版商对本书中包含的材料不作任何明示或暗示的保证。

本书使用无酸纸印刷  
由 Springer Science+Business Media New York 向全球图书市场发行，地址：233 Spring Street, 6th Floor, New York, NY 10013。电话：1-800-SPRINGER，传真：(201) 348-4505，电子邮件：orders-ny@springer-sbm.com，或访问 www.springeronline.com。  
Apress Media, LLC 是一家加利福尼亚州有限责任公司，其唯一成员（所有者）是 Springer Science + Business Media Finance Inc (SSBM Finance Inc)。SSBM Finance Inc 是一家特拉华州公司。

## 引言

计算机科学是研究计算机及其操作的学科。它包括可计算性概念以及软件设计方法，这些内容现如今甚至教给年仅六岁的学生。它也涉及最大、最新、最先进的计算机和系统的复杂概念。

本书为那些出于实用原因想学习基础知识的人们提供了一个入门介绍：他们希望理解计算机科学原理，以帮助他们成为开发者（或更优秀的开发者）。本书的重点在于计算机科学的实际应用。沿着这一思路，本书使用最初由苹果公司开发的现代语言 Swift，并在 Swift Playgrounds 中展示了许多示例。你将找到关于调试技术和用户界面设计等各种问题的实用讨论，这些知识对于当今构建应用至关重要。请注意，Swift Playgrounds 用于演示许多计算机科学概念，但本书并非仅仅关于 Swift。书中并未演示所有的语言结构。

对于那些想要学习如何开发应用的人，我有一条重要的建议：使用它们。下载并尝试使用你所能接触到的每一个应用。阅读应用的评论。与他人交流他们的使用体验。人们常常在不了解当前（市场和）技术现状的情况下，就贸然开始尝试编写应用。

许多人对本书的编写提供了帮助。Waterside Productions 的 Carole Jelen 再次在促成此书问世方面发挥了关键作用。在 Apress，Jessica Valiki 和 Aaron Black 在帮助构思本书及其内容方面一直是我们重要的指导者和合作伙伴。

在本书的写作过程中，我有幸参与了几个应用开发项目，这些项目为应用开发过程提供了案例研究和实例。特别要感谢纽约州立大学普拉茨堡分校地球与环境科学中心的 Curt Gervich、Maeve Sherry 和 Michael Otton，以及普拉茨堡高中的 Sonal Patel-Dame。

## 下载本书的 Playgrounds

你可以从作者的网站 champlainarts.com 下载本书的 Playgrounds。

### 关于作者

Jesse Feiler 是一位作家和开发者，专注于利用创新工具和技术为非营利组织和小型企业服务。他积极参与社区活动，曾担任 Mid-Hudson 图书馆系统董事会成员（包括三年主席）、Philmont Main Street 委员会成员、Philmont 和 Plattsburgh 公共图书馆董事会成员、HB Studio 和 Playwrights Foundation 成员、Plattsburgh 规划委员会成员、Saranac River Trail 之友成员、Saranac River Trail Greenway 成员以及 Spectra Arts 成员。

他的应用包括 NP Risk — 非营利风险评估应用（与 Gail B. Nayowith 合作）、Saranac River Trail、Minutes Machine 和 Utility Smart。这些应用可通过 App Store 上的 Champlain Arts 获取，地址为 [`http://bit.ly/ChamplainArts`](http://bit.ly/ChamplainArts)。

他参与的大型项目包括：为纽约联邦储备银行系统开发和数据处理职能部门的特别项目参谋部和系统组件部门制定应急计划并支持公开市场货币政策及银行监管操作；在 Young & Rubicam 实施自然销售预测模型（首个基于计算机的新产品预测模型）；为 Prodigy 开发 Mac 客户端以实施其首个网页浏览器；为律师事务所、苹果公司和 Johnson Company 开发管理信息系统和接口；以及就千年虫问题提供咨询、写作和演讲。

为企业和非营利组织开发的中小型项目包括：设计并开发 Josef Albers 的《*色彩交互*》首个数字版本（为 Josef 和 Anni Albers 基金会及耶鲁大学出版社），为 Archipenko 基金会开发数据库和网站，以及为那些在毫无准备时才发现应急计划重要性的个人和组织提供“救援”任务。他与纽约州立大学普拉茨堡分校副教授 Curt Gervich 共同创建了 Utility Smart，这是一款帮助人们监控共享自然资源使用情况的应用。

Jesse 是 Saranac River Trail 之友和 Philmont Main Street 委员会的创始人。他定期出现在 WAMC 公共广播电台为东北地区制作的《The Roundtable》节目中，探讨社会与技术的交汇点。他是一位演讲者和客座讲师，也是一位专注于 iOS 应用开发的商业和技术领域的教师和培训师。他还为那些需要帮助聚焦目标并使用现代技术实现目标的组织提供咨询服务。他与 Gail B. Nayowith 合著了《*非营利风险手册*》以及《*非营利风险评估应用 — NP Risk*》。


