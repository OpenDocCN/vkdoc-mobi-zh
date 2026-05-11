# 排版后的文本

### 2. 图像与文本的相对布局

### 3. 创建欢迎屏、帮助屏及其他静态内容

### 4. 构建线框和原型

## 总结

我们回顾了游戏中关于风格的一些简单概念。本附录通过展示风格保持一致的示例和不一致的示例，强调了风格一致的重要性。我们通过演示如何向应用程序添加包括图标和启动屏幕在内的基本辅助图像，继续讨论了应用中的图形处理。我们探讨了如何确定最终美术素材的正确尺寸。我们讨论了为何应首先设计游戏布局，再计算每个元素所需尺寸。最后，我们介绍了三款用于为游戏创建内容且非常实用的开源应用程序。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00474.png)

## 索引

## A

- `ImageRepresentation` 类，143–149
- `aboutClicked`，46, 48, 50
  - 实现，140–143
- `AccelerometerController` 类，212
  - `Saucer`、`Bullet`、`Shield` 和 `Health` 类，156–162
  - `BehaviorFollowActor` 类，160–161
  - `Bullet` 类，161–162
  - 实例化 `HealthBar` 类，160
  - 实例化 `Saucer` 类，159–160
- `AccelerometerController`，213
- `acceptingInput`，77, 79–80, 86, 88, 92
- 成就，通过 Game Center 服务授予，225–227
- `Actor` 类，106–112, 135–136, 155–179
  - 添加与移除，132–133
  - 用于 Belt Commander 游戏，265–267
  - 在屏幕上绘制，112–119
    - 在屏幕中放置 `UIImageView` 类，117–119
  - 子类
    - `Asteroid02` 类，110–112
    - `Viper02` 类，108–110
  - 用于其的 `UIImageView` 类，147–148
  - 为每个角色类更新 `UIView` 类，116–117
- 角色状态与动画，119–126
  - 旋转效果，123–126
  - 翻滚效果，120–123
  - 通过 `VectorRepresentation` 类使用 Core Graphics 库绘制，162–167
    - `Bullet` 类，166–167
    - `HealthBar` 类，165–166
    - `UIView` 类的子类 `VectorActorView`，164–165
- `Actor View`，113
- `Actor02` 类，106–110, 113–117
- `Actor03` 类，121–124
- `actorClassToActorSet`，128–129, 132–133
- `actorId` 属性，106–108, 115–117, 121, 123, 265
- `Example2Controller` 示例，106–107
- 角色
  - 将触摸输入应用于，186–187
  - `Bullet`，206
  - 为其查找正确图像，145–147
  - 实现，137–138
  - 粒子系统，168–179
    - 简单粒子，170–175
    - 基于矢量粒子，175–179
  - 用于其的 `Power-up` 类，138–154
  - 行为示例，149–154
- `actorsOfType` 方法，157–158, 169–170
- `actorsToBeAdded`，128–133
- `actorsToBeRemoved`，128–133
- `actorsView`，128, 132, 169, 191, 196, 199, 204, 207, 264, 272

**305**

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00324.png)

**306**

## 索引

- `actorView`，113–119, 123, 134
- 应用状态
  - 通过委托方法与, 51–53
    - `CoinsControllerDelegate` 协议，51–52
    - 实现定义的任务，52–53
- `addActor` 方法，113–115, 128, 132–133, 140
- `addActor` 任务，265, 272, 274–276, 281–284
- `addGestureRecognizer` 任务，186, 191, 195, 198, 202, 204, 207
- `applicationDidEnterBackground`，62–63
- `addOperation` 方法，47
- `addScore`，53–54, 57–59
- `addSubview`，22, 24, 36, 47, 51, 53, 70–71, 78, 80
- `application:handleOpenURL`，235
- 应用程序
  - 配置多视图，44–47
    - 将视图置于屏幕，46–47
    - `GameController.h` 文件，45–46
  - `GameController_iPhone.xib` 文件，自定义通用，16–21
  - Facebook 服务，230–231
- `applicationWillTerminate`，62
- `applyGameLogic` 任务，264–265, 273
- `applyToActor:In`，161, 193
- `archivedDataWithRootObject`，56
- 归档，游戏状态，62–64
- 美术，288–292
  - 品牌与感知，291–292
  - 风格，288–290
- `animScaleDown`，89–91
- `aSortedActorClasses`，133
- `Asteroid` 类，170–172, 210–211



API（应用程序编程接口），与 Facebook 服务相关，235–236

应用 ID，217–219，221，231

`AppDelegate` 类，19–20

苹果应用商店，237–251
- 购买，从已有购买中驱动 UI，247–249
- 应用内购买，237–243
- 响应成功购买，250–251
- 购买类型，238–239

苹果操作系统（iOS），229–230

应用程序编程接口（API），与 Facebook 服务相关，235–236

