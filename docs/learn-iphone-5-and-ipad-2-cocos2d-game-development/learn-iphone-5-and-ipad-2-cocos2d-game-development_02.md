# 目录

- 第 6 章：深入讲解精灵
  - 视网膜显示屏
  - CCSpriteBatchNode
  - 何时使用 CCSpriteBatchNode
  - 演示项目
  - 传统方式实现精灵动画
  - 动画辅助类目
  - 使用纹理图集
  - 什么是纹理图集？
  - TexturePacker 介绍
  - 为 TexturePacker 准备项目
  - 使用 TexturePacker 创建纹理图集
  - 在 cocos2d 中使用纹理图集
  - 更新 CCAnimation 辅助类目
  - 合为一体，一应俱全
  - 小结

- 第 7 章：滚动与摇杆控制
  - 高级视差滚动
  - 创建条纹状背景
  - 通过代码重新创建背景
  - 移动 ParallaxBackground
  - 视差速度因子
  - 无限滚动
  - 修复闪烁问题
  - 重复、重复、再重复
  - 虚拟摇杆
  - SneakyInput 介绍
  - 集成 SneakyInput
  - 触控按钮进行射击
  - 为按钮设计皮肤
  - 控制动作
  - 数字控制
  - 小结

- 第 8 章：射击游戏
  - 添加 BulletCache 类
  - 敌人如何处理？
  - 实体类层次结构
  - EnemyEntity 类
  - EnemyCache 类
  - 组件类
  - 射击机制
  - 为 BOSS 添加血条
  - 小结

- 第 9 章：粒子效果
  - 粒子效果示例
  - 传统方式创建粒子效果
  - 继承 CCParticleSystem：点还是四边形？
  - CCParticleSystem 属性
  - 粒子设计器
  - 粒子设计器介绍
  - 使用粒子设计器效果
  - 共享粒子效果
  - 结合粒子效果的射击游戏
  - 小结

- 第 10 章：使用瓦片地图
  - 什么是瓦片地图？
  - 使用 TexturePacker 准备图片



Tiled (Qt)地图编辑器

创建新瓦片地图

设计瓦片地图

在 Cocos2d 中使用正交瓦片地图

定位被触摸的瓦片

优化与可读性的实践

使用对象层

绘制对象层矩形

滚动瓦片地图

小结

![images](img/square.jpg)第 11 章：等距瓦片地图

设计等距瓦片图形

用 Tiled 编辑等距瓦片地图

创建新的等距瓦片地图

创建新的等距瓦片集

制定基本规则

等距游戏编程

在 Cocos2d 中加载等距瓦片地图

为等距瓦片地图配置 Cocos2d

定位等距瓦片

滚动等距瓦片地图

这个世界应有更好的结局

添加可移动玩家角色

为游戏添加更多内容

小结

![images](img/square.jpg)第 12 章：物理引擎

物理引擎的基本概念

物理引擎的局限性

对决：Box2D 与 Chipmunk

Box2D

Box2D 视角下的世界

限制移动至屏幕范围内

坐标转换

向 Box2D 世界添加盒子

连接精灵与刚体

碰撞检测

关节合作

Chipmunk

对象化的 Chipmunk

太空中的 Chipmunks

将盒子包围起来

添加小小的廉价盒子

更新盒子的精灵

Chipmunk 碰撞课程

Chipmunk 的关节

小结

![images](img/square.jpg)第 13 章：弹球游戏

形状：凸形与逆时针

使用 PhysicsEditor

定义柱塞形状

定义台面形状

定义挡板

定义缓冲器和球

保存并发布

编写弹球游戏程序



BodyNode 类

创建弹球台

Box2D 调试绘制

添加球体

强制球体运动

添加缓冲器

弹射器

挡板

总结

![images](img/square.jpg)第 14 章：Game Center

启用 Game Center

在 iTunes Connect 中创建应用

设置排行榜和成就

创建 Cocos2d Xcode 项目

配置 Xcode 项目

Game Center 设置总结

Game Kit 编程

GameKitHelper 代理

检查 Game Center 可用性

验证本地玩家身份

块对象

接收本地玩家的好友列表

排行榜

成就

匹配机制

发送和接收数据

总结

![images](img/square.jpg)第 15 章：Cocos2d 与 UIKit 视图

什么是 Cocoa Touch？

同时使用 Cocoa Touch 和 cocos2d

为何将 Cocoa Touch 与 cocos2d 混合使用？

混合使用 Cocoa Touch 与 cocos2d 的限制

Cocoa Touch 与 cocos2d 有何不同？

提醒：你在 cocos2d 中的第一个 UIKit 视图

在 cocos2d 应用中嵌入 UIKit 视图

在 cocos2d 视图前方添加视图

使用 UIImage 为 UITextField 添加外观皮肤

在 cocos2d 视图后方添加视图

添加使用 Interface Builder 设计的视图

自动旋转方向教程

在 Cocoa Touch 应用中嵌入 cocos2d 视图

使用 cocos2d 创建基于视图的应用项目

设计混合应用的用户界面

启动你的 cocos2d 引擎

停止并重启 cocos2d 引擎

切换场景

总结

![images](img/square.jpg)第 16 章：Kobold2D 介绍

使用 Kobold2D 的好处

Kobold2D 即开即用

Kobold2D 是免费的

Kobold2D 易于升级

Kobold2D 提供“库服务”

Kobold2D 致力于跨平台

Kobold2D 工作区

Hello Kobold2D 模板项目

Hello World 项目文件

Kobold2D 如何启动应用

Hello Kobold2D 场景与图层

使用 iSimulate 运行 Hello World

适用于 Mac 的 Doodle Drop 与 KKInput

