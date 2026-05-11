# 使用加速计与 Core Motion

设备中加速计的另一个用途是确定设备方向。大多数情况下，当设备方向改变时你无需干预。只需在你的视图控制器的 `shouldAutorotateToInterfaceOrientation:` 方法中返回正确的值，它就会自动旋转界面来适应方向变化。但在某些场景下，你可能希望在不改变用户界面方向的前提下，接收方向变化的通知。

要开始接收这些通知，你必须调用设备的 `beginGeneratingDeviceOrientationNotifications` 方法，该方法可通过 `currentDevice` 类方法获取。你可以像下面这样组合方法并开始生成通知：

```
[[UIDevice currentDevice] beginGeneratingDeviceOrientationNotifications];
```

一旦启用了通知，你有责任在不再需要时通过调用 `endGeneratingDeviceOrientationNotifications` 方法告知设备。如果不这样做，加速计硬件将保持比平时更活跃的状态，导致你的应用消耗更多电量。

启用方向通知后，代表设备的 `UIDevice` 对象会以名称 `UIDeviceOrientationDidChangeNotification` 向默认通知中心发布通知。只需为这个通知添加一个观察者，然后检查设备的 `orientation` 属性即可确定其当前方向。方向的可能值如下：

- `UIDeviceOrientationUnknown`
- `UIDeviceOrientationPortrait`
- `UIDeviceOrientationPortraitUpsideDown`
- `UIDeviceOrientationLandscapeLeft`
- `UIDeviceOrientationLandscapeRight`
- `UIDeviceOrientationFaceUp`
- `UIDeviceOrientationFaceDown`

列表中最后两个尤其值得注意。如果用户将手机平放，设备的加速计将无法确定界面方向，只能判断设备是面朝上还是面朝下。请务必始终考虑这两个值，因为忽略它们可能导致不可预见的情况。

## 使用 Core Motion 处理原始加速计、陀螺仪和磁力计数据

有时，设备方向对于你的应用程序来说粒度不够精细。这在游戏中经常出现，它们需要高精度的实时更新。加速计数据完全能够满足这些需求。部分 iOS 设备还配备了陀螺仪，有助于提供更精确的运动数据。这些数据通过 Core Motion 框架提供给您的应用程序。让我们来看一个快速示例应用，它利用设备运动在屏幕上移动一个圆点。

**注意：** 使用 Core Motion 需要在 iOS 设备上测试代码，因为 iPhone 模拟器不支持它。如果你还没有在设备上运行过代码，请确保拥有有效的 iOS 开发者账号，并阅读 `http://developer.apple.com/ios` 上的文档，了解如何在设备上开始运行代码。附录 A 中也会详细说明这一点。

打开 Xcode，选择 File ➤ New ➤ Project…或按 `+Shift+N` 创建新项目。在左侧栏 iOS 下选择 Application，然后在右侧选择 Single View Application 模板，点击 Next。输入 `MotionDot` 作为产品名称，填写你的公司标识符和类前缀。在 Device Family 旁边选择 iPhone，确保未勾选 Use Storyboards 和 Include Unit Tests，同时确保勾选了 Use Automatic Reference Counting。点击 Next，选择项目保存位置，点击 Create 创建项目。项目中会有一个已创建的视图控制器类——我的是 `LCTViewController`。打开其实现文件（`LCTViewController.m`）。

首先，我们将创建一个根据设备运动移动的视图。通过将加粗代码添加到文件顶部的类扩展中（如果没有类扩展，请将这段代码完整输入在 `#import` 语句和 `@implementation` 指令之间），添加一个实例变量来存储这个视图：

```
@interface LCTViewController () {
    UIView *_blueDot;
}
@end
```

修改 `viewDidLoad` 方法来创建这个视图，并修改 `viewDidUnload` 方法销毁它，添加加粗行：

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    _blueDot = [[UIView alloc] initWithFrame:CGRectMake(0.0f, 0.0f, 20.0f, 20.0f)];
    [_blueDot setBackgroundColor:[UIColor blueColor]];
    [[self view] addSubview:_blueDot];
}