[www.it-ebooks.info](http://www.it-ebooks.info/)

![images/00139.png](img/00139.png)

![images/00464.png](img/00464.png)

![images/00176.png](img/00176.png)

**索引**

认证，Facebook 服务，231–236
- API 调用，235–236
- 初始化，233

自动续期订阅，239

`Asteroid` 对象，199–200，210

`Asteroid02` 类，110–113，115–116

`Asteroid03` 类，122，124

`asteroidButton`，246，249

`AsteroidRepresentationDelegate` 类，171–173

小行星
- 摧毁，173–174
- 与粒子，使用同一类表示，172–173

`asteroids_destroyed` 字段，270，272，275

`asteroids_destroyed` 变量，226

`asteroiOfLevel:At` 构造方法，170

`authenticateWithCompletionHandler`，222–223

Belt Commander 游戏，253–285
- `Actor` 类，265–267
- `Behavior` 类，267–268
- `BeltCommanderController` 类，268–285
  - 添加 Actors，274–275
  - 与 `Asteroid` 类，282–283
  - 碰撞检测，275–277
  - 处理输入，272–273
  - 与 `Powerup` 类，284–285
  - 与 `Saucer` 类，283–284
  - 开始新游戏，272
  - 更新抬头显示，277–278
  - 与 `Viper` 类，279–282
- 启动，258–259
- 概述，254–258
- `Representation` 类，267
- 视图，261–264
- 对应的 XIB 文件，259–261

`BeltCommanderController` 类，226，261，263，270，272–273，275–276，278，285

`BeltCommanderController.m` 文件，226

`BeltCommanderDelegate` 协议，261，263，270

`beltcommander.highscores`，223–225

`beltCommanderView`，263

```
backFromExtras task
```

`baseImageName` 属性，143–146

`BOOL` 属性，280

`becomeFirstResponder`，211

`beginAnimations:context`，50，71–73

`bullatAt:WithDirection`，158

`Behavior` 类，159–160，267–268

`Behavior FollowActor` 类，160–161

`Behavior` 对象，136，150，162，266

行为，应用程序的，24–26

`Behavior` 协议，127，131，135–136，138，150，152–154

`behaviors` 数组，266

行为，`Power-up` 类的示例，149–154
- `ExpireAfterTime` 行为，152–154
- `Linear Motion` 行为，150–152

`behaviors` 属性，266

Blender 3D 工具，302–303

`Blenders` 接口，302

品牌化与感知，291–292

`Bullet` actor，206

`Bullet` 类
- 绘制，166–167
- `Saucer`、`Shield` 和 `Health Bar` 类与，160–162

`Bundle Identifier`，217–219

`byValue`，91–93

C 函数，15，19–20，29，31–33

`CABasicAnimation`，89–93

`CADisplayLink` 类，100，103–104，113–115，128–130

`CALayer` 对象，90，93

`canSendTweet`，229

`center` 属性，101，214，266

`CFArrayRef`，167，178

`CGColorSpaceRef`，166–167，177

`CGContectClearRect` 方法，165

`CGContextDrawLinearGradient`，167

`CGContextDrawRadialGradient`，178

`CGContextFillRect` 方法，165–166

`CGContextStrokeRect` 方法，278

`CGFloat`，68–69

`CGGeometry.h` 文件，68–69

`CGGradientCreateWithColors`，167，178

`CG_INLINE`，69，82

`CGPoint center`，135，141–142，151

`CGPoint gameCenter`，159，171，176

`CGRect`，68–70，78，80，87，100，117，119，123

`CGSize`，68–69

角色，127–154
- 游戏引擎类，127–138
  - `Actor`，135–136
  - `GameController`，128–129
- `Actor` 类的 `Power-up` 类，138–154
  - 行为示例，149–154
  - `ImageRepresentation` 类，143–149
  - 实现，140–143

`checkAchievements`，226

`checkMatches`，77，91，93–94

`clipToBounds` 属性，78

`clockwise` 属性，124–125

Coin Sorter 应用程序，38

Coin Sorter 游戏，74–88
- 继续游戏，79–80

碰撞检测，在 Belt Commander 游戏中，275–277

`Comet` actors，175

`Comet` 类，176，209

`Comet` 对象，178

`commitAnimations`，49–51，53，71，73

`completionHandler`，228

可消耗型应用内购买，239

继续游戏按钮，40

`continueButton`，45，53，64

`continueGame`，77，79–80

`coordFromLocation`，77，86–87

`CoordMake` 函数，80–83，85–86，91–93

Core Animation 框架，使用其进行视图动画，88–94

Core Graphics 框架，68–70

Core Graphics 库，通过 `VectorRepresentation` 类进行绘制，162–167

ClayWare Games，290


好的，作为高级文档工程师和翻译员，我将严格遵循您的注意事项和示例，将英文文本翻译成中文。


## 索引

## A

`implementing game state`, 76–77

## B

`Bullet class`, 166–167

`initialization and setup`, 78–79

## C

`HealthBar class`, 165–166

`initiating UIView class for each`

`VectorActorView subclass of UIView`

`coin`, 80–81

`class`, 164–165

`interpreting user input`, 86–88

`createAndLayoutImages method`, 77

`model`, 81–86

`79–80`

`starting new`, 79

`currentFrame`, 143, 146, 148–149

`COIN_CIRCLE constant`, 82–83, 88

`currentRunLoop method`, 100, 103, 114

`coin_circle0029 file`, 300

`CoinController class`, 74

`CoinController object`, 78

## D

`dateLabel`, 55

`coinCoord variable`, 86–88

`decodeInt:ForKey`, 59

`coinForCoord`, 81–85, 87

`decodeObjectForKey`, 57–59, 62

`CoinGame class`, 44, 75

`defaultQueue method`, 246–247, 250–251

`coins`, in Coin Sorter game, 80–81

`CoinsController class`, 37, 44–45, 47, 51–53

`CoinsControllerDelegate`, 45, 51–53, 76–77

`CoinsControllerDelegate protocol`, 51–52

`coinsFrame variable`, 78, 80, 87

`CoinsGame class`, 37, 46, 62–63, 76, 80, 82–85

`COIN_SQUARE constant`, 82–83, 87

`delegate property`, 51–52

`coinsView`, 77–80, 86–87, 89, 91–93

`deltaX`, 150–152

`coinsViewWidth`, 78

`deltaY`, 150–152

`COIN_TRIANGLE constant`, 82–83, 87

`designing graphically`, designing UI, 26–36

`[www.it-ebooks.info](http://www.it-ebooks.info/)`

`![](img/00434.png)`

`![](img/00066.png)`

`![](img/00146.png)`

**索引** **309**

`Device Family list`, 4, 14

`drawing HealthBar class`, 165–166

`device types`, customizing application behavior based on, 24–26

`VectorActorView subclass of UIView class`, 164–165

`devices`

`drawRect task`, 277–278

`interpreting movements of`, 209–214

`accelerometer data`, 212–214

`motion event`, 209–211

`responding to shaking of`, 209–211

## E

`dismissModalViewControllerAnimated`, rotating, 123–126

`effects`

`displayLink`, 99–100, 113–114

`tumbling`, 120–123

`displayLinkCalled method`, 128–130

`encodeInt:ForKey`, 59

`distanceFromCenter`, 141, 143

`encodeObject:forKey`, 58

`doAddActors task`, and `doRemoveActors task`, 131–132

`encodeWithCoder`, 57–59, 62

`doAddNewTrouble method`, 270, 273–274

`endOfGameCleanup method`, 261–264

`doCollisionDetection method`, 270, 273–277

`event code`, 183–185

`doCollision:In`, 108, 110

`Example01Controller class`, 98–100, 102–103, 140

`documentDirPath`, 63

`Example02Controller class`, 106–107, 109, 111, 113–114, 116, 119, 157–158

`doEndGame method`, 77, 92–93

`Example03Controller class`, 121–125, 168–170

`doEndGame task`, 270, 273–274

`Example2Controller example`, 106–107

`doHit method`, 158, 170, 173, 211

`existingGame`, 63–64

`doHit task`, 275–277, 282–283

`expireAfter`, 150, 153

`doNewGame method`, 262–263, 270–272

`ExpireAfterTime class`, 150, 152–154

`doRemoveActors task`, and `doAddActors task`, calling, 131–132

`ExpireAfterTimeDelegate protocol`, 141, 152–153, 174

`doSetup class`, 128–129, 265, 271

`ExpiresAfterTime Behavior`, 175

`doSetup task`, 186, 190–191, 198–199, 202, 204, 206, 210, 213

`extrasController`, 261–262

`doSwitch:With`, 89

`ExtrasController class`, 246–247, 250–251

`doubleTap task`, 186–187, 191

`doUpdateHUD method`, 270, 273–274

## F

`drag gestures`, 197–200

`drawActor:WithContext:InRect`, 164

`Facebook button`, 258, 289

`drawing`

`Facebook object`, 231, 233–235, 261

`actor classes`, drawing on screen, 112–119

`Facebook service`, integration, 229–236

`with Core Graphics library via VectorRepresentation class`, 162–167

`Facebook service applications`, 230–231

`drawing Bullet class`, 166–167

`Facebook service authentication`, 231–236

`Far East style`, 288

`FBConnect project`, 229

`fbDidLogin`, 233, 235

`fbDidLogout`, 233

`fbDidNotLogin`, 233

`file structures`, of sample project created with Xcode tools, 4

`finalRotation`, 202

`findMatchingCols`, 82–85, 91

`findMatchingRows`, 82–85, 91, 93

`fireBulletAt:WithDamage method`, 205–206

`Flickr`, 290–291

`FollowActor class`, 159–161, 268

`frame-by-frame games`, 95–126

## G

`gameArchivePath variable`, 63–64

`gameAreaSize`, 109–110, 113, 116, 118, 122–123, 129, 134, 151

`gameCenter`, 141, 143

`GameController class`, 128–129

`game state`

`calling doAddActors and doRemoveActors tasks`, 131–132

`UIView class`, 134

`updateScene task`, 130–131

`game state` (续)

`implementing for Coin Sorter game`, 76–77

`preserving`, 60–64



### 抽象化 UI，第 105–119 页

### Actor 类

`actor class`，第 106–112 页

添加与移除，第 132–133 页

动画

排序，第 133 页

`actor state` 与，第 119–126 页

调用 `doAddActors` 和

按帧设置

`doRemoveActors` 任务，

游戏，第 97 页

第 131–132 页

`CADisplayLink` 和 `NSRunLoop`

`UIView` 类，第 134 页

类，第 103–104 页

`updateScene` 任务

`Simple Movement` 示例，第 98–103 页

调用 `displayLinkCalled` 任务

实现类，第 99–100 页

与，第 130 页

移动飞船，第 100–103 页

更新，第 131 页

`frame` 属性，第 47, 67–68, 74, 78 页，

`GameController` 对象，第 47, 52 页

第 80–81, 91–92, 101 页

`GameController.h` 文件，第 45–46, 48 页

`frameInterval`，第 103–104 页

`GameController_iPhone.xib` 文件，第 45 页

`fromValue`，第 89–90, 93 页

`GameController.m` 文件，第 52 页

`GameControllerThe` 类，第 264 页

■

`gameDidStart:coinsGame`，第 80 页

**G**

`gameDidStart:with` 方法，第 53 页

Game Center 账户，第 222 页

`gameOver` 方法，第 263, 270, 274 页

Game Center 应用，第 225–226 页

`GameParameters` 类，第 246–249, 270, 272, 274 页

Game Center 服务，第 216–227 页

授予成就，第 225–227 页

`GAME_PARAM_KEY`，第 248 页

启用

`gameParams` 对象，第 246, 249–250 页

在 iTunes Connect 工具中，第 217–221 页

游戏，项目，第 1–10 页

针对用户，第 222–223 页

使用 Xcode 工具创建，第 2–4 页

向排行榜提交分数，第 223–225 页

自定义，第 5–10 页

`gameSize` 变量，第 141, 143, 151 页

游戏引擎类，第 127–138 页

手势识别器，第 187–206 页

`Actor`，第 135–138 页

长按手势，第 203–206 页

`GameController`，第 128–129 页

`Bullet` 棋子，第 206 页

`Actor` 类，第 132–133 页

响应用户，第 205–206 页

平移与拖拽手势，第 197–200 页

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00306.png)

