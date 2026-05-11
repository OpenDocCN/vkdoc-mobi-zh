# 第 10 章：硬件 API

[www.it-ebooks.info](http://www.it-ebooks.info/)

默认情况下，该属性设置为 `UIImagePickerControllerQualityTypeMedium`，苹果公司声明此设置适合通过 Wi-Fi 发送视频。将质量设置为 `UIImagePickerControllerQualityTypeHigh` 将完全阻止图像选择器控制器降低质量；对于配备高质量摄像头的设备，这些文件可能会变得非常大，因此除通过 USB 线缆传输外，不建议使用此质量设置来传输视频文件。

`UIImagePickerControllerQualityTypeLow` 将生成一个足够小的视频文件，使其能够轻松通过蜂窝网络传输。

还有三个用于固定分辨率的质量常量：`UIImagePickerControllerQualityType640x480`、`UIImagePickerControllerQualityTypeIFrame960x540` 和 `UIImagePickerControllerQualityTypeIFrame1280x720`。

无论您将 `videoQuality` 属性设置为何值，视频都只能被缩小，而不能被放大。如果您的源类型是用户的照片库，并且他们选择了一个视频，则 `videoQuality` 属性将用于降低视频的分辨率（如果需要）。发生这种情况时，在 `imagePickerController:didFinishPickingMediaWithinfo:` 方法中传递给代理的信息字典将包含 `UIImagePickerControllerReferenceURL` 键下的原始视频 URL。

如果您将图像选择器控制器的 `cameraFlashMode` 属性设置为 `UIImagePickerControllerCameraFlashModeOn`，那么在图像选择器控制器显示并设置为拍摄视频时，闪光灯将保持开启。这可能会对电池寿命产生不利影响，因此请确保仅在必要时使用。

### 使用 UIVideoEditorController 处理视频

一旦您使用 `UIImagePickerController` 获取了视频的 URL，您可能希望为用户提供编辑视频的方式。这就是 `UIVideoEditorController` 类的用武之地。要使用它，只需创建一个 `UIVideoEditorController` 实例，将其 `videoPath` 属性设置为视频的路径，然后像呈现任何其他视图控制器一样呈现它。它与 `UIImagePickerController` 有两个共同的属性：`videoMaximumDuration` 和 `videoQuality`，它们的功能与图像选择器控制器的对应属性完全相同，尽管图像选择器控制器的默认视频质量为 `UIImagePickerControllerQualityTypeMedium`，而视频编辑器控制器的默认视频质量为 `UIImagePickerControllerQualityTypeLow`。

在创建视频编辑器控制器之前，请通过调用类方法 `canEditVideoAtPath:` 来验证它是否能够编辑该视频。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**注意：** `UIVideoEditorController` 的 `videoPath` 属性是一个 `NSString`，但 `UIImagePickerController` 类传递给代理方法 `imagePickerController:didFinishPickingMediaWithInfo:` 的是一个包含编辑信息的字典，其中包含键 `UIImagePickerControllerMediaURL`，其值是一个 `NSURL` 对象。要将 `NSURL` 转换为其所代表的 `NSString` 路径，请使用其 `path` 方法。

当用户完成视频编辑后，视频编辑器控制器会调用代理方法 `videoEditorController:didSaveEditedVideoToPath:`，并传入编辑后视频的路径（作为字符串）。代理还需要实现 `videoEditorControllerDidCancel:` 以处理用户取消操作的情况，以及 `videoEditorController:didFailWithError:` 以处理任何错误情况。

`UIImagePickerController` 和 `UIVideoEditorController` 类为您提供了在应用中使用照片和视频所需的大部分功能。对于本书范围之外的高级图像处理，请查看 Core Image 框架；对于更高级的视频处理，请查看 AVFoundation 框架。我们稍后还会在本书中介绍如何在您的应用中播放音频和视频。接下来在本章中，我们将讨论如何在您的 iOS 应用中使用另一种硬件：加速计。

## 使用加速计

响应用户操作的一种方式是使用设备的加速计。该设备提供关于设备方向的实时数据，让您确切知道用户是如何手持它的。来自加速计的数据非常详尽——您可快至每十毫秒接收一次数据——对于简单的任务来说，这绝对是杀鸡用牛刀。幸运的是，有一些更高级的方法来响应用户的运动，不需要您处理原始的加速计数据。以防您需要使用它，我们也会介绍这部分内容，但首先，我们将了解一些更易于使用的 API。

### 加速计事件

正如我们在第 5 章中看到的，当用户点击屏幕时，您会收到一个 `UIEvent` 对象，其中包含一个或多个代表触摸的 `UITouch` 对象。类似地，当用户摇动设备时，它会生成一个代表该运动的 `UIEvent`。此事件沿着响应者链向上传递，直到某个响应者实现了 `motionBegan:withEvent:` 方法或 `motionEnded:withEvent:` 方法。在这些方法中，您可以适当响应该事件。例如，如果您有一个具有撤销方法的视图控制器，并且您希望允许用户摇动手机来调用撤销方法，那么您的视图控制器首先需要成为第一响应者才能接收该事件。要使对象成为第一响应者，请按如下方式实现 `canBecomeFirstResponder`：

```
- (BOOL)canBecomeFirstResponder
{
    return YES;
}
```

然后，您需要告知您的视图控制器在其视图出现时成为第一响应者，并在其视图消失时放弃第一响应者身份：

```
- (void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];
    [self becomeFirstResponder];
}

- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    [self resignFirstResponder];
}
```

一旦您的视图控制器成为第一响应者，您就可以按如下方式实现触摸方法：

```
- (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
}

- (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
    [self undo];
}

- (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
}
```

如您所见，这里代码并不多。您甚至不需要在 `motionBegan:withEvent:` 或 `motionCancelled:withEvent:` 方法中放置任何内容；它们只需要存在，以便应用程序可以向该对象发送运动事件。当您实现“摇动以撤销”时，您需要向用户提供一个选择，但处理摇动的代码仍然如此简单。

在我看来，“摇动以撤销”有点难以理解且不易被用户发现，同时很容易意外触发，但如果您想支持它，这并不困难。通常，您会使用 `UIAlertView` 来提供选择；一个典型的 `motionEnded:withEvent:` 方法如下所示：

```
- (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
    // 此处的 "self" 应遵循 UIAlertViewDelegate 协议。
    UIAlertView *alertView =
        [[UIAlertView alloc] initWithTitle:@"撤销"
                                  message:@"您想要撤销吗？"
                                 delegate:self
                        cancelButtonTitle:@"不撤销"
                        otherButtonTitles:@"撤销", nil];
    [alertView show];
}
```

当用户选择“撤销”或“不撤销”后，您的代码将相应地执行撤销操作或不执行任何操作。

### 设备方向通知



