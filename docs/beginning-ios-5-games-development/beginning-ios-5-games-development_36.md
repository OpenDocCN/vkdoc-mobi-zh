# 目录

第 4 章 快速构建一个输入驱动的游戏
探索如何在屏幕上呈现内容
- 理解 UIView
- Core Graphics 类型定义
- 使用 Core Graphics 类型
- 理解动画
- UIView 的静态动画任务
- 构建游戏 Coin Sorter
- 实现游戏状态
- 初始化与设置
- 开始新游戏
- 继续游戏
- 为每个硬币初始化 UIView
- 模型
- 解读用户输入
- 使用 Core Animation 实现视图动画
- 总结
全文(4)

第 5 章 快速构建逐帧游戏
设置你的第一个逐帧动画
- 简单移动
- 实现类
- 移动飞船
- 响应用户点击
- 理解 CADisplayLink 与 NSRunLoop
- 抽象化 UI
- 理解 Actor
- Example2Controller 概览
- 一个简单的 Actor
- Actor 子类：Viper02
- Actor 子类：Asteroid02
- 在屏幕上绘制 Actor
- 为每个 Actor 更新 UIView
- 在屏幕上放置 UIImageView
- Actor 状态与动画
- 翻倒效果
- 旋转效果
- 总结
全文(5)

第 6 章 创建你的角色：游戏引擎、图像 Actor 与行为
理解游戏引擎类
- GameController 类
- 设置 GameController
- 调用 displayLinkCalled 与 updateScene
- 更新 updateScene
- 调用 doAddActors 与 doRemoveActors
- 添加与移除 Actor
- 对 Actor 进行排序
- 管理 UIView
- Actor 类
- 实现 Actor
- 使用增强道具角色
- 实现增强道具角色
- 检查 ImageRepresentation
- 创建 Powerup 的实现
- 为 Actor 找到正确的图像
- 为 Actor 创建 UIImageView
- 更新视图
- 通过示例理解行为
- 行为：线性运动
- 行为：超时失效
- 总结
全文(6)

第 7 章 构建你的游戏：向量 Actor 与粒子
飞碟、子弹、护盾与生命值条
Actor 类
- 实例化 Saucer 类
- 实例化 HealthBar 类
- 行为 FollowActor 类
- Bullet 类
- 通过 VectorRepresentation 使用 Core Graphics 绘制 Actor
- VectorRepresentation 类
- 基于向量的 Actor 的 UIView：VectorActorView
- 绘制 HealthBar
- 绘制 Bullet 类
- 为你的游戏添加粒子系统
- 简单的粒子系统
- Asteroid 类
- 用同一个类表示小行星和粒子
- 销毁小行星
- Particle 类
- 创建基于向量的粒子
- 总结
全文(7)

第 8 章 构建你的游戏：理解手势与运动
触摸输入：基础
- 扩展 UIView 以接收触摸事件
- 查看事件代码
- 将触摸事件应用于 Actor
- 理解手势识别器
- 点按手势
- TemporaryBehavior 类
- 捏合手势
- 响应捏合手势
- 平移（或拖拽）手势
- 响应平移手势
- 旋转手势
- 长按手势
- 响应用户
- 添加子弹
- 轻扫手势
- 解读设备运动
- 响应运动事件（摇动）
- 响应加速计数据
- 总结
全文(8)

第 9 章 Game Center 与社交媒体
Game Center
- 在 iTunes Connect 中启用 Game Center
- 在游戏中使用 Game Center
- 为用户启用 Game Center
- 向排行榜提交分数
- 颁发成就
- Twitter 集成
- Facebook 集成
- iOS 与 Facebook 入门
- 创建 Facebook 应用
- Facebook 认证
- 初始化 Facebook
- 通过 Facebook 进行认证
- Facebook API 调用
- 总结
全文(9)

第 10 章 通过 Apple App Store 盈利
应用内购买
购买类型概览
- 非消耗品
- 消耗品
- 订阅
- 自动续期订阅
- 准备应用内购买
- 启用并创建应用内购买



- 创建测试用户
- 应用内购买的类与代码
- 应用内购买实现
- 从现有购买驱动用户界面
- 进行购买
- 响应成功购买
- 总结
- 全文(10)

## 第 11 章 完成的视图：Belt Commander

- Belt Commander：游戏回顾
- 实现视图间导航
- 启动应用程序
- XIB 文件
- 视图导航
- 实现游戏
- 游戏类
- 角色
- 表示
- 行为
- 理解 BeltCommanderController
- BeltCommanderController：开始
- 理解设置
- 新游戏
- 处理输入
- BeltCommanderController：一步一步来
- 添加角色
- 碰撞检测
- 更新 HUD
- 实现角色
- Viper 角色
- Asteroid 类
- Saucer 类
- Powerup 类
- 总结

## 附录

- 附录 A 设计与创建图形
- 电子游戏中的艺术
- 电子游戏中的风格
- 品牌与感知
- 创建图像文件
- 命名规范
- 支持图像
- 多分辨率图像
- 多分辨率示例
- 创建最终资源
- 屏幕空间
- 工具
- GIMP
- Blender 3D
- Inkscape
- 总结

## 索引