![](img/00337.png)

![](img/00399.png)

**索引**

**311**

捏合手势，第 194–196 页

`Interface Builder` 应用程序，第 27–30 页

旋转手势，第 200–203 页

图形，第 287–304 页

轻拍手势，第 189–193 页

美术，第 288–292 页

手势，第 181–214 页

品牌与感知，第 291–292 页

手势识别器，第 187–206 页

风格，第 288–290 页

长按手势，第 203–206 页

图像文件，第 292–300 页

平移与拖拽手势，第 197–200 页

多分辨率，第 297 页

捏合手势，第 194–196 页

命名规范，第 293–294 页

旋转手势，第 200–203 页

分辨率，第 298–300 页

轻拍手势，第 189–193 页

支持，第 294–297 页

解读设备运动，第 209–214 页

`Inkscape` 应用程序，第 303–304 页

加速度计数据，第 212–214 页

工具，第 300–303 页

运动事件，第 209–211 页

`Blender 3D`，第 302–303 页

滑动，第 206–209 页

`GIMP`，第 301–302 页

轻拍，响应，第 102–103 页

触摸输入，第 181–187 页

应用于棋子，第 186–187 页

■ **H**

事件代码，第 183–185 页

`handleOpenURL`，第 234–235 页

扩展 `UIView` 类以接收，第 182–183 页

Hassey, Phil，第 289 页

`getFrameCountForVariant:AndState`，第 144–145 页

平视显示器 (HUD)，第 277–278 页

`getFrameCountForVariation:AndState`，第 146, 148 页

`HealthBar` 类，第 160–161 页

绘制，第 165–166 页

`getFramesCountForVariant:AndState`，第 70–73 页

实例化，第 160 页

`getImageForActor`，第 144, 147–149 页

`healthBarView`，第 270, 277 页

`getImageNameForActor`，第 144–145, 147–148 页

高分视图，第 37, 40–41, 50–51, 53, 70–73 页

`getNameForState`，第 143–146 页

高分视图，第 38 页

`getNameForVariant`，第 143–146, 172 页

`HighscoreController` 类，第 53–60 页

`getViewForActor`，第 131–132, 134–136, 185 页

`Highscores` 类，第 56–58 页

实现与布局，第 54–56 页

`GIMP` (GNU 图像处理程序)，第 301–302 页

