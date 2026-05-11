# 第 11 章：在应用中播放媒体：音频与视频 343

```
[[UIImagePickerController
availableMediaTypesForSourceType:UIImagePickerControllerSourceTypeCamera]
containsObject:(NSString *)kUTTypeMovie]) {
    canUseCamera = YES;
}

BOOL canUsePhotoLibrary = NO;
if ([UIImagePickerController
isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary] &&
[[UIImagePickerController
availableMediaTypesForSourceType:UIImagePickerControllerSourceTypePhotoLibrary]
containsObject:(NSString *)kUTTypeMovie]) {
    canUsePhotoLibrary = YES;
}

// 如果两种来源类型都可用，则显示操作列表供用户选择。
if (canUseCamera == YES && canUsePhotoLibrary == YES) {
    UIActionSheet *actionSheet =
    [[UIActionSheet alloc] initWithTitle:nil
                               delegate:self
                      cancelButtonTitle:@"取消"
                 destructiveButtonTitle:nil
                      otherButtonTitles:@"拍摄视频", @"使用照片库", nil];
    [actionSheet showInView:[self view]];
}
else if (canUseCamera == YES && canUsePhotoLibrary == NO) {
    [self showImagePickerForSourceType:UIImagePickerControllerSourceTypeCamera];
}
else if (canUseCamera == NO && canUsePhotoLibrary == YES) {
    [self showImagePickerForSourceType:UIImagePickerControllerSourceTypePhotoLibrary];
}
else {
    // 摄像头和照片库均不可用。
    UIAlertView *alertView =
    [[UIAlertView alloc] initWithTitle:@"加载视频出错"
                              message:@"没有可用的来源类型。"
                             delegate:nil
                    cancelButtonTitle:@"确定"
                    otherButtonTitles:nil];
    [alertView show];
}
```

在此方法中，我们分别使用 `canUseCamera` 和 `canUsePhotoLibrary` 来存储摄像头和照片库是否可用。为了确定这一点，我们使用 `UIImagePickerController` 类方法 `isSourceTypeAvailable:` 判断来源是否可用，然后使用 `availableMediaTypesForSourceType:` 判断该来源类型能否返回视频。这一点很重要，因为某些旧款 iOS 设备虽然有摄像头，但无法拍摄视频（只能拍照）。如果发现两种来源类型都可用且能返回视频，我们就创建一个包含两个选项的 `UIActionSheet` 并呈现给用户。如果只有一种来源类型可用且能返回视频，我们就用该来源类型调用视图控制器的 `showImagePickerForSourceType:` 方法。如果两种来源类型均不可用，则通过 `UIAlertView` 通知用户。如果显示了操作列表，当用户选择某个选项时，会调用操作列表的代理方法 `actionSheet:clickedButtonAtIndex:`。

在实现此方法之前，我们先添加一条 `#pragma mark` 语句来帮助组织代码。将以下加粗的代码添加在刚才实现的方法之后、`@end` 指令之前：

```
#pragma mark - UIActionSheetDelegate 协议方法

- (void)actionSheet:(UIActionSheet *)actionSheet
clickedButtonAtIndex:(NSInteger)buttonIndex
{
    if (buttonIndex == 0) {
        // 用户选择了"拍摄视频"。
        [self showImagePickerForSourceType:UIImagePickerControllerSourceTypeCamera];
    }
    else if (buttonIndex == 1) {
        // 用户选择了"使用照片库"。
        [self showImagePickerForSourceType:UIImagePickerControllerSourceTypePhotoLibrary];
    }
}
```

如你所见，该方法仅检查所选按钮的索引，并调用用户所选来源类型的 `showImagePickerForSourceType:` 方法。添加此方法后，我们来实现 `showImagePickerForSourceType:`。为保持代码组织的连贯性，将此方法添加在之前添加的 `#pragma mark` 行之前，插入以下加粗的代码行：

