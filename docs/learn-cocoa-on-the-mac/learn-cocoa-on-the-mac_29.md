# LOL 入门第一步

首先，在 Xcode 中创建一个启用了**自动引用计数**（Automatic Reference Counting）的基础 Cocoa 项目，但本次无需支持文档或 Core Data。尽管这两项功能在项目中都能派上用场，但当前我们只专注于绘图部分。这个项目实际上不会保存任何内容。将项目命名为 `LOLmaker`，并使用 `LOL` 作为类前缀。

现在，编辑 `LOLAppDelegate.h` 文件，添加以下粗体显示的行：

```
#import <Cocoa/Cocoa.h>
#import "LOLView.h"
@interface LOLAppDelegate : NSObject <NSApplicationDelegate>
@property (assign) IBOutlet NSWindow *window;
@property (weak) IBOutlet LOLView *lolView;
@property (strong) NSImage *image;
@property (copy) NSString *text;
@end
```

这些属性将配合 Cocoa Bindings 使用，让用户拖入图像并输入文本，这些内容会自动绑定到我们的控制器对象，进而更新视图。请注意（Xcode 也会注意到）我们导入了 `LOLView.h` 头文件，并声明了一个 `LOLView*` 类型的属性，尽管 `LOLView` 类尚未完成。要立即解决这个问题，请创建另一组类文件，这次针对 `NSView` 的子类 `LOLView`。

我们将很快实现 `LOLView`，但现在先配置图形用户界面（GUI）。点击 `MainMenu.xib` 在 Interface Builder 画布中打开它，然后使用库（Library）将一个自定义视图、一个图像框（image well）和一个文本字段拖入空白窗口。使用身份检查器（Identity Inspector）将自定义视图的类设置为 `LOLView`，并使用属性检查器（Attributes Inspector）为图像框勾选“可编辑”（Editable）复选框。最后，将应用委托的 `lolView` 出口连接到窗口中的 `LOLView`。布局完成后，窗口应如图 14-10 所示。

![A978-1-4302-4543-8_14_Fig10_HTML.jpg](img/A978-1-4302-4543-8_14_Fig10_HTML.jpg)

图 14-10. LOLmaker 窗口布局

此窗口允许用户拖入图像（到拖放区域），并输入将显示在图像上的文本。`LOLView` 会检测到拖入的图像和编辑后的文本，并通过 Cocoa Bindings 触发自身的重新显示。绑定配置的前半部分将在 Interface Builder 中完成，后半部分则在代码中实现。

首先，选择图像框并切换到绑定检查器（Bindings Inspector）。为 Value 属性创建一个绑定，在弹出列表中选择应用委托，并在“模型键路径”（Model Key Path）字段中输入 `self.image`。然后选择文本字段，为其 Value 属性创建绑定，同样在弹出列表中选择应用委托，这次在“模型键路径”字段中输入 `self.text`。

这样就完成了控件的绑定设置。但由于 Interface Builder 不了解 `LOLView` 及其可绑定值，我们需要在代码中为其设置绑定。返回 Xcode，编辑 `LOLAppDelegate.m`，在 `@implementation` 部分添加以下方法：

```
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
  [self.lolView bind:@"image" toObject:self withKeyPath:@"image"
    options:nil];
  [self.lolView bind:@"text" toObject:self withKeyPath:@"text"
    options:nil];
}
```

至此，应用程序中所有对象之间的基本通信已配置完成。用户在窗口控件中的编辑操作会传递到应用委托，再从那里通过 Cocoa Bindings 传递到 `LOLView`。我们的应用“管道”现已完成！