- (void)viewDidUnload
{
    [super viewDidUnload];
    _blueDot = nil;
}
```

我们不希望用户界面旋转，因此删除划掉的行，并在 `shouldAutorotateToInterfaceOrientation:` 方法中添加加粗行：

```
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
    return (interfaceOrientation == UIInterfaceOrientationPortrait);
}
```

接下来，我们将设置实际的运动处理方法。首先，需要将 Core Motion 框架添加到项目中。在文件浏览器顶部选择项目，然后选择 `MotionDot` 目标。在编辑窗格中选择 Build Phases 标签，然后通过点击标题左侧箭头展开 Link Binary With Libraries 阶段。点击展开库列表底部的加号（+），从列表中选择 `CoreMotion.framework`，点击 Add。接着，再次打开视图控制器的实现文件（`LCTViewController.m`），在文件顶部添加 Core Motion 头文件的 `#import` 语句，并在类扩展中添加一个 `CMMotionManager` 类的实例变量，添加加粗行：

```
#import "LCTViewController.h"
#import <CoreMotion/CoreMotion.h>

@interface LCTViewController () {
    CMMotionManager *_motionManager;
    UIView *_blueDot;
}
@end
```

现在我们可以准备接收运动数据了。在 `viewDidLoad` 方法之后，通过添加以下加粗代码来添加 `viewWillAppear:` 和 `viewWillDisappear:` 的方法实现：

```
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    CGRect bounds = [[self view] bounds];
    CGFloat width = CGRectGetWidth(bounds);
    CGFloat height = CGRectGetHeight(bounds);
    CGRect blueDotFrame = [_blueDot frame];
    CGFloat dotWidth = CGRectGetWidth(blueDotFrame);
    CGFloat dotHeight = CGRectGetHeight(blueDotFrame);

    _motionManager = [[CMMotionManager alloc] init];
    if ([_motionManager isAccelerometerAvailable] == NO) {
        return;
    }
    [_motionManager setAccelerometerUpdateInterval:1.0 / 60.0];

    CMAccelerometerHandler accelerometerHandler =
    ^(CMAccelerometerData *accelerometerData, NSError *error) {
        CMAcceleration acceleration = [accelerometerData acceleration];
        CGFloat x = floorf(((width - dotWidth) / 2.0f) + (100 * acceleration.x));
        CGFloat y = floorf(((height - dotHeight) / 2.0f) + (100 * acceleration.y));
        CGFloat dotwidth = floorf(dotWidth * (20 * acceleration.z));
        CGFloat dotheight = floorf(dotHeight * (20 * acceleration.z));
        [_blueDot setFrame:CGRectMake(x, y, dotwidth, dotheight)];
    };

    [_motionManager startAccelerometerUpdatesToQueue:[NSOperationQueue mainQueue]
                                        withHandler:accelerometerHandler];
}

- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    if ([_motionManager isAccelerometerActive]) {
        [_motionManager stopAccelerometerUpdates];
    }
    _motionManager = nil;
}
```



这段代码的工作原理是，首先检查加速度计是否可用，然后将更新间隔设置为每秒 60 次。`accelerometerHandler` 变量持有一个代码块，该代码块处理来自运动管理器的返回数据，数据形式为 `CMAccelerometerData` 对象。而该对象又有一个名为 `acceleration` 的属性，它是一个包含 `x`、`y` 和 `z` 三个成员的 C 语言结构体，这三个成员是来自设备的实际测量值。我们使用这些数据来移动蓝点。当视图开始消失时，我们检查加速度计是否处于活动状态，如果是，则将其禁用。

在设备上构建并运行这段代码，然后稍微移动一下设备。你会注意到蓝点抖动得很厉害，但你可以通过移动设备在屏幕上移动它。你现在正在使用加速度计数据！虽然严肃的、面向业务的应用通常用不到这些数据，但如果你正在开发一款驾驶游戏，你绝对会想研究一下这个功能。

Core Motion 框架也涵盖了陀螺仪数据。其方法与加速度计的方法非常相似：你需要在调用 `setGyroUpdateInterval:` 和 `startGyroUpdatesToQueue:withHandler:` 之前调用 `isGyroAvailable`。这些方法的结构与它们的加速度计对应方法完全相同，区别在于传递给 `startGyroUpdatesToQueue:withHandler:` 的代码块接收的是一个 `CMGyroData` 对象，该对象包含一个 C 语言结构体 `CMRotationRate`，它有三个成员（`x`、`y` 和 `z`），用于测量沿着每个轴的旋转速率，单位是弧度每秒。当你完成接收陀螺仪数据后，务必调用 `stopGyroUpdates`。

