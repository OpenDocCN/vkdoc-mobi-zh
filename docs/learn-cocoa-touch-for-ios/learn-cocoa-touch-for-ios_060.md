# 第 9 章：用户界面设计

可以想象，你的图片文件数量很容易成倍增长，导致应用程序的文件体积变得很大，尤其是当你在 iPhone 和 iPad 上都使用了全尺寸图形时。

- `blueButton~ipad.png`
- `blueButton~ipad@2x.png`

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 视图动画

iPhone 如此成功的一个原因在于，使用它给人一种神奇的感觉。这听起来可能像是市场部门才会想出来的说辞，但事实是，提供一个引人入胜的用户界面可以让你的用户感觉他们不是在操作 iPhone 或 iPad，而是在直接与你的应用互动。获得这种感受的一个好方法是利用动画来增加应用的交互性。有些动画是内置的，例如在导航控制器中从一个视图控制器导航到另一个时。其他动画则可以由你自己创建。要对你的视图进行动画处理，可以使用 `UIView` 上的一些类方法。举一个基本示例，下面展示了如何在两秒内将一个名为 `myView` 的 `UIView` 从不透明（100% 透明度）动画到完全透明（0% 透明度）：

```
[UIView animateWithDuration:2.0
    animations:^{
        [myView setAlpha:0.0f];
}];
```

`animateWithDuration:animations:` 是可用的最基本的动画方法。第一个参数是 `NSTimeInterval` 值（实际上就是 `double`），第二个参数是一个代码块。在这个代码块中，只需设置你想要进行动画处理的视图的属性，`UIView` 类就会负责创建动画。许多属性都可以通过这种方式进行动画处理：

- `frame`
- `bounds`
- `center`（不过对此属性进行动画处理与直接设置它存在相同的缺陷）
- `transform`
- `alpha`
- `backgroundColor`
- `contentStretch`

要动画处理一个在屏幕上移动的视图，你需要将其 `frame` 值更改为你希望它在动画结束时拥有的 frame。还有一些更复杂但功能更强大的动画方法可用，其中最强大的是 `animateWithDuration:delay:options:animations:completion:`。这个名称冗长的方法，以及其他带有名为 `completion` 参数的方法，允许你在动画完成时运行一段代码。这段示例代码会等待五秒钟，然后将 `myView` 动画为完全透明；动画完成后，它会将 `myView` 从其父视图中移除：

```
[UIView animateWithDuration:2.0
    delay:5.0
    options:0
    animations:^{
        [myView setAlpha:0.0f];
    }
    completion:^(BOOL finished) {
        [myView removeFromSuperview];
}];
```

`completion` 参数接受一个带有一个参数的代码块，该参数是一个名为 `finished` 的 `BOOL` 值。如果动画成功完成，则 `finished` 会被设置为 `YES`。如果没有成功完成（例如，如果 `myView` 的父视图从视图层级中被移除，就可能发生这种情况），那么 `finished` 会被设置为 `NO`。

你也可以嵌套动画。以下代码会用 `myOtherView` 替换 `myView`，淡出 `myView` 并淡入 `myOtherView`：

```
[UIView animateWithDuration:2.0
    delay:5.0
    options:0
    animations:^{
        [myView setAlpha:0.0f];
    }
    completion:^(BOOL finished) {
        [[myView superview] addSubview:myOtherView];
        [myView removeFromSuperview];
        [myOtherView setAlpha:0.0f];
        [UIView animateWithDuration:2.0
            animations:^{
                [myOtherView setAlpha:1.0f];
        }];
}];
```

如你所见，在动画处理 `myOtherView` 之前，我们将其 `alpha` 属性设置为 0。由于我们在动画代码块外部更改了这个属性，因此这个更改是即时生效的。

