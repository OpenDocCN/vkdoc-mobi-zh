# 5. 购买软件以实现业务自动化

我们大概都说过“有个应用程序可以解决这个问题”。而这句话在创业的各个方面体现得尤为真切。基于应用程序的经济为小企业带来的好处之一，就是让它们能与大型企业并驾齐驱。过去你总希望拥有一个能处理几乎所有事务的工具，如今这样的日子已一去不复返——你确实拥有了。曾经需要支付巨额费用购买庞大、单一、并强迫你以特定方式开展业务的软件，那种日子也已成为历史。如今，我们可以将各种小型工具和工作流程串联起来，实现从管理故障单、修复系统、向您发送设备问题通知，到处理收款等几乎所有事务的自动化。

因此，你需要在本章做的是：组合一套工具集，帮助实现业务自动化，这样你就不必凡事亲力亲为。从技术入手。始终从为客户构建最佳解决方案开始；在本章中，我将其称为“技术核心”。然后，围绕该方案，接入能够自动化业务服务端的功能模块；这些就是专业服务自动化（PSA）和托管服务自动化（MSA）工具。最后，利用来自应用经济中的常见现成产品，将它们全部整合在一起。但正如我所说，这一切都从“技术核心”开始。

## 选择你的工具

本章后面的“技术核心”部分提供了相当长的技术解决方案列表。为什么我要列出这么多选项，而不是给你一条易于遵循、全面详尽、包罗万象的工具路线图呢？因为它们各有千秋，难分伯仲。也许你想要一套基于开源的工具链；也许你想保持简单，只对 Mac 进行 MDM（移动设备管理）式的设备管理；也许你有足够多的设备，值得雇佣一个开发运维团队或平台工程团队来自动化大量维护和设备管理任务；又或者，你想构建大量的零层资产，让客户能够自助服务，并将其接入基于机器学习的聊天机器人。也许你偏爱 100%的个性化触达，只想在出现问题时收到警报。

无论你选择做什么，最重要的是了解市面上有哪些选择，并有意识地决定哪些要做、哪些不做。首先，让我们来看看设备管理解决方案，或者说那些允许你远程集中管理设备状态、从而免去你执行日常任务之苦的工具。



### 设备管理

在本章稍后列出的工具中，设备管理工具将是您选择的最重要的工具之一。当我撰写第一本关于企业 Mac 管理的书时，市面上只有四种设备管理工具。而如今，设备管理已成为一个价值数十亿美元的行业，有数十种工具可供选择，每种都有其特定的利基市场。这主要是因为苹果公司开发了一套用于管理设备的 API，即移动设备管理（`MDM`）。过去，在将设备交到用户手中之前，你必须先对设备进行镜像部署。借助设备注册计划（`DEP`），苹果公司基本上消除了镜像部署的需求，其方式是让设备在首次开机时加入一个 `MDM` 解决方案，从而允许对设备进行自动化配置以替代镜像部署。苹果公司还推出了批量购买计划（`VPP`），它允许 `MDM` 解决方案从 App Store 向设备部署应用程序。许多组织所需的大部分管理工作，随后都可以通过一个基于大量 API 调用以规范化苹果服务的图形用户界面（GUI）来完成。

简而言之，这就是 `MDM`。但除此之外，还有一些带有不同成熟度代理的工具。`Jamf Pro`、`Munki` 和 `FileWave` 可能属于其中最重要的几个。这些工具可能带有也可能不带有 `MDM` 组件。`Jamf Pro` 和 `FileWave` 是其中功能最全面的。另一种新兴的组合是 `AirWatch` 和 `Munki`，尽管这种不太可能的合作关系的未来尚不明朗。

哪个是最好的管理工具？我不会在本书中深入探讨这些问题，因为它们总是在变化，而且这些工具的作者和供应商（我也是其中之一）脸皮都很薄。但这些工具都有 API，并且可以配置为与通用服务台和/或专业服务自动化工具协同工作。例如，我编写了一个集成 `Jamf Pro` 与 Salesforce Cases 的工具，以及另一个集成 `Jamf Pro` 与 ZenDesk 的工具。`Jamf` 在 ServiceNow 市场上有一个集成方案。另一个例子是集成热门 PSA 软件 `ConnectWise` 与 `Jamf` 的工具。我为 `Jamf` 工作，所以对这些最为熟悉，但您选择的任何管理工具，很可能都存在不同程度成熟度的集成方案。

此外，还有一些管理工具是由也从事专业服务自动化（PSA）等业务的机构开发的。`LabTech`、`ConnectWise` 和 `Kaseya` 都是首先面向 Windows 的工具，它们在非苹果平台上（例如，它们都有 Mac 代理）运行良好。所有这些代理本质上都是经过美化的脚本运行器，内置的逻辑非常少。因此，如果您使用这类工具，您很可能还需要另一个工具，能够实际帮助您管理设备并有效地获取设备信息。这并不是说您不能使用它们。为了节省精力和成本，在它们变得不够用之前，您应该使用它们。只是请对这种情况能持续多久抱有理性的预期。

一个例外是 `Solarwinds MSP` 工具。这个工具集的维护者正在努力构建一个全面的解决方案，使得托管服务提供商（MSP）不仅能够销售设备管理（对于托管服务提供商来说，这更像是一种降低成本的工具），还能提供备份和防病毒解决方案，这些附加组件的成本很容易转嫁给您的客户。这将增加销售额，增加月度经常性收入（MRR），并降低您在支持设备时产生的成本（例如，与排查损坏的设备相比，从备份中恢复通常是一种快速的修复方法）。这个工具链可能因某种原因不适合您。如果是这样，仍然值得看看他们提供的产品，这些产品可以作为增加收入和让客户满意的方法。

## PSA、MSA、RMM 和 BDR（天呐！）

选择合适的专业服务自动化（`PSA`）和/或托管服务自动化（MSA）是许多托管服务提供商（MSP）将要做出的最重要决策之一。这是一种旨在为您节省时间的昂贵软件。至于应该使用哪种正确的自动化软件，并没有明确的答案。为什么呢？因为每种软件都有其优缺点。我不会在本书中深入探讨这些问题，因为它们总是在变化，而且这些工具的供应商脸皮都很薄。

但我可以提供指导。首先，寻找那些专注于您主要支持的平台的工具。如果您主要做 Mac 业务，请关注软件中对 Mac 的支持；如果您更偏向 Windows 业务，并将 Mac 作为附加项，请寻找与 Windows 安装程序良好且深度集成的工具。甚至更好的是，实际上有一些工具专注于您可能支持的垂直市场，这些通常是最好的，因为它们内置了逻辑，可以帮助您在与该市场的客户合作时节省时间和金钱。

`PSA` 或 `MSA` 解决方案的主要目标是节省您的人力成本，同时为您来之不易的客户提供额外的价值。如果您能在设备用户遇到某种工作中断之前主动解决问题，那么您就赢得了那份支持合同。稍加跟进就能产生深远影响；在不小题大做的情况下，将此信息传达给客户，让他们理解您所提供服务的价值。

远程管理和监控（`RMM`）是一种允许您远程管理设备的工具。一个好的 `RMM` 工具，就像许多其他事物一样，能让您完成多项任务。我最喜欢的一个功能就是直接接入并控制桌面进行故障排查。虽然您需要与客户进行面对面交流，但您也需要控制成本并为客户提供更快的解决方案；因此，`RMM` 对于提高效率至关重要，它能让技术人员坐在办公室里，而不是奔波在路上。

### BDR 软件

BDR 代表备份和灾难恢复。这种类型的软件被许多人视为基础服务的附加组件。但重要的是要考虑到，没有什么比数据丢失更快地让您丢掉饭碗了。解释数据丢失的原因，以及判定是内部 IT 部门、最终用户还是您的组织造成了数据丢失，都是无关紧要的。

当涉及到数据丢失时，人们的看法是：您的咨询公司本应防止这种情况发生。除了防止数据丢失，`BDR` 软件还为您提供了许多额外的选择，用于简化故障排查和设备升级/更换流程。您可以将 `BDR` 捆绑到您的服务中；然而，这会导致您的基准价格看起来更高。无论如何，即使您以极低的利润率运营 `BDR` 服务，您越能确信设备已备份（这意味着您需要将最近完成的备份作为设备管理软件的一部分），您就能越灵活。除了对全面 `BDR` 的需求之外，关于 `RMM`、`PSA` 和 `MSA` 软件还有其他要点，我们将在下一节中介绍。



### 值得考虑的几种 RMM、PSA 和 MSA 解决方案

到目前为止（或者如果你还没开始，在某个时间点），你可能正告诉大多数客户，让他们基于软件来运营业务。会计、CRM、销售点、ERP 等解决方案有助于降低劳动力成本，并让企业保持敏捷。你自己的业务也不例外。起初，如果只是你一个人，你或许还能靠电子表格和一个 `Quicken` 账户勉强维持。但随着业务增长，情况就不再是这样了。

因此，有许多工具值得你关注，包括以下解决方案，它们至少可以用来管理 Apple 设备，即使它们开箱即用提供的价值并不多：