一些 iOS 设备中的另一个传感器是磁力计，它测量设备周围的磁场。虽然听起来像是科幻电影里的东西，但这现在已经很常见了。磁力计的方法遵循与加速度计和陀螺仪方法相同的模式：首先调用 `isMagnetometerAvailable`，然后调用 `setMagnetometerUpdateInterval:` 和 `startMagnetometerUpdatesToQueue:withHandler:`，最后在完成操作后调用 `stopMagnetometerUpdates`。传递给处理器的对象是一个 `CMMagnetometerData` 对象，它有一个属性 `magneticField`，该属性是一个包含三个成员（`x`、`y` 和 `z`）的结构体，每个成员测量该方向上的磁场强度，单位是微特斯拉。有了这些信息，你就有足够的信息来创建属于自己的指南针应用了。

Core Motion 框架的杀手锏功能不在于它能使用这三个传感器中的任何一个，而在于它能同时使用它们。通过结合加速度计和陀螺仪，我们可以过滤掉重力，从而更纯粹地了解用户的运动，我们还可以利用周围的磁场来校准这些测量值。不过，这些都是复杂的计算，远远超出了本书的范围。幸运的是，你不需要自己解决这个问题。

Core Motion 返回的第四种数据类型，恰当地被称为设备运动。你将像使用其他类型一样使用它：调用 `isDeviceMotionAvailable`，然后调用 `setDeviceMotionUpdateInterval:`，接着调用 `startDeviceMotionUpdatesToQueue:withHandler:`，最后调用 `stopDeviceMotionUpdates`。处理器接收到一个 `CMDeviceMotion` 对象，其中包含了来自所有三个传感器的综合数据。其中的 `rotationRate` 属性与陀螺仪返回的数据类似，但已经由 Core Motion 移除了偏差（即传感器在没有输入信号时的输出）。加速度计数据则被分离为 `gravity` 和 `userAcceleration` 属性，允许你分别测量它们。`magneticField` 属性存储磁场数据，同样也由 Core Motion 移除了偏差。

当你使用 Core Motion 中的设备运动数据时，还有一种其他类型的数据不会返回的数据类型：`attitude` 属性。这是一个 `CMAttitude` 对象，包含 `roll`、`pitch` 和 `yaw`；以及四元数和旋转矩阵。如果你了解使用这些数据的数学原理，那么你可以直接使用，无需自行计算。

Core Motion 是一个非常强大的框架。你可能永远都用不到它，但如果你需要，你可以从中获取丰富的数据。然而，如果你需要确定手机的位置，你将使用 GPS 芯片和 Core Location 框架。

## 使用位置数据

人们喜欢在他们的设备上搜索本地数据。从餐厅评论到天气预报再到本地交友，位置数据对你的用户来说有数不清的用途。话虽如此，你也应该尽一切可能保护用户的隐私，并且绝不要将他们的位置数据发送到他们没有明确要求你发送的任何地方。这是非常严肃的事情——搞砸了用户隐私，美国国会可能会向苹果公司询问你的应用。

### 使用 CoreLocation

要获取原始位置数据，你将使用 Core Location 框架。在通过 `locationServicesEnabled` 类方法检查其可用性后，你将创建一个 `CLLocationManager` 对象的实例。第一次尝试访问用户的位置时，系统会显示一个提示，要求他们授权你的应用使用他们的位置数据。如果应用需要这些数据的原因不明确，你可以将位置管理器的 `purpose` 属性设置为一个字符串来解释你将要做的事情。你始终可以通过 `authorizationStatus` 类方法检查授权状态，该方法返回一个 `CLAuthorizationStatus` 值。如果该值等于 `kCLAuthorizationStatusDenied`，则表示用户拒绝了授权提示或完全禁用了定位服务。此时，除非用户打开“设置”应用并解除对你的应用的限制，或者重新启用被禁用的定位服务，否则你将无法使用 Core Location。

Core Location 框架从三个来源获取位置数据。第一个来源是预计只有通过蜂窝网络通信的设备才可用的移动基站三角定位。通过查看附近的移动基站并估算设备与基站之间的距离，设备可以大致了解你的位置。仅凭这些信息，你无法获得极其精确的位置，但如果你的应用只需要确定用户所在的国家，这通常就足够了。Core Location 还会检查附近有哪些 Wi-Fi 网络，并查询一个位置数据库，以帮助根据附近的网络确定用户的位置。这会更精确一些，因为 Wi-Fi 网络比移动基站更多。最后，对于带有硬件 GPS 芯片的设备，设备可以利用 GPS 卫星精确地定位用户的位置。前面确定用户位置的方法有助于将 GPS 搜索范围缩小到一个粗略的位置，然后 Core Location 可以利用这个粗略位置更快地找到正确的 GPS 卫星。