cocos3d 带你进入三维世界

AppDelegate 类的变更

cocos3d 眼中的世界

将 cocos3d 添加到现有的 Kobold2D 项目

总结

![images](img/square.jpg)第 17 章：别出心裁

学习与工作的额外资源

寻求帮助的途径

值得借鉴的源代码项目

Cocos2D 播客

工具，工具，工具

Cocos2d 参考应用

游戏制作的商业之道

与发行商合作

寻找自由职业者

寻找免费的艺术与音频资源

寻找行业工具

市场营销

提高玩家参与度以增加收入

总结

![images](img/square.jpg)索引



## 关于作者

![image](img/steffen_itterheim.jpg) **斯特芬·伊特海姆**自 20 世纪 90 年代初起便是一名游戏开发爱好者。他在《毁灭战士》和《毁灭公爵 3D》社区中的工作为他赢得了第一份自由职业——担任 3D Realms 的测试员。他拥有超过十年的专业游戏开发经验，职业生涯大部分时间在 Electronic Arts Phenomic 担任游戏玩法与工具程序员。2009 年，他首次接触`cocos2d`，同年与人共同创立了雄心勃勃的 iOS 游戏初创公司 Fun Armada。他热爱教学并乐于帮助其他游戏开发者，让他们能够更聪明地工作，而非更辛苦。偶尔，你会看到他在白天漫步于住所附近的郁郁葱葱的葡萄园，而夜晚则在内华达沙漠中收集瓶盖。

![image](img/andreas_low.jpg) **安德烈亚斯·勒夫**从 10 岁拥有第一台 Commodore C16 电脑时起，就成了一个电脑狂人。他自学编写游戏，并于 1994 年用纯汇编语言为 Commodore Amiga 平台发布了首款电脑游戏《Gamma Zone》。获得电气工程学位后，他任职于哈曼国际，负责为汽车行业开发带有语音识别功能的导航和信息娱乐系统。他发明了自己的编程语言和开发工具，这些工具目前已被全球各地搭载语音识别技术的汽车所采用。

借助 iPhone，他回归初心，开始开发一款名为`TurtleTrigger`的游戏。他意识到 cocos2d 社区对优秀工具有着巨大的需求。凭借其在游戏和工具开发方面的专业知识，他的产品`TexturePacker`和`PhysicsEditor`迅速成为所有`cocos2d`用户不可或缺的核心开发工具。

## 关于技术审校者

![image](img/boon_chew.jpg) **文布恩**是 Nanaimo Studio 的董事总经理，这家游戏工作室总部设在西雅图和上海，专注于网页和移动游戏。他在游戏开发和互动媒体方面拥有丰富经验，曾任职于维旺迪环球、亚马逊、微软等公司，以及多家游戏工作室和广告公司。他的热情在于创造事物并与优秀的人共事。你可以通过[`boon@nanaimostudio.com`](http://boon@nanaimostudio.com)联系布恩。

## 前言

2009 年 5 月，我首次与`cocos2d`结缘。那是人生中第一次接触 Mac OS 平台，并开始学习 Xcode、Objective-C 和`cocos2d`。即使对于我和同事这样经验丰富的开发者来说，这也是一场艰苦的战斗。就在那时，我意识到`cocos2d`虽然优秀，但缺乏文档、教程和操作指南——尤其是与我当时正在学习的其他技术相比。

时间快进到一年后的 2010 年 5 月。我完成了四个`cocos2d`项目。我对 Objective-C 和`cocos2d`已能熟练运用。看到其他开发者仍在为同样的基本问题苦苦挣扎，并重蹈我一年前落入的相同误区，这让我感到痛心。而`cocos2d`的文档依然严重匮乏。

我注意到，其他`cocos2d`开发者通过撰写教程、分享自己对`cocos2d`的了解，成功吸引了大量读者访问他们的博客。迄今为止，大部分`cocos2d`文档都是由其他开发者以去中心化的方式积极创建的。我意识到需要一个网站来整合分散在众多不同网站上的所有信息。

于是，我创建了[`www.learn-cocos2d.com`](http://www.learn-cocos2d.com)网站，旨在分享我所了解的关于`cocos2d`和游戏开发的知识，撰写教程和常见问题解答，并为对`cocos2d`感兴趣的读者指引所有重要的信息来源。作为回报，我会销售一些与`cocos2d`相关的产品，希望有朝一日能让我接近实现财务独立的最终目标。我知道我能让这个网站成为对所有人都有益的平台。

从第一天起，这个网站就取得了成功——其程度远超我最疯狂的想象。随后，就在网站上线后的 24 小时内，杰克·纳廷问我是否考虑过写一本关于`cocos2d`的书。接下来的事便众所周知了，结果就是你现在正在阅读的这本书。

我把为网站构思的所有内容都写进了书里。但仅凭这些内容，最多也只占了本书的四分之一。我希望我全职投入四个月编写这本书的付出是值得的，能够提供关于`cocos2d`如何运作以及如何使用`cocos2d`的、前所未有的详尽指导。

在这个过程中我学到了很多，在随后数月更新第二版各章节时更是收获颇丰。我最大的愿望就是你能从本书中学到大量关于`cocos2d`和游戏开发的知识。

通过撰写关于`cocos2d`的内容，我意识到它仍有很大的改进空间。我坚信这个世界需要一个对初学者更友好的、更好的`cocos2d`。其成果便是`Kobold2D`，你将在第 16 章以及[`www.kobold2d.com`](http://www.kobold2d.com)上找到相关介绍。当然，你在本书中学到的几乎所有内容仍然适用于`Kobold2D`。

斯特芬·伊特海姆

## 第 1 章