- `Continuum`
- `Autotask`
- `Kaseya`
- `Solarwinds MSP`
- `Connectwise`
- `N-Central`
- `Pulseway`
- `NinjaRMM`
- `LogicMonitor`
- `ManageEngine`
- `Cloud Management Suite`
- `Atera`
- `CENTREL`
- `Loom Systems`
- `Auvik Networks`
- `Trend Micro`
- `mspStack`
- `Acronis`
- `HelpSystems`
- `ECi Software Solutions`
- `Opsview`
- `OptiTune`
- `Spiceworks MSP`
- `Avast Managed Workplace RMM`
- `Unigma`
- `Zenith Infotech`
- `packetTrap`
- `BeAnywhere`
- `LogMeIn Central`
- `AVG Cloudcare`
- `Comodo One RMM`
- `Panorama9`
- `Auvik MSP Edition`
- `gfiMax`
- `HarmonyPSA`

在选择时请务必严谨。请记住，当你的企业规模尚小时，更换工具会有些麻烦，但还是可行的。随着业务增长，考虑到你可能需要处理的设备数量，更换工具会变得越来越有挑战性，而且这样做会丢失大量历史数据。因此，在选择工具时要严谨。虽然有些工具最初看起来可能很昂贵，但随着业务增长，你很可能会注意到它们带来的真正价值链。

在构建工具链时，请考虑以下属性：

- 工具的成本与其带来的价值相比如何？
- 是否支持你所有的计费类型？
- 该工具能否与其他设备管理解决方案集成？
- 该工具是否为你的员工提供一条清晰且易于解释的路径，将工单创建、工单完成、应收账款和工时记录串联起来？
- 该工具是否允许技能矩阵，以便随着业务增长，你无需依赖一个了解组织中每种技能组的调度员来分配工单？
- 该工具是否实现与客户沟通的自动化？
- 如果存在其他不支持的必要工作流程，你是否能为该工具编写自定义脚本？
- 你是否可以只买一个工具而不是三个？
- 如果该工具日后不再适用，你有哪些其他选择，以及数据迁移到那些其他选择的可移植性如何？
- 该工具是否需要专业服务才能启动？
- 制造该工具的公司的支持组织如何？
- 你是否真的喜欢使用这个工具？

还有很多其他问题，但这些问题应该能引导你走上正确的道路。客户应该能够在线创建工单，在线查看工单状态，在线查看其设备群的状态，收到工单已关闭的消息，对你在工单上的表现进行评分，查看他们的工作账单，并支付账单或更改他们的账单信息。而很可能的情况是，尤其是在 Apple 方面，单靠一个工具无法让你完成所有这些事情。但通过一些良好的工作，你可以串联起一个完全自动化的解决方案。

### 提示

一旦选择了某个工具，也要考虑该工具的未来发展！

你应该设定一个目标，即最终超越你所购买的任何 MSP 解决方案。这些工具对于成长型和小型企业来说很棒，但一旦你达到某个临界点，就没有哪个工具能够满足你不断增长的需求了。起初，如果你编写了太多脚本和其他自定义工作流程，而这些在使用这些工具时毫无意义，那么很可能是你的业务逻辑出了问题。但随着业务增长，如果你固守它们特定的工作流程，你最终要么沦为同质化的商品，要么人工成本开始飙升。这时，聘请一位业务顾问，或向经历过类似成长阵痛的人寻求建议，是值得的。

#### 注意

市面上所有的 PSA 和 MSA 工具用起来都会很糟糕，除非你调整自己的业务逻辑去适应它们的逻辑。即使这样，它们可能还是很糟糕。我很遗憾地告诉你，选择过程应该假设这些工具会有些糟糕之处。但从头开始构建你自己的工具，这本身就是一种商业模式，你可以在其中销售你的工具——前提是你确实认为自己能比别人做得更好（并且希望有机会证明自己是正确的）。

## 基于应用的经济

很少有单个软件能够自动化整个组织；你可能需要将多个软件包串联起来，才能充分发挥任何给定工具的全部优势。而一旦你做到了，你必然会发现自己想要实现越来越多的自动化。我曾经用过的一句话是“被一个小 shell 脚本取代意味着升职！”

那么你应该关注哪些工具呢（甚至也许能想出一种服务可以卖给客户）。有几种解决方案脱颖而出：

- `IFTTT`（If This Then That 的缩写）是一个面向消费者的工具，旨在实现简单、可读性强的自动化。`IFTTT` 可轻松用于连接 `Nest`、`Google Apps` 和其他常见云服务等。`IFTTT` 的妙处在于，我看到人们有能力构建自己的自动化，这价值巨大。但问题在于，你可能会花费不少时间从那些重叠或构建不佳的自动化中筛选出真正能可靠完成你需求的功能。但它是免费的，这算是一个优点。

- `Workato` 是一个可用于连接多种更专业级别的云服务的工具。我强烈建议你检查一下 `Workato` 中所有与你所用工具相关的受支持工作流程。你可能会看到一些起初看似不起眼的东西，但最终却能激发一个创新甚至革命性的工作流程，从而节省你的时间，或以你从未想过的方式取悦你的客户。

- `Workflow` 非常棒，最近被苹果收购了。类似于 `Workflow`，它连接各种拥有 API 的工具，但它不是我的首选，唯一原因是我对其技术的长期性有所顾虑，因为苹果在允许访问其在线服务方面，以其对 API 不友好而闻名。

而且每天都有新工具问世。在一个微服务和 API 优先的世界里，许多解决方案的真正价值在于它们的互操作性。大多数供应商希望成为框架，而不关注那种互操作性，因此连接其他工具的“胶水工具”市场正在蓬勃发展。其他一些值得考虑的解决方案包括 `Zapier`、`AutomationFuel`、`Conexio`、`Automate.ioo`、`Integromat`、`Flow XO Integrations`、`Microsoft Flow`、`OneSaas`、`Flashnode` 和 `Built.io`。

#### 注意

上述工具中可用的自动化只是一个开始。这些工具中每天都有新功能涌现。我建议定期查看这个列表（也许每个月在喝晨间咖啡时看一次）。

在上一章中，我们讨论了会计软件。别忘了这一点。最初，大多数组织会使用一个 App 或网页应用来手动给客户开账单。但请不要忘记，虽然你可以用 App 手动给客户开账单，但发票开得越多，这个过程就越耗时。这就是为什么有多种方法可以将你的计费与任务管理、工单管理和其他工作流程集成起来。像 `Freshbooks` 和 `Freshdesk` 这样的工具甚至更进一步，构建了 `Freshservice`，这样你就可以将许多工具整合到一个真正好用的工具中。

如果你能简化业务逻辑，使其与这些集成度更高的解决方案之一相匹配，那么在尝试排查为什么所有这些像“纸牌屋”一样的工作流程不能总是正常工作时，你就不太可能抓狂了。



## 自动化服务

请务必谨慎。如果你不够细心，本章迄今为止讨论的所有自动化内容可能会变得极其复杂。如果你无法快速解释你的业务是如何运作的，那说明存在问题。当然，除非你经手的是数十亿美元的合同。

同时，不要忽视人的因素。在本章的“延伸阅读”部分，我推荐了盖伊·川崎所著的《魅力》一书。原因在于，当你通过实施自动化来削减成本时，重要的是要意识到你正在减少与客户的接触点。客户可能完全不知道你为维护其系统稳定所做的出色工作。因此，可以通过自动化方式发送通信，让他们看到你为每个已解决的问题生成的服务工单，但无论如何，你仍需定期与客户举行例行会议。

一旦你尽可能地实现了自动化，请花时间在这些客户身上，只为提醒他们你有多重视他们。这不仅有助于你留住客户，还能让你更深入地理解他们的需求。最重要的是，在此过程中你或许还能结交一些朋友。

## 构建软件时应注意什么

在本章前文，我们探讨了现成的解决方案，用于帮助你管理设备、工单、员工等。但你对业务应该如何运作的理解，可能比成千上万个其他组织更深刻。不，真的，你或许确实如此。一旦你自问过“你确定吗？”，就可以开始考虑构建软件了。

如果你从未构建过软件或委托他人构建过软件，了解软件开发现实（以及通常构建软件的人）中的一些普遍规律会很有帮助。如果你决定走这条路，以下是关于软件项目的一些建议：

*   一个项目永远不会有足够的人来实现你想要的所有功能。句号。
*   随着软件项目接近完成，剩余的工作量会与代码的混乱程度成正比增长。你采取的临时解决方案和捷径越多，最后那 1%所花费的时间就越长。
*   有时，创建一个全新版本比试图改进和完善旧版本更容易。切勿对你的代码过于钟爱，以至于无法将其彻底删除并重建。
*   每年你都会在编写代码方面变得更好，以至于最终会为自己代码的糟糕程度感到难堪，并只想重写所有项目。前提是你真的在不断进步。
*   功能越少，缺陷也越少。
*   开发人员越多，合并冲突就越多。扩大团队规模并不一定意味着生产力会成比例提高。
*   随着项目增长，你无法对每个可能的回归问题进行质量保证或单元测试。但你必须尽力尝试！
*   代码永远没有真正完成的时候。总会有理由保留这个或那个。但你必须持续交付！
*   谁在乎你使用的是什么方法论，只要深思熟虑就行！`看板`、`Scrum`、`极限编程`等等。它们本质上都差不多。重要的是团队要达成共识。
*   几乎没有足够的项目经理会在项目计划中考虑到节假日、年假和病假。
*   优秀开发者难寻。但请耐心等待，否则你可能会因为招到破坏团队凝聚力的人而陷入困境。
*   团队绩效越高，团队成员对水平不高的成员就越感到不满。
*   大多数开发者在开始时并不具备领域知识（即他们为之编写软件的领域的知识）。一旦开发者获得了领域知识，他们就能编写出更好的软件，并且需要的具体客户故事也更少！
*   除了每天几分钟的会议之外，会议的投资回报率绝对为负。
*   技术债务永远存在。永远。

