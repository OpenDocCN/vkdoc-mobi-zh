# 附录 A：在 iOS 设备上运行代码

iOS 开发意味着许多事情：充满挑战、乐趣、回报和兴奋。但有一件事它绝不是：免费的。首先，任何合法的 iOS 开发都需要使用 Mac OS X，而这需要有一台符合苹果许可协议的 Mac 电脑。拥有 Mac 后，你可以从 Mac App Store 免费下载 Xcode。使用免费版的 Xcode，你可以尽情编写 iOS 应用，并在 iOS 模拟器中运行它们。然而，要将应用在 iOS 设备上运行，你需要成为 iOS 开发者计划的成员。

## iOS 开发者计划

iOS 开发者计划可在 [`developer.apple.com/programs/ios`](http://developer.apple.com/programs/ios) 查阅，它涵盖了你作为 iOS 开发者与苹果互动的多个方面。注册该计划不仅能让你获得代码级技术支持、iOS SDK 的预发布版本以及 iOS 系统本身的预发布版本，还能让你在 iOS 设备上测试代码。同时，这也是向 App Store 提交应用（无论是免费还是付费应用）的必要条件。iOS 开发者计划的会员资格每年费用为 99 美元。此外，还有 iOS 开发者企业计划，可在 [`developer.apple.com/programs/ios/enterprise/`](http://developer.apple.com/programs/ios/enterprise/) 查阅，年费为 299 美元。这两个计划的区别在于：iOS 开发者计划允许你在最多 100 台设备上运行代码，而 iOS 开发者企业计划允许你在不限数量的设备上运行代码。如果你计划向 App Store 提交应用，需要的是非企业版计划，不过你也可以两者都购买。

[www.it-ebooks.info](http://www.it-ebooks.info/)

为什么在设备上运行代码必须成为 iOS 开发者计划成员？有人可能会说，这是苹果在向开发者一点点榨取费用，但在笔者看来，这种说法是有缺陷的。考虑到开发者的数量，相对于苹果的其他业务，每年 99 美元的费用产生的收入微乎其微，在统计上几乎可以忽略不计。相反，我将这 99 美元的费用视为一种门槛：那些对 iOS 应用开发不够认真的人不会购买会员资格，从而不会为该平台制作劣质应用。要求加入 iOS 开发者计划也让苹果能够执行更严格的安全标准。为了理解为什么在开发过程中需要经历这些步骤，让我们来看看 iOS 的安全模型以及它与我们的关系。

## iOS 应用安全

iOS 设备上的每个应用都必须拥有有效的加密签名才能运行。本书不会深入探讨涉及的加密技术细节，但请放心，这采用的是行业标准。这个加密签名有两个非常重要的特性。首先，它包含了对应用包内文件的签名；如果一个恶意用户以某种方式修改了你的应用的可执行代码，签名就会失效，设备将拒绝运行该应用。其次，用于创建加密签名的证书虽然具体指向创建该应用的开发者，但由苹果签发。如果某个恶意应用在 iOS 用户群体中造成严重破坏，苹果可以通过吊销该证书来禁用该应用，同样会导致 iOS 拒绝运行它。

## 获取证书

那么，如何获得一个证书呢？在 iOS 开发的早期，这是一个漫长的过程，涉及在开发者计划网站的 iOS 配置门户部分进行操作。而在 Xcode 4.3 中，这个过程完全自动化了。打开 Xcode，选择 `Window` ➤ `Organizer` 或按下 `Command+Shift+2` 打开组织器窗口。组织器窗口顶部的带状区域充当标签栏；选择 `Devices` 部分。在屏幕左侧的 `Library` 标题下方，选择 `Provisioning Profiles`。点击屏幕底部的 `Refresh` 按钮。系统会提示你登录 iOS 开发者计划账户。如果你还没有证书，Xcode 会为你创建一个。就这样，你便完成了一个证书的创建。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## iOS 应用配置



应用在设备上运行的第二个部分是它的配置文件（provisioning profile）。配置文件包含决定应用能否启动所需的两条信息：允许签署应用的证书列表，以及允许运行该应用的设备列表（通过设备的唯一标识符列出）。如果你要配置的是 App Store 或企业级应用，则无需指定单个设备，因为你的应用不会受限于特定设备。

要创建配置文件，首先至少需要指定一个可以运行该应用的 iOS 设备。这是另一个 Xcode 已经自动化了原本繁琐流程的领域。用 USB 线将 iOS 设备连接到 Mac。在 Xcode 的 Organizer 窗口中，它应该会出现在左侧边栏的`Devices`下。点击选中该设备。如果其右侧边栏旁有一个绿色圆圈，则说明它已配置为可用于开发。否则，请点击`Use for Development`按钮。点击后，Xcode 会为你创建一个用于通用开发的配置文件，并将其安装到设备上。此时，你应该就能在设备上运行代码了！在打开项目的 Xcode 中，点击工具栏上 Scheme 上方的按钮。这会打开 Scheme 弹出菜单。如果你看到目标名称右侧有一个三角形，将鼠标光标移到它上面以展开菜单。从列表中选择你的设备名称，点击`Run`，你的代码就会为你的设备编译并运行！既然现在你能在设备上运行代码了，就可以更好地测试那些利用硬件 API（如摄像头、GPS 芯片和加速度计）的代码了。

关于更多后续操作（例如向 App Store 提交应用）的信息，请参考 Apple 的文档“Tools Workflow Guide for iOS”，该文档可在 iOS Dev Center 的`http://developer.apple.com/ios`获取。

`http://www.it-ebooks.info/`

### 索引

**A, B**

**C, D, E, F**

**Blocks (代码块)**  
Camera (摄像头)  
arrays (数组)  
types, 278  
comparison selectors (比较选择器), 199  
`UIImagePickerController`  
`NSComparator`, 200–201  
`allowsEditing` property, 283  
sort descriptors (排序描述符), 199–200  
`cameraFlashMode`, 284  
asynchronous callbacks (异步回调), 193–194  
custom overlay view (自定义覆盖视图), 279  
block-based methods (基于块的方法), 192  
`mediaTypes` property, 282  
code, 201–203  
`MyStuff`, 278–279  
encapsulated functions (封装函数), 182  
photos (照片), 278–281  
enumeration (枚举)  
rear-facing and front-facing (后置和前置)  
fast enumeration (快速枚举), 196  
camera (摄像头), 280–281  
for loops (for 循环), 194–195  
`UIView` subclass, 280  
Mac OS X and iOS 4, 197–198  
`videoQuality`, 284  
`NSEnumerator`, 195–196  
videos (视频), 282–284  
selectors (选择器), 197  
`UIVideoEditorController`, 284–285  
meaning, 181–182  
memory management (内存管理), 184–185  

**G**  
Grand central dispatch (Grand Central Dispatch)  
objects, 185–186  
parameters to methods (方法参数), 189–190  
code dispatch (代码分发), 231–233



`retain` 对象, 188–189

`dispatch_async()` 函数, 231

作用域, 186

`dispatch_sync_f()`, 231

存储限定符, 187

框架, 230

`TwitterExample` 代码

面向对象框架, 236

活动指示器, 206–208

队列, 233–237

完成处理程序, 203–206

信号量, 237–240

`Typedefs`, 183

时间, 240–241

`UIView` 动画, 190–192

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 索引

## H

`viewDidUnload` 方法, 289

处理用户触摸, 109

`CoreLocation`, 293–295

自定义视图, 111–112

iOS 设备, 307

直接操作, 109

iPhone、iPod 和 iPad, 277

图形用户界面, 109

位置数据, 293

响应者链, 109–111

磁力计设备, 307–308

滚动视图, 118–119

### MapKit

UI 变更, 120

`CLGeocoder` 对象, 297–298

操作表, 134

`CoreLocation` 头文件, 296–297

下拉列表, 130

297

编辑模式, 136, 138

框架, 295

图像设置, 产品, 131

`LCTTweet`, 298–301

nib 文件, 124

`LCTTweetMapViewController`,

资产, 120–132

301, 304

表格视图重新排序, 137–139

`LCTTwitterController`, 295–296

文本字段, 302–303

表格视图, 135–137

触发器, 305

`UIActionSheet`, 132–135

推文地图, 306

`UIImagePNGRepresentation()`,

`TWRequest` 对象, 298

121

`viewDidUnload` 方法, 304

`viewDidLoad` 方法, 126

Web 应用 vs. 原生应用, 277

`viewWillAppear`, 125

`UIGestureRecognizer`, 112

## I

集成网络和 Web 服务。*另见* Twitter

### 内置手势识别器

113–115

自定义, 115–118

手势识别器生命周期, 113

目标-动作方法, 113

### 硬件 API。*另见* 相机

加速度计数据, 141

`CMMotionManager` 类, 290

### 异步操作网络

152

`NSURLConnection`, 148–149

URL 连接委托方法, 149–152

### 下载



### 索引

## A
- `Core Motion`, 288–293

## C
- `Caches`目录, 164–165

## D
- `device motion`, 292
- 文件, 162–164
- `device orientation`
- 图像, 165
- 通知, 287–288

## J
- `JSON`
  - 事件, 285–287
  - `foundation/model`对象
  - `gyroscope`数据, 291
    - 159–162
  - `magnetometer`, 292
- `JavaScript`语言, 157–158
- `MotionDot`, 289
- 表示, 158
- 实时数据, 285

## L
- 加载数据
  - `viewDidLoad`方法, 290
  - 解释, 143–145
- [www.it-ebooks.info](http://www.it-ebooks.info/)

## M, N
- `LCTViewController`, 357
- `Media`, 309 *另见* 播放音频
  - 用户界面, 354–356
  - `viewDidLoad`方法, 356–358
- `Model-View-Controller (MVC)`, 38–39
- `NSLocale`类, 352
- 多线程代码
  - 长时间运行的任务, 225–226
  - 处理器, 222
  - `iOS`设备, 371
  - 线程安全, 223–225
  - 应用安全, 372
  - `twitter`个人资料图片, 226–230
  - 证书, 372
  - `UIApplicationMain()`, 222–223
  - 开发者计划, 371–372
  - 配置文件, 373

## O
- `Objective-C`
  - 地址簿创建, 16
  - `JSON`
    - 类别, 34–35
    - `foundation/model`对象, 159–
    - 类扩展, 35–36
    - 162

## P
- `loSimpleWeather`应用, 146
- 使用, 81–82
- `NSURLConnection`类, 142
- 工作, 82
- 接收数据, 145–148
- `URL`连接, 142–143
- `URL`请求, 142
- 本地化
  - `Xcode`编辑器窗口, 148
  - 内置工具, 365
  - 解析`JSON`和`XML`, 153–154
  - 文件检查器, 363–364
  - 发送数据, 165
  - `HelloLocalization`, 366–368
  - `XML`解析器, 154–157
  - 关键区别, 363
- 国际化
  - 语言, 364
  - `App Store`, 351
  - `nibs`, 369
  - 日历、日期组件和时区, 360–361
  - 资源, 369
  - 字符串文件, 365–366
  - 日期, 359–360
  - 文本, 365–366
  - 通用部分, 352–353
- `LocaleNumbers`
  - 应用运行, 358

## U
- 处理用户输入, 361–362
- `iOS`设备, 371
- `UIApplicationMain()`, 222–223



code, 28

`JavaScript` 语言，157–158

`数据`，24–27

表示方法，159

前置声明，37

`web 服务`，153–154

`函数`，18

`头文件`和`实现文件`，

**K**

21–22

`连字符`(-)，19

`键值观察`（KVO）

实现，19

操作, 84–88

信息，20–21

手动实现，83–84

继承变量和方法，16

`NSObject` 类，80

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

`Objective-C`（*续*）

委托方法, 316

初始化方法，22

分类，309

实例变量，17

`SoundBoard 项目`

内存管理

添加按钮，318–319

ARC 与属性，33

AVFoundation 与

自动引用计数，

AudioToolbox 框架，

32–33

319

自动释放池，31

IBOutlet 和 IBAction, 319–

垃圾回收, 30

321

堆内存，30

标识与类型，317

iOS 设备，29

系统声音服务

引用计数，30

警告声音，313–314

栈内存，29

AudioServicesCreateSystemSo

方法声明，18

undID() 函数，310

`模型-视图-控制器`，38–39

`AudioServicesPlayAlertSound()`

新建文件，19–20

函数，313–314

对象, 16

`CFURLRef`，310

参数，23

限制，310

`指针`，17

MySystemSoundCallback

属性, 27–28

函数, 312

协议，36–38

系统声音，310–313

sayHelloButtonPressed，24

触发振动，314

方括号，18

播放音乐

可靠方法，15

媒体查询, 326–327

变量，17

`MPMediaPickerController`

委托方法, 324

**P、Q**

初始化方法，323

父视图控制器与子视图控制器

`MPMediaItem` 类，322

手势识别器，59

协议，324

模态视图控制器，57

`MPMusicPlayerController`，324–

导航控制器，57–58

326

页面视图控制器，59

`TitularSongs 项目`

`setRootViewController`，57

委托方法, 332–333

分割视图控制器, 59



`iTunes`, 327–328

`tab bar controllers`（标签栏控制器），58

`MediaPlayer` 框架，328–

**播放音频**

329

`APIs`（应用程序接口），317

`MPMediaItemArtwork` 对象，

`Audio Queue Services`（音频队列服务），317

331–332

`AVAudioPlayer`

`protocols`（协议），328

`advantage`（优势）, 315–316

`songsQuery` 方法，330–331

`afconver`t，314

`user interface`（用户界面）, 327–328

`callback functions`（回调函数），316

`viewDidLoad` 方法，329–330

**播放视频**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**索引**

`AirPlay`，338

`app bundle`（应用程序包），106

`CustomPlayer`

`caches directory`（缓存目录），107

`initWithNibName:bundle:`

`documents directory`（文档目录），106–107

方法，342

`moving data`（移动数据）

`LCTViewControlle`r，340

`delegate chai`ns（委托链），80

`MediaPlaye`r，341

`Key-Value Observin`g（键值观察），80–88

`MobileCoreServices`

`MyStuf`f，79

`frameworks`（框架）, 341

`notifications`（通知），88–90

`notifications`（通知），349

`singletons`（单例），90–92

`NSNotificationCenter`, 347–348

`notifications`（通知）

`outlets and actions`（输出口和操作）, 340–341

`common system`（常见系统），90

`selectVideoButtonPressed:`

`dealloc` 方法, 89

346

`post:`，89–90

`showImagePickerForSourceType:`

注册，88–89

pe，344， 346

`NSCoding`

`UIAlertView`，344

`initWithCoder:`，101

`UIImagePickerControll`er，338，

`modification`（修改），

345–346

`loadPossessionsFromDisk`，

`video player UI`（视频播放器用户界面），338–340

102

`view controller methods`（视图控制器方法），342–

`NSKeyedArchi`ver，104

344

`possessionsArchivePath`

`MPMoviePlayerController`，333–

方法，102–103

334

`savePossessionsToDisk:`

`MPMoviePlayerController` 的视图，

方法，101–102

335

`serializatio`n（序列化），104

`MPMoviePlayerViewController`，

`NSUserDefaults`

334–335

`class implementation`（类实现），98

`network video`（网络视频）, 335–336

`defaults` 命令，92

`thumbnails`（缩略图）, 337–338

`files`（文件），96–97

`loadPossessionsFromDisk`

**R**

方法，99

**运行循环**

`modification`（修改）, 98

`convenience method`s（便利方法），220

`mutable array`（可变数组），94

`cycl`e（循环），220

`event-driven application`（事件驱动型应用），219

`possession value`（财产价值）, 95–96

`PossessionListViewController`.



`main()` , 221–222

`m, 94`

`runUntilDate`, 221

属性列表, 93

`UIApplicationMain` 函数, 222

`savePossessionsToUserDefault`

`s, 94`

`[synchronize]` 方法, 95

## S

Xcode 属性列表, 99

### 保存内容

持久化数据

文件位置，iOS

核心数据, 107–108

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 索引

保存内容，持久化数据

表格视图, 174–176

(*续)*

时间线检索方法, 174

缺点, 92

推文, 171

文件位置，iOS, 106–107

`viewWillA`ppear, 176

手动文件处理, 104–105

`NSCoding`, 100–104

## U

`NSUserDefaults`, 92–100

用户界面设计, 243

`SQLite` 数据库, 105

动画, 266–268

原型单例, 91

着色界面元素

### 选择器

`didFinishLaunchingWithOption`

后台线程, 211

`s` 方法, 244

hood 消息, 210

导航控制器, 245

`performSelecto`r, 212

参数, 246–247

发送消息, 209

`tableVie`w, 247–248

目标-动作范式, 211

`tintColo`r, 245

`Twit`terExample, 213–215

推特客户端, 249

`UIColor` 类, 244

## T

### 定时器

`cellForRowAtIndexPath`, 253– 254

字典, 216

`LCTTimelineViewControll`er, 216

`NSDictionary`, 215

图像, 255–257

调度代码, 215

`initWithStyle` 方法, 250

`scheduledTimerWithTimeInterval`,

关键方法, 254

215

标签字体, 250

`scheduleTweetRefresh`, 217

`LCTTimelineViewController`,

`viewWillDisappear`, 218

250

### 推特

表格视图单元格, 254–255

账户, 169–170

`tableVie`w, 251–252

授权, 167

`UIFont` 类方法, 249

配置选项, 168

### reddit 照片浏览器

控制器对象, 170

`@synthesize` 图像, 269

框架, 168–169, 179

`JSON`, 268

函数, 166

`LCTViewControll`er, 271

实现文件, 171–173

`loadNextImageInSlideshow`

`initWithStyl`e, 178, 179

方法, 274–275

`LCTAppDel`egate, 177–178

`parseJSONData` 方法, 270–



LCTTwitterController, 174

273

`OAuth` 服务, 167–168

subreddits, 268

pointer, 173

`viewDidLoad` 方法, 268–272

`reloadButtonPressed`, 178

`UIKit` 框架， 243

`sharedInstance` 方法, 171

视图布局

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

自动调整大小值, 261–262

应用程序, 63–64

内容模式, 262–263

检查器连接, 66–67

坐标系, 258–260

委托协议, 74–78

显示属性, 260

头文件, 62

层级结构, 258

实现文件, 68

iPad, 265–266

`init` 方法, 63

`retina` 显示设备, 264–

模态视图控制器, 71–74

265

`MyStuf`f, 60

父视图与子视图, 258

页面布局, 65–66

`UIView` 子类, 263–264

父子视图控制器, 70–71

**V, W**

持有物类, 61–62

视图控制器。*另请参阅* 父视图和

`PossessionDetailViewControlle`

子视图控制器

r, 67

应用程序实现

持有物, 64–65

`Apple` 的控件, 47

降低耦合性, 60–61

`clickedButtonAtIndex` 方法,

表格视图

49

单元格, 50–51

`didTapButton`, 47

自定义单元格, 53–54

iOS 应用程序, 46

数据存储, 52–53

`sliderValueChanged` 方法,

委托方法, 52

48

删除/插入单元格, 53

`UIAlertView` 类, 48–49

编辑模式, 53

`UIButton` 类, 47

页眉和页脚文本, 52

`UISegmentedControl`, 47

协议方法, 50

函数, 41

分区, 52

iOS 应用程序, 41

`UITableView` 类, 49–50

生命周期, 42

`UIViewControl`ler, 41–42

`didReceiveMemoryWarning`

方法, 46

`IBActi`on, 44

**X, Y, Z**

`Xcode`

`Cocoa`, 1

开发者工具, 2–3

Hello, World

应用程序, 9–10

`Connections`, 12

nib 加载

头文件, 10

`IBOutlets`, 45

`Interface Builder`, 42–43

冗长方法, 46

`setTitleLabel`, 43–44

独立应用程序, 42

`viewDidLoad` 方法, 45



.xib 文件，54–55

实现文件，12–13

iPhone 和 iPad，56

初始项目布局，7

表格视图单元格，55–56

iOS 应用运行，7–8

数据传递

方法创建，11

[www.it-ebooks.info](http://www.it-ebooks.info/)

![Image 57](img/index-382_1.jpg)