[www.it-ebooks.info](http://www.it-ebooks.info/)

动画是为你的应用增添一丝活力的好方法。优秀的应用会以巧妙的方式使用动画，引导用户使用它们。苹果地图应用中的动画是一个精妙的例子。当你搜索某物并将结果显示为地图上的图钉时，图钉并不是凭空出现的；相反，它们会从上方落下，帮助用户看清它们的位置。人类大脑天生对运动敏感；动画可以利用这一点来吸引用户的注意力。

## 示例：Reddit 图片浏览器

为了展示我们新学的用户界面技能，让我们为社交新闻聚合网站 Reddit 构建一个酷炫的图片浏览器。如果你不熟悉 Reddit，它会将其内容组织成称为子版块的子类别。我们称这个应用为 `RedditPics`，它将显示发布到 Reddit 上的图片的动画幻灯片。

在构建基于第三方 API 的应用时，首先要回答的问题是：“我如何获取数据？” 对于 Reddit，答案很简单：只需在任何 URL 后追加 `.json`，即可接收该 URL 的 JSON 格式内容。我们可以通过加载以下 URL 来获取首页的主要 JSON 数据：

[`www.reddit.com/.json`](http://www.reddit.com/.json)

JSON 数据会返回一个项目数组，每个项目都有一个 URL。我们将检查 URL 中是否包含图片文件扩展名——`.png`、`.jpg` 和 `.gif`——并下载这些图片用于我们的幻灯片。除此之外，我们还将使该应用成为通用应用，以便它能够在 iPhone 和 iPad 上运行。打开 Xcode，从欢迎屏幕中选择“创建一个新的 Xcode 项目”。如果欢迎屏幕没有出现，请选择 File → New → Project…，或按 `⌘`+`Shift`+`N`。在左侧栏的 iOS 下选择 Application，然后选择 Single View Application 模板并点击 Next。对于 Product Name，使用 `RedditSlideshow`。如果你还没有设置 Company Identifier 或 Class Prefix，请现在设置；我将分别使用 `com.learncocoatouch` 和 `LCT`。对于 Device Family，选择 iPhone。不要勾选 Use Storyboards 和 Include Unit Tests，同时应勾选 Use Automatic Reference Counting。确认设置无误后，点击 Next。选择你希望将项目保存到磁盘的位置，再次点击 Next 以创建项目。

我们要做的第一件事是显示一张图片。首先，我们需要加载 Reddit 网站并解析其 JSON 输出。为此，我们首先在类扩展中声明一个方法来解析 JSON，同时，我们将声明一个数组来存储返回的图片 URL。导航至 `LCTViewController.m`（你的文件名可能因类前缀而异）。如果 `LCTViewController.m` 文件中已有类扩展，则将下面加粗的行添加到其中。如果没有，则将整个下面的类扩展添加到文件顶部，即 `@implementation` 指令之前，同时添加 `@synthesize` 行：

```
@interface LCTViewController ()

@property (strong) NSMutableArray *imageURLs;
- (void)parseJSONData:(NSData *)jsonData;

@end

@implementation LCTViewController

@synthesize imageURLs = _imageURLs;
```

接下来，找到 `viewDidLoad` 方法。如果不存在 `viewDidLoad` 方法，则将以下代码完整地复制到视图控制器的 `@implementation` 和 `@end` 指令之间；否则，只需添加加粗的行：

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // 加载视图后进行任何额外的设置，通常来自 nib 文件

    NSURL *redditURL = [NSURL URLWithString:@"http://www.reddit.com/r/aww/.json"];
    NSURLRequest *redditURLRequest = [NSURLRequest requestWithURL:redditURL];

    [NSURLConnection sendAsynchronousRequest:redditURLRequest
        queue:[NSOperationQueue currentQueue]
        completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
            if (data != nil) {
                [self parseJSONData:data];
            }
            else {
                NSLog(@"加载 JSON 出错: %@", error);
            }
    }];
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)



**注意：** Reddit，如同互联网上的许多其他内容，由用户生成。它返回的内容可能不适宜儿童、具有冒犯性或文化不敏感。之前使用的 URL 指向网站的一个部分（称为*子版块*），该部分专门用于发布可爱的图片（通常是动物的），名为“aww”。其他子版块可能包含不同类型的内容；浏览风险自负。

此代码加载 Reddit URL 并检查是否返回了数据。

如果返回了数据，它会将数据传递给`parseJSONData:`方法。让我们实现该方法。如果你查看 Web 服务返回的 JSON，其顶层对象是一个字典，后面跟着一个`data`键，该键有一个名为`children`的键，它映射到一个子字典数组。在这些字典中，我们将查看`url`键，以确定链接是否为图片。将以下方法实现添加到类实现中，位于文件末尾的`@end`编译器指令之前：