你实际上可能为客户构建软件。这是向客户交叉销售附加服务并提供更多价值的绝佳方式。你构建的软件也会让你与客户的联系越来越紧密。特别是当需要填补苹果平台软件缺失的空白时，软件开发很容易最终贡献你年收入的大约三分之一；因此，如果你尚未掌握这门手艺，请深思熟虑。并且，也要阅读相关的书籍！

## 客户记分卡

关键绩效指标（即 KPI）是用于评估客户环境中某项要素健康状况的度量标准。这个要素可能是客户的网络设备部署、苹果设备管理、利用社交媒体触达客户的方式等等。我们不想将复杂的图表交给客户。因此，我曾见过一些公司使用贵金属、火箭、漫画人物等机制来轻松报告客户环境的健康状况。

许多公司也将展示 KPI 的方式称为健康检查。我见过一些组织使用净推荐值（即`NPS`）作为衡量公司健康状况的基础。

让我们看一个例子。假设你进行了一项调查，收到了 1000 份回复。如果 100 份回复来自贬损者（评分在 0-6 范围），300 份回复来自被动者（评分在 7-8 范围），那么剩下 600 份来自推荐者（评分为 9-10）。我们来计算一下，用推荐者的 60% 减去贬损者的 10%，得到总分 50。

我会说，你还有工作要做，但即使得分是 10，我也可能会这么说。任何高于 0 的分数都被认为是好分数，而超过 70 分则是世界级水平。我会把 50 分左右评为优秀分数。如果你在查看银行账户余额后只想关注一件事，那就看这个分数。但如果你想更进一步，我建议收集一系列 KPI，并将它们放入一个平衡计分卡中。

## 平衡计分卡

平衡计分卡（简称`BSC`）通常被称为`4x4`。这样称呼的原因是，它包含四个类别，每个类别有四个支撑属性，可以让你一目了然地了解业务状况。这让你比仅使用`NPS`获得更多的洞察，并且通常可以轻松地进行深入分析。我上大学时第一次开始使用它，并在整个职业生涯中一直沿用。我总是从财务列开始。



### 财务

最终，每个企业或业务单元都以其财务结果作为评判标准。无论员工或团队怎么想，不包含财务指标的一列都是一个错误。财务这一列显示的是好消息还是坏消息，你对员工越透明，他们就越能理解自己的表现如何影响公司的业绩。我曾听到过员工会在艰难时期离职的论点。但这没关系；请记住，虽然优秀的员工是商业中最难求的资源之一，但忠诚无疑是成为“好员工”的重要组成部分。

让我们看看我喜欢在平衡计分卡财务部分使用的一些指标：

-   **合同续签**：前面我们提到了净推荐值（NPS），这是客户告诉你他们愿意向他人推荐你公司的程度。但我喜欢合同续签的地方在于，这是客户用钱包投票的地方。话虽如此，如果你有很高的续签率但净推荐值很低，客户可能感觉被你的公司束缚住了，这是一个问题。你不可能一直提供糟糕的服务。
-   **现有客户增长**：企业需要增长。引进新客户很重要。留住客户也很重要。在现有客户中拓展业务是一个关键指标，它显示了你如何培养和维护现有客户的信任。
-   **新客户**：关于全新客户，这告诉了你对于你的市场而言，你的吸引力或易被找到的程度如何。你在销售和营销上的投入，以及你在构建服务产品方面的成果，都会在平衡计分卡中体现出来。
-   **毛利率**：关注利润率的好处在于它覆盖了运营费用。如果你不小心，很容易就会在获取和维护客户上花费超出其价值的成本。

其他我看到过的指标，通常用于解决组织内有时限性的问题，包括应收账款和应付账款。这两者都可以归入像现金流这样的单一属性，但通常我会在这里划清我要与员工分享内容的界限，因为这往往是信息过多了。是的，透明度是好的，但肯定有些地方你需要划定界限（我也将薪资归入此类——尽管薪资透明度是一个日益重要的话题，且在国际组织中并不一致）。

#### 备注

我喜欢薪资透明的概念，因为它对多元化、职级平等等方面有着巨大的影响。如果由我决定，一夜之间，世界上每家公司可能都会在互联网上公布所有薪资。但事实并非如此。除非每个人都这样做，否则很难说会发生什么。但无论对你的员工产生什么影响，如果你选择这样做，我都支持你，当然前提是你的薪资也在其中！

### 客户

财务是计分卡上最容易理解的大局指标。但根据我的经验，最有可能推动计分卡整个类别未来变化的因素是客户满意度。什么是满意度？通常是指你的组织高效工作、提供价值、良好沟通，并在服务交付中保持一致。让我们看看我喜欢包含在平衡计分卡客户部分的一些组成部分。

-   **净推荐值 (NPS)**：上一节已经描述了净推荐值，但概括来说，这是一个行业标准，也是我认为所有组织都应该追踪的最关键指标之一。
-   **客户满意度 (CSAT)**：与净推荐值类似，客户满意度是一个简单的评分。在这里，你要求客户对你的表现进行评分。我倾向于在用户关闭服务单时进行评分。这种功能目前已内置于大多数服务台解决方案中，使客户能够即时评估你的表现。
-   **平均关闭时间 (MTTC)**：平均需要多长时间来关闭服务单。确保按类别细分，因为每个类别可能有不同的服务等级协议。考虑到时间会变化，并且特定客户基本上可以购买更好的服务等级协议，我喜欢的一种方法是简单地统计你满足服务等级协议的频率。例如，如果六月份你开了 390 个服务单，其中指派并在规定时间内关闭的百分比是多少？
-   **最终用户评分**：这很棘手。大多数公司要么调查最终用户，要么调查他们在客户方的联系人（这往往与所支持设备群的规模相关）。让最终用户和你的客户联系人给你的表现打分。

客户应该满意。但你需要运营一个高效的组织。为了支持这一点，我们将在平衡计分卡的下一部分探讨内部流程。

### 内部流程

内部流程的存在是为了向客户提供一致性因素，控制成本，并让你在保持稳定高质量服务的同时，能够以越来越宽的利润率持续接纳新客户。我喜欢关注的一些流程属性包括：

-   **补丁部署时间**：从受*支持*的软件发布到它出现在计算机上所需的时间。这可能很棘手。我的意思是，如果软件有缺陷而你又决定不将其部署到你管理的计算机群上，那会发生什么？
-   **因补丁产生的呼叫**：我从未见过其他人追踪这个指标，所以可能只是我个人的想法，但对我来说，要适当缩短系统补丁的部署时间，唯一的方法就是自动化该流程。如果你在进行自动化，就需要减少该自动化对客户的影响。那么，追踪自动化的影响对于追踪你工作的成效就至关重要。
-   **通过自动化关闭的服务单（不计入平均关闭时间）**：几年前我开始纳入这个指标。那句老口号“我会用一个很小的 shell 脚本来取代你”并非完全准确。关键在于能够解放聪明且富有动力的员工，让他们免于执行琐碎的任务。每完成一个可以自动化的任务，你的团队就有时间处理其他服务单，或者更好地，进行业务创新。
-   **通过远程方式关闭的服务单百分比**：与通过自动化关闭服务单类似，你想让员工待在座位上。出差时间成本高且压力大。像`LogMeIn`、`TeamViewer`、`PCAnywhere`和`Bomgar`这样的工具能帮助你远程访问系统。客户和员工可能会对你使用这些工具的意愿有所抵触。处理好这些反对意见，因为这个百分比越高，从长远来看，你的组织就能获得越高的利润。

然后我总是把关于公司长期表现的内容留到最后。此外，我还见过人们包含纯运营性质的指标，例如在给定时间内关闭的服务单数量、用于关闭服务单的劳动力成本、开设的服务单总数（通常针对托管服务客户）、里程或费用报告的提交速度，以及其他一些非常短期的问题，好的系统会帮助你解决这些问题，而不是让你一直为小事抓狂。



### 学习与成长

不要只关注公司的短期业绩和财务表现。也要思考公司及员工的长期潜力。这将使你的公司成为一个理想的工作场所。

- **路线图完成情况：** 在软件开发公司，路线图规划了你想发布的功能。但我喜欢在服务型公司使用“路线图”这个词。从根本上说，路线图指明了公司的发展方向。这很可能是判断你是在深思熟虑地引领公司，还是仅仅随波逐流（无论好坏）的最佳长期指标。我喜欢构建一份包含开发新服务（例如 BDR、防病毒、CRM）的路线图，并跟踪这些服务的就绪状态、销售效率以及交付所需的时间。我也喜欢将自动化工作、集中式工单系统和设备管理系统的实施纳入我的路线图中。