```
- (void)showImagePickerForSourceType:(UIImagePickerControllerSourceType)sourceType
{
    UIImagePickerController *imagePicker =
    [[UIImagePickerController alloc] init];
    [imagePicker setDelegate:self];
    [imagePicker setMediaTypes:[NSArray arrayWithObject:(NSString *)kUTTypeMovie]];
```



`[imagePicker setSourceType:sourceType];`

`[self presentModalViewController:imagePicker animated:YES];`

在这个方法中，我们创建了一个 `UIImagePickerController`，将我们的视图控制器设置为其代理，将其 `mediaTypes` 属性设置为仅包含 `kUTTypeMovie` 类型的数组，以防止用户选择图片，最后将源类型设置为给定的源类型。然后我们以模态方式呈现图片选择器控制器。用户随后会按下“取消”、拍摄视频或选择视频，此时图片选择器控制器会调用其代理方法。

现在让我们实现这些方法。我们将为这些方法添加一个新的 `#pragma mark` 部分，因此在 `actionSheet:clickedButtonAtIndex:` 方法之后、`@end` 指令之前添加以下加粗的行：

```
#pragma mark - UIImagePickerControllerDelegate Protocol Methods

- (void)imagePickerControllerDidCancel:(UIImagePickerController *)picker
{
    [self dismissModalViewControllerAnimated:YES];
}

- (void)imagePickerController:(UIImagePickerController *)picker
didFinishPickingMediaWithInfo:(NSDictionary *)info
{
    [self dismissModalViewControllerAnimated:YES];
    NSURL *movieURL = [info objectForKey:UIImagePickerControllerMediaURL];
    if (movieURL != nil) {
        _moviePlayerController =
        [[MPMoviePlayerController alloc] initWithContentURL:movieURL];
        [_moviePlayerController setControlStyle:MPMovieControlStyleNone];
        [[_moviePlayerController view]
         setAutoresizingMask:(UIViewAutoresizingFlexibleWidth |
                             UIViewAutoresizingFlexibleHeight)];
        [[_moviePlayerController view] setClipsToBounds:YES];
        [[_moviePlayerController view] setFrame:[[self movieHostingView]
                                                  bounds]];
        [[self movieHostingView] addSubview:[_moviePlayerController view]];
    }
}
```

无论用户是取消、拍摄视频还是选择视频，我们首先要做的就是在视图控制器上调用 `dismissModalViewControllerAnimated:` 以移除图片选择器控制器。如果用户没有取消，我们就从 `imagePickerController:didFinishPickingMediaWithInfo:` 方法中的 `info` 字典里获取视频 URL。在这里，我们创建了一个指向此 URL 的 `MPMoviePlayerController`。我们将其控制样式设置为 `MPMovieControlStyleNone` 以移除内置控件，然后将其视图的自动调整遮罩设置为随其包含视图一起调整大小。我们将电影播放器控制器视图的 `clipsToBounds` 属性设置为 `YES`，以防止其绘制到框架之外，然后将其框架设置为电影托管视图的边界，这将使其完全填充电影托管视图。最后，我们将电影播放器控制器的视图作为子视图添加到电影托管视图中。

现在我们已经添加了这段代码，如果此应用的用户再次按下相机按钮选择新视频，我们的代码会向电影托管视图添加新的电影播放器控制器视图。为了防止这种情况，请修改 `selectVideoButtonPressed:` 方法，在方法开头添加加粗的行：

```
- (void)selectVideoButtonPressed:(id)sender
{
    // 如果已经存在视频播放器控制器，则进行清理。
    if (_moviePlayerController != nil) {
        [[_moviePlayerController view] removeFromSuperview];
        _moviePlayerController = nil;
    }

    // 确定我们可以从图片选择器控制器获取视频的方式。
    BOOL canUseCamera = NO;
    ...
```

这将防止我们在视图层次结构中同时拥有多个电影播放器控制器的视图。接下来，让我们为“播放”和“全屏”按钮添加方法。在 `showImagePickerForSourceType:` 之后、`#pragma mark` 部分之前，通过添加以下加粗代码来添加这两个方法：

