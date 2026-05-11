# 第 3 章：使用视图控制器管理屏幕内容

如果你不小心将文本字段连接到了错误的输出口，可以在`连接检查器`中修复。要打开它，请选择你错误连接的文本字段，然后按下`⌘+Option+6`或选择`视图` ➤ `工具` ➤ `显示连接检查器`。如图 3-7 所示，`连接检查器`列出了你在`引用输出口`下为文本字段建立的连接。图 3-7 中的输出口是正确的。如果你将其连接到了错误的输出口，请按下引用对象名称左侧的小`x`（在图 3-7 中为`文件所有者`）以断开连接。

**图 3-7.** *连接检查器*

连接好文本字段的输出口后，让我们为视图控制器再添加一个属性：指向我们正在显示详情对象的指针。打开`PossessionDetailViewController.h`，并添加加粗部分的内容：

```
@class Possession;

@interface PossessionDetailViewController : UIViewController

@property (weak) IBOutlet UITextField *nameField;

@property (weak) IBOutlet UITextField *valueField;

@property (strong) Possession *possession;

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 3 章：使用视图控制器管理屏幕内容 68

我们使用`Possession`类的前向声明（`@class Possession;`这一行）来确保其他导入`PossessionDetailViewController.h`头文件的类不会同时导入`Possession.h`头文件。这在大型项目中很重要，因为你可能遇到难以解决的头文件循环引用问题。同时，尽可能少地导入头文件也能加快编译速度。创建前向声明时，你不会获得 Xcode 对该类名称的代码补全功能，但这没关系。要在视图控制器的实现文件中使用`Possession`类，你仍然需要导入`Possession`头文件，即使已经在视图控制器的头文件中进行了前向声明。

别忘了在视图控制器的实现文件中为`possession`添加`@synthesize`行。打开`PossessionDetailViewController.m`，并添加加粗部分的行：

```
@implementation PossessionDetailViewController

@synthesize nameField = _nameField;

@synthesize valueFeild = _valueField;

@synthesize possession = _possession;
```

现在我们已经理清了头文件，接下来在实现文件中编写一些方法。我们需要一个“完成”按钮来返回列表，并且需要用正确的值填充文本字段。以下代码块是完整的实现文件（除了顶部的注释）；我们正在添加的行以粗体显示。

```
#import "PossessionDetailViewController.h"

#import "Possession.h"

@interface PossessionDetailViewController()

- (void)doneButtonPressed:(id)sender;

@end

@implementation PossessionDetailViewController

@synthesize nameField = _nameField;

@synthesize valueField = _valueField;

@synthesize possession = _possessionField;

- (id)initWithNibName:(NSString *)nibNameOrNil

bundle:(NSBundle *)nibBundleOrNil

{

self = [super initWithNibName:nibNameOrNil

bundle:nibBundleOrNil];

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 3 章：使用视图控制器管理屏幕内容 69

if (self) {

[self setTitle:@"项目详情"];

UIBarButtonItem *doneButtonItem =

[[UIBarButtonItem alloc]

initWithBarButtonSystemItem:UIBarButtonSystemItemDone

target:self

action:@selector(doneButtonPressed:)];

[[self navigationItem] setRightBarButtonItem:doneButtonItem];

}

return self;

}

- (void)viewWillAppear:(BOOL)animated

{

[super viewWillAppear:animated];

[[self nameField] setText:[[self possession] name]];

[[self valueField] setText:[[[self possession] value] stringValue]];

}

- (void)doneButtonPressed:(id)sender

{

if ([[possession name] isEqualToString:[[self nameField] text]] == NO)

{

[possession setName:[[self nameField] text]];

}

NSNumber *newValue = [NSNumber numberWithInt:[[[self valueField] text]

intValue]];

if ([[possession value] isEqualToNumber:newValue] == NO) {

[possession setValue:newValue];

}
```