```objc
- (void)parseJSONData:(NSData *)jsonData
{
    [self setImageURLs:[NSMutableArray array]];
    
    NSError *parseError = nil;
    
    id returnedObject = [NSJSONSerialization JSONObjectWithData:jsonData
                                                         options:0
                                                           error:&parseError];
    
    if (returnedObject != nil) {
        if ([returnedObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *data = [returnedObject objectForKey:@"data"];
            NSArray *children = [data objectForKey:@"children"];
            for (NSDictionary *childDict in children) {
                NSString *url = [[childDict objectForKey:@"data"]
                                            objectForKey:@"url"];
                
                // Is this an image?
                if ([url hasSuffix:@".png"] ||
                    [url hasSuffix:@".jpg"] ||
                    [url hasSuffix:@".jpeg"] ||
                    [url hasSuffix:@".gif"]) {
                    NSURL *imageURL = [NSURL URLWithString:url];
                    [[self imageURLs] addObject:imageURL];
                }
            }
        }
    } else {
        NSLog(@"Error parsing data: %@", parseError);
    }
}
```

此代码遍历响应，查找带有图片扩展名的 URL，并将匹配项填充到`imageURLs`数组中。完成后，`imageURLs`数组将填充`NSURL`对象，每个对象代表一张不同的图片。一旦我们有了 URL 列表，就需要在屏幕上显示图片！首先，添加一个实例变量、一个属性和两个方法声明到类扩展，以及该属性的合成存取方法，还有一个稍后会用到的常量。通过将以下加粗的行添加到`LCTViewController.m`的顶部：

`static const NSTimeInterval kPictureDisplayTime = 15.0;`

```objc
@interface LCTViewController () {
    NSUInteger _currentImageIndex;
}

@property (strong) NSMutableArray *imageURLs;
@property (strong) UIImageView *imageView;

- (void)parseJSONData:(NSData *)jsonData;
- (void)startSlideshow;
- (void)loadNextImageInSlideshow;

@end

@implementation LCTViewController

@synthesize imageURLs = _imageURLs;
@synthesize imageView = _imageView;
```

我们将使用`startSlideshow`方法加载第一张图片，并使用`loadNextImageInSlideshow`方法加载下一张。因此，通过添加以下加粗的行，修改`viewDidLoad`方法，使其在解析从 Reddit 返回的数据后调用`startSlideshow`：

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    NSURL *redditURL = [NSURL
                        URLWithString:@"http://www.reddit.com/r/aww/.json"];
    NSURLRequest *redditURLRequest = [NSURLRequest requestWithURL:redditURL];
    
    [NSURLConnection sendAsynchronousRequest:redditURLRequest
                                       queue:[NSOperationQueue currentQueue]
                           completionHandler:^(NSURLResponse *response,
                                               NSData *data,
                                               NSError *error) {
        if (data != nil) {
            [self parseJSONData:data];
            [self startSlideshow];
        } else {
            NSLog(@"Error loading JSON: %@", [error
                                               localizedDescription]);
        }
    }];
}
```

接下来，添加`startSlideshow`方法的实现，以及`loadNextImageInSlideshow`的空实现（这样调用时应用不会崩溃）。在`parseJSONData:`方法之后、`@end`指令之前插入以下加粗的代码：


```objective-c
- (void)startSlideshow
{
    if ([[self imageURLs] count] == 0) {
        return;
    }
    UIActivityIndicatorView *activityIndicatorView = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleGray];
    CGRect activityIndicatorFrame = [activityIndicatorView frame];
    activityIndicatorFrame.origin = CGPointMake(floorf((CGRectGetWidth([[self view] bounds]) + CGRectGetWidth(activityIndicatorFrame)) / 2.0f),
                                               floorf((CGRectGetHeight([[self view] bounds]) + CGRectGetHeight(activityIndicatorFrame)) / 2.0f));
    [activityIndicatorView setFrame:activityIndicatorFrame];
    [[self view] addSubview:activityIndicatorView];
    [activityIndicatorView startAnimating];
    // 加载第一张图片
    _currentImageIndex = 0;
    NSURL *firstURL = [[self imageURLs] objectAtIndex:0];
    NSURLRequest *firstImageRequest = [NSURLRequest requestWithURL:firstURL];
    [NSURLConnection sendAsynchronousRequest:firstImageRequest
                                       queue:[NSOperationQueue currentQueue]
                           completionHandler:^(NSURLResponse *response,
                                               NSData *data,
                                               NSError *error) {
                               UIImage *image = [UIImage imageWithData:data];
                               if (image == nil) {
                                   return;
                               }
                               UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
                               [imageView setAutoresizingMask:(UIViewAutoresizingFlexibleWidth|UIViewAutoresizingFlexibleHeight)];
                               [imageView setContentMode:UIViewContentModeScaleAspectFit];
                               [imageView setFrame:[[self view] bounds]];
                               [[self view] addSubview:imageView];
                               [activityIndicatorView removeFromSuperview];
                               [self setImageView:imageView];
                               int64_t popTime = kPictureDisplayTime * NSEC_PER_SEC;
                               dispatch_time_t nextPictureLoadDelay = dispatch_time(DISPATCH_TIME_NOW, popTime);
                               dispatch_after(nextPictureLoadDelay, dispatch_get_main_queue(), ^{
                                   [self loadNextImageInSlideshow];
                               });
                           }];
}

