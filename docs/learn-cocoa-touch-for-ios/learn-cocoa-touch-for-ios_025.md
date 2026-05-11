# 第 5 章：处理用户触摸

然后将它们连接到操作。现在，我们用代码来创建。打开 `PossessionDetailViewController.m`，并添加 `viewDidLoad` 方法：

```objectivec
- (void)viewDidLoad
{
[super viewDidLoad];
UITapGestureRecognizer *tapGestureRecognizer =
[[UITapGestureRecognizer alloc] initWithTarget:self
action:@selector(imageViewTapped:)];
[[self imageView] addGestureRecognizer:tapGestureRecognizer];
[[self imageView] setUserInteractionEnabled:YES];
}
```

我们必须将 `imageView` 的 `userInteractionEnabled:` 属性设置为 `YES`，因为图片视图默认情况下该属性是关闭的，它们通常不会直接接收轻点操作。接下来，我们需要添加 `imageViewTapped:` 方法。向类扩展中添加该方法的声明：

```objectivec
@interface PossessionDetailViewController()
- (void)doneButtonPressed:(id)sender;
- (void)imageViewTapped:(UITapGestureRecognizer *)tapGestureRecognizer;
@end
```

在编写该方法时，我们将使用一个名为 `UIImagePickerController` 的 UIKit 类来为对象拍照。



`UIImagePickerController`类继承自`UIViewController`，因此我们可以创建一个实例并以模态视图控制器的方式呈现它。编写方法如下：

```objc
- (void)imageViewTapped:(UITapGestureRecognizer *)tapGestureRecognizer
{
    UIImagePickerController *imagePickerController =
        [[UIImagePickerController alloc] init];

    if ([UIImagePickerController
            isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
        [imagePickerController
            setSourceType:UIImagePickerControllerSourceTypeCamera];
    }
    else {
        [imagePickerController
            setSourceType:UIImagePickerControllerSourceTypeSavedPhotosAlbum];
    }

    [imagePickerController setDelegate:self];
    [self presentModalViewController:imagePickerController animated:YES];
}
```

你可能会注意到这段代码中调用`setDelegate:`（对`imagePickerController`）时出现编译器警告。我们稍后会修复它，目前可以先忽略。

图像选择器控制器可用于通过设备相机拍照（如果设备有相机的话），或从用户的相册中获取图片。在此例中，如果相机可用则优先使用相机，否则回退到用户的已保存相册。创建好实例并设置其来源类型后，我们将详情视图控制器（此方法中的`self`）设置为它的委托，以便接收用户选择的图片。最后以模态方式呈现它。之后图像选择器控制器接管控制权，直到用户选择（或拍摄）图片后才将控制权返回给我们的代码。

**注意：** 在 iPhone 模拟器上进行测试时，无法使用相机，但可以使用“已保存的相册”。向 iPhone 模拟器的“已保存的相册”添加图片的最简单方法是通过 Safari；长按图片并保存即可。

在测试这段代码之前，我们需要为图像选择器控制器添加一个委托。有趣的是，`UIImagePickerController`的`delegate`属性被定义遵守两个协议：`UINavigationControllerDelegate`和`UIImagePickerControllerDelegate`。在苹果的源代码中，他们用以下行声明（来自 iOS 系统框架中的`UIImagePickerController.h`）：

```objc
@property(nonatomic,assign) id <UINavigationControllerDelegate,
    UIImagePickerControllerDelegate> delegate;
```

因此，为了避免之前代码产生的编译器警告，我们需要让`PossessionDetailViewController`同时遵守`UINavigationControllerDelegate`和`UIImagePickerControllerDelegate`协议，即使我们并不会实现`UINavigationControllerDelegate`中定义的任何方法。打开头文件（`PossessionDetailViewController.h`），并相应地调整类声明：

```objc
@interface PossessionDetailViewController : UIViewController
    <UIImagePickerControllerDelegate, UINavigationControllerDelegate>
```

我们将要实现的方法来自`UIImagePickerControllerDelegate`。将这两个方法添加到`PossessionDetailViewController`的实现中：

```objc
#pragma mark – UIImagePickerControllerDelegate Protocol Methods

- (void)imagePickerController:(UIImagePickerController *)picker
    didFinishPickingMediaWithInfo:(NSDictionary *)info
{
    // 如果我们的 possession 为 nil，则无法保存图像。如果它尚不存在，则创建一个并填入用户已输入的值。
    if ([self possession] == nil) {
        [self setPossession:[[Possession alloc] init]];
        [[self possession] setName:[[self nameField] text]];
        [[self possession] setValue:[NSNumber numberWithInt:[[[self valueField] text] intValue]]];
    }

    UIImage *image = [info
        objectForKey:UIImagePickerControllerOriginalImage];
    [[self possession] setImage:image];

    [self dismissModalViewControllerAnimated:YES];
}

- (void)imagePickerControllerDidCancel:(UIImagePickerController *)picker
{
    [self dismissModalViewControllerAnimated:YES];
}

#pragma mark -
```

为了帮助组织代码，我们使用了`#pragma mark`编译器指令。