CoreLocation 的一大优点是，你不需要管理这些不同的数据源。Core Location 会根据需要自动使用它们，无缝地从一种切换到另一种。你只需要让 `CLLocationManager` 类开始更新用户的位置，它就会接管后续操作。

`CLLocationManager` 类与 `CMMotionManager` 类有许多相似之处，但没有现代的基于代码块的语法。你将调用 `startUpdatingLocation` 来开启位置管理器，然后它会调用带有数据的委托方法。你可以通过以下方式控制其返回的内容：



`desiredAccuracy` 属性。如果您需要确定街道级别的精度，应将 `desiredAccuracy` 设置为 `kCLLocationAccuracyBest`；但如果您只需确定用户位于某个特定地理区域，则可以使用另一个极值 `kCLLocationAccuracyThreeKilometers`，该值的精度仅为三公里。通常，您应尽可能指定最低的所需精度；定位管理器要求的精度越高，就越可能使用设备中高耗电的通信系统，而 GPS 芯片是其中最耗电的。

您将接收到的委托消息是 `locationManager:didUpdateToLocation:fromLocation:`。您会同时收到新位置和旧位置，从而可以判断位置变化是否足以触发操作。位置是 `CLLocation` 类的实例。对于用户位置而言，最重要的属性是 `coordinate` 属性（包含经纬度数据）以及 `altitude` 属性。还有一些其他有用的属性，例如 `course` 和 `speed`。

**注意：** 一旦您已根据需求充分获取了用户位置，请务必通过调用定位管理器上的 `stopUpdatingLocation` 来禁用定位服务。所涉及的硬件在功耗方面成本极高，因此运行时间越短越好。

如果您不需要超高频率地更新用户位置，只需知道他们已移动到不同位置；您可以改为通过调用定位管理器上的 `startMonitoringSignificantLocationChanges` 来监控重大位置变化。这仍然会使定位管理器调用 `locationManager:didUpdateToLocation:fromLocation:`，但频率不会那么高。请务必使用 `significantLocationChangeMonitoringAvailable` 类方法检查此功能是否可用。

如果您想知道用户何时进入或离开某个区域，则可以使用 `startMonitoringForRegion:desiredAccuracy:` 方法，并传入一个 `CLRegion` 对象，该对象包含中心坐标和以米为单位的半径。当您在监控某个区域时，定位管理器会适时调用 `locationManager:didEnterRegion:` 和 `locationManager:didExitRegion:` 方法。

### 使用 MapKit

如果您想向用户显示地图，则应使用 MapKit 框架。MapKit 的核心是 `MKMapView` 类，它会绘制一张用户可交互的地图，同时也允许您提供自己的地图数据。您可以在地图上高亮显示特定点、在特定地理区域上绘制线条，以及在地图上绘制任意形状。截至 iOS 5.1，MapKit 使用的所有地图数据均由 Google 提供，不过这在未来的 iOS 版本中可能会有所变化。

学习使用 MapKit 的最佳方式可能是直接动手实践，那么我们就开始吧。我们将为 TwitterExample 项目添加一个非常酷的功能：本地推文地图。在 Xcode 中打开项目。首先，我们需要将 CoreLocation 和 MapKit 框架添加到项目中。点击文件浏览器顶部的项目，然后在 TARGETS 下选择 TwitterExample 目标，并导航到 Build Phases 选项卡。展开 Link Binary With Libraries 阶段，点击加号按钮，选择 `CoreLocation.framework` 和 `MapKit.framework`（您可以按住按键同时点击来选择多个项目），然后点击 Add。接下来，我们需要为项目添加搜索功能。

打开 `LCTTwitterController.h`，并通过添加以下粗体行来为 `CLLocation` 类添加前置类声明以及两个新方法声明：

```objc
#import <Foundation/Foundation.h>

@class CLLocation;

@interface LCTTwitterController : NSObject

+ (id)sharedInstance;

- (void)authorizeAccountWithCompletionHandler:(void(^)(void))handler;

- (void)getTweetsInUserTimelineWithCompletionHandler:(void(^)(NSArray *tweets))handler;
```



- (void)getTweetsNearStreetAddress:(NSString *)streetAddress  
searchRadius:(NSUInteger)searchRadiusInMeters  
completionHandler:(void(^)(NSArray *tweets))handler;  

