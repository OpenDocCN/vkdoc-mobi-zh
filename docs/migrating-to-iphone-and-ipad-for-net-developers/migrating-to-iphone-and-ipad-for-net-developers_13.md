# 附录

## 完成“月球着陆器”应用

在阅读本书的过程中，我一直在逐步为你搭建“月球着陆器”游戏的相关功能，但并未将它彻底完成，这是我刻意为之。直接把成品游戏交给你，乐趣何在呢？通过本书学习到的 iOS、其 SDK 以及 Xcode 的知识，将使你具备自行完成并发布这款游戏的能力——或者，你或许想从头开始创作一款完全不同的游戏。

不过，让你悬而未决也非公平之举。因此，在本附录中，我将讨论一些你可能会考虑添加到游戏中的元素。随书提供的源代码包含与本章配套的代码片段，可以帮助你完成游戏开发。

### 实现游戏物理引擎

为确保你的着陆器遵循物理定律，你需要为其实现一个简单的物理引擎。你可以在[`www.physicsclassroom.com/class/newtlaws/u2l4a.cfm`](http://www.physicsclassroom.com/class/newtlaws/u2l4a.cfm)找到描述所需物理原理的参考资料。别担心，接下来我会带你了解基础知识。

#### 重力

这个模拟相当简单。首先施加重力：

```
Y = Y + Gravity * TotaleSecondsOfThrust;
```

这里，通过将`Gravity`值（双精度浮点数 9.8）乘以施加推力的秒数，来调整控制着陆器高度的`Y`值。推力秒数的计算方法为：检测按下推力键时的时间戳，并计算从松开按键起所经过的秒数。你可以使用`NSTimeInterval()`方法来实现。

#### 推力

接着，你需要根据推力和旋转角度来施加推力作用。为此，你需要引入`Math.h`头文件，但必须自行实现角度归一化宏，因为 Objective-C 中没有与.NET 中`NormalizeAngle()`等效的函数。不过没关系，这很容易实现。首先定义这个宏：

```
#define NormalizeAngle(x) ((return x % 360)+(x < 0 ? 360 : 0))
```

然后根据推力大小调整 x 和 Y 轴：

```
Y = Y – Math.sin(NormalizeAngle(rotation + M_PI / 2)) * totalseconds * thrustspeed;
X = X – Math.cos(NormalizeAngle(rotation + M_PI / 2)) * totalseconds * thrustspeed;
```

变量`rotation`显然代表了着陆器的旋转角度，该角度受用户按下左右方向键的影响；`totalseconds`值则表示推力键按下的持续秒数。`thrustspeed`是一个常量值 20，代表推力的大小。

最后，别忘了在引擎推进时减少燃料量。



### 旋转

你不会覆盖左右旋转的代码，因为它们彼此对称。我们来看看向左旋转需要做什么。当然，这个方法假设存在一个用初始值（此处为 1000）初始化的燃料箱，并且你传入了旋转键被按下的总秒数。你的方法如下所示：

```
// rotateLeft - 向左旋转着陆器
- (void)rotateLeft:(float) totalseconds
{
    // 如果没有燃料，则无法推进
    if (fuel <0)
        return;

    // 向左旋转
    rotationMomentum -= M_PI / 2.0f * totalseconds;

    // 消耗燃料
    fuel -= 0.5 // 每次推进都会消耗燃料
}
```

向右旋转的明显区别在于，你需要增加 `rotationMomentum` 的值，而不是减少它。

### 启用用户交互

显然，用户需要与游戏进行交互，尽管具体实现方式取决于你。一些选项包括检测按键、使用屏幕图像检测点击，甚至使用加速度计检测设备向左或向右倾斜。无论如何，你需要解释这些操作，并且使用以下按键与着陆器进行交互。如果不进行任何操作，着陆器就会在重力的作用下坠落到地面。对于你的着陆器，你需要执行以下操作。

*   如果上箭头被按下（无论是在屏幕上还是在键盘上），则推进器点火。
*   如果左箭头被按下（无论是在屏幕上还是在键盘上），则着陆器向左旋转。
*   如果右箭头被按下（无论是在屏幕上还是在键盘上），则着陆器向右旋转。

### 捕捉游戏事件

你需要监控一些额外的游戏事件：例如，检测何时着陆以及着陆时的速度。通过结合月球着陆器的 y 坐标和地面高度，并使用着陆器的速度作为判断是否移动过快的指标，这相当简单。

该方法仅在平坦地面时有效。如果你决定实现一个具有不同坡度的月球表面，则需要在你的游戏中检测这一点——也许可以查看着陆器周围的像素值。

### 处理图形

你的登月舱实现已经通过实现 `drawRect` 方法为绘制游戏图形做好了准备，尽管默认实现只是在 x 和 y 位置绘制相同的图形（非推进状态的着陆器）。

你需要调整此代码以代表其他状态，包括游戏运行中时，判断何时应该推进或自由下落，并以适合着陆器的角度进行绘制。角度显然与向左或向右旋转的按键有关。

要旋转图像，你可以在 Microsoft .NET 中使用 `rotateAt()` 方法，但 Objective-C 中不存在此类方法。相反，你可以使用一个简单的宏来在度数和弧度之间进行转换，弧度是被称为 `transform` 的 `UImagerotate` 等价物所使用的度量单位。首先定义如下宏：

```
#define degreesToRadians(x) (M_PI * (x) / 180.0)
```

然后，假设 `deg` 变量表示你想要旋转的度数，添加以下代码行：

```
myView.transform = CGAffineTransformMakeRotation(degreesToRadians(deg) );
```

如果你有兴趣了解更多信息，[`www.platinumball.net/blog/2010/01/31/iphone-uiimage-rotation-and-scaling/`](http://www.platinumball.net/blog/2010/01/31/iphone-uiimage-rotation-and-scaling/) 上的文章详细介绍了如何进行图像旋转和缩放。

### 显示最高分

这应该很简单，因为你已经在第 8 章中介绍了如何创建最高分代码，并且在第 6 章中还研究了显示动态用户界面的不同机制。将这些与游戏最高分机制相结合，你可以创建一个最高分图表，并可以在游戏处于主菜单时显示。

### 资源

除了本附录中提供的帮助你完成登月舱游戏的信息以及随书提供的额外代码之外，以下资源可能有助于你完成和/或定制你的游戏：

*   *PhET 登月舱：* 包含作为教学辅助工具提供的在线版本游戏在内的多种资源。[`http://phet.colorado.edu/en/simulation/lunar-lander`](http://phet.colorado.edu/en/simulation/lunar-lander).
*   *LunarView.java：* 一个基于 Java 的登月舱游戏实现，适用于 Android 移动设备。[`http://developer.android.com/resources/samples/LunarLander/src/com/example/android/lunarlander/LunarView.html`](http://developer.android.com/resources/samples/LunarLander/src/com/example/android/lunarlander/LunarView.html).
*   *Code Project：* 一个基于 .NET 的登月舱游戏实现，使用 C# 编写。[`http://www.codeproject.com/KB/game/lunarlander.aspx`](http://www.codeproject.com/KB/game/lunarlander.aspx).
*   *登月舱历史：* 维基百科中关于登月舱游戏的历史。[`http://en.wikipedia.org/wiki/Lunar_Lander_(video_game)`](http://en.wikipedia.org/wiki/Lunar_Lander_(video_game)).



### ![image](img/square1.jpg) A

`Accelerate Framework`（`Accelerate.framework`），96

`Accelerometer` 类，273

加速度计，271–273

操作表，185

`Active Server Pages`（`ASP`），92

活动指示器和进度指示器，174–175

`ADC`（`Apple Developer Connection`），4，11

`add()` 方法，226

`添加仓库` 选项，32

`addHighScoreEntry`，199–203

`addObject` 方法，190–191，199–200

`Address Book UI`（`AddressBookUI.framework`），93

`AddressBook`（`AddressBook.framework`），94

`Adhoc` 机制，通过其发布，253–254

`Adhoc` 方法，260

`Adobe Flash Professional Creative Studio 5` 平台，21–22

警告表，185

警告，184–185

`alloc()` 方法，36–37，40，46

`API` 限制，5

`应用设计策略`，30

`App Store`

平台，22–25

在`App Store`上销售应用，23–24

向`App Store`提交应用，24–25

通过`App Store`发布，254–259

准备提交，254–256

上传应用二进制文件，256–259

`Appcelerator Titanium Mobile` 平台，18–19，69–75

使用其创建的`Hello, World`应用，70–75

安装，69–70

`Apple`

组件，使用其进行应用开发，10–13

平台和技术，7–13

使用`Apple`组件进行应用开发，10–13

`iOS`，9–10

`Apple`使用的术语和概念，7–9

`Apple Developer Agreement`，209，218–219，221

`Apple Developer Connection`（`ADC`），4，11

注册为`Apple 开发者`，2–4

`Apple 操作系统`。*参见* `iOS`

关于 UI 的`Apple`资源，185–186

应用主目录，89

`Application Loader`，256–257

`Application` 对象，82

应用沙盒，89

应用类型及关联的视图控制器，154–157

基于导航的应用，156–157

基于标签栏的应用，155–156

基于工具的应用，154–155



`applicationDidBecomeActive` 方法，86–87

`applicationDidEnterBackground` 方法，87

`ApplicationID`，89

应用程序，81–83，223–260

与 .NET Framework 的比较，93–94

行为，88–89

与应用程序沙盒，89

多任务处理，89

方向变化，88

调试

使用 `NSLog` 命令捕获诊断信息，232

分析应用程序，233–237

使用模拟器，237–239

Xcode 4 调试器，229–231

部署，240–252

创建证书以签名应用程序，241–242

`Provisioning Portal` 功能，244–250

注册设备，243–244

设计考虑因素，82

设计模式，82–83

开发，相关资源，259–260

开发

原生应用程序，6–7

使用 Apple 组件，10–13

Web 应用程序，6

初始化，45–47

生命周期，85–86

内部数据管理，189–191

基于导航，及关联的视图控制器，156–157

分析，233–237

发布，253–259

相关资源，259–260

通过 `Adhoc` 机制，253–254

通过 App Store，254–259

在 App Store 平台销售，23–24

状态，86–87

提交到 App Store 平台，24–25

基于标签栏

及关联的视图控制器，155–156

实现，157–163

测试，223–229

在设备上，239–240

相关资源，259–260

单元测试，224–229

上传二进制文件，256–259

基于工具，及关联的视图控制器，154–155

`Applications` 文件夹，63，65，69，77

`applicationWillEnterForeground` 方法，87

`applicationWillResignActive` 方法，87

`ARC`（自动引用计数），40，54–56

`ASP`（活动服务器页面），92

程序集，在 .NET framework 中，217–218

`属性检查器`，108

自动引用计数（`ARC`），40，54–56

`AV Foundation`（`AVFoundation.framework`），94

### ![image](img/square1.jpg)B

`bbitem IBOUTLET`，168

行为，88–89

与应用程序沙盒，89

多任务处理，89

方向变化，88

定制方法，139，141，143

二进制，应用程序，256–259

`Block` 对象，83

构建文件夹，73

`构建阶段` 标签页，193

`Builder` 文件，44，268，276

`Button` 类，177



### ![image](img/square1.jpg) C

- `C#` 类, 100
- `C#` 接口, 100
- `Calculator` 类, 226
- `CalculatorTest.m` 文件, 229
- 相机, 266–267
  - 相机基础, 266–267
  - 相机示例应用, 267–271
  - iOS 5 中更新的功能, 279–280
- `cameraViewController`, 269
- 层叠样式表（`CSS`）, 10
- 创建用于签署应用的证书, 241–242
- `CFNetwork`（`CFNetwork.framework`）, 94
- `CGRect`, 135, 140
- `CIL`（公共中间语言）, 15
- `Class` 类, 136
- `class` 关键字, 100
- Objective-C 中的类, 38–39, 97
- `CLLocation` 类, 262–265
- `CLLocation` 参数, 262
- `CLLocationManagerDelegate`, 262–263
- `Close()` 方法, 128
- `CLR`（公共语言运行时）, 40
- Cocoa Touch, 7–8, 10, 12, 14–15
- IDE 工作区中的代码补全, 107
- XCode 4 中的代码片段, 111
- 集合类, 190–191
- Objective-C 中的注释, 104
- 常用控件, 178
- 公共中间语言（`CIL`）, 15
- 公共语言运行时（`CLR`）, 40
- 连接检查器, 108
- 常量（自文档化代码）, 137
- 第三方工具的约束, 57–58
- 内容视图, 180–183
  - 表格内容视图, 180–181
  - 文本内容视图, 181–182
  - 网页内容视图, 182–183
- iPad 专属控制器, 163–174
  - 弹出视图控制器, 163–171
  - 分视图控制器, 171–174
- 控件, 174–185
  - 操作列表, 185
  - 活动与进度指示器, 174–175
  - 警告框, 184–185
  - 常用控件, 178
  - 内容视图, 180–183
    - 表格内容视图, 180–181
    - 文本内容视图, 181–182
    - 网页内容视图, 182–183
  - 日期时间与通用选择器, 175–176
  - 详细信息展开按钮, 176
  - 信息按钮, 176–177
  - 导航与信息栏, 179–180
    - 导航栏, 180
    - 状态栏, 179
    - 工具栏, 179
  - 页面指示器, 177
  - 搜索栏, 177
  - 分段控件, 178
  - 开关控件, 177
- `Core Data`（`CoreData.framework`）, 95
- `Core Graphics`（`CoreGraphics.framework`）, 94
- `Core Mono` 组件, 14–15
- `Core OS`, 10
- `Core Services`, 7, 10, 13, 94
- `Core Telephony`（`CoreTelephony.framework`）, 95
- `Core Text`（`CoreText.framework`）, 94
- `CoreData.framework`（`Core Data`）, 95
- `CoreGraphic.framework`, 94, 214
- `CoreTelephony.framework`（`Core Telephony`）, 95
- `CoreText.framework`（`Core Text`）, 94
- `createTabGroup()` 方法, 73
- `CS`（创意工作室）, 21–22
- `CSS`（层叠样式表）, 10



### ![image](img/square1.jpg) D

**数据库**
- 连接数据库，197
- 内嵌于 iOS 的数据库，192–197
- 创建或打开数据库，194
- 在数据库中创建表，195
- 从数据库读取数据，196–197
- SDK 选项，193–194
- 向数据库写入数据，195–196

**DataView 控件**，181

**日期与时间选择器，及通用选择器**，175–176

**DateTimePicker 类**，175

`dealloc()` 方法，52, 54–55, 122, 125

**释放内存**，270

**调试区视图**，110

**Debug-iphoneos 文件夹**，213

**Debug-iphonesimulator**，213

**调试**
- 使用 `NSLog` 命令捕获诊断信息，232
- 分析应用程序，233–237
- 使用模拟器，237–239
  - 更换设备，238
  - 更换 iOS 版本，238
  - “主屏幕”功能，239
  - “锁定”功能，239
  - “模拟硬件键盘”功能，239
  - 模拟运动，238
  - “切换通话中状态栏”功能，239
  - 触发低内存警告，238
  - “电视输出”功能，239
- Xcode 4 调试器，229–231

`Debug.WriteLine`，232

**声明类**，129

**委托**，83, 96, 103–104

**部署**，240–252
- 创建签名应用程序的证书，241–242
- 配置门户功能，244–250
- 注册设备，243–244

**应用程序设计**
- 注意事项，82
- 模式，5, 82–83

`desiredAccuracy` 属性，263

**详细信息披露按钮**，176

**开发资源**，259–260

**设备兼容性**，5

**设备**
- 在模拟器中更换设备，238
- 外形因素，使用示例应用程序，147–149
- 支持设备方向，150–154
- 平台与设备约束，146–154
- 注册设备，243–244
- 用代码针对多个设备进行开发，276–277
- 在设备上测试，239–240

**诊断信息，使用 `NSLog` 命令捕获**，232

`didAccelerate` 方法，273

`didFailWithError`，262–263

`didFinishLaunchingWithOptions()` 方法，48, 86, 122

`didFinishPickingMediaWithInfo` 方法，269

`DidLoad` 事件，273

`didLoad` 方法，266

`didReceivedMemoryWarning` 事件，238

`didRotateFromInterfaceOrientation`，150

`didUpdateToLocation`，262–263

`dismissModalViewControllerAnimated`，128, 133

`dismissPopoverAnimated`，169–170

**显示屏的尺寸与分辨率**，146–150
- 使用设备外形因素的示例应用程序，147–149
- 点与像素，149
- 屏幕尺寸，149–150

**DragonFire SDK**，2, 16–18

`drawRect` 方法，135, 140, 283

**动态库**，208–209

### ![image](img/square1.jpg) E

**编辑器区**，48

**启用 ARC**，55

**Engine 类**，37

**Engine 示例**，39

**Engine 对象**，37

**枚举类型，自文档化代码**，138

**Event Kit 框架（`EventKit.framework`）**，95

**异常处理**，39–40, 96

**外部配件框架（`ExternalAccessory.framework`）**，96

### ![image](img/square1.jpg) F

**文件检查器**，108

**项目的文件结构**，44–45

**基于文件系统的存储，使用沙盒**，188–189

**FirstView**，161–162

**设备外形因素，使用示例应用程序**，147–149

**Foundation.framework 框架**，95, 214

**框架**，159

`fromInterfaceOrientation`，150–151


  
### G

- `Game`（游戏）事件，捕获，283
- `Game`（游戏）界面，127
- `Game Kit`（游戏工具包）（`GameKit.framework`），93
- `Game`（游戏）状态，用于`Lunar Lander`（月球着陆器）应用，118
- `GameDifficulty`（游戏难度），129–130，138
- `GameKit.framework`（游戏工具包），93
- 游戏，在其中实现物理效果，281–282  
  - 重力，281  
  - 旋转，282  
  - 推力，282
- `GameState`（游戏状态），129–130，138
- `GameView`（游戏视图）类，120，126，128–129，135–137
- `GameView`（游戏视图）头文件，用于`Lunar Lander`（月球着陆器）应用，128–137
- `GameView`（游戏视图）接口，126
- `GameView`（游戏视图）对象，137
- `GameViewController`（游戏视图控制器）类，120，123–126，128–129，132
- `GameViewController`（游戏视图控制器）属性，125
- `GameView.h` 文件，128
- `GameView.xib` 文件，138
- `GDI`（图形设备接口），92
- `General Public License`（通用公共许可证）（`GPL`），219
- `GeoCoordinate`（地理坐标）类，265
- `GeoCoordinateWatcher`（地理坐标监视器）对象，265
- `GeoPositionAccuracy`（地理定位精度）属性，265
- 手势检测，274–275  
  - 轻扫，275  
  - 触摸事件，274–275
- Getter 方法，98
- `Github` 库，221
- `Global Positioning System`（全球定位系统）。参见 `GPS`
- `GPL`（通用公共许可证），219
- `GPS`（全球定位系统），261–265  
  - 基于位置的服务  
    - 实现，262–264  
    - 概述，262  
    - 用途，264–265
- 图形用户界面（`GUI`），145
- `Graphics Display Interface`（图形设备接口）（`GDI`），92
- 图形，处理，283
- 重力，281
- `GUI`（图形用户界面），145

### H

- 硬件要求，适用于 `iOS` `SDK`，28
- 头文件，38–39，45，48，51
- `Hello, World`（你好，世界）应用  
  - 使用 `Appcelerator Titanium Mobile` 平台，70–75  
  - 使用 `Marmalade` `SDK`，77–78  
  - 使用 `MonoTouch` 组件，66–68
- `HelloWorldAppDelegate`（HelloWorld 应用委托），44
- `HelloWorld.cs` 文件，61
- `HelloWorld.exe`，62
- `HelloWorldViewController`（HelloWorld 视图控制器），45，48–49，51–52
- `HellowWorldView.xib` 文件，68
- 高分榜类，持久化，197–201  
  - 初始化，203  
  - 测试，201–203
- 高分榜示例，197–203  
  - 与 .NET 实现对比，204–205  
  - 持久化高分榜类，197–201  
    - 初始化，203  
    - 测试，201–203
- 高分榜，显示，284
- `HighScore`（高分榜）类，199
- `HighScoreEntry`（高分榜条目）类，198–201，203
- `HighScore.h` 文件，212，216
- 模拟器的`Home`（主页）功能，239



### I

`iAd`（`iAd.framework`），92

`IBAction` 属性，123，126，128–129，167，169

`IBOutlet` 属性，51–52，167

iCloud 应用，277–278

图标文件，252

`icon.png` 文件，252

`ID` 类型，102

IDE（集成开发环境），7，9，29

IDE 工作区，106–108

代码补全功能，107

项目编辑器，108

方案，107–108

标识检查器，108

图像文件，12

图像 I/O（`ImageIO.framework`），94

`Image` 属性，267

`ImageIO.framework`（图像 I/O），94

iMessage 服务，iOS 5，279

实现文件，45，226

信息按钮，176–177

`Info.plist` 文件，85–86，179

信息栏与导航栏，179–180

初始化

应用程序，45–47

视图，48–53

`initWithParameters` 方法，198–199，201，203

`Insert()` 方法，204

检查器面板，48

Xcode 4 中的检查器，108

安装 iOS SDK，30–35

集成开发环境（IDE），7，9，29

集成测试，225

Interface Builder，12，47–48

界面控件，153，174，178，186

接口，82，84，88，91–92，96–97，100–103

支持网络的表格，220

利用网络存储数据，192

iOS（苹果操作系统），9–10，278–280

在模拟器中更改版本，238

iMessage 服务，279

集成 Twitter 服务功能，279

库，对比 .NET 框架库，209–210

报亭应用，279

通知中心功能，278–279

提醒事项应用，279

SDK，12–13

更新后的功能，279–280

iOS 开发中心，3，8

iOS 开发者，3–4，10

iOS 嵌入式数据库

创建或打开，194

在其中创建表，195

从中读取数据，196–197

SDK 选项，193–194

向其写入数据，195–196

iOS 人机界面指南，30，115

iOS SDK，27–56

ARC，54–56

启用，55

迁移至，55

概述，55

使用 ARC 编程，55

创建用户界面，47–53

初始化视图，48–53

使用 Interface Builder，47–48

硬件要求，28

初始化应用程序，45–47

安装，30–35

Objective-C，35–40

类，38–39

异常处理，39–40

导入，38

内存管理，40

命名约定，38

对象模型，36–37

方括号，37–38

术语，36

项目

创建，41–44

文件结构，44–45

资源，30

Xcode 的新功能，29

iOS 用户界面控件，115

iPad

专用控制器，163–174

弹出视图，163–171

分屏视图，171–174

通过代码定位多个设备，276–277

iPhone，通过代码定位多个设备，276–277

`ISerializable` 类，190

iTunes Connect，254–257，259–260

### J

越狱，24

### K

`kCLLocationAccuracyBest`，263–264

`kUTTypeImage`，266，268–269

`kUTTypeMovie`，266



### ![image](img/square1.jpg)L

`Label`（`UILabel`），178

`lander_nothrust` 属性，130，134–135，139–140

`Lander.tiff`，139

后进先出（`LIFO`），157

### 库，207–221

苹果开发者协议，218–219

定义，208

动态库，208–209

iOS 与 .NET 框架对比，209–210

静态库，208–218

.NET 框架中的程序集，217–218

使用 `Xcode 4` 工具，210–217

第三方库，219–221

分类，219

Github 库，221

实用库列表，220

SourceForge 库，221

类型，208

`Library` 面板，48，50

`libsqlite3.dylib` 库，193，209，217

许可协议，5

应用的生命周期，85–86

`LIFO`（后进先出），157

链接器，208

`LLVM` 编译器，107

`loadRequest`，182–183

`Localizable.string` 文件，100

实现基于位置的服务，262–264

`LocationManager` 类，262–264

模拟器的 `Lock` 功能，239

在模拟器中触发低内存，238

### Lunar Lander 应用，113–143，281–284

捕获游戏事件，283

创建项目，119–121

显示最高分，284

启用用户交互，282–283

`GameView` 头文件，128–137

处理图形，283

实现游戏物理，281–282

重力，281

旋转，282

推力，282

实现导航，127–128

初始化 XIB 资源，138–140

手动绘制用户界面，140

规划，114–118

设计资源，115–116

游戏状态，118

需求规格说明，116–117

用户界面，118

资源，284

自文档化代码，137–138

使用常量，137

使用枚举类型，138

测试，141–143

用户界面，121–126

使用定制方法，141

`Lunar Lander` 图形，118

`LunarLanderAppDelegate`，119，122，124

`LunarLanderViewController` 类，119，121–124，126，128

`LunarLanderViewController.xib` 文件，123，126

`Lunary Lander` 游戏，226



### M

`.m` 扩展名，39

`main()` 方法，45–47

主 `.nib` 文件，85

`MainViewController.m` 文件，216

`MainWindow.xib` 文件，44，48，85，119，161

`makeKeyAndVisible`，48–49

托管内存模型，83

地图工具包（`MapKit.framework`），92

Marmalade SDK（软件开发工具包），19–21，75–78

使用 Marmalade SDK 的 Hello, World 应用程序，77–78

安装，75–76

Marmalade Studio，20，75–76

Marmalade System，20，75–76

媒体层，10，94

媒体播放器（`MediaPlayer.framework`），94

`MediaLibrary` 对象，267

`MediaPlayer.framework`（媒体播放器），94

`mediaType`，269–270

`mediatypes` 属性，266

内存，低，238

内存管理，40，95

消息文件，39

消息 UI（`MessageUI.framework`），92

方法

使用方括号调用，37

在 Objective-C 中声明，97–98

Microsoft 开发者网络 (MSDN)，83

`Microsoft.Devices.PhotoCamera` 类，267

`Microsoft.Devices.Sensors` 命名空间，273

迁移到 ARC，55

移动设备，85，93–95

`MobileCoreServices.framework`，267

`.mobileprovision` 文件，253

模型-视图-控制器 (MVC)，5，83

Mono 环境，14–16

Core Mono 组件，14–15

安装，59–62

MonoDevelop 组件，15–16

MonoTouch 组件，15

以及 MonoTouch 组件，58–68

使用 MonoTouch 的 Hello, World 应用程序，66–68

安装，59–66

MonoDevelop 组件

安装，62–64

概述，15–16

MonoDroid，58

MonoMac，58

MonoTouch 组件，15

安装，64–66

Mono 环境以及 MonoTouch 组件，58–68

使用 MonoTouch 组件的 Hello, World 应用程序，66–68

安装，59–66

`Motion` 类，273

运动，模拟，238

MSDN（Microsoft 开发者网络），83

支持多设备，277

多任务处理，89，96

`multitaskingSupported` 属性，89

MVC（模型-视图-控制器），5，83

`MyStaticLibrary`，212



### N

- `Name` 属性，162
- 命名约定，针对 Objective-C，38
- 原生应用，开发，6–7
- `Navigate()` 方法，183
- 导航栏，和信息栏，179–180
- 导航，180
  - 状态，179
  - 工具栏，179
- 基于导航的应用，以及相关的视图控制器，156–157
- 导航，针对 Lunar Lander 应用，127–128
- `Navigator` 视图，110
- 导航器，在 XCode 4 中，109–110
- NDA（保密协议），218
- `NET` 控件，156, 178, 265
- .NET Framework，90–96
  - 应用程序服务，93–94
  - 程序集，217–218
  - 库，iOS 库对比，209–210
  - 运行时服务，95–96
  - 工具对比，与 Xcode 工具，105–106
  - 用户界面服务，91–92
- .NET 实现，与高分示例对比，204–205
- `NewGame()` 方法，130, 132, 134, 139–141
- `newMediaAvailable`，267–269
- Newsstand 应用，iOS 5，279
- `NIB` 文件，106, 120, 162, 173
- `Nil` 对象，35, 37
- 保密协议（NDA），218
- `NormalizeAngle()` 方法，282
- `Notification Center` 功能，iOS 5，278–279
- `NSArray`，190–191
- `NSAutoRelease` 类，46
- `NSCachesDirectory`，188
- `NSClassFromString()` 方法，277
- `NSData`，190
- `NSDate`，190
- `NSDictionary`，190–191
- `NSLog` 命令，使用其捕获诊断信息，232
- `NSLog()` 方法，109, 196
- `NSMutableArray`，190–192, 198–200, 203–204
- `NSMutableMutable` 类，190
- `NSNumber`，190
- `NSObject`，160
- `NSSet`，274–275
- `NSString` 类，190, 192, 194, 196–198, 200–203
- `NSTimeInterval()` 方法，281
- `NSTimer` 类，128, 135, 143

### O

- `Object` 类，190
- 对象模型，针对 Objective-C，36–37
- Objective-C，35–40, 96–104
  - 类
    - 声明，97
    - 概述，38–39
  - 注释，104
  - 委托，103–104
  - 异常处理，39–40
  - 导入，38
  - 接口与协议，100–103
  - 内存管理，40
  - 方法，声明，97–98
  - 命名约定，38
  - 对象模型，36–37
  - 属性，98–99
  - 方括号，37–38
    - 调用方法，37
    - 传递与检索数据，38
  - 字符串，99–100
  - 术语，36
- ODBC（开放数据库连接），197, 204
- OpenAL 和 OpenGL ES（`OpenAL.framework`），94
- `OpenGLES.framework`，94
- 方向变化，88
- 设备方向，支持，150–154



### ![image](img/square1.jpg)P

- 页面指示器，177
- 沿链传递调用，125
- 使用方括号传递，38
- `pathForResource` 方法，134、139
- PDF（便携式文档格式），277
- 持久化高分榜类，197–201
  - 初始化，203
  - 测试，201–203
- 照片，iOS 5 中的更新功能，279–280
- 物理引擎，在游戏中实现，281–282
  - 重力，281
  - 旋转，282
  - 推力，282
- `Picker` 类，176
- `Picker` 控件，115
- 选择器，日期与时间以及通用，175–176
- 每英寸像素数（PPI），150
- 像素，点与像素对比，149
- 规划
  - 针对登月者应用程序，114–118
    - 设计资源，115–116
    - 游戏状态，118
    - 需求规范，116–117
    - 用户界面，118
- 平台
  - 与设备，约束条件，146–154
  - 与技术，苹果，7–13
- 点，与像素对比，149
- 弹出视图控制器，163–171
- `PopOverExampleViewController`，167
- `PopoverSelection`，165
- `PopOverSelection` 类，166–167、169–170
- `PopOverSelection.h` 文件，166–167
- `PopOverSelection.m` 文件，166
- `PopOverSelection.xib` 文件，166
- 便携式文档格式（PDF），277
- PPI（每英寸像素数），150
- `presentPopoverFromBarButtonItem`，169–170
- 性能分析应用程序，233–237
- 编程，使用 ARC，55
- 进度指示器，活动指示器与进度指示器，174–175
- `ProgressBar` 类，175
- 项目编辑器，IDE 工作区，108
- 项目导航器，109
- 项目
  - 创建，41–44
  - 文件结构，44–45
- 属性，在 Objective-C 中，98–99
- 属性列表，用作存储，191
- 协议，在 Objective-C 中，100–103
- 配置门户功能，244–250
- `Public` 类，101
- 发布，253–259
  - 相关资源，259–260
  - 通过 Adhoc 机制发布，253–254
  - 通过 App Store 发布，254–259
    - 准备提交，254–256
    - 上传应用程序二进制文件，256–259

### ![image](img/square1.jpg)Q

- 快速帮助，108
- `quitGame`，128–129、133、141
- `QuitGame()` 方法，132

### ![image](img/square1.jpg)R

- RDBMS（关系数据库管理系统），192
- `readHighScores` 方法，202
- 注册，设备，243–244
- 关系数据库管理系统（RDBMS），192
- 提醒事项应用程序，iOS 5，279
- `Remove()` 方法，204
- 需求捕获阶段，114
- 分辨率，尺寸与分辨率，显示方面，146–150
- 资源文件夹，73
- 资源，适用于 iOS SDK，30
- `retain` 方法，51–52、54–55
- 检索，使用方括号，38
- `rootViewController`，161、173–174
- `rotateAt()` 方法，283
- `RotateLeft()` 方法，129–130、132–133、135、141
- `RotateRight()` 方法，129–130、132–133、135、141
- 旋转，282
- 圆角矩形按钮（`UIButton`），178
- 运行时服务，91、95–96



### S

- `s3dHelloWorld.mkb` 文件，77
- `.s3e` 文件，77

### Safari 浏览器，iOS 5 中更新的功能

279–280

### 沙盒，使用基于文件系统的存储

188–189

### `scheduledTimerWithTimeInterval` 方法

133，135–136

### 模式驱动的对象

193

### 方案，IDE 工作区

107–108

### `Score` 类

204

### 得分表

181

### 屏幕，尺寸

149–150

### SDK（软件开发工具包）

- 完整性，58
- DragonFire，16–18
- iOS，12–13
- Marmalade，19–21
- iOS 嵌入式数据库的选项，193–194

### 搜索栏

177

### `SecondView`

161–162

### 安全性

96

### 安全框架（`Security.framework`）

96

### 分段控件

178

### `SELECT` 语句

196，203

### `selector` 参数

133，135–136

### 自文档化代码

137–138

- 使用常量，137
- 使用枚举类型，138

### `self.view` 属性

153

### 服务器数据库

197

### `setNeedsDisplay` 方法

133，136–137

### `setter` 方法

98

### 短消息服务（SMS）

92

### `ShowDialog()` 方法

126

### 信号强度

239

### 简单对象访问协议（SOAP）

190

### 模拟器，模拟硬件键盘功能

239

### 模拟器，使用调试

237–239

- 更改设备，238
- 更改 iOS 版本，238
- 主页功能，239
- 锁定功能，239
- 模拟硬件键盘功能，239
- 模拟移动，238
- 切换通话中状态栏功能，239
- 触发低内存，238
- TV Out 功能，239

### 尺寸检查器

108

### 滑块（`UISlider`）

178

### SMS（短消息服务）

92

### SOAP（简单对象访问协议）

190

### 软件开发工具包——*参见* SDK

### `Sort()` 方法

204

### `sortArrayUsingSelector` 方法

203–204

### SourceForge 库

221

### 分视图控制器

171–174

### `SplitContainer` 类

174

### `sprintf()` 方法

232

### SQL 命令

195

### SQL 语句

195–196，201，204

### SQL（结构化查询语言）

192

### SQLite 数据库

13

### `sqlite3_bind_text()` 方法

196

### `sqlite3_prepare()` 方法

196，200–202

### `sqlite3_step()` 方法

196

### `sqlite_open()` 方法

194

### 方括号

37–38

- 调用方法，37
- 使用其传递和检索，38

### 开始游戏按钮

121，123，125–126

### 开始触摸事件

126

### `startAnimating` 方法

175

### `startUpdatingLocation`

262，264

### 应用状态

86–87

### 静态分析，在 XCode 中

4，111

### 静态库

208–218

- .NET 框架中的程序集，217–218
- 使用 Xcode 4 工具，210–217

### 静态链接库

208

### 状态栏

179

### `StatusBar` 类

179

### `STFail()` 方法

226

### `stopUpdatingLocation`

262

### 存储

187–205

- 高分示例，197–203
- 与 .NET 实现的对比，204–205
- 持久化高分等级，197–201
- 数据选项，188–197
  - 数据库，192–197
  - 使用沙盒的基于文件系统的存储，188–189
  - 互联网，192
  - 在应用内管理，189–191
  - 属性列表，191

### Store Kit（`StoreKit.framework`）

95

### 字符串，在 Objective-C 中

99–100

### 结构化查询语言（SQL）

192

### `Such` 类

98

### `superinitWithCoder:aDecoder` 命令

139

### 轻扫，检测

275

### 开关控件

177

### 符号导航器

109

### 合成

98

### `System.Collections.Generic.Dictionary` 类

190

### `System.Date` 类

190

### `System.Device.Location` 命名空间

265

### `System.Diagnostics` 命名空间

232

### `System.Runtime.Serialization.Formatters.Binary`

190

### 系统配置（`SystemsConfiguration.framework`）

95

### `System.String` 类

190

### `System.Threading.Timer` 类

135



### T

- 基于`tab bar`的应用
  - 及其关联的视图控制器，155–156
  - 实现方法，157–163
- `Tab`对象，73
- `tabBarController`属性，160–161
- `TabBarExample`示例，158–160
- `TabBarExampleAppDelegate.h`文件，160
- `TabBarExampleAppDelegate.m`文件，161
- `TabControl`控件，156
- `TABLE`命令，195
- 表格视图，180–181
- 在数据库中创建表，195
- 目标-动作模式，83
- TDD（测试驱动开发），224–226
- 技术与平台，7–13
- Objective-C 术语，36
- 测试与打包选项标签页，72
- 测试驱动开发（TDD），224–226
- `testExample()`方法，226–228
- 测试，223–229
  - 在设备上进行测试，239–240
  - 集成测试，225
  - 登月者应用，141–143
  - 持久化高分类，201–203
  - 相关资源，259–260
  - 单元测试，224–229
    - 定义测试方法，224–226
    - 编写与运行测试，226–229
- `TestMethod()`方法，217–218
- 文本输入框（`UITextField`），178
- 文本视图，181–182
- 第三方库，219–221
  - 库分类，219
  - Github 库，221
  - 实用库列表，220
  - SourceForge 库，221
- 第三方工具，5–6，13–22，57–78
  - Adobe Flash Professional Creative Studio 5 平台，21–22
  - Appcelerator Titanium Mobile 平台，18–19，69–75
    - 使用该平台开发的 Hello, World 应用，70–75
    - 安装步骤，69–70
  - 约束条件，57–58
  - DragonFire SDK，16–18
  - Marmalade SDK，19–21，75–78
    - 使用该 SDK 开发的 Hello, World 应用，77–78
    - 安装步骤，75–76
  - Mono 环境，14–16
    - Core Mono 组件，14–15
    - MonoDevelop 组件，15–16
    - MonoTouch 组件，15
  - Mono 环境与 MonoTouch 组件，58–68



《Hello, World》应用的页码，66–68

安装指南，59–66

推力，282

`Thrust()` 方法，128–129，132

`thrustEngine`，130，134–135，141

`ThrusterState`，129–130，138

`thrustspeed` 值，282

Titanium Developer 图标，69

`Titanium.UI` 命名空间，73

模拟器的“切换通话中状态栏”功能，239

`togglePopOverController`，167–169

工具栏，179

`Toolbar` 类，165，179

### 工具

适用于 .NET Framework 的工具 vs. Xcode 工具，105–106

加速度计，271–273

App Store 平台，22–25

在 App Store 销售应用，23–24

向 App Store 提交应用，24–25

Apple 平台与技术，7–13

使用 Apple 组件进行应用开发，10–13

iOS，9–10

其使用的术语与概念，7–9

应用开发

原生开发，6–7

Web 开发，6

相机

基础知识，266–267

示例应用，267–271

开发原则，5–6

未来发展方向，277–280

iCloud 应用，277–278

iOS 5，278–280

手势检测

滑动手势，275

触摸事件，274–275

全球定位系统 (GPS)，261–265

基于位置的服务，262

其用途，264–265

注册为 Apple 开发者，2–4

使用代码适配多款设备，276–277

第三方选项，13–22

Adobe Flash Professional Creative Studio 5 平台，21–22

Appcelerator Titanium Mobile 平台，18–19

DragonFire SDK，16–18

Marmalade SDK，19–21

Mono 环境，14–16

`totalseconds` 值，282

检测触摸事件，274–275

`touchesBegan` 方法，274–275

`touchesCancelled:withEvent`，274

`touchesEnded:withEvent`，274

变换，283

模拟器的 TV Out 功能，239

Twitter 服务，iOS 5 的集成功能，279

类型管理，95



### ![image](img/square1.jpg)U

`UIAccelerometer` 类，272 页

`UIAccelerometerDelegate` 协议，272 页–273 页

`UIActionSheet` 类，185 页

`UIActivityIndicatorView` 类，175 页

`UIAlertView` 类，184 页

`UIApplication`，179 页

`UIApplicationDelegate` 协议，86 页，160 页

`UIApplicationMain()` 函数，46 页–47 页，85 页

`UIButton` 类，123 页，176 页

`UIButton`（圆角矩形按钮），178 页

`UIDatePicker` 类，175 页

`UIDevice`，89 页

`UIEvent`，274 页–275 页

`UIGestureRecognizer` 类，274 页

`UIImage` 变量，130 页，134 页，139 页–140 页，236 页

`UIImagePickerController` 类，266 页–270 页

`UIImagePickerController` 对象，269 页

`UIImagePickerControllerSourceTypePhotoLibrary`，269 页

`UIImageView` 控件，268 页

`UIInterfaceOrientation`，150 页，153 页，169 页

`UIKit`（`UIKit.framework`），92 页，214 页

`UILabel` 控件，50 页

`UILabel` 对象，49 页，51 页–52 页

`UImage` 类，143 页

`UINavigationBar` 类，180 页

`UINavigationController` 界面，157 页

`UIPageControl` 类，177 页

`UIPopoverController` 类，164 页，167 页，170 页

`UIProgressView` 类，175 页

`UIs`（用户界面），145 页–186 页

苹果相关资源，185 页–186 页

应用类型及相关视图控制器，154 页–157 页

基于导航的应用，156 页–157 页

基于标签栏的应用，155 页–156 页

基于实用工具的应用，154 页–155 页

控件，174 页–185 页

操作列表，185 页

活动和进度指示器，174 页–175 页

警告，184 页–185 页

常用控件，178 页

内容视图，180 页–183 页

日期时间及通用选择器，175 页–176 页

详细信息 disclosure 按钮，176 页

信息按钮，176 页–177 页

导航和信息栏，179 页–180 页

页面指示器，177 页

搜索栏，177 页

分段控件，178 页

开关，177 页

创建，47 页–53 页

初始化视图，48 页–53 页

使用 Interface Builder，47 页–48 页

实现基于标签栏的应用，157 页–163 页

iPad 专用控制器，163 页–174 页

弹出视图，163 页–171 页

分屏视图，171 页–174 页

用于 Lunar Lander 应用，118 页–121 页，126 页

手动绘图，140 页

平台和设备限制，146 页–154 页

显示尺寸与分辨率，146 页–150 页

支持设备方向，150 页–154 页

`UISearchBar` 类，177 页

`UISegmentedControl` 类，178 页

`UISlider`（滑块），178 页

`UISplitViewController` 类，172 页–173 页

`UIStatusBarHidden`，179 页

`UIStatusBarStyle`，179 页

`UISwitch` 类，177 页

`UITabBarController` 类，156 页，161 页

`UITabBarController` 界面，157 页

`UITabController` 类，161 页

`UITabControllerDelegate` 协议，160 页

`UITableView` 类，180 页

`UITableViewController` 类，156 页，165 页，170 页

`UITextField`（文本字段），178 页

`UITextView` 类，181 页

`UIToolbar` 类，179 页

`UITouch`，274 页–275 页

`UIView` 类，136 页，274 页–275 页

`UIViewController` 类，120 页，123 页，129 页，267 页，273 页–275 页

`UIWebView` 类，182 页

`UIWindow` 对象，48 页

单元测试，224 页–229 页

定义测试方法，224 页–226 页

集成测试，225 页

TDD，225 页–226 页

编写与运行，226 页–229 页

`updateInterval` 属性，272 页–273 页

`UpdateLander()` 方法，131 页–132 页，137 页

`useCamera` 方法，267 页–268 页

用户交互，启用，282 页–283 页

用户界面指南，115 页

用户界面服务，.NET Framework 比较，91 页–92 页

用户界面。*参见* UIs

`UTCoreTypes.h` 文件，267 页

`UTF8String` 方法，194 页，196 页，200 页

实用工具视图，110 页

基于实用工具的应用及相关视图控制器，154 页–155 页



### ![image](img/square1.jpg) V

视图控制器，与应用程序类型相关，154–157  
基于导航的，156–157  
基于标签栏的，155–156  
基于实用工具的，154–155

`ViewController` 类，68，135–136

`viewControllers` 数组，48，173–174，273

`viewDidLoad` 事件，124，132–133，135，169–170，182

`ViewDidLoad()` 方法，52，68

`viewDidUnload` 方法，169–170

视图  
初始化，48–53  
在 XCode 4 中，110

可见性修饰符，125

### ![image](img/square1.jpg) W

WAP（无线应用协议），91  
Web 应用程序开发，6  
Web 视图，182–183  
`WebBrowser` 类，183  
Windows Forms，82，90，92–93  
`Windows` 方法，112  
Windows Presentation Foundation (WPF)，90  
无线应用协议 (WAP)，91  
全球开发者大会 (WWDC)，277  
WPF (Windows Presentation Foundation)，90  
`writeToFile` 方法，191，198  
`writeToURL` 方法，192  
WWDC（全球开发者大会），277

### ![image](img/square1.jpg) X, Y, Z

XCode 4，106–112  
代码片段，111  
调试器，229–231  
IDE 工作区，106–108  
代码补全，107  
项目编辑器，108  
方案，107–108  
检查器，108  
导航器，109–110  
静态分析，111  
静态库，210–217  
视图，110

`Xcode` 界面，106，109

Xcode 工具  
新功能，29  
概述，11–12  
对比 .NET Framework 工具，105–106

`XIB` 文件，108，121，126，128，136，138，166

`XIB` 资源，初始化，138–140

`XML` 文件，85，191，204  
`XMLSerializer` 类，204