- (void)loadNextImageInSlideshow
{
}
```

这段代码为第一张图片创建了一个 URL 请求，然后通过异步连接加载它。数据加载完成后，代码从中创建一个 `UIImage` 对象，再创建一个用于显示该图片的图像视图。接着，利用 `popTime` 变量创建 `dispatch_time_t` 类型的时间表示，并在此时间后调度 `loadNextImageInSlideshow` 方法。

构建并运行应用，你会看到加载完毕后图片才会出现，在此之前会看到一个活动指示器。一旦加载完成，应用不会再做任何事，因为我们把 `loadNextImageInSlideshow` 方法留空了。现在让我们填充这个方法，让幻灯片加载新的图片！请在 `loadNextImageInSlideshow` 的实现中添加以下**粗体**代码：

```objective-c
- (void)loadNextImageInSlideshow
{
    _currentImageIndex += 1;
    if (_currentImageIndex >= [[self imageURLs] count]) {
        return;
    }
    NSURL *imageURL = [[self imageURLs] objectAtIndex:_currentImageIndex];
    NSURLRequest *urlRequest = [NSURLRequest requestWithURL:imageURL];
    [NSURLConnection sendAsynchronousRequest:urlRequest
                                       queue:[NSOperationQueue currentQueue]
                           completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
                               UIImage *image = [UIImage imageWithData:data];
                               if (image == nil) {
                                   return;
                               }
                               UIImageView *nextImageView = [[UIImageView alloc] initWithImage:image];
                               [nextImageView setAutoresizingMask:(UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight)];
                               [nextImageView setContentMode:UIViewContentModeScaleAspectFit];
                               [nextImageView setFrame:[[self view] bounds]];
                               [nextImageView setAlpha:0.0f];
                               [[self view] addSubview:nextImageView];
                               [UIView animateWithDuration:1.0
                                                animations:^{
                                                    [[self imageView] setAlpha:0.0f];
                                                    [nextImageView setAlpha:1.0f];
                                                }
                                                completion:^(BOOL finished) {
                                                    [[self imageView] removeFromSuperview];
                                                    [self setImageView:nextImageView];
                                                }];
                               int64_t popTime = kPictureDisplayTime * NSEC_PER_SEC;
                               dispatch_time_t nextPictureLoadDelay = dispatch_time(DISPATCH_TIME_NOW, popTime);
                               dispatch_after(nextPictureLoadDelay, dispatch_get_main_queue(), ^{
                                   [self loadNextImageInSlideshow];
                               });
                           }];
}
```


### 硬件 API

该方法与之前的方法非常相似：获取下一个 URL，下载图像数据，然后创建图像视图。新增的部分是它使用的动画。旧图像视图淡出（其 `alpha` 属性动画至 0），同时新图像淡入（其 `alpha` 属性动画至 1）。加载新图像后，我们以相同方式调度下一个加载任务：创建一个 `dispatch_time_t` 时间表示，并在该时间后调度一个代码块。

再次构建并运行应用，效果就出来了！现在你应该能看到 Reddit 图片的幻灯片放映，并且图片之间带有动画过渡效果！

## 总结

本章全部关于用户界面。现在，你应该能够创建符合自己喜好的自定义用户界面，让你的应用脱颖而出。你应该能够使用字体、颜色和图像来设计视图，在屏幕上布局它们，并制作动画效果。在下一章中，我们将探索一些与硬件组件（如 GPS 芯片和加速度计）交互的 API，从而在屏幕上呈现更多数据。

[www.it-ebooks.info](http://www.it-ebooks.info/)

---

在移动开发的讨论中，经常出现的一个争论点是关于 Web 应用与原生应用。到目前为止，我们只编写了原生应用；所谓原生应用，是指用 Objective-C 编写、编译成二进制可执行格式并安装在设备上的应用。另一种选择是 Web 应用，它使用 HTML、CSS 和 JavaScript 编写，安装在服务器上，并通过浏览器访问——JavaScript 在运行时被解释，而不是编译成可执行二进制文件。有一些框架试图弥合这一差距，本质上是将网站隐藏在一个仅相当于 Web 浏览器的原生应用内部，但通常很容易区分原生应用和 Web 应用。原生应用运行速度更快、用户交互更流畅，并且在手机上能做的事情比 Web 应用更多。本书一直在介绍如何编写原生应用，而本章则关注这些应用如何做更多事情。

iPhone、iPod touch 和 iPad 拥有多个硬件传感器，你可以利用它们为己所用。这些小巧的设备中集成了一个或两个摄像头、一个或两个麦克风、一个 GPS 芯片、一个加速度计、一个陀螺仪，甚至还有一个磁力计！我们可以利用这些传感器，让应用能够知道你在哪里、移动速度有多快、以及你面朝哪个方向。加速度计可以告诉我们你是如何握持设备的，陀螺仪则告诉我们你是如何移动它的。将这些数据融入你的应用，就能创造出引人入胜、沉浸式的体验。在本章中，我们将介绍如何使用摄像头、加速度计、陀螺仪、GPS 和磁力计，以及它们相关的框架。我们将查看一些示例代码，并扩展我们的 Twitter 示例应用，使其能够使用你当前的位置。首先，我们将介绍如何使用摄像头拍摄照片和视频。

[www.it-ebooks.info](http://www.it-ebooks.info/)

---

## 使用摄像头

在 MyStuff 示例项目中，我们已经初步使用了摄像头 API，用它们来获取物品的图像并保存到磁盘。然而，在那个项目中，我们仅仅触及了摄像头 API 所能实现功能的皮毛。在 iOS 设备上使用摄像头有两种方式：第一种，正如我们在 MyStuff 中所做的那样，使用 `UIImagePickerController` 类及其内置 UI 从摄像头获取图像；第二种，使用 `AVFoundation` 框架。`AVFoundation` 框架是苹果为 iOS 创建的高级视听框架，后来也被移植到了 Mac OS X 上。它可用于许多强大的任务，包括音频和视频播放、实时视频处理，甚至视频编辑！事实上，iOS 上的 iMovie 应用就是使用 `AVFoundation` 编写的，并且没有使用任何私有 API，因此苹果在该应用中所做的一切，你同样可以在自己的应用中实现。



### AVFoundation 概述

`AVFoundation`是一个非常高级的框架，因此我们不会在这里对其进行详细介绍。关于这个主题已有大量书籍可供参考，同时苹果的官方文档也一如既往地提供了详尽的帮助。

### 使用`UIImagePickerController`处理照片

为了完成大多数与照片相关的任务，我们可以使用`UIImagePickerController`类来满足需求。正如我们在`MyStuff`示例中所见，创建并显示一个图片选择器控制器是简单的任务：

```
UIImagePickerController *imagePickerController =
[[UIImagePickerController alloc] init];
[imagePickerController setSourceType:UIImagePickerControllerSourceTypeCamera];
[imagePickerController setDelegate:self];
[self presentModalViewController:imagePickerController animated:YES];
```

这段代码创建了一个名为`imagePickerController`的`UIImagePickerController`实例。接着，它将源类型（`sourceType`）设置为`UIImagePickerControllerSourceTypeCamera`，从而触发拍照功能；我们也可以使用`UIImagePickerControllerSourceTypePhotoLibrary`或`UIImagePickerControllerSourceTypeSavedPhotosAlbum`来访问设备上已有的照片。在示例代码中，我们有一个名为`myViewController`的视图控制器，并将其设置为图片选择器控制器的代理（`delegate`）。

然后我们使用这个视图控制器来呈现图片选择器控制器。通常的做法是让一个视图控制器同时完成这两项操作：由于图片选择器控制器的代理也负责将其从屏幕上移除，因此让它同时负责初始呈现图片选择器控制器也是合理的。

以这种方式呈现的图片选择器控制器，默认会显示拍照的标准控件，并通过`imagePickerController:didFinishPickingMediaWithInfo:`方法向代理返回一张照片，或者通过`imagePickerControllerDidCancel:`方法指示用户取消了操作。

自定义`UIImagePickerController`行为的方式有很多。其中最简单的一种是向图片预览中添加一个覆盖视图（overlay view），你可以用它来辅助构图，或者使其与应用的用户界面风格保持一致。

图 10-1 展示了一个带有自定义覆盖视图的图片选择器控制器。

**图 10-1.** *带有自定义覆盖视图的图片选择器控制器*

这个覆盖层只是一张半透明的图片。创建图片选择器控制器并生成一个用作覆盖层的图片视图的代码如下（图片文件名为`Overlay.png`）：

```
UIImagePickerController *imagePickerController =
[[UIImagePickerController alloc] init];
[imagePickerController setSourceType:UIImagePickerControllerSourceTypeCamera];