- (void)getTweetsNearLocation:(CLLocation *)location  
searchRadius:(NSUInteger)searchRadiusInMeters  
completionHandler:(void(^)(NSArray *tweets))handler;  

@end  

我们将从控制器外部调用的方法是`getTweetsNearStreetAddress:searchRadius:completionHandler:`，它会将第一个参数提供的街道地址转换为`CLLocation`对象，然后调用`getTweetsNearLocation:searchRadius:completionHandler:`完成搜索，使用 Twitter 搜索 API 查找该位置附近的推文，并将结果解析为推文数组。导航至`LCTTwitterController.m`，并通过在文件顶部添加加粗行来导入 Core Location 和 MapKit 头文件：

```objc
#import "LCTTwitterController.h"

#import <Accounts/Accounts.h>

#import <CoreLocation/CoreLocation.h>

#import <MapKit/MapKit.h>

#import <Twitter/Twitter.h>
```

接下来，在`@end`指令之前，通过添加加粗的行，将第一个方法添加到实现中：

```objc
- (void)getTweetsNearStreetAddress:(NSString *)streetAddress
searchRadius:(NSUInteger)searchRadiusInMeters
completionHandler:(void (^)(NSArray *))handler
{
    // 对地址进行地理编码
    CLGeocoder *geocoder = [[CLGeocoder alloc] init];
    [geocoder geocodeAddressString:streetAddress
                 completionHandler:^(NSArray *placemarks,
                                     NSError *error) {
        if ([placemarks count] > 0) {
            CLPlacemark *placemark = [placemarks objectAtIndex:0];
            CLLocation *location = [placemark location];
            if (location != nil) {
                // 现在我们有了地址，可以搜索 Twitter 了。
                [self getTweetsNearLocation:location
                              searchRadius:searchRadiusInMeters
                         completionHandler:handler];
            }
        }
        else {
            NSLog(@"对 %@ 进行地理编码时出错：%@", streetAddress, error);
        }
    }];
}
```

该方法首先创建一个`CLGeocoder`对象，用于将街道地址地理编码为位置坐标。一旦以`CLLocation`对象的形式获取到位置，它便会将该位置与搜索半径和完成处理程序一同传递给下一个方法。在第一个方法之后，通过添加加粗的行来添加下一个方法：

```objc
- (void)getTweetsNearLocation:(CLLocation *)location
                searchRadius:(NSUInteger)searchRadiusInMeters
           completionHandler:(void (^)(NSArray *))handler
{
    NSString *searchURI =
        [NSString stringWithFormat:
         @"http://search.twitter.com/search.json?geocode=%f,%f,%fkm",
         [location coordinate].latitude,
         [location coordinate].longitude,
         (float)searchRadiusInMeters / 1000.0f];
    
    NSURL *searchURL = [NSURL URLWithString:searchURI];
    TWRequest *searchRequest = [[TWRequest alloc] initWithURL:searchURL
                                                   parameters:nil
                                                requestMethod:TWRequestMethodGET];
    
    [[UIApplication sharedApplication]
        setNetworkActivityIndicatorVisible:YES];
    
    [searchRequest performRequestWithHandler:^(NSData *responseData,
                                               NSHTTPURLResponse *response,
                                               NSError *error) {
        [[UIApplication sharedApplication]
            setNetworkActivityIndicatorVisible:NO];
        
        if (responseData) {
            id topLevelObject = [NSJSONSerialization
                JSONObjectWithData:responseData
                           options:0
                             error:NULL];
            
            if ([topLevelObject isKindOfClass:[NSDictionary class]]) {
                NSArray *results = [topLevelObject
                    objectForKey:@"results"];
                
                if ([results isKindOfClass:[NSArray class]] && [results
                    count] > 0) {
                    if (handler != NULL) {
                        handler(results);
                    }
                }
                else {
                    NSLog(@"没有结果。");
                }
            }
        }
        else {
            NSLog(@"搜索时出错：%@", error);
        }
    }];
}
```

在这个方法中，我们基于位置的坐标属性和`searchRadiusInMeters`参数创建搜索 URL，为了适配 API，我们将搜索半径转换为千米。获取到 URL 后，搜索过程与之前相同：创建一个`TWRequest`对象并执行它。



由于我们要在地图上显示推文，因此需要一个实现 `MKAnnotation` 协议的类。标注是地图上的一个点，例如，我们可以在这里放置一个用户可以点击的图钉。由于地图上的每个点都对应一条推文，我们将创建一个 `LCTTweet` 类来存储推文信息。点击 **文件 -> 新建 -> 文件...** 或按下 **Command+N**。

