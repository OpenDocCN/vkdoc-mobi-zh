# 本地化与国际化

## 在代码中使用 `NSLocalizedString`

为了支持多语言，请添加 `Localized.strings` 文件的本地化版本。当用户选择特定语言时，系统将使用对应的翻译文本替代默认文本。在代码中，请按如下方式使用 `NSLocalizedString` 宏：

```objc
UIButton *submitButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
[submitButton setTitle:NSLocalizedString(@"SubmitButtonTitle", NULL) forState:UIControlStateNormal];
```

该宏的第一个参数是键（作为 `NSString` 对象），第二个参数是可选的注释（此处省略，使用 `NULL` 代替）。返回值是来自字符串文件的本地化字符串。

#### 使用字符串文件的优势

字符串文件的优势在于，您很可能无需自行提供所有翻译。通过将所有可本地化的内容放入一个文件，您可以将其交给翻译人员，确保他们理解任务。此外，Apple 提供了 `genstrings` 工具，可基于您的注释自动生成这些字符串文件。最佳工作流程是：始终在代码中使用 `NSLocalizedString`，然后在本地化之前运行 `genstrings` 生成字符串文件。

#### 多语言的字体支持

使用多语言时，请考虑字体支持。如果您在应用中提供了自定义字体，请确保它们支持应用所使用的所有语言。

#### 示例：HelloLocalization

让我们逐步完成一个简单的“Hello, World!”应用的本地化过程。

1. 打开 Xcode，选择 **File → New → Project…**（或按下 **Command + Shift + N**）。
2. 在 **iOS** 下，选择最左侧栏的 **Application**，然后选择右侧的 **Single View Application**。
3. 点击 **Next**，输入 `HelloLocalization` 作为项目名称。
4. 输入您的公司标识符和类前缀（例如 `com.learncocoatouch` 和 `LCT`）。
5. 将 **Device Family** 选择为 **iPhone**，确保勾选 **Use Automatic Reference Counting**，并取消勾选 **Use Storyboards** 和 **Include Unit Tests**。
6. 点击 **Next**，然后 **Create** 保存项目。