`Score` 类，第 58–60 页

`highscoreController.view`，第 71–73 页

`GKAchievement`，第 226 页

`Highscores` 类，第 44, 53–54, 56–60 页

`GKLeaderboardViewControllerDelegate`，第 225 页

`highscores` 对象，第 55–56, 58, 60 页

`GKLocalPlayer`，第 222, 261 页

`highscoresView`，第 54–55 页

`GKScore` 对象，第 224 页

`highscoreView`，第 55–56 页

`GNU 图像处理程序` (GIMP)，第 301–302 页

HUD (平视显示器)，第 277–278 页

图形化设计，UI，第 26–36 页

■ **I, J**

向 XIB 文件添加元素，第 30–34 页

`IBAction`，第 46, 48–51 页

适应方向变化，第 35–36 页

`IBOutlet` 字段，从 `Interface Builder` 应用程序新建，第 33–34 页

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00453.png)

**312**

**索引**

`IBOutletCollection`，第 45–46 页

`initWithTarget:selector:object`，第 47 页

`IBOutlets`，第 29, 33, 45, 261, 270 页

`Inkscape` 应用程序，第 303–304 页

图像尺寸，第 296 页

输入驱动型游戏，第 65–94 页

`imageForCoin` 函数，第 87 页

动画，第 70–74 页

`imageName` 属性，第 106–107, 111–112, 116–117, 121–124 页

`UIView` 类的静态任务，第 70–74 页

`imageNamed` 方法，第 294 页

视图动画，第 88–94 页

`imageNameVariations` 数组，第 110–111 页

Coin Sorter 游戏，第 74–88 页

`ImageRepresentation` 类，第 143–149 页

继续，第 79–80 页

为 `Actor` 类查找正确的图像，第 145–147 页



实现游戏状态，第 76–77 页

Powerup 类实现、初始化与设置，第 78–79、144–145 页  
为 Actor 类的每个 UIImageView 类初始化 UIView 类（金币），第 80–81、147–148 页  
解释用户输入，第 86–88 页  
更新视图，第 148–149 页  
模型，第 81–86 页  
ImageRepresentation 对象，第 160 页  
启动新游戏，第 79 页

ImageRepresentationDelegate 协议，在屏幕上呈现内容，第 66–70、141、143–145、147 页  
Core Graphics 框架，第 68–70 页  
图像文件，第 292–300 页  
UIView 类，第 66–68 页  
多分辨率，第 297–298 页

`int asteroidIndex`，第 198 页  
命名约定，第 293–294 页  
`int score`，第 58–59 页  
分辨率，第 298–300 页

Interface Builder 应用程序，从中创建新的 IBOutlet 字段，第 33–34 页  
概述，第 27–30 页

`imageVariant property`，第 122 页  
`interfaceOrientation variable`，第 35 页

应用内购买，第 237–243 页  
iOS（苹果操作系统），第 229–230 页  
类和代码，第 243–245 页  
iPad 3，第 293、299 页  
消耗型，第 239 页  
iPad 模拟器，第 12、17 页  
启用与创建，第 240–242 页  
iPhone 3GS，第 293–294、296–297、299–300 页  
实现，第 246–247 页  
非消耗型，第 239 页  
iPhone 4，第 293–294、296–297、299–300 页  
测试用户，第 242–243 页

`isFirstCoinSelected`，第 77、86–88 页  
`includeAsteroids`，第 248–249 页  
`isSetup variable`，第 128–130 页  
`includeSaucers`，第 248–250 页

iTunes Connect 工具，在其中启用 Game Center 服务，第 217–221 页  
`indexForCoord`，第 81–87、89、91–92 页

`info.plist file`，第 18、20、296 页  
`initAt:WithRadius:AndImage`，第 110 页  
`initAt:WithRadius:AndRepresentation`，第 142、162 页

**K**

`kCAMediaTimingFunctionEaseIn`，第 89–92 页

**L**

横向占位视图，第 32 页  
`landscapeView`，第 34–36 页  
`latestScore`，第 54–56 页

启动图片框，第 38 页  
`layer property`，第 90 页  
`layoutScores`，第 54–55、60 页  
`leaderboadViewControllerDidFinish`，第 225 页  
排行榜按钮，第 257 页  
管理 Game Center 按钮，第 219–220 页  
`leaderBoardClicked`，第 225 页  
`matchingCols variable`，第 77、82、84、91–93 页  
`matchingRows variable`，第 77、82、84–86、91–93 页  
`maxHealth property`，第 277、279–280 页  
`maxYValue property`，第 198–199 页  
`minYValue property`，第 198–199 页

模型-视图-控制器（MVC），第 21 页  
Coin Sorter 游戏模型，第 81–86 页  
运动事件，响应，第 209–211 页  
`motionBegan:withEvent`，第 211 页  
`motionEnded:withEvent`，第 211 页  
设备移动，解释，第 209–214 页  
`moveToPoint property`，第 99–100、102–103、109–110、119、123–124、126 页  
多分辨率，第 297–298 页  
MVC（模型-视图-控制器），第 21 页

线性运动行为，第 150–152 页  
`LinearMotion class`，第 150–151、268、279–281、283 页  
`linearMotionInDirection:AtSpeed`，第 151 页  
`linearMotionRandomDirectionAndSpeed`，第 150–151 页

**N**

图像文件命名约定，第 293–294 页  
Xcode 工具中视图的导航，第 7–8 页  
`needsImageUpdated property`，第 121–123、125–126 页  
`needsViewUpdated`，第 135、137–138、148–149、266–267 页  
`needViewUpdated property`，第 154 页

长按手势，第 203–206 页  
子弹角色，第 206 页  
响应用户，第 205–206 页  
`LongPressController class`，第 204–206 页  
`longPressGesture`，第 204–206 页  
`longPressRecognizer`，第 204–206 页

**M**

`main function`，第 258 页  
`main.m file`，第 258 页  
`mainQueue method`，第 46–47 页  
`NSArray`，第 45–46、56、63、278 页  
`NSCoder protocol`，第 248 页  
`NSCoding protocol`，第 56–58、62、64 页  
`NSData object`，第 56、60 页



`particle systems`，第 168–179 页

`NSDictionary`，第 20 页

`simple`，第 170–175 页

`NSDocumentDirectory`，第 63 页

`Asteroid` 类，第 170–172 页

`NSInvocationOperation`，第 46–47 页

销毁小行星，第 173–174 页

`NSKeyedArchiver` 类，第 56、63 页

`Particle` 类，第 174–175 页