在左侧栏中选择 **Cocoa Touch**，然后在右侧选择 **Objective-C class**。

点击 **下一步**。在 **Subclass Of** 中输入 `NSObject`，然后在 **Class** 中输入 `LCTTweet`。点击 **下一步**，然后点击 **创建** 将文件保存到磁盘。打开 `LCTTweet.h` 并添加加粗显示的代码：

```
#import <Foundation/Foundation.h>
#import <MapKit/MapKit.h>

@interface LCTTweet : NSObject <MKAnnotation>

@property (copy, nonatomic) NSString *text;
@property (copy, nonatomic) NSString *username;
@property (copy, nonatomic) CLLocation *location;

@end
```

接下来，打开 `LCTTweet.m` 并添加加粗显示的代码：

```
#import "LCTTweet.h"

@implementation LCTTweet

@synthesize text = _text;
@synthesize username = _username;
@synthesize location = _location;

#pragma mark - MKAnnotation Protocol Methods

- (NSString *)title
{
    return [self text];
}

- (NSString *)subtitle
{
    return [self username];
}

- (CLLocationCoordinate2D)coordinate
{
    return [[self location] coordinate];
}

#pragma mark -

@end
```

如你所见，这个类相当简洁。我们稍后会负责创建推文并填充数据；目前，我们只需要这个类实现 `MKAnnotation` 协议的方法 `title`、`subtitle` 和 `coordinate`。

我们将在 `LCTTwitterController` 类中将推文解析为 `LCTTweet` 对象。

打开 `LCTTwitterController.m`，并在文件顶部导入 `LCTTweet` 的头文件，添加加粗显示的行：

```
#import "LCTTwitterController.h"
#import <Accounts/Accounts.h>
#import <CoreLocation/CoreLocation.h>
#import <MapKit/MapKit.h>
#import <Twitter/Twitter.h>
#import "LCTTweet.h"
```

接下来，修改 `getTweetsNearLocation:searchRadius:completionHandler:` 方法，删除带删除线的行并添加加粗显示的行：

```
- (void)getTweetsNearLocation:(CLLocation *)location
searchRadius:(NSUInteger)searchRadiusInMeters
completionHandler:(void (^)(NSArray *))handler
{
    NSString *searchURI =
    [NSString stringWithFormat:
     [@"http://search.twitter.com/search.json?geocode=%f,%f,%fkm"],
     [location coordinate].latitude,
     [location coordinate].longitude,
     (float)searchRadiusInMeters / 1000.0f];

    NSURL *searchURL = [NSURL URLWithString:searchURI];
    TWRequest *searchRequest = [[TWRequest alloc] initWithURL:searchURL
                                                   parameters:nil
                                                requestMethod:TWRequestMethodGET];

    [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];

    [searchRequest performRequestWithHandler:^(NSData *responseData,
                                                NSHTTPURLResponse *response,
                                                NSError *error) {
        [[UIApplication sharedApplication]
         setNetworkActivityIndicatorVisible:NO];

        if (responseData) {
            id topLevelObject = [NSJSONSerialization
                                 JSONObjectWithData:responseData
                                 options:0
                                 error:NULL];

            if ([topLevelObject isKindOfClass:[NSDictionary class]]) {
                NSArray *results = [topLevelObject objectForKey:@"results"];
                if ([results isKindOfClass:[NSArray class]] && [results count] > 0) {
                    NSMutableArray *tweets = [NSMutableArray array];
                    for (NSDictionary *tweetDict in results) {
                        LCTTweet *tweet = [[LCTTweet alloc] init];
                        [tweet setText:[tweetDict objectForKey:@"text"]];
                        [tweet setUsername:
                         [tweetDict objectForKey:@"from_user_name"]];

                        // 并非每条推文都有精确位置。
                        NSDictionary *geoDict = [tweetDict
                                                 objectForKey:@"geo"];
                        if ([geoDict isKindOfClass:[NSDictionary class]]) {
                            NSArray *coords = [geoDict
                                               objectForKey:@"coordinates"];
                            float latitude = [[coords objectAtIndex:0]
                                              floatValue];
                            float longitude = [[coords objectAtIndex:1]
                                               floatValue];
                            CLLocation *location =
                            [[CLLocation alloc] initWithLatitude:latitude
```

*（注：代码块中的 URL 原本有一个多余的右括号 `]` 和一个不匹配的引号；这里已尽可能保持与原文一致。）*