// 此处的 "self" 应为 UIViewController 子类的实例，
// 且需遵循 UIImagePickerControllerDelegate 和
// UINavigationControllerDelegate 协议。
[imagePickerController setDelegate:self];

UIImage *overlayImage = [UIImage imageNamed:@"Overlay"];
UIImageView *overlayView = [[UIImageView alloc] initWithImage:overlayImage];
[imagePickerController setCameraOverlayView:overlayView];
[self presentModalViewController:imagePickerController animated:YES];
```

任何`UIView`的子类都可以使用；只需将图片选择器控制器的`cameraOverlayView`属性设置为你的覆盖视图即可。

自定义覆盖层不仅能美化外观，其实力远不止于此。实际上，你可以完全替换图片选择器控制器的默认界面（chrome），改用你自己定义的用户界面：只需将图片选择器控制器的`showsCameraControls`属性设置为`NO`。如果这样做，你需要实现自己的拍照按钮。该按钮应是覆盖视图的子视图，并调用图片选择器控制器的`takePicture`方法，这会导致图片选择器控制器调用其代理上的`imagePickerController:didFinishPickingMediaWithInfo:`方法。

如果你确实实现了带有“拍照”按钮的自定义覆盖界面，就可以使用图片选择器控制器拍摄多张照片，这是默认界面无法做到的。每次调用图片选择器控制器的`takePicture`方法时，它都会向代理发送另一张图片，但在第一张图片的代理方法仍在处理期间，你不能拍摄下一张。如果你希望连续拍摄多张照片，这个特性会非常有用。

当你隐藏内置的相机界面时，也会同时隐藏允许用户开启相机闪光灯的控制按钮。如果你想提供自己的控件来启用或禁用闪光灯，可以通过图片选择器控制器的`cameraFlashMode`属性来控制该设置。有效的设置值包括：`UIImagePickerControllerCameraFlashModeAuto`（默认设置）、`UIImagePickerControllerCameraFlashModeOff`和`UIImagePickerControllerCameraFlashModeOn`。请注意，并非所有配备摄像头的 iOS 设备都有闪光灯，因此此设置可能会被忽略。

内置相机界面提供的另一个控件是用于在具有两个摄像头的 iOS 设备上切换后置摄像头和前置摄像头的按钮。你也可以通过图片选择器控制器来控制此设置：只需将`cameraDevice`属性设置为`UIImagePickerControllerCameraDeviceRear`或`UIImagePickerControllerCameraDeviceFront`。在这样做之前，你需要通过调用`UIImagePickerController`类方法`isCameraDeviceAvailable:`并传入所需设备作为参数，来确保所选设备可用。你还可以通过`UIImagePickerController`类方法`isFlashAvailableForCameraDevice:`来根据摄像头设备判断闪光灯是否可用。

通过合理使用这些方法，你可以避免在相机界面中添加用于设置不可用选项的按钮；例如，如果用户从带有闪光灯的后置摄像头切换到没有闪光灯的前置摄像头，那么你应该移除或禁用你提供的闪光灯按钮。

虽然为图片选择器控制器提供自定义界面功能强大，但使用内置界面也有其优势。如果将图片选择器控制器的`allowsEditing`属性设置为`YES`，那么用户拍照后，系统会提示他们进行编辑，允许他们裁剪和移动裁剪后的图像。如果这样做，图片选择器控制器的代理可以通过在作为参数传入的字典中使用不同的键来同时访问原始图像和编辑后的图像。以下是获取每种图像的方法：

```
- (void)imagePickerController:(UIImagePickerController *)picker
didFinishPickingMediaWithInfo:(NSDictionary *)info
{
    UIImage *originalImage = [info
        objectForKey:UIImagePickerControllerOriginalImage];
    UIImage *editedImage = [info
        objectForKey:UIImagePickerControllerEditedImage];
    ...
}
```

当你允许用户编辑他们通过图片选择器控制器拍摄的照片时，应确保：如果存在编辑后的图像，则使用它；如果不存在（即用户未编辑图像），则使用原始图像。如果你允许用户编辑，就不应该使用原始图像，因为用户不会期望你的应用接收到了它。

### 使用`UIImagePickerController`处理视频

如果你希望在应用中支持视频功能，`UIImagePickerController`类为视频提供了与照片基本相同的大部分功能。



为了正确使用视频的图像选取器控制器，您首先需要通过其`mediaTypes`属性告诉它您想要哪种媒体类型。`mediaTypes`是一个`NSArray`，默认情况下其中只包含`kUTTypeImage`（对应图像）。替代选项是用于电影的`kUTTypeMovie`。图像选取器控制器的可用媒体类型取决于其源类型（例如，相机或相册）。因此，要将图像选取器控制器设置为只提供视频，我们将使用以下代码：

```
[imagePickerController setMediaTypes:[NSArray arrayWithObject:(id)kUTTypeMovie]];
```

**注意：** 要使用`kUTTypeMovie`常量，您需要导入声明该常量的头文件，方法是在文件顶部添加以下内容：

```
#import <MobileCoreServices/UTCoreTypes.h>
```

您还需要将项目链接到`MobileCoreServices`框架。

由于`mediaTypes`属性是一个数组，您可以创建一个能够选择或拍摄照片和视频的图像选取器控制器。为此，只需将`mediaTypes`属性设置为可用媒体类型的列表：

```
UIImagePickerControllerSourceType sourceType = [imagePickerController sourceType];
NSArray *mediaTypes = [UIImagePickerController availableMediaTypesForSourceType:sourceType];
[imagePickerController setMediaTypes:mediaTypes];
```

您可以通过这种方式使用图像选取器控制器从相机以及用户的相册中获取视频。

当用户完成拍摄视频或从相册中选择视频时，会在传递给委托方法`imagePickerController:didFinishPickingMediaWithInfo:`的`info`字典中为不同的键提供对象。尽管图像选取器控制器会将原始图像数据传递给此方法，但电影数据太大，无法完整地保留在内存中。为此，图像选取器控制器将改为将带有`UIImagePickerControllerMediaURL`键的文件 URL 传递给该方法。您可以像这样访问此 URL：

```
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info
{
    NSURL *movieURL = [info objectForKey:UIImagePickerControllerMediaURL];
}
```

如果您期望接收照片或视频，那么您需要查看存储在键`UIImagePickerControllerMediaType`下的对象，以确定用户选择了哪种类型的媒体。该委托方法的一个完整实现可能如下所示：

```
- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info
{
    NSString *mediaType = [info objectForKey:UIImagePickerControllerMediaType];
    if ([mediaType isEqualToString:(NSString *)kUTTypeMovie]) {
        NSURL *movieURL = [info objectForKey:UIImagePickerControllerMediaURL];
        // 在此处处理视频 URL
    }
    else if ([mediaType isEqualToString:(NSString *)kUTTypeImage]) {
        UIImage *image = [info objectForKey:UIImagePickerControllerEditedImage];
        if (image == nil) {
            image = [info objectForKey:UIImagePickerControllerOriginalImage];
        }
        // 在此处处理图像
    }
    [self dismissModalViewControllerAnimated:YES];
}
```

与照片一样，可以将图像选取器控制器的`allowsEditing`属性设置为`YES`，以允许用户在录制后编辑视频。对于视频来说，这允许用户裁剪视频的开头或结尾部分。`UIImagePickerController`还有一些视频特有的属性，您可以在录制视频时设置这些属性来控制其行为。`videoMaximumDuration`属性可以限制视频的长度，这在例如您计划将视频上传到仅接受特定长度以下视频的服务时会很有用。默认情况下，图像选取器控制器会将视频限制为十分钟。

如果您想控制图像选取器控制器返回的视频质量，请将`videoQuality`方法设置为您希望接收的质量。