`NSKeyedUnarchiver` 类，第 60、63–64 页

表示小行星和粒子

`NSLog` 语句，第 148 页

使用相同类表示，第 172–173 页

`NSMutableArray`，第 56–57、113–115 页

基于向量的粒子，第 175–179 页

184–186、198–199 页

`particleAt:WithRep:Steps`，第 174 页

`NSMutableDictionary`，第 113–114、117 页

粒子

128–129、132–133 页

小行星和粒子，使用相同类表示

`NSMutableSet`，第 128–129、131–133、248 页

第 172–173 页

`NSNumber` 硬币，第 81 页

基于向量的，第 175–179 页

`NSNumber` 对象，第 84 页

`pauseGameAlertView`，第 263 页

`NSOperationQueue`，第 46–47 页

`percentComplete` 属性，第 226–227 页

`NSRunLoop` 类，第 100、103–104、114 页

感知，品牌与，第 291–292 页

`NSSearchPathForDirectoriesInDomains`

`Person` 类，第 21 页

函数，第 63 页

捏合手势，第 194–196 页

`NSSet` potentialProducts，第 247 页

`pinchGesture`，第 195–196 页

`NSSet` purchases，第 249 页

Play 视图，第 38、40、45、51 页

`NSSet` 任务，第 182、184 页

`playButtonClicked` 任务，第 262 页

`NSString`，第 233–235 页

plist 文件，第 18 页

`NSString` imageName，第 146 页

PNG 文件，第 38、143 页

`NSTimer` 类，第 93 页

`pointInGame`，第 118–119 页

`NSUserDefaults` 类，第 56、60、245、248 页

`pointOnView`，第 118–119 页

`NSUserDomainMask`，第 63 页

点数，第 221 页

`NSUsersDefaults` 方法，第 247 页

`popViewControllerAnimated` 方法，

`NUMBER_OF_IMAGES` 常量，第 122 页

第 262–263 页

`numberOfTapsRequired`，第 191–192 页

Portrait Game Holder 视图，第 67 页

`numberOfTouches`，第 186–187、191–192 页

Portrait Holder 视图，第 32–33 页

Portrait Play 视图，第 45、67 页

**O**

`portraitView`，第 34–36 页

方向变化，图形化设计 UI 以适应，

`Power-up` 类，用于 `Actor` 类，

第 35–36 页

第 138–154 页

`overlapsWith` 任务，第 267、275–276 页

行为示例，第 149–154 页

Owner 对象，第 30 页

`ImageRepresentation` 类，第 143–149 页

实现，第 140–143 页

**P, Q**

`Powerup` actor，第 171 页

平移手势，第 197–200 页

`Powerup` 类

`panGesture` 方法，第 198–200 页

在 Belt Commander 游戏中，第 284–285 页

`PanGestureController` 类，第 197–199 页

实现，第 144–145 页

`panRecognizer` 方法，第 198–200 页

`Powerup` 对象，第 150、153、190–191 页

`parentView`，第 70 页

`presentModalViewController`，第 225、228 页

`Particle` 类，第 174–175 页

定价与可用性章节，第 242 页

