# 目录

关于作者

关于技术审校

致谢

前言

![images](img/sq1.jpg)第 1 章：引言

第三版有哪些新内容？

关于 ARC

为何在 iOS 上使用 cocos2d？

免费

开源

面向对象，明白吗？

它是 2D 的

它支持物理引擎

技术门槛更低

仍然是编程

拥有出色的社区

为何选择 Kobold2D 而非 cocos2d-iphone？

其他 cocos2d 游戏引擎

这本书适合你

预备知识

编程经验

Objective-C

你将学到什么

iOS 游戏开发初学者将学到什么

iOS 应用开发者将学到什么

Cocos2d 开发者将学到什么

本书内容概览

第 2 章：“入门”

第 3 章：“核心要素”

第 4 章：“你的第一个游戏”

第 5 章：“游戏构建模块”

第 6 章：“精灵深入解析”

第 7 章：“滚动之乐”

第 8 章：“射击游戏”

第 9 章：“粒子效果”

第 10 章：“使用瓦片地图”

第 11 章：“等角瓦片地图”

第 12 章：“物理引擎”

第 13 章：“弹珠游戏”

第 14 章：“Game Center”

第 15 章：“Cocos2d 与 UIKit 视图”

第 16 章：“Kobold2D 简介”

第 17 章：“结语”

如何获取本书源代码

问题与反馈

![images](img/sq1.jpg)第 2 章：入门

开始前需要准备什么

系统要求

注册为 iOS 开发者

证书与配置文件

下载并安装 Xcode 和 iOS SDK



下载 cocos2d 或 Kobold2D  
安装 Kobold2D  
创建 Kobold2D 项目  
安装 cocos2d 及其 Xcode 项目模板  
如何创建 cocos2d 项目  
如何在 cocos2d 项目中启用 ARC  
cocos2d 与 Kobold2D 应用程序结构剖析  
支持文件组  
使用 ARC 进行内存管理  
改变世界  
你还需要了解的其他内容  
iOS 设备  
关于内存使用  
iOS 模拟器  
关于性能与日志记录  
总结  

![images](img/sq1.jpg) 第 3 章：基础要点  
cocos2d 场景图  
CCNode 类层次结构  
CCNode  
使用节点  
使用动作  
定时消息  
导演、场景与层  
导演  
CCScene  
场景与内存  
压入与弹出场景  
CCTransitionScene  
CCLayer  
CCSprite  
锚点揭秘  
CCLabelTTF  
菜单  
带 Blocks 的菜单项  
动作  
间隔动作  
即时动作  
方向、单例、测试与 API 参考  
设备方向入门  
cocos2d 中的单例  
Cocos2d 测试用例  
Cocos2d API 参考  
Xcode 及其他地方的 API 参考  
总结  

![images](img/sq1.jpg) 第 4 章：你的第一个游戏  
创建 DoodleDrop 项目  
从启用 ARC 的 cocos2d 项目开始  
创建 DoodleDrop 场景  
添加玩家精灵  
简单的加速度计输入  
首次测试运行  
玩家速度  
添加障碍物  
碰撞检测  
标签与位图字体  
添加分数标签  
介绍 CCLabelBMFont  
使用 Glyph Designer 创建位图字体  
简单播放音频  
iPad 注意事项  
支持 Retina iPad  
一个通用应用还是两个独立应用？  
限制设备支持  
总结  

![images](img/sq1.jpg) 第 5 章：游戏构建模块  
使用多个场景  
添加更多场景




正在加载下一段，请稍候

使用多个图层

如何最佳地实现关卡

CCLayerColor 与 CCLayerGradient

从 CCSprite 子类化游戏对象

使用 CCSprite 组合游戏对象

奇妙有趣的 CCNode 类

CCProgressTimer

CCParallaxNode

CCMotionStreak

总结

![images](img/sq1.jpg) 第 6 章：深入理解精灵

视网膜显示屏

CCSpriteBatchNode

何时使用 CCSpriteBatchNode

Sprites01 演示项目

精灵动画的复杂实现方式

动画辅助类别

使用纹理图集

什么是纹理图集？

TexturePacker 简介

为 TexturePacker 准备项目

使用 TexturePacker 创建纹理图集

在 cocos2d 中使用纹理图集

更新 CCAnimation 辅助类别

合而为一，一统全局

总结

![images](img/sq1.jpg) 第 7 章：快乐滚动

高级视差滚动

将背景创建为条纹

在代码中重新创建背景

移动 ParallaxBackground

视差速度因子

无限滚动以至更远

修复闪烁问题

重复，重复，再重复

虚拟游戏手柄

SneakyInput 简介

触摸按钮进行射击

为按钮换肤

控制动作

数字控制

总结

![images](img/sq1.jpg) 第 8 章：射击游戏

添加 BulletCache 类

让我们造些敌人

Enemy 类

EnemyCache 类

组件类

射击

Boss 的血量条

总结

![images](img/sq1.jpg) 第 9 章：粒子效果

示例粒子效果

创建粒子效果的复杂方法

子类化 CCParticleSystem

CCParticleSystem 属性

Particle Designer

Particle Designer 简介

使用 Particle Designer 效果

共享粒子效果

为射击游戏添加粒子效果

总结

![images](img/sq1.jpg) 第 10 章：使用瓦片地图

什么是瓦片地图？




## 使用 TexturePacker 准备图像