[www.it-ebooks.info](http://www.it-ebooks.info/)

##### 构建用户界面

用户界面很简单：只有一个标签。在 Xcode 中打开主视图控制器的界面文件（`LCTViewController.xib`）。通过选择 **View → Utilities → Show Object Library**（或按下 **Control + Option + Command + 3**）打开对象库。将一个标签拖入视图中并居中。将标签展开以填满视图的宽度。

通过打开视图控制器的头文件（`LCTViewController.h`）并添加以下粗体行，为这个标签添加一个输出口（outlet）：

```objc
#import <UIKit/UIKit.h>

@interface LCTViewController : UIViewController

@property (weak, nonatomic) IBOutlet UILabel *helloLabel;

@end
```

回到 `LCTViewController.xib` 并连接该输出口：按住 **Control**，从 **File's Owner** 拖到标签，然后从列表中选择 `helloLabel`。

打开视图控制器的实现文件（`LCTViewController.m`）并为该标签添加一个 `@synthesize` 指令：

```objc
@implementation LCTViewController

@synthesize helloLabel;
```

在 `viewDidLoad` 中设置标签的文本：

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    [[self helloLabel] setText:NSLocalizedString(@"helloWorld", NULL)];
}
```

如果现在运行应用，标签将显示 `"helloWorld"`，因为尚未为此键提供文本。

##### 使用 `genstrings` 生成字符串文件

打开 **终端** 并导航到项目的代码目录（例如 `cd /Users/jeff/Projects/HelloLocalization/HelloLocalization`）。运行：

```bash
genstrings –o en.lproj *.m
```

这将创建一个名为 `en.lproj` 的文件夹（如果不存在），并在其中生成一个 `Localizable.strings` 文件。该文件的初始内容如下：

```
/* No comment provided by engineer. */
"helloWorld" = "helloWorld";
```

将 `Localizable.strings` 拖入 Xcode 项目中的 **Supporting Files** 组。点击 **Finish**；字符串文件出现，并已设置英语本地化。在 Xcode 中打开它，删除被划掉的行，并添加粗体行：

```
/* No comment provided by engineer. */
"helloWorld" = "helloWorld";
"helloWorld" = "Hello, World!";
```

构建并运行应用。现在，对于所有语言，标签都会显示 `"Hello, World!"`。

##### 添加另一种语言（法语）

要添加法语：

1. 在 Xcode 的项目导航器中选择 `Localizable.strings`。
2. 打开文件检查器（**View → Utilities → Show File Inspector** 或按下 **Option + Command + 1**）。
3. 在 **Localization** 下，点击 **plus** 按钮添加新的本地化。如果文件还没有任何翻译，点击加号可能会选择另一个文件；重新选择 `Localizable.strings` 并再次点击 **plus**。
4. 在项目导航器中，`Localizable.strings` 旁边会出现一个箭头；点击展开以显示翻译。
5. 选择 `Localizable.strings (French)`，删除被划掉的行，并添加粗体行：

```
/* No comment provided by engineer. */
"helloWorld" = "Hello, World!";
"helloWorld" = "Bonjour, Monde!";
```

构建并运行应用。如果您的设备语言设置为法语（或法语在 iOS 首选语言列表中排在英语之前），您将看到 `"Bonjour, Monde!"`；否则将显示英语版本。

要测试，请在 **设置** 应用中更改语言。请注意，更改后，设置本身也会被翻译，导致难以恢复。如果您切换到法语并卡住："Settings" 在法语中是 `"Réglages"`，路径是 **Général → International → Langue**。更改语言会终止正在运行的应用，因此您需要重新启动应用才能继续测试。

## 总结

`genstrings` 命令简化了字符串文件的生成。任何显示给用户的文本都应来自 `NSLocalizedString()`，从而轻松实现本地化。接下来，我们将介绍如何本地化应用中的非文本部分。

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 本地化资源

本地化资源只需点击 **plus** 按钮，但注意不要过度。App Store 将蜂窝网络下载限制为 50MB；如果您为每种语言包含全尺寸图像，应用的大小会迅速增加——尤其是针对第三代 iPad Retina 显示屏的 2048×1536 图形。如果大图中只有一小部分需要更改（例如样式化标题），请考虑将其拆分为两个图像，并仅本地化标题。这将显著节省文件大小。对于捆绑的视频，请移除所有可本地化的文本，或将本地化视频托管在线上；用户不会愿意下载数 GB 的视频来获取他们不使用的语言。



### 本地化 Nib 文件

与其他资源一样，nib 文件也可以进行本地化。这对于德语等单词通常较长的语言尤为实用。当较长的单词需要你重新调整用户界面时，你可以只修改特定语言的 nib 文件，而无需改动其他语言的版本，从而实现针对单一语言的界面调整。要本地化一个 nib 文件，只需按照之前的方法，使用文件检查器创建另一种语言版本，然后将其中的英文文本替换为相应语言的文本。根据需要移动界面元素，保存即可完成。

然而，本地化 nib 文件也可能暗藏风险。你应尽量在开发周期的末期进行此操作，甚至可以考虑改用本地化字符串。设想以下场景：你开发了一款应用，并决定将其发布为五种语言版本。该应用表现良好，于是你开始更新以修复 Bug。在修复 Bug 的过程中，你需要修改某个 nib 文件中视图的属性。但由于你已经本地化了该 nib 文件，此时面临两种选择：要么在每一个 nib 文件中都进行修改，要么删除其他 nib 文件，然后基于最新更新的 nib 文件重新创建它们。无论选择哪种方式，都可能带来大量工作，因此或许应彻底避免这种情况，转而使用本地化字符串。

[www.it-ebooks.info](http://www.it-ebooks.info/)