- **资源分配：** 很多员工在忙碌时最开心。但你应该控制期望值，并确保团队有充足的时间学习。我喜欢设定一个目标资源分配率。当分配率超过目标时，就该招聘了。当分配率低于目标时，就该在获取新客户方面发挥创意了。

- **员工满意度：** 你的员工喜欢他们的工作场所吗？他们喜欢哪些方面？文化是人们留在某个组织最重要的原因之一，同时也向你展示了员工的敬业程度和效率。你可能已经猜到了，我喜欢做调查。Culture Amp (`http://www.cultureamp.com`) 的好人们也喜欢。相比于自己制定指标，我更喜欢使用像 Culture Amp 这样的工具，原因在于，他们才是量化员工敬业度、员工体验和员工效能的专家。

- **学习计划完成情况：** 这完全关乎员工。永远不要忘记，咨询公司是服务业，而服务业最宝贵的部分通常是提供服务的人员。通常，正在学习的员工是快乐的员工。我们跟踪资源分配的原因是为了确保人员配置充足。制定学习计划有助于引导团队朝着正确的方向发展。跟踪他们在学习计划上的进度，可以衡量那部分时间的有效性，同时为每个团队成员提供灵活性，让他们以自己需要的方式学习，而不是你认为他们应该学习的方式。

一切皆有可能。所以，如果你希望将这些内容全部呈现在一个可以展示在办公室 Apple TV 上的网页中，那么你就可以做到。实现它可能需要大量工作，因此，如果需要进行一些手动数据录入或设置自动化流程将指标存入数据库，那就去做吧。这一切都是值得的。

关于记分卡的最后一个要点。在可能的情况下，客户应该能够看到平衡计分卡（BSC），或者至少是其中面向客户的部分。过去，与客户分享的合理类别包括：学习与成长、客户、以及内部流程（或至少是这些类别的一部分）。在我尝试在不同系统之间建立连接时，我经常不得不转而使用 API 从各种不同的系统（例如 Autotask、QuickBooks 和 Jamf）提取数据到中央数据库。尽管销售人员的承诺天花乱坠，也曾有供应商声称可以自动神奇地为我提供漂亮的仪表盘，但我从未见过一个能不经大量定制就覆盖我所用的每个系统的方案。

## 测试

永远不要相信任何销售人员。尤其当我也在销售电话中时更是如此。尽管对方可能心怀好意，但你仍可能被骗。好吧，不是被骗，但他们的回答可能没有你询问的具体问题所涉及的信息那么准确。或者你可能只听到了你想听的内容。关键是，你需要亲眼验证，以避免任何潜在的沟通误解。不要轻信系统工程师、销售员、甚至工具实际开发者的话。在你承诺购买之前，你需要看到事情按照你想要的方式运行。否则，这个工具就只是雾件（vaporware）。当你要拼凑一堆工具，通过定义好的工作流程来构建一个综合性解决方案时，这个问题会更加突出。

那么，你应该如何测试这些复杂的工作流程呢？一旦你想出了一个可以处理的场景，就要思考如何将其贯穿整个端到端的流程。列出一份必备功能清单、一份你可以自行构建的功能清单，以及一份你不可或缺的功能清单。然后构建一个包含一系列任务中每个步骤的测试矩阵。接着，尝试配置你部署方案中的所有组件。

一旦你的部署方案开始运作，你不会想对它做太多改动。但你不得不这样做。软件更新会来，新功能会催生新的自动化流程，而每次你做出改动，你都想重复所有的测试。因此，我建议建立一个与生产环境尽可能一致的完整测试环境。



## 极客工具箱

本书接下来的几页将介绍一系列工具，这些工具将使 Apple 设备的使用更简便、更易实现自动化，且更具成本效益。这些工具是技术人员、DevOps 和帮助台将要使用的，此外还有一些业务线工具，它们可能为客户提供追加销售附加服务的好机会。

为了保持供应商中立，我尝试按类别以字母顺序列出解决方案。每个类别的简要说明及所列工具类别如下：

- **杀毒软件**：用于扫描 Mac 病毒和其他恶意软件的解决方案。

- **自动化工具**：用于自动化管理 Mac 的脚本类工具。

- **备份**：我强烈建议向您的客户（无论是家庭、小型企业还是大型企业）捆绑或转售某种形式的备份服务。必要时从备份中恢复设备的能力，是将成本控制在可接受水平、并在合理时间内将设备交还客户手中的最重要因素之一。

- **客户关系管理**：用于跟踪联系人及与联系人沟通的 Mac 友好型工具。

- **协作套件**：曾几何时，Mac 服务器很擅长提供共享日历、联系人和电子邮件。但大多数企业都不愿处理邮件服务器可能出现的停机后果。没有什么比电子邮件中断更能让您辛辛苦苦赢得的客户解雇您了。因此，虽然列出了 Mac 服务器，但为了最佳客户保持率，请考虑云选项。

- **DEP 启动画面和帮助菜单**：通过向用户提供更多信息，使 DEP 和服务台流程更加用户友好的工具。

- **开发工具、IDE 和文本编辑器**：用于构建脚本、编写和调试软件以及操作文本的工具。

- **数字标牌和自助服务终端**：我将其列入其中，是因为我知道许多组织通过代表客户转售或部署这些工具，创造了不错的小额额外收入流。我也有朋友仅围绕这些工具就创建了托管服务产品。总的来说，这是一个可能的新的收入来源，而且额外的好处是，您很可能获得一份 NFR（或非转售版软件），因此您可以在办公室拥有很酷的标牌（如果您喜欢这类东西的话）。

- **目录服务**：主要提供对共享服务目录的本地访问，并允许对这些服务进行单点登录的工具。

- **文件共享**：以 Mac 为中心的云端和本地文件共享与同步工具。

- **身份管理**：主要提供基于 SAML 的单点登录解决方案的提供商，这些方案联合了 Apple 设备的安全认证以访问基于 Web 的服务。

- **镜像与配置工具**：用于将设备置于特定状态或创建该状态的工具。这包括适用于传统 Mac 的工具以及为 iOS 构建的工具。

- **业务线**：传统上专注于 Mac 的解决方案，用于自动化各种业务功能。

- **日志收集与分析**：集中式日志记录对于大型、不断增长的设备群来说一直是一项必需品。现代工具可以从客户端计算机存储大量日志，并允许快速和复杂的搜索，从而使您能够快速有效地定位问题。作为额外的好处，您还可以集中管理网络设备的日志，从而能够在一个完整的设备生态系统中隔离问题的根源。

- **管理套件**：用于管理 Apple 设备设置的工具。每个都标记为 MDM、基于代理或两者兼有。

- **打印服务器**：提供打印机访问或允许更精细打印功能（如成本核算）的服务器。

- **远程控制与管理**：这些工具允许您控制设备的屏幕、键盘和鼠标。我无法告诉您哪些是最好的。但我可以告诉您，我希望我的远程控制解决方案是跨平台的、基于云的、能提示用户接受远程控制会话的，并能审计连接，以便我知道谁在接管哪些设备。

- **打印服务器**：我一直讨厌打印机。无论是老式的 Fiery 打印服务、常见的基于 LPR 的打印机，还是共享打印服务之一，我仍然无法忍受管理打印机。打印机卡纸、损坏、驱动程序似乎在每次操作系统更新时都充满问题，打印机通常通过 ad-hoc 网络（如 Bonjour）连接，并且您通常需要特殊软件才能访问其酷炫功能。总而言之，打印机糟透了，但这些工具可能会让它们的使用稍微容易一些，或者如果没有，它们能帮助核算谁在使用打印机，以便您的客户尽可能多地向其部门收费。

- **销售点**：类似于数字标牌，但您也可能在这些解决方案中运营店面或跟踪客户数据。

- **远程管理**：用于控制 Apple 设备屏幕的工具。

- **安全工具**：用于管理防火墙、FileVault 以及执行其他基于给定组织安全态势所需的安全 Mac 任务。

- **服务台工具**：这些工具用于工单和工单管理。如果您能选择一个既与您的计费解决方案集成，又与您选择使用的其他各种极客工具集成的工具，那将非常棒。

- **软件打包与包管理**：用于在 Apple 平台上规范软件以进行大规模分发的工具。

- **存储**：专注于 Apple 的文件共享解决方案。

- **故障排除、维修与服务工具**：用于修复硬盘逻辑问题、检查硬件故障、修复各种系统问题或仅用于清理 Mac 的工具。

- **虚拟化与仿真**：并非所有软件都能在 Mac 上运行。客户可能会有某些需要 Windows 机器的任务。您可以使用 Citrix 或 Microsoft 终端服务器来满足这一潜在需求。或者，特别是如果用户需要在离线时从其 Windows 应用程序获取数据，您可以使用本地虚拟化工具。



## 杀毒软件