```
- (void)playPauseButtonPressed:(id)sender
{
    if (_moviePlayerController == nil) {
        return;
    }
    if ([_moviePlayerController playbackState] ==
        MPMoviePlaybackStatePlaying) {
        // 视频正在播放。
        [_moviePlayerController pause];
    }
    else {
        // 视频未播放。
        [_moviePlayerController play];
    }
}

- (void)fullscreenButtonPressed:(id)sender
{
    if (_moviePlayerController == nil) {
        return;
    }
    [_moviePlayerController setControlStyle:MPMovieControlStyleDefault];
    [_moviePlayerController setFullscreen:YES animated:YES];
}
```

在每个方法中，如果不存在电影播放器控制器，我们将立即通过调用 `return` 退出。在 `playPauseButtonPressed:` 中，如果电影播放器控制器当前正在播放，我们对其调用 `pause`；否则，我们调用 `play`。在 `fullscreenButtonPressed:` 方法中，在调用电影播放器控制器的 `setFullscreen:animated:` 之前，我们将其控制样式设置回 `MPMovieControlStyleDefault`。如果不这样做，全屏电影将没有任何播放控件，这会使我们难以返回！现在我们已经添加了这些方法，请在支持视频录制或媒体库中有视频的设备上构建并运行此应用。按下相机按钮，选择或拍摄一个视频，然后您就可以使用“播放”按钮在视图控制器的视图中开始播放视频！如果您从库中选择了一个视频，您可能会看到一个标有“正在压缩视频…”的进度条，表示视频正在为在应用内播放进行压缩。

我们还要做一些事情来改善此应用的体验。电影播放器控制器在播放时会向默认的 `NSNotificationCenter` 发布通知，我们可以利用这一点来修改用户界面。修改 `initWithNibName:bundle:` 方法以监听这些通知，添加以下加粗的行：

```
- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        [self setTitle:@"CustomPlayer"];
        SEL selectVideoSelector = @selector(selectVideoButtonPressed:);
        UIBarButtonItem *selectVideoButton =
        [[UIBarButtonItem alloc]
         initWithBarButtonSystemItem:UIBarButtonSystemItemCamera
         target:self
         action:selectVideoSelector];
        [[self navigationItem] setRightBarButtonItem:selectVideoButton];

        [[NSNotificationCenter defaultCenter]
         addObserverForName:MPMoviePlayerPlaybackStateDidChangeNotification
         object:nil
         queue:[NSOperationQueue mainQueue]
         usingBlock:^(NSNotification *note) {
            MPMoviePlayerController *moviePlayerController = [note object];
            if ([moviePlayerController playbackState] ==
                MPMoviePlaybackStatePlaying) {
                [[self playPauseButton] setTitle:@"暂停"
                                        forState:UIControlStateNormal];
            }
            else {
                [[self playPauseButton] setTitle:@"播放"
                                        forState:UIControlStateNormal];
            }
        }];

        [[NSNotificationCenter defaultCenter]
         addObserverForName:MPMoviePlayerDidExitFullscreenNotification
         object:nil
         queue:[NSOperationQueue mainQueue]
         usingBlock:^(NSNotification *note) {
            MPMoviePlayerController *moviePlayerController = [note object];
            [moviePlayerController setControlStyle:MPMovieControlStyleNone];
        }];
    }
    return self;
}
```

在这段代码中，我们响应两个通知：`MPMoviePlayerPlaybackStateDidChangeNotification` 和 `MPMoviePlayerDidExitFullscreenNotification`。对于前者，我们修改“播放/暂停”按钮的标题；如果视频正在播放，我们将其设置为“暂停”，否则设置为“播放”。对于后者，一旦电影播放器不再全屏播放，我们将电影播放器控制样式设置回 None。由于我们已经注册接收这些通知，我们需要记住在不再需要这些通知时通知通知中心。为此，请在 `initWithNibName:bundle:` 之后添加一个 `dealloc` 方法，添加以下加粗的行：

```
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self name:nil
                                                  object:nil];
}
```