[www.it-ebooks.info](http://www.it-ebooks.info/)

`productsRequest:didReceiveResponse`，

![](img/00161.png)

![](img/00194.png)

![](img/00036.png)

**索引**

**315**

Project Navigator，第 17 页

`randomPointAround`，第 134、143 页

项目模板，第 3 页

`randomzeCols`，第 86 页

项目，第 11–36 页

`readFromDefaults`，第 246、248 页

使用 Xcode 工具创建示例项目，第 2–4 页

`remainingTurnsLabels`，第 45–46、52 页

自定义，第 5–10 页

`removeActor` 方法，第 128、132–133 页

图形化设计 UI，第 26–36 页

将元素添加到 XIB 文件，第 30–34 页

`removeActor` 任务，第 265、276、283–284 页

适应方向变化，第 35–36 页

`removeAllActors` 方法，第 272 页

Interface Builder 应用程序，第 27–30 页

`removeAllObjects`，第 132–133、138 页

结构，第 42–60 页

`removed` 属性，第 266 页

更改视图以响应用户操作，第 48–51 页

`removeFromSubview` 函数，第 36 页

使用委托方法通信应用程序状态，第 51–53 页

`removeFromSuperview`，第 24、35–36、47、49–51、53、55、115 页

为多个视图配置应用程序，第 44–47 页

渲染，第 298 页

`HighscoreController` 类，第 53–60 页

`rep` 变量，第 134、142–143 页

`UIViewController` 类，第 21–26 页

`reportAchievementWithCompletionHan`

自定义通用应用程序，第 16–21 页

`der`，第 227 页

`publish_stream` 数组，第 234 页

`reportScoreWithCompletionHandler`，第 223–224 页

`Representation` 类，用于 Belt Commander 游戏，第 267 页

`PURCHASE_INGAME_POWERUPS`

`Representation` 对象，第 138、143、163 页

常量，第 246–247、249 页

`Representation` 协议，第 127、132、135–137、143、163–164、174–175 页

`PURCHASE_INGAME_SAUCERS`

`requestWithGraphPath:andParams:and`

常量，第 246–247、249–250 页

`HttpMethod:andDelegate`，第 236 页

购买

`resignFirstResponder`，第 211 页

从现有购买驱动 UI，第 247–249 页

分辨率，第 298–300 页

应用内购买，第 237–243 页

`restoreCompletedTransactions`，第 251 页

类和代码，第 243–245 页

Rock, Paper, Scissors 视图，第 8 页

启用和创建，第 240–242 页

`RockPapaerScissorView` 类，第 1 页

实现，第 246–247 页

`RockPaperScissorsController` 类，第 22、32、34 页

测试用户，第 242–243 页

`RockPaperScissorsController.h` 文件，第 22 页

响应成功购买，第 250–251 页

`PWR_STATE_COUNT`，第 141 页

购买类型，第 238–239 页


好的，作为高级文档工程师和翻译员，我将遵循您的注意事项，将给定的英文文本翻译成中文。


`RockPaperScissorsView` 类, 8–9, 21

勾股定理, 143

`RockPaperScissorsView.h` 文件, 8

`RockPaperScissorsView.m` 文件, 8

■

`rootViewController`, 20–21

**R**

`RootViewController` 类, 222, 228

`radius` 属性, 106–108, 110–111, 117, 121–123, 233, 235, 260–261, 263, 266, 270, 272, 274

`randomizeCols`, 82–85, 91

`rootViewController` 属性, 53

`randomizeRows`, 82–86, 91

旋转效果, 123–126

旋转手势, 200–203

`rotation` 属性, 121, 123, 125–126, 266

`rotationGesture`, 201–202

`RotationGestureController`, 200–202

`rotationRecognizer`, 201–202

■

**S**

`Safari` 图标, 228

`Saucer` 类

在 Belt Commander 游戏中, 283–284

Bullet, Shield, 和 Health Bar 类与, 156–162

`saucerButton`, 246–247, 249

`saucerButtonClicked` 任务, 250

`saveHighscores`, 54, 56, 60

缩放, 297–298

`Score` 类, 44, 58–60

`score` 属性, 224

`scoreChangedOnStep` 属性, 277

`scoreIncreases:with`, 53

`scoreLabels` 数组, 45–46, 52

`Scores` 对象, 57

分数, 提交到排行榜, 223–225

屏幕空间, 299–300

屏幕

将视图放置到, 46–47

在屏幕上获取内容, 66–70

Core Graphics 框架, 68–70

`UIView` 类, 66–68

将 `UIImageView` 类放置到屏幕中, 117–119

辅助点按, 189

`secondSelectedCoin` 变量, 77, 88

`setAnimationDidStopSelector`, 73

`setAnimationDuration`, 71–73, 81

`setAnimationImages` 方法, 81

`setAnimationPaused` 方法, 195–196

`setAnimationTransition:forView:cache`, 72

`setFill` 方法, 165

`setGameParameters` 任务, 248–249

`setGameParams` 方法, 246–247, 249–251

`setHealth` 方法, 277

`setInitialText`, 228

`setIsPaused` 方法, 262–263, 271–272, 274

`setMoveToPoint:within` 方法, 273

`setMoveToPoint:within` 任务, 280

`setNeedsDisplay` 方法, 164

`setNeedsViewUpdate` 方法, 166

`setPercent`, 159, 165–166

`setPreviousGame`, 46, 64

`setSortedActorClasses`, 133, 157, 169

`setText` 方法, 277

`setVariant` 方法, 159–160, 171, 173

`ShakeController` 类, 210–211

摇晃设备, 响应, 209–211

`Shield` 类, 159–160

`shouldAutorotateToInterfaceOrientation`, 35–36, 53

简单移动示例, 98–103

实现类, 99–100

移动飞船, 100–103

单视图应用, 13

单视图应用模板, 3

`SKPayment` 对象, 250

`SKPaymentQueue`, 246–247, 250–251

`SKPaymentTransaction`, 250–251

`SKPaymentTransactionObserver` 协议, 246–247, 250

`SKPaymentTransactionStatePurchase`, 250

`SKPaymentTransactionStatePurchased`, 250–251

`SKPaymentTransactionStateRestored`, 250–251

`SKProductsRequest`, 246–247

`SKProductsRequestDelegate` 协议, 229

`sleepForTimeInterval`, 47

`sliderValueChanged`, 113, 119

社交图谱 API, 235

社交媒体, 215–236

Facebook 服务集成, 229–236

应用, 230–231

认证, 231–236

iOS 与, 229–230

Game Center 服务, 216–227

颁发成就, 225–227

启用, 217–223

提交分数到排行榜, 223–225

Twitter 服务集成, 227–229

`sortedActorClasses` 属性, 128–129, 133

Space Attack 游戏, 291–292

飞船, 在简单移动示例中, 100–103

`Spark` 类, 184–187

`sparks` 数组, 184–185

`spinCoinAt`, 77, 80–81, 88, 91, 93

`standardUserDefaults` 方法, 56, 60

`startAnimating` 方法, 81

`startCenter`, 198–200

`startRadius` 变量, 195–196

`startRotation`, 201–203

`state` 属性, 266

`STATE_GLOW`, 141, 145, 153–154

`STATE_NO_GLOW`, 141, 145, 154

状态

应用的, 通过委托方法与, 51–53

订阅, 自动续期, 239

`subView`, 70

摘要标签页, 15

SUMO Productions, 289

支持图片, 294–297

支持文件分组, 19

轻扫手势, 206–209

`SwipeGestureController` 类, 206

`swipeRecognizer` 属性, 208

■

**T**

轻点手势, 189–193

响应, 102–103

`TemporaryBehavior` 类, 191–193

`tapGesture`, 77–79, 86, 88, 190–192, 204–205

`TapGestureController` 类, 190–193

`tapRecognizer`, 78–79, 87

`targetRotation`, 125–126

`TemporaryBehaviorDelegate` 协议, 190, 192

测试用户按钮, 243

测试用户, 用于应用内购买, 242–243

`theScores` 数组, 55–58

`timingFunction`, 89–92

工具, 用于图形, 300–303

Blender 3D 工具, 302–303

GIMP, 301–302



## 索引

## A
- `of games`、`preserving`，60–64
- `totalStepsAlive` 属性，174–175，178
- `STATE_TRAVELING`，124–126
- `touch input`，181–187
- `STATE_TURNING`，124–125
- `applying to actors`，186–187
- `static inline`，69
- `event code`，183–185
- `static tasks`，属于 `UIView` 类，70–74
- `extending UIView class to receive`，182–183
- `status bar`，16–19
- `stepNumber` 属性，128–131，140
- `touchCancelled:withEvent`，184
- `touchesBegan:withEvent`，182
- `stepsRemaining`，152–153
- `touchesCancelled:withEvent`，182，185
- `stepsUpdated`，153
- `touchesEnded:withEvent`，182，185
- `stopAnimation` 方法，87
- `touchesMoved:withEvent`，182，185
- `stopCoinAt` 方法，77，86–88
- `TouchEventsController` 类，183–184
- `Storyboard` 功能，38
- `stringByAppendingPathComponent`，63
- `TouchEventsView` 类，183–184
- `style`，288–290
- `toValue`，89–90，93
- `www.it-ebooks.info`
- `transform` 属性，89–90
- `UIImageViews`，98，112–113，143
- `transform.scale`，89–90
- `UIKit`，66，76
- `trestorePurchasesClicked`，251
- `UILabel`，22–23，31，39，45，52，55
- `tumbling effect`，120–123
- `UILongPressGestureRecognizer`，187，204–205
- `turnsRemainingDecreased:with`，53
- `UINavigationController` 类，260–262
- `Tweet` 按钮，257
- `tweetSheet`，228
- `UIPanGestureRecognizer` 类，187，197–199
- `Twitter` 对话框，257
- `UIPinchGestureRecognizer`，187，194–195
- `Twitter` 服务，集成，227–229
- `UIRotationGestureRecognizer`，201–202
- `TWTweetComposeViewController`，228–229
- `type definitions`，属于 Core Graphics 框架，68–69
- `UIs`（用户界面）
    - 抽象化，105–119
    - 基于现有购买内容驱动，247–249
    - 图形化设计，26–36
        - 向 XIB 文件添加 UI 元素，30–34
        - 针对方向变化的处理，35–36
        - Interface Builder 应用程序，27–30
    - `UISwipeGestureRecognizer`，187，207–208
    - `UITapGestureRecognizer` 类，78，99，115，169，187，191，193，205，271–272
    - `UITapGestureRecognizers`，189，191–192
    - `UIView` 类，66–68，134
        - 扩展以接收触摸输入，182–183
        - 为 Coin Sorter 游戏中的每个硬币进行初始化，80–81
        - 其静态任务，70–74
        - 为每个角色更新，116–117
        - 其子类 `VectorActorView`，164–165
    - `UIView` 对象，31
    - `UIView view`，自定义，9–10
    - `UIViewAnimationFlipFromLeftForView`
    - `UIGraphicsGetCurrentContext`，165
    - `UIViewController` 类，21–26，32–33，225，229
    - `UIViewController_iPad` 类，26
- `UIImageView` 类
    - 用于 `Actor` 类，147–148
    - 放置在屏幕中，117–119
- `UIViewControllers`，11，21–22，45，54，260–261
- `UIViews`，45
- `UIWebView`，227，233–234
- `UIWindow`，20–21，66–67
- `unarchiveObjectFromFile` 方法，64
- `unarchiveObjectWithData`，60
- `universal applications`，自定义，16–21
- `updateActorView`，113，116，122–123
- `updateCoinView`，93
- `updateLocation`，99–103，110
- `updateScene` 任务
    - 调用 `displayLinkCalled` 任务和，114
    - 更新，131
- `updateViewForActor`，128，131，134
- `viewDidLoad`，46–47，60，78–79，99，103，113–114，294
- `viewDidAppear`，211
- `viewDidUnload`，34
- 视图
    - 使用 Core Animation 框架实现的动画，88–94
    - 用于 Belt Commander 游戏，261–264
    - 根据用户操作进行更改，48–51
    - 为多个视图配置应用程序，44–47
        - 将视图带到屏幕上，46–47
        - `GameController.h` 文件，45–46
        - `GameController_iPhone.xib` 文件，48–51
    - 在 Coin Sorter 游戏中解释来自视图的输入，86–88
    - 响应来自视图的长按手势，205–206
    - 响应来自视图的点击手势，102–103
    - 在游戏中，37–42
    - 更新，148–149
- `viewTapped`，99–100，102–103

## V
- `VARIATION_CASH`，284
- `VARIATION_HEALTH`，284
- `vector-based particles`，175–179
- `VectorActorView` 类，163–165
- `VectorRepresentation` 类，通过 Core Graphics 库进行绘制，162–167
    - 绘制 `Bullet` 类，166–167
    - 绘制 `HealthBar` 类，165–166
    - `UIView` 类的子类 `VectorActorView`，164–165
- `VectorRepresentationDelegate` 协议，163–164，176
- `view` 属性，21，36，210–211
- `ViewController` 类，20–21，24，26–29，34–35，294
- `ViewController_iPad.m` 文件，35
- `ViewController_iPad.xib` 文件，27
- `ViewController_iPhone` 类，20，26–29
- `ViewController_iPhone.m` 文件，35
- `ViewController.m` 文件，294
- `Viper` 类，在 Belt Commander 游戏中，279–282
- `Viper` 对象，202，270，272
- `Viper01` 类，99–101，108，110
- `Viper02` 类，108–110，113–114
- `Viper03` 类，121，124–125
- `VPR_STATE_CLOCKWISE`，202
- `VPR_STATE_COUNTER_CLOCKWISE`
- `VPR_STATE_DOWN`，279–281
- `VPR_STATE_STOPPED`，202–203，205–206
- `VPR_STATE_TRAVELING`，205
- `VPR_STATE_UP`，279–281

## W
- `Welcome` 视图，37–38，40，45，48，51，64，71–72，79
- `welcomeController`，261
- `window` 对象，21
- `workComplete` 变量，128–130
- `writeToDefaults`，248，250
- `WYSIWYG` 元素，27
- `WYSIWYG` 布局，303

## X
- `X` 值，117
- Xcode 工具
    - 排列视图，5–6
    - 视图导航，7–8
    - 新视图，6–7
    - 石头剪刀布视图，8
    - `UIView` 视图，9–10
- `xcodeproj` 文件，4
- `xFactor`，116–119，123
- XIB 文件
    - 向其中添加 UI 元素，30–34
    - 从 Interface Builder 应用程序创建新的 `IBOutlet` 字段，33–34
    - `UIViewController` 类，32–33
    - 用于 Belt Commander 游戏，259–261
- `xOffest`，161

## Y, Z
- `Y` 值，102，117，119
- `yFactor`，117–119，123
- `yOffset`，161

---

`www.it-ebooks.info`



*   `GetFullPageImage`
*   `front-matter`
    *   扉页
    *   版权页
    *   内容概览
    *   目录
    *   关于作者
    *   关于技术审校
    *   致谢
    *   引言
        *   逐章概览
            *   第 1 章
            *   第 2 章
            *   第 3 章
            *   第 4 章
            *   第 5 章
            *   第 6 章
            *   第 7 章
            *   第 8 章
            *   第 9 章
            *   第 10 章
            *   第 11 章
            *   附录 A
*   `fulltext`
    *   第 1 章 第一个简单的游戏
        *   在 Xcode 中创建项目：示例 1
            *   项目的文件结构
        *   自定义你的项目
            *   排列 Xcode 视图以简化操作
            *   添加新视图
            *   简单的导航
            *   添加“石头、剪刀、布”视图
            *   自定义`UIView`
        *   小结
*   `fulltext(1)`
    *   第 2 章 设置你的游戏项目
        *   创建你的游戏项目
        *   自定义通用应用程序
            *   iOS 应用程序的初始化方式
        *   理解`UIViewControllers`
            *   根据设备类型自定义行为
        *   以通用方式图形化设计你的 UI
            *   初识 Interface Builder
            *   向 XIB 文件中添加 UI 元素
                *   向 XIB 中添加`UIViewController`
                *   从 Interface Builder 创建新的`IBOutlets`
            *   响应方向变化
        *   小结
*   `fulltext(2)`
    *   第 3 章 探索游戏应用程序的生命周期
        *   理解游戏中的视图
            *   探索每个视图扮演的角色
        *   理解项目的结构
            *   为多视图配置应用程序
                *   检查`GameController_iPhone.xib`
                *   检查`GameController.h`
                *   将视图显示到屏幕上
            *   根据用户操作切换视图
            *   使用委托来通信应用程序状态
                *   声明`CoinsControllerDelegate`
                *   实现定义的任务
            *   `HighscoreController`：一个简单的可重用组件
                *   `HighscoreController`的实现与布局
                *   `Highscores`类
                *   `Score`类
        *   保存游戏状态
            *   归档与解档游戏状态
                *   实现生命周期任务
        *   小结
*   `fulltext(3)`
    *   第 4 章 快速构建一个输入驱动的游戏
        *   探索如何将内容显示到屏幕上
            *   理解`UIView`
            *   Core Graphics 类型定义
            *   使用 Core Graphics 类型
        *   理解动画
            *   `UIView`的静态动画任务
        *   构建游戏 Coin Sorter
            *   实现游戏状态
            *   初始化与设置
            *   开始新游戏
            *   继续游戏
            *   为每个硬币初始化`UIViews`
            *   模型
            *   解读用户输入
        *   使用 Core Animation 给视图添加动画
        *   小结
*   `fulltext(4)`
    *   第 5 章 快速构建一个逐帧游戏
        *   设置你的第一个逐帧动画
        *   简单的移动
            *   实现类
            *   移动飞船
                *   响应用户的点击
        *   理解`CADisplayLink`和`NSRunLoop`
        *   抽象化 UI
            *   理解角色
                *   `Example2Controller`概述
                *   一个简单的角色
                *   角色子类：`Viper02`
                *   角色子类：`Asteroid02`
            *   在屏幕上绘制角色
                *   为每个角色更新`UIView`
                *   将`UIImageView`放置到屏幕上
        *   角色状态与动画
            *   翻滚效果
            *   旋转效果
        *   小结
*   `fulltext(5)`
    *   第 6 章 创建你的角色：游戏引擎、图像角色与行为
        *   理解游戏引擎类
            *   `GameController`类
            *   设置`GameController`
                *   调用`displayLinkCalled`和`updateScene`
                *   更新`updateScene`
                *   调用`doAddActors`和`doRemoveActors`
                *   添加与移除角色
                *   对角色进行排序
                *   管理`UIView`
            *   `Actor`类
            *   实现`Actor`
        *   使用增强道具角色
            *   实现我们的增强道具角色
            *   检查`ImageRepresentation`
                *   创建`Powerup`的实现
                *   为角色找到正确的图像
                *   为角色创建`UIImageView`
                *   更新我们的视图
            *   通过示例理解行为
                *   行为：线性运动
                *   行为：`ExpireAfterTime`
        *   小结
*   `fulltext(6)`
    *   第 7 章 构建你的游戏：矢量角色与粒子
        *   飞碟、子弹、护盾与血条
            *   角色类
                *   实例化飞碟类
                *   实例化`HealthBar`类
                *   `FollowActor`行为类
                *   子弹类
        *   通过`VectorRepresentation`使用 Core Graphics 绘制角色
            *   `VectorRepresentation`类
            *   用于矢量角色的`UIView`：`VectorActorView`
            *   绘制`HealthBar`
            *   绘制子弹类
        *   向游戏添加粒子系统
            *   简单粒子系统
                *   小行星类
                *   使用同一个类来表示小行星和粒子
                *   摧毁小行星
                *   粒子类
            *   创建基于矢量的粒子
        *   小结
*   `fulltext(7)`
    *   第 8 章 构建你的游戏：理解手势与动作
        *   触摸输入：基础
            *   扩展`UIView`以接收触摸事件
            *   查看事件代码
            *   将触摸事件应用于角色
        *   理解手势识别器
            *   点击手势
                *   `TemporaryBehavior`类
            *   捏合手势
                *   响应捏合手势
            *   平移（或拖拽）手势
                *   响应平移手势
            *   旋转手势
            *   长按手势
                *   响应用户
                *   添加子弹
        *   轻扫手势
        *   解读设备动作
            *   响应运动事件（摇晃）
            *   响应加速计数据
        *   小结
*   `fulltext(8)`
    *   第 9 章 Game Center 与社交媒体
        *   Game Center
            *   在 iTunes Connect 中启用 Game Center
            *   在你的游戏中使用 Game Center
                *   为用户启用 Game Center
                *   向排行榜提交分数
            *   授予成就
        *   Twitter 集成
        *   Facebook 集成
            *   iOS 与 Facebook 入门
            *   创建一个 Facebook 应用
            *   Facebook 认证
                *   初始化 Facebook
                *   使用 Facebook 进行认证
                *   Facebook API 调用
        *   小结
*   `fulltext(9)`
    *   第 10 章 通过 Apple App Store 盈利
        *   应用内购买
        *   购买类型概述
            *   非消耗品
            *   消耗品
            *   订阅
            *   自动续订订阅
        *   为应用内购买做准备
            *   启用并创建应用内购买
            *   创建测试用户
        *   应用内购买的类与代码
        *   应用内购买实现
        *   根据现有购买驱动 UI
        *   进行购买
        *   响应成功的购买
        *   小结
*   `fulltext(10)`
    *   第 11 章 完整的视图：Belt Commander
        *   Belt Commander：游戏回顾
            *   实现视图到视图的导航
            *   启动应用程序
            *   XIB 文件
            *   视图导航
        *   实现游戏
            *   游戏类
                *   `Actor`
                *   `Representation`
                *   `Behavior`
            *   理解`BeltCommanderController`
                *   `BeltCommanderController`：入门
                *   理解设置
                *   新游戏
                *   处理输入
            *   逐步理解`BeltCommanderController`
                *   添加角色
                *   碰撞检测
                *   更新 HUD
                *   实现角色
                *   Viper 角色
                *   小行星类
                *   飞碟类
                *   `Powerup`类
        *   小结
*   `back-matter`
    *   附录 A 设计与创建图形
        *   视频游戏中的艺术
            *   视频游戏中的风格
            *   品牌与感知
        *   创建图像文件
            *   命名规范
            *   支持图像
            *   多分辨率图像
            *   多分辨率示例
            *   创建最终资产
                *   屏幕空间
        *   工具
            *   GIMP
            *   Blender 3D
        *   Inkscape
        *   小结
    *   索引