- [AVG](http://www.checkoutapp.com/)：提供基础的病毒与间谍软件检测及清除功能。
- [Avast](https://www.pingidentity.com/?gclid=EAIaIQobChMIgqS41MS-1wIV04KzCh2TLwdeEAAYAyAAEgKDMPD_BwE)：集中式杀毒软件，配备云端控制台，可追踪安全事件与设备状态。
- [Avira](https://atom.io/)：杀毒软件及浏览器扩展。`Avira Connect` 允许您在线查看设备状态。
- [BitDefender](https://puppet.com/)：通过中央控制台管理病毒和恶意软件。
- [CarbonBlack](https://macadmins.herokuapp.com/)：提供杀毒与 `Application Control`（应用程序控制）功能。
- [Cylance](http://grandperspectiv.sourceforge.net/)：除标准杀毒功能外，还针对勒索软件、高级威胁、无文件恶意软件及恶意文档提供防护。
- [Kaspersky](https://github.com/rtrouton/Simple-Package-Creator)：杀毒软件，配备集中式云仪表板以追踪设备状态。
- [Malware Bytes](http://mothersruin.com/software/SuspiciousPackage/)：通过中央控制台管理病毒和恶意软件。
- [McAfee Endpoint Security](http://s.sudre.free.fr/Software/Packages/about.html)：杀毒及高级威胁管理，配备集中式服务器以追踪设备。
- [Sophos](http://netatalk.sourceforge.net/)：通过中央控制台管理病毒和恶意软件。
- [Symantec Mobile Device Management](http://www.microsoft.com/)：通过中央控制台管理病毒和恶意软件。
- [Trend Micro Endpoint Security](https://github.com/unixorn/luggage)：在集中式控制台中提供应用程序白名单、杀毒及勒索软件防护功能。
- [Wandera](https://github.com/munki/munki-pkg)：恶意热点监控、越狱检测、移动威胁检测的 Web 网关，可与常见 MDM 解决方案集成。

## 自动化工具

- [AutoCasperNBI](http://www.kioskproapp.com/)：自动创建用于 `Casper Imaging` 的网络启动镜像（即 NBI）。
- [AutoDMG](https://www.symantec.com/products/endpoint-management)：获取 macOS 安装器（10.10 或更高版本），构建适用于 `Imagr`、`DeployStudio`、`LANrev`、`Jamf Pro` 以及其他基于 `asr` 或 `Apple Systems Restore` 的镜像工具部署的系统镜像。
- [AutoNBI](https://github.com/bruienne/autonbi)：自动构建和定制 Apple 网络安装镜像。
- [Dockutil](http://macpaw.com/cleanmydrive)：用于管理程序坞项目的命令行工具。
- [Homebrew](https://www.avg.com/)：macOS 的包管理器。
- [Cakebrew](https://itunes.apple.com/gb/app/disk-doctor-clean-your-drive/id455970963)：为 `Homebrew` 提供漂亮的图形用户界面。
- [Jamjar](https://www.alsoft.com/DiskWarrior/)：将 `jamf`、`autopkg` 与 `munki` 协同整合，从各产品的核心能力中精选功能，创建一个创新、可扩展且模块化的更新框架。
- [MacPorts](https://objective-see.com/products.html)：一项开源社区倡议，旨在设计一个易于使用的系统，用于在 Mac 上编译、安装和升级基于命令行、X11 或 Aqua 的开源软件。
- [Precache](https://www.synology.com/en-us)：以编程方式缓存 Mac 和 iOS 更新，无需等待设备向本地缓存服务器发起缓存请求。
- [Outset](https://itunes.apple.com/us/app/macos-server/id883878097)：在启动序列、用户登录时或按需自动处理软件包、配置文件及脚本。

## 备份

- [Acronis](https://duo.com/)：集中管理的备份，支持基于镜像的恢复。
- [Archiware](https://macmule.com/projects/autocaspernbi/)：集中管理的磁盘及磁带备份，配备多种代理用于备份常见的 Apple 需求，例如 `Xsan`。
- [Arq](http://www.libimobiledevice.org/)：一次性付费的云端备份，提供无限存储空间。
- [Backblaze](http://developer.apple.com/)：不限量的连续备份，支持 30 天回滚功能。
- [Carbon Copy Cloner](https://www.bresink.com/osx/HardwareMonitor.html)：适用于 macOS 的文件或磁盘级克隆工具。
- [Carbonite](https://www.malwarebytes.com/)：为 Mac 客户端提供 SaaS 或本地服务器备份。
- [Crashplan](http://www.memtestosx.org/)：备份至云端及本地存储，拥有出色的重复数据删除引擎。
- [Datto](https://www.prosofteng.com/drive-genius-mac-protection-software/)：本地和云端备份与恢复，并为多种服务提供云端故障切换。
- [Druva](https://www.symantec.com/mobile-device-management/)：为本地计算机提供备份，也为部分云服务提供备份。
- [Quest Backup（原 Netvault）](https://itunes.apple.com/us/app/easyfind/id411673888)：可将 Mac 客户端及 `Xsan` 卷备份至集中式磁带或磁盘备份服务器。
- [SuperDuper!](https://istumbler.net/)：将卷的内容复制到其他磁盘。
- **Time Machine**：macOS 内置的备份工具。

## 协作套件与文件共享

- [Atlassian](http://twocanoes.com/push-diagnostics)：面向开发的套件，包含 Wiki（`Confluence`）、问题跟踪（`Jira`）、消息传递（`HipChat`）及其他工具。
- [Box](https://www.mobileguardian.com/)：云端文件共享。
- [Dropbox](https://www.macbartender.com/)：云端文件共享。
- [Egnyte](https://www.bresink.com/osx/TinkerTool.html)：缓存来自流行云服务的资源，使其在频繁访问的网络环境中加载更快。
- [G Suite](https://www.amazon.com/Breakthrough-Company-Companies-Extraordinary-Performers/dp/0307352196)：共享邮件、联系人、日历。提供群件功能，可通过 Apple 内置工具、Microsoft Outlook 及 Web 访问。
- [Kerio Connect](https://veertu.com/)：共享邮件、联系人、日历。提供群件功能，可通过 Apple 内置工具、Microsoft Outlook 及 Web 访问。
- [Office 365](https://nmap.org/?ms.url=office365com)：共享邮件、联系人、日历。提供群件功能，可通过 Apple 内置工具、Microsoft Outlook 及 Web 访问。

## 客户关系管理

- [Daylite](https://www.macports.org/)：用于管理联系人与沟通的 Mac 工具。
- [Hike](https://www.omnigroup.com/more)：用于管理联系人与沟通的 Mac 工具。
- [GroCRM](https://www.titanium-software.fr/en/onyx.html)：用于管理联系人与沟通的 iOS 工具。

## DEP 启动屏与帮助菜单

- [ADEPT](https://www.virtualbox.org/)：为 DEP 注册添加启动屏，让用户了解设备当前执行的操作。
- [DEPNotify](http://www.jamf.com/)：为 DEP 注册添加启动屏，让用户了解设备当前执行的操作。
- [HelloIT](https://www.quest.com/kace/)：可自定义的帮助菜单，用户可借此获取系统信息或 IT 支持。
- [MacDNA](https://twocanoes.com/products/mac/winclone/)：可自定义的帮助菜单，用户可借此获取系统信息或 IT 支持。
- [SplashBuddy](https://bombich.com/)：为 DEP 注册添加启动屏，让用户了解设备当前执行的操作。



### 开发工具、集成开发环境与文本编辑器

- [**aText**](https://www.robotcloud.net/dashboard/)：将你定义的缩写替换为常用短语。
- [**Atom**](https://www.citrix.com/)：一款现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**BBEdit**](https://www.parallels.com/)：一款现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**Charles Proxy**](http://www.derlien.com/)：一款代理工具，可用于检查流量，以便通过编程方式重现流量，或在解决问题或构建工具时逆向分析发生的情况。
- [**CocoaDialog**](https://github.com/chilcote/vfuse)：创建比 AppleScript 等传统工具更优质的对话框。
- [**Coda**](https://github.com/chilcote/outset)：一款集成开发环境兼现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**Dash**](https://www.manageengine.com/)：可离线访问超过 150 个 API 文档集。
- [**Docker**](https://github.com/wdas/reposado)：容器化工具。
- [**FileMaker**](http://www.filemaker.com/)：来自 Apple 的快速应用开发软件。
- [**git**](https://www.amazon.com/What-Digital-Future-Holds-Groundbreaking/dp/0262534991)：代码版本控制、合并和跟踪——结合 GitHub，可提供一个存放和共享代码的仓库。
- [**Hopper Disassembler**](https://brew.sh/)：作为逆向工程和安全测试的一部分，对二进制文件进行反汇编。
- [**Microsoft Visual Studio**](https://www.amazon.com/Enchantment-Changing-Hearts-Minds-Actions/dp/1591845831)：支持多种语言的集成开发环境。
- [**MySQL Workbench**](https://www.amazon.com/Rework-Jason-Fried/dp/0307463745)：创建和编辑 MySQL 数据库，并用于构建复杂查询。
- [**Navicat Essentials**](https://peakhourapp.com/)：创建和编辑 MySQL 数据库，并用于构建复杂查询。
- [**Pashua**](https://www.amazon.com/Automate-Grow-Blueprint-Businesses-Marketing/dp/1977842216)：从对 Mac OS X 图形用户界面支持有限或完全不支持的编程语言（如 AppleScript、Bash 脚本、Perl、PHP、Python 和 Ruby）中创建原生 Aqua 对话框。
- [**Platypus**](https://cocoadialog.com/)：将 shell 脚本或 Perl、Ruby 和 Python 程序等解释型脚本创建为原生 Mac OS X 应用程序。
- [**Script Debugger**](http://puppetlabs.com/)：提供类似字典浏览器的工具以及更具集成开发环境特征的功能，用于构建 AppleScript 应用程序。
- [**SequelPro**](https://www.djangoproject.com/)：创建和编辑 MySQL 数据库，并用于构建复杂查询。
- [**Snippets Manager**](https://www.carbonblack.com/)：收集和组织代码片段。
- [**SourceTree**](https://www.cylance.com/en_us/home.html)：适用于 Git 和 GitHub 的图形用户界面工具。
- [**SublimeText**](https://www.elastic.co/)：一款现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**TextExpander**](https://www.amazon.com/Myth-Revisited-Small-Businesses-About/dp/0887307280)：将你定义的缩写替换为常用短语。
- [**TextWrangler**](https://www.mcafee.com/sg/products/endpoint-protection/index.aspx)：一款现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**Tower**](https://home.sophos.com/)：一款现代化的文本编辑器，功能丰富，可像集成开发环境一样用于常见脚本语言。
- [**VisualJSON**](http://www.microsoft.com/)：适用于 Mac 的简单 JSON 美化查看器。
- [**Xcode**](https://www.bluem.net/en/projects/pashua/)：Apple 的工具，用于使用常见语言编写应用程序和脚本。

### 数字标牌与信息亭

- [**Carousel Digital Signage**](https://www.grocrm.com/)：通过 AppleTV 运行数字标牌。
- [**Kiosk Pro**](https://www.itglue.com/)：将任何 iPad 转变为单用户信息亭工具，可通过 API 进行管理（例如，与 Jamf Pro 集成）。
- [**Risevision**](https://github.com/MagerValp/AutoDMG)：通过 Mac 运行数字标牌。

### 目录服务与身份验证工具

- [**Apple Enterprise Connect**](https://www.micromat.com/products/techtool-pro)：Apple 销售的工具，无需绑定到 Active Directory 即可连接到 Active Directory 环境。
- [**AdmitMac**](https://github.com/)：增加对边缘 Active Directory 需求的支持。
- [**JumpCloud**](https://www.chef.io/chef/)：在云中运行你的目录服务。
- [**LDAP**](https://www.cakebrew.com/)：开源目录服务。
- [**macOS Server Open Directory**](https://github.com/dataJAR/jamJAR?mt=12)：macOS Server 中安装的目录服务，基于 OpenLDAP。
- [**Microsoft Active Directory**](https://www.amazon.com/Small-Business-Time-Saver-Processes/dp/1514801124)：来自 Microsoft 的集中式目录服务。
- [**Nomad**](http://www.deploystudio.com/)：无需绑定到 Active Directory 即可将客户端连接到 Active Directory 环境。此外，还有其他一些便捷功能。

### 身份管理

- [**Centrify**](https://github.com/google/restor)：提供跨常见 Web 服务和其他支持 SAML 的解决方案的联合登录，并解决 Active Directory 的常见问题。还集成了用于合规性的配置文件管理工具。
- [**Duo Mobile**](https://www.vendhq.com/)：在安全身份领域提供的额外选择，在 Apple 领域有许多出色的研究成果。
- [**LastPass Enterprise**](https://www.papercut.com/)：提供跨常见 Web 服务和其他支持 SAML 的解决方案的联合登录。
- [**Microsoft Azure Active Directory**](https://www.arqbackup.com/)：Azure 云中的 Active Directory。
- [**Okta**](https://www.sourcetreeapp.com/)：提供跨常见 Web 服务和其他支持 SAML 的解决方案的联合登录。
- [**OneLogin**](https://www.labtech.com/)：提供跨常见 Web 服务和其他支持 SAML 的解决方案的联合登录。
- [**Ping Identity**](https://www.carbonite.com/)：提供跨常见 Web 服务和其他支持 SAML 的解决方案的联合登录。

### 映像与配置工具

- [**Apple Configurator**](https://www.barebones.com/products/textwrangler/?mt=12)：批量配置 iOS 和 tvOS 设备，自动化 MDM 注册，并分发数据。
- [**Blast Image Config**](https://www.datto.com/)：鉴于设备映像的现状，将不再开发，但允许管理员快速将 Macintosh 恢复到已知状态（10.12.2 及以下版本）。
- [**createOSXInstallPackage**](https://www.crashplan.com/en-us/)：从“Install OS X.app”或 InstallESD.dmg 创建安装程序包（10.12.4 及以下版本）。
- [**Deep Freeze**](https://www.barebones.com/products/bbedit/)：冻结 Mac 的状态。
- [**DeployStudio**](https://www.druva.com/)：适用于 Mac 的免费映像服务器。
- [**Google Restor**](https://www.trendmicro.com/en_us/business/products/user-protection/sps/endpoint.html)：从单一源为 macOS 计算机制作映像。这是一个旨在机器上交互式运行的应用程序。
- [**Ground Control**](https://wandera.com/)：批量部署（和注册）iOS 设备。
- [**Imagr**](https://www.tynsoe.org/v2/geektool/)：对许多组织而言，可替代 DeployStudio 等工具，且无需在 OS X 服务器上运行。
- [**libimobiledevice**](https://github.com/rtrouton/Payload-Free-Package-Creator)：一套用于配置、检查、擦除 iOS 设备等的工具。
- [**WinClone**](https://github.com/scriptingosx/quickpkg)：创建用于部署到 Mac 上的 Windows 映像。



## 日志收集与分析

- [`ElasticSearch`](http://www.cultureamp.com/)：开源，非常快速的日志分析工具。
- [`RobotCloud Dashboard`](https://itunes.apple.com/us/app/lingon-3/id450201424)：提供对由 `Jamf Pro` 管理的设备的更精细、更直观的可视性。
- [`Splunk`](http://www.kerio.com/products/kerio-connect/server)：大数据日志分析工具。
- [`Tableau`](https://products.office.com/en-US/)：大数据分析工具。
- [`Watchman Monitoring`](https://www.marketcircle.com/)：专注于 Mac 的监控代理，可检查常见的第三方工具。
- [`Zentral`](https://github.com/krypted/precache)：开源，基于 `ElasticSearch` 构建，并与许多其他工具以及针对 Mac 日志的自定义配方集成。

## 管理套件

- [`Addigy`](https://www.amazon.com/How-Startup-Hero-Textbook-Entrepreneurs-ebook/dp/B078HWH29T)：基于代理和 MDM
- [`AirWatch`](https://www.acronis.com/)：基于 MDM 和代理
- [`Altiris`](http://applejack.sourceforge.net/)：基于代理
- [`Apple Profile Manager`](https://itunes.apple.com/us/app/macos-server/id883878097?mt=12)：MDM
- [`BigFix`](https://github.com/kcrawford/dockutil)：基于代理
- [`Chef`](http://www.xirrus.com/wifi-inspector)：基于代理
- [`ConnectWise`](http://www.trankynam.com/atext/)：功能有限的基于代理的 Mac 管理，主要面向 MSP（托管服务提供商）
- [`FileWave`](https://github.com/munki/createOSXinstallPkg)：基于 MDM 和代理
- [`Fleetsmith`](https://www.git-tower.com/)：基于代理和 MDM
- [`IBM MaaS360`](https://www.charlesproxy.com/)：MDM
- [`Ivanti`](http://www.quest.com/)：基于 MDM 和代理
- [`Jamf Now`](http://www.shirt-pocket.com/SuperDuper/SuperDuperDescription.html)：面向小企业的 MDM
- [`Jamf Pro`（原名 `Casper Suite`）](https://www.vmware.com/products/fusion.html)：基于 MDM 和代理
- [`KACE`](https://github.com/google/macops-planb)：基于代理
- [`Kaseya`](https://www.backblaze.com/)：面向托管服务提供商的基于代理的 Mac 管理
- [`Labtech`](https://usa.kaspersky.com/)：基于代理
- [`LanRev`](https://gsuite.google.com/)：基于 MDM 和代理（目前正逐步淘汰）
- [`Lightspeed Mobile Manager`](https://code.visualstudio.com/)：MDM
- [`Meraki Systems Manager`](http://dev.mysql.com/downloads/workbench/)：MDM
- [`microMDM`](https://navicat.com/)：开源 MDM
- [`Microsoft Intune`](https://hikeup.com/mac-pos-system/)：(MDM) 和 [`SCCM`](https://www.addigy.com/)：(基于代理)
- [`Manage Engine`](https://www.amazon.com/Enterprise-Administrators-Guide-CHARLES-EDGE/dp/1484217055)：基于代理
- [`Mobile Guardian`](https://www.stellarinfo.com/)：MDM
- [`MobileIron`](https://www.amazon.com/Learning-iOS-Security-Allister-Banks/dp/1783551747)：MDM
- [`Mosyle`](https://www.egnyte.com/)：MDM
- [`Munki`](https://github.com/ftiff/SplashBuddy)：基于代理
- [`Parallels Mac Management for SCCM`](https://smilesoftware.com/textexpander)：面向 Mac 的基于代理的 `SCCM` 插件
- [`Profile Manager`（macOS 服务器版）](https://www.avira.com/?mt=12)：MDM
- [`Puppet`](http://clc.its.psu.edu/UnivServices/itadmins/mac/blastimageconfig)：基于代理
- [`Sal`](https://github.com/youknowone/VisualJSON)：基于代理的 `Munki`、`Puppet`、`Django` 和 `SB Admin 2` 的 SaaS 版本。
- [`SAP MobileSecure`](https://github.com/jhbush/Arek/tree/master/Development/MacDNA%2520Menulet)：MDM，可与其它 SAP 产品集成。
- [`SimpleMDM`](https://www.ibm.com/security/bigfix/)：MDM
- [`Solarwinds MSP`](https://www.hopperapp.com/)：基于代理，为托管服务提供商集成了备份和工单系统。
- [`Sophos`](https://www.openldap.org/)：MDM
- [`Tabpilot`](https://itunes.apple.com/us/app/macos-server/id883878097)：MDM
- [`Zuludesk`](https://enterprise.microsoft.com/)：MDM，带有父级管理应用

## 杂项

- [`Jamf NetSUS`](https://github.com/zentralopensource/zentral)：为 Jamf 服务器打包的 `Reposado`。
- [`InfineaIQ`](https://panic.com/coda/)：外设管理软件。
- [`ITGlue`](https://www.atlassian.com/)：将常用 IT 工具的凭证和信息存储在基于 SaaS 的数据库中。
- [`Reposado`](https://www.amazon.com/Enterprise-iPhone-Administrators-Guide-Professionals/dp/1430230096)：苹果软件更新服务器的开源实现。
- [`Sassafras Keyserver`](https://www.sassafras.com/)：集中式软件许可证管理服务器。
- [`ipaSign`](https://www.kaseya.com/)：以编程方式使用新密钥对 `ipa` 文件进行重新签名。

## 销售点（POS）系统

- [`Checkout`](https://www.sublimetext.com/)：可在苹果设备上运行的销售点解决方案。
- [`Lightspeed`](https://www.connectwise.com/)：可在苹果设备上运行的销售点解决方案。
- [`Paygo`](https://itunes.apple.com/us/app/apple-configurator-2/id1037126344)：可在苹果设备上运行的销售点解决方案。
- [`Posim`](http://www.faronics.com/products/deep-freeze/mac)：可在苹果设备上运行的销售点解决方案。
- [`Shopkeep`](https://www.ibm.com/security/mobile/maas360.html)：可在苹果设备上运行的销售点解决方案。
- [`SquareUp`](http://sveinbjorn.org/platypus)：可在苹果设备上运行的销售点解决方案。
- [`Vend`](https://www.groundctl.com/)：可在苹果设备上运行的销售点解决方案。

## 打印服务器

- [`Papercut`](https://github.com/grahamgilbert/imagr)：适用于 Mac 的打印机成本核算软件。
- [`Printopia`](https://www.renfei.org/snippets-lab/)：允许从 iOS 设备进行更好的打印。

## 远程管理

- [`Apple Remote Desktop`](https://itunes.apple.com/us/app/apple-remote-desktop/id409907375?mt=12)：苹果公司用于远程控制其他 Mac、向 Mac 发送软件包以及在局域网内或直接通过 IP 地址在 Mac 上运行脚本的工具。
- [`Bomgar`](http://mosyle.com/)：允许跨平台远程控制设备的设备。
- [`CoRD`](https://jumpcloud.com/)：RDP 客户端。
- [`LogMeIn`](http://splunk.com/)：跨平台远程控制工具。
- [`GoToMyPC`](https://www.tableau.com/)：跨平台远程控制工具。
- [`Remote Desktop`](https://www.watchmanmonitoring.com/)：Mac 上的官方 RDP 客户端。
- [`Remotix`](https://www.microsoft.com/en-us/cloud-platform/microsoft-intune)：功能丰富的 RDP 和 VNC 服务器。
- [`TeamViewer`](https://www.trms.com/carousel)：跨平台远程控制工具。



### 安全工具

- [**花椰菜背心**](https://www.air-watch.com/)：将 FileVault 密钥存储在中央服务器上。
- [**Crypt**](https://www.box.com/)：FileVault 2 托管解决方案。
- [**Digital Guardian**](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis)：数据丢失防护。
- [**Google Santa**](https://www.sap.com/products/secure-mobile-device-management-cloud.html)：Mac 的二进制黑名单与白名单管理。
- [**iOS 位置提取器**](https://www.onelogin.com/)：转储 iOS 和 macOS 上位置数据库文件的内容。
- [**iOS 频繁位置提取器**](https://www.parallels.com/products/mac-management/)：转储位于 `/private/var/mobile/Library/Caches/com.apple.routined/` 目录下的 `StateModel#.archive` 文件内容。
- [**Little Snitch**](https://www.filewave.com/)：提供关于哪些程序正在访问网络资源以及这些资源位置的信息。
- [**Objective-See**](https://www.fleetsmith.com/)：提供 `KnockKnock`、`Task Explorer`、`BlockBlock`、`RansomWhere?`、`Oversight` 和 `KextViewr` 等工具，用于查找计算机上运行的端口和服务的更多信息。
- [**Osquery**](http://salsoftware.com/)：以实时、精细的搜索方式查询 Mac 上的信息。
- [**Portecle**](http://centrify.com/)：创建和管理密钥库、密钥、证书、证书请求以及证书吊销列表。
- [**PowerBroker**](https://www.jamf.com/products/jamf-now/)：允许 Mac 上的标准用户执行管理任务，而无需输入提权凭据。
- [**Prey**](https://gitlab.com/Mactroll/DEPNotify)：在 Mac 和 iOS 设备被盗时进行追踪。

### 服务台工具

- [**Freshdesk**](https://www.docker.com/)：案例/工单管理，支持通过 Freshbooks 自动计费。
- [**Salesforce Cases**](https://www.dropbox.com/)：案例/工单管理，可与 SalesforceCRM 自动集成。
- [**ServiceNow**](http://www.thursby.com/products/admitmac)：案例/工单管理，拥有丰富的集成应用市场。
- [**Webhelpdesk**](http://www.sds-corp.com/products/unified-endpoint-management/heat-lanrev-client-management/)：案例/工单管理。
- [**Zendesk**](https://www.lightspeedsystems.com/products/mobile-manager/)：案例/工单管理，拥有丰富的集成应用市场。

### 软件打包与包管理

- [**Autopkg**](https://meraki.cisco.com/products/systems-manager/)：使用配方自动创建 Mac 软件分发包。
- [**CreateUserPkg**](https://github.com/micromdm/micromdm)：创建在安装时创建本地用户账户的软件包（适用于 10.12 及以下版本）。
- [**JSSImporter**](https://developer.apple.com/xcode/downloads/)：将 Autopkg 连接到 Jamf Pro。
- [**Iceberg**](https://www.microsoft.com/en-us/cloud-platform/system-center-configuration-manager)：创建 Mac 软件分发包。
- [**InstallApplication**](https://github.com/google/cauliflowervest)：动态下载用于 MDM 的 `InstallApplication` 命令的软件包。
- [**Jamf Composer**](https://www.zuludesk.com/)：创建 Mac 软件分发包。
- [**Luggage**](https://github.com/jamf/NetSUS)：开源项目，创建一个用于生成 Mac 的 pkg 包的封装器，以便通过检查 Makefile 不同版本之间的差异来对软件包进行同行评审。
- [**Munkipkg**](https://ipcmobile.com/infineaiq/)：一个简单的工具，能以一致、可重复的方式，从项目目录中的源文件和脚本构建软件包。
- [**Pacifist**](https://kapeli.com/dash)：一款共享软件应用程序，可打开 macOS 的 `.pkg` 包文件、`.dmg` 磁盘映像以及 `.zip`、`.tar`、`.tar.gz`、`.tar.bz2` 和 `.xar` 归档文件，并允许从中提取单个文件和文件夹。
- [**Payload Free Package Creator**](https://www.risevision.com/)：一个 Automator 应用程序，它在后台使用 AppleScript、Shell 脚本和 `pkgbuild` 来创建无有效负载的包。
- [**QuickPkg**](http://business-static.apple.com/us/apple-professional-services/Apple_Professional_Services_AD_Integration_Services.pdf)：创建 Mac 软件分发包。
- [**Simple Package Creator**](https://simplemdm.com/)：创建 Mac 软件分发包。
- [**Suspicious Package**](https://www.solarwindsmsp.com/)：查看 Mac 软件分发包的内容。
- [**Whitebox Packages**](https://www.sophos.com/en-us/products/endpoint-antivirus.aspx)：创建 Mac 软件分发包。

### 存储

- [**Netatalk**](https://www.tabpilot.com/)：从 Mac 到 Windows 及其他存储平台提供更佳的 AFP 连接。
- [**Promise**](https://posim.com/)：经过 Apple 验证的直接附加存储 (DAS)、存储区域网络 (SAN) 等。
- [**Synology**](http://nomad.menu/)：专门针对 Mac 工作流定制的存储设备。
- [**Xsan**](http://munki.github.io/munki/?mt=12)：Apple 内置的 SAN 文件系统。



### 故障排除、修复与服务工具

- [`AppCleaner`](https://www.beyondtrust.com/products/powerbroker-for-mac/)：清理 Mac 上不需要的文件。
- [`AppleJack`](http://www.sequelpro.com/)：当 Mac 无法完全启动时，从单用户模式修复磁盘/权限并清理缓存/交换文件。
- [`Bartender`](https://github.com/IronSummitMedia/startbootstrap-sb-admin-2)：管理 Mac 菜单栏中的项目。
- [`CleanMyDrive`](https://www.okta.com/)：将文件直接拖放到任意驱动器、查看磁盘状态，并自动清理外部驱动器中隐藏的垃圾文件。
- [`Data Rescue`](https://github.com/munki/munki)：Mac 数据恢复工具。
- [`Disk Doctor`](https://www.avast.com/lp-managed-workplace-rmm?mt=12)：修复逻辑驱动器并清理不需要的文件。
- [`DiskWarrior`](https://itunes.apple.com/us/app/macos-server/id883878097)：修复 Mac 上的逻辑卷损坏问题。
- [`Drive Genius`](https://get.gotomypc.com/)：自动监控硬盘错误、查找重复文件、允许重新分区卷、克隆卷、执行安全擦除和碎片整理。
- [`Disk Inventory X`](https://github.com/MagerValp/CreateUserPkg)：可视化显示 macOS 逻辑卷中的内容。
- [`EasyFind`](https://www.nulana.com/remotix-mac/?mt=12)：无需通过 Spotlight 建立索引即可查找任意文件、文件夹或内容。
- [`iStumbler`](http://teamviewer.com/)：Mac 无线发现工具，可定位 Wi-Fi 网络、蓝牙设备、Bonjour 服务，并执行频谱分析。
- [`GeekTool`](https://github.com/erikng/installapplications)：将脚本输出和日志直接显示在 Mac 桌面上。
- [`Google PlanB`](https://grahamgilbert.com/blog/2013/01/18/crypt-a-filevault-2-escrow-solution/)：通过安全下载磁盘映像，然后将设备纳入管理平台，修复偏离指定状态的 Mac。
- [`GrandPerspective`](http://www.salesforce.com/)：可视化显示 macOS 逻辑卷中的内容。
- [`Hardware Monitor`](https://www.bomgar.com/)：读取 Mac 上的硬件传感器信息。
- [`Lingon`](https://github.com/dorianj/CoRD?mt=12)：在 macOS 上创建、管理和删除 LaunchAgents 和 LaunchDaemons。
- [`Memtest OS X`](http://logmein.com/)：测试 Mac 中的每个 RAM 模块。
- [`nMap`](http://paygopos.com/)：高级端口扫描、网络映射和网络故障排除。
- [`Peak Hour`](https://www.bitdefender.com/)：网络性能、质量和用量监控。
- [`Omni DiskSweeper`](https://www.shopkeep.com/)：在 macOS 中查找并移除未使用的文件，以节省和回收磁盘空间。
- [`OnyX`](https://squareup.com/)：验证系统文件的启动磁盘和结构、执行维护和清理任务、配置设置（例如 Finder、Dock、Safari）、删除缓存，以及重建各种数据库和索引。
- [`Push Diagnostics`](http://latenightsw.com/)：测试 APNs 流量的端口和主机访问。
- [`Stellar Phoenix`](https://www.lastpass.com/enterprise)：Mac 数据恢复工具。
- [`TechTool Pro`](https://www.decisivetactics.com/products/printopia/)：硬盘修复、RAM 测试和数据保护。
- [`TinkerTool`](https://github.com/krypted/ipasign)：用于更改 Mac 偏好的图形界面，否则这些偏好需要通过 `defaults` 命令来管理。
- [`Xirrus Wi-Fi Inspector`](https://github.com/mac4n6/Mac-Locations-Scraper)：搜索 Wi-Fi 网络、进行现场勘测、排查 Wi-Fi 连接问题、定位 Wi-Fi 设备以及检测恶意应用。

### 虚拟化与模拟

- [`Anka veertu`](https://www.lightspeedhq.com/pos/retail/)：在 Mac 上运行虚拟机。
- [`Citrix`](http://www.zendesk.com/)：发布最终用户可使用标准 RDP 客户端从 Mac 连接的 Windows 应用会话。
- [`Parallels`](https://github.com/sheagcraig/JSSImporter)：在 Mac 上运行虚拟机。
- [`Microsoft Windows 终端服务器`](https://www.jamf.com/products/jamf-composer/)：发布最终用户可使用标准 RDP 客户端从 Mac 连接的 Windows 会话。
- [`vFuse`](https://github.com/sheagcraig/JSSImporter)：从尚未启动的 DMG 创建 VMware Fusion 虚拟机的脚本。
- [`VirtualBox`](http://s.sudre.free.fr/Software/Iceberg.html)：在 Mac 上运行虚拟机。
- [`VMware Fusion`](https://www.charlessoft.com/)：在 Mac 上运行虚拟机。

### 值得一提

- [`The MacAdmins Slack`](https://freshdesk.com/)：加入一个由 15,000 名负责管理大型 Apple 设备群的管理员组成的社区。
- [`Apple Developer Program`](https://www.mobileiron.com/)：注册开发者账户，以便获取其他途径无法获得的测试版资源和文档。
- **您的 Apple SE 或本地零售店**：获取信息的绝佳资源！
- 咖啡……大量的咖啡

## 结论

虽然我没有深入探讨单个解决方案或它们如何集成在一起的细节，但值得指出的是，这应该像一个流水线。自动化程度越高，您能完成的工作就越多，您就有更多时间用于为个别客户提供个性化关注，您的组织也就越具备高度的可扩展性。您创建的这个工具链会随着时间而变化，并需要随着新更新的到来而进行一些维护和“滋养”。各个组件也应该变得更加可互换。

市面上有很多工具。从中筛选出最合适的工具并不容易。但您可以四处打听，其他人肯定会很乐意帮助您。测试一切，然后再次测试，接着再测一次。一旦您感觉满意了，就在客户中进行小范围测试。并且对每个新工具或更新都这样做。

最后，最困难的是让您的部署方案在未来也适用。您不想每次技术栈变更时都重复进行同样的工作。相反，如果您在构建技术栈时着眼于未来，就能为自己省去很多工作。Apple 已经明确表示，Apple 管理的未来是 MDM，因此无论您做什么，都要确保有一个关于 MDM 管理的方案（即使它仍在路线图上）。



## 延伸阅读

- 《六西格玛手册》，托马斯·派兹德克、保罗·凯勒，[`https://www.amazon.com/Six-Sigma-Handbook-Fourth/dp/0071840532`](https://www.webhelpdesk.com/)
- 《魅力》，作者：盖伊·川崎，[`https://www.amazon.com/Enchantment-Changing-Hearts-Minds-Actions/dp/1591845831`](https://github.com/mac4n6/iOS-Frequent-Locations-Dumper)
- 《重来》，作者：贾森·弗里德、大卫·海涅迈尔，[`https://www.amazon.com/Rework-Jason-Fried/dp/0307463745`](https://www.obdev.at/products/littlesnitch/index.html)
- 《小企业节省时间指南：自动化业务流程、推广自我及在线取悦客户的工具》，安德鲁·麦考利、希瑟·波特、凯蒂·乔尔，[`https://www.amazon.com/Small-Business-Time-Saver-Processes/dp/1514801124`](https://www.promise.com/us/)
- 《自动化与增长：初创企业及中小企业实现营销、销售与客服自动化的蓝图》，迈克尔·德维拉诺，[`https://www.amazon.com/Automate-Grow-Blueprint-Businesses-Marketing/dp/1977842216`](https://osquery.io/)
- 《如何成为创业英雄：企业家与准企业家指南与教科书》，蒂姆·德雷珀，[`https://www.amazon.com/How-Startup-Hero-Textbook-Entrepreneurs-ebook/dp/B078HWH29T`](http://portecle.sourceforge.net/)
- 《在企业中管理 Mac》，威廉·史密斯、查尔斯·埃奇，[`https://www.amazon.com/Enterprise-Administrators-Guide-CHARLES-EDGE/dp/1484217055`](https://freemacsoft.net/appcleaner/)
- 《企业中的 iOS》，查尔斯·埃奇，[`https://www.amazon.com/Enterprise-iPhone-Administrators-Guide-Professionals/dp/1430230096`](https://www.preyproject.com/)
- 《学习 iOS 安全》，阿利斯特·班克斯、查尔斯·埃奇，[`https://www.amazon.com/Learning-iOS-Security-Allister-Banks/dp/1783551747`](https://digitalguardian.com/)
- 《数字未来何去何从》，麻省理工学院斯隆管理评论，[`https://www.amazon.com/What-Digital-Future-Holds-Groundbreaking/dp/0262534991`](https://github.com/google/santa)
- 《突破性公司：普通公司如何成为卓越表现者》，基思·R·麦克法兰，[`https://www.amazon.com/Breakthrough-Company-Companies-Extraordinary-Performers/dp/0307352196`](https://www.prosofteng.com/data-recovery-software/)
- 《再谈 E 神话》，迈克尔·E·格伯，[`https://www.amazon.com/Myth-Revisited-Small-Businesses-About/dp/0887307280`](https://www.amazon.com/Six-Sigma-Handbook-Fourth/dp/0071840532)

