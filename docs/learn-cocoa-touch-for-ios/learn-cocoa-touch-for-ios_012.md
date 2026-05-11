# 第 3 章：使用视图控制器管理屏幕内容

### 将数据从父视图控制器传递到子视图控制器

```objc
[[self navigationController] popViewControllerAnimated:YES];
}
@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

当按下"完成"按钮时，这段代码会更改资产的值并返回列表。我们几乎已经达到了可以使用它的阶段，但还需要添加一些代码来进入详情视图控制器。打开列表视图控制器（`PossessionListViewController.m`），并添加加粗显示的那一行（省略号表示这两部分之间还有我省略的代码）：

```objc
#import "PossessionListViewController.h"
#import "Possession.h"
#import "PossessionDetailViewController.h"
...
```

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSString *cellIdentifier = @"PossessionCell";
    UITableViewCell *cell = [tableView
                             dequeueReusableCellWithIdentifier:cellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleValue1
                                      reuseIdentifier:cellIdentifier];
    }
    Possession *possession = [self possessionAtIndex:[indexPath row]];
    [[cell textLabel] setText:[possession name]];
    [[cell detailTextLabel] setText:[[possession value] stringValue]];
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];
    return cell;
}
```

```objc
- (void)tableView:(UITableView *)tableView
    didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    PossessionDetailViewController *detailViewController =
        [[PossessionDetailViewController alloc] initWithNibName:nil
                                                         bundle:nil];
    [detailViewController setPossession:[self possessionAtIndex:[indexPath row]]];
    [[self navigationController] pushViewController:detailViewController
                                           animated:YES];
}
...
@end
```

现在运行你的应用程序。当你选择第一个项目时，你会看到它的详情视图控制器推入屏幕，而当你点击"完成"时，它会带你回到主列表。此时，"返回"按钮不会保存我们的更改，但"完成"按钮会。然而，如果你更改了值，你不会立即在列表视图控制器上看到它们。如果你将该项目滚动出屏幕再滚回来，这些值将会更新。这是因为我们从未告诉表格视图重新加载任何行。目前，我们可以在列表视图控制器中使用一个方法来解决这个问题：

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [[self tableView] reloadData];
}
```

对于这个目的来说，这种方法有点过于粗放，因为它每次都会强制表格视图重新加载所有内容，但至少能实现我们的目标。在后续章节中，我们将学习如何在应用程序中更好地传递数据。不过现在，我们还需要实现一个功能才能让第一个应用程序完整：即向列表中添加新项目的能力。

### 向模态视图控制器传递数据并从中接收数据

让我们在列表视图的导航栏上添加一个按钮来添加新项目，同时在类扩展中声明一个供其调用的方法，在`PossessionListViewController.m`中添加加粗显示的行：

```objc
@interface PossessionListViewController() {
    NSMutableArray *_possessions;
}
- (void)addItemButtonPressed:(id)sender;
- (Possession *)possessionAtIndex:(NSUInteger)index;
@end

@implementation PossessionListViewController

- (id)initWithNibName:(NSString *)nibNameOrNil
               bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil
                           bundle:nibBundleOrNil];
    if (self) {
        Possession *iPhone = [[Possession alloc] init];
        [iPhone setName:@"iPhone 4S"];
        [iPhone setValue:[NSNumber numberWithInt:649]];
        Possession *iPad = [[Possession alloc] init];
        [iPad setName:@"iPad 2"];
        [iPad setValue:[NSNumber numberWithInt:499]];
        _possessions = [NSMutableArray arrayWithObjects:iPhone, iPad, nil];
```

[www.it-ebooks.info](http://www.it-ebooks.info/)



`[self setTitle:@"My Stuff"];`

```
UIBarButtonItem *addItemButton =
[[UIBarButtonItem alloc]
initWithBarButtonSystemItem:UIBarButtonSystemItemAdd
target:self
action:@selector(addItemButtonPressed:)];
[[self navigationItem] setRightBarButtonItem:addItemButton];
}
return self;
}
```

这段代码为我们创建了一个带有加号的漂亮按钮。为了让其生效，我们让它创建一个新的`PossessionDetailViewController`并以模态方式呈现。我们将从一个基本实现开始；在`PossessionListViewController.m`文件中，`@end`指令之前添加以下代码：

```
- (void)addItemButtonPressed:(id)sender
{
PossessionDetailViewController *detailViewController =
[[PossessionDetailViewController alloc] initWithNibName:nil
bundle:nil];
[self presentModalViewController:detailViewController
animated:YES];
}
```

添加此方法后运行应用，你很快会发现这还不够。首先，无法退出模态视图控制器。为了给此视图控制器添加另一个导航栏，我们将创建另一个导航控制器并以模态方式呈现。修改刚才创建的方法，添加**粗体**行：

```
- (void)addItemButtonPressed:(id)sender
{
PossessionDetailViewController *detailViewController =
[[PossessionDetailViewController alloc] initWithNibName:nil
bundle:nil];
UINavigationController *navigationController =
[[UINavigationController alloc]
initWithRootViewController:detailViewController];
[self presentModalViewController:navigationController
animated:YES];
}
```

这样好一些了，但“完成”按钮仍然无法工作，因为在导航栈中没有可以返回的视图控制器。我们需要修改“完成”按钮的行为来确定其具体操作。为了正确封装行为，我们需要确保列表视图控制器决定详情视图控制器的行为，因为列表视图控制器为不同目的创建了详情视图控制器。让我们在详情视图控制器上创建一个可用于此目的的标志。在`PossessionDetailViewController.h`中，在其他`@property`行之后添加**粗体**行：

```
@property (weak) IBOutlet UITextField *nameField;
@property (weak) IBOutlet UITextField *valueField;
@property (strong) Possession *possession;
@property (getter = isModal) BOOL modal;
```

在详情视图控制器的实现文件（`PossessionDetailViewController.m`）中添加`@synthesize`行，添加**粗体**行：

```
@synthesize nameField = _nameField;
@synthesize valueField = _valueField;
@synthesize possession = _possession;
@synthesize modal = _modal;
```

接下来，修改此文件的`doneButtonPressed:`方法，将删除线行替换为**粗体**行：

```
- (void)doneButtonPressed:(id)sender
{
if ([[possession name] isEqualToString:[[self nameField] text]] == NO) {
[possession setName:[[self nameField] text]];
}
NSNumber *newValue = [NSNumber numberWithInt:[[[self valueField] text]
intValue]];
if ([[possession value] isEqualToNumber:newValue] == NO) {
[possession setValue:newValue];
}
[[self navigationController] popViewControllerAnimated:YES];
if ([self isModal]) {
[self dismissModalViewControllerAnimated:YES];
} else {
[[self navigationController] popViewControllerAnimated:YES];
}
}
```

这将使用`modal`属性的值来确定正确的关闭行为。回到我们的列表视图控制器，当从“添加”按钮创建详情视图控制器时，我们将此属性设置为`YES`。打开`PossessionListViewController.m`，修改`addItemButtonPressed:`方法，添加**粗体**行：

```
- (void)addItemButtonPressed:(id)sender
{
PossessionDetailViewController *detailViewController =
[[PossessionDetailViewController alloc] initWithNibName:nil
bundle:nil];
[detailViewController setModal:YES];
…
```

### 使用委托协议在视图控制器之间传递数据



现在，详情视图控制器在两种情况下都能正确显示和消失，但添加新条目仍然无法工作，因为当我们添加新条目时，详情视图控制器中的`possession`属性为`nil`。要实现这一点，我们需要在列表视图控制器中添加新条目时通知它。

我们不想让详情视图控制器依赖于列表视图控制器的任何实现细节，因为这有助于保持其可重用性。如果我们直接从详情视图控制器调用列表视图控制器的方法，那么当以后替换列表视图控制器时，就必须重写详情视图控制器。为了避免这种情况，我们将创建一个协议，让列表视图控制器来遵循该协议。在 Xcode 中，通过选择 `File` ➤ `New` ➤ `New File...` 创建一个新文件。这次，在 `Cocoa Touch` 类别中使用 "Objective-C protocol"，并将其命名为 `PossessionDetailViewControllerDelegate`。我们将定义一个必需的方法，无论何时详情视图控制器完成编辑条目时都会调用此方法。打开新的头文件 `PossessionDetailViewControllerDelegate.h`，并添加加粗的行：

```objc
#import <Foundation/Foundation.h>

@class Possession;

@class PossessionDetailViewController;

@protocol PossessionDetailViewControllerDelegate <NSObject>

@required

- (void)possessionDetailViewController:(PossessionDetailViewController *)detailViewController
                  didEditPossession:(Possession *)possession;

@end
```

现在，在列表视图控制器的头文件中，我们可以导入此协议的头文件，并声明列表视图控制器遵循该协议。打开 `PossessionListViewController.h`，并添加加粗的代码：

```objc
#import <UIKit/UIKit.h>

#import "PossessionDetailViewControllerDelegate.h"

@interface PossessionListViewController : UITableViewController
<PossessionDetailViewControllerDelegate>

@end
```

对于实现部分，如果条目尚不在数组中，我们就将其添加到 `_possessions` 数组中。打开 `PossessionListViewController.m`，并在 `@end` 指令之前添加以下加粗的方法：

```objc
- (void)possessionDetailViewController:(PossessionDetailViewController *)detailViewController
                  didEditPossession:(Possession *)possession
{
    if ([_possessions containsObject:possession] == NO) {
        [_possessions addObject:possession];
    }
}

@end
```

现在，我们需要告诉详情视图控制器在完成编辑时该调用哪个方法。此外，当详情视图控制器完成编辑，并且其 `possession` 属性为 `nil` 时，我们还需要创建一个新的 `possession`。首先，为详情视图控制器添加一个 `delegate` 属性。打开 `PossessionDetailViewController.h`，并添加加粗的头文件导入和属性声明行：

```objc
#import <UIKit/UIKit.h>

#import "PossessionDetailViewControllerDelegate.h"

@class Possession;

@interface PossessionDetailViewController : UIViewController

@property (weak) IBOutlet UITextField *nameField;
@property (weak) IBOutlet UITextField *valueField;
@property (strong) Possession *possession;
@property (getter = isModal) BOOL modal;
@property (weak) id <PossessionDetailViewControllerDelegate> delegate;

@end
```

接下来，打开详情视图控制器的实现文件 `PossessionDetailViewController.m`，并添加加粗的行以导入并合成 `delegate` 的访问器方法：

```objc
@implementation PossessionDetailViewController

@synthesize nameField = _nameField;
@synthesize valueField = _valueField;
@synthesize possession = _possession;
@synthesize modal = _modal;
@synthesize delegate = _delegate;
```

接着，在详情视图控制器的 `doneButtonPressed:` 方法中，添加加粗的代码以调用代理方法，并在必要时创建新的 `possession`：

```objc
- (void)doneButtonPressed:(id)sender
{
    if ([self possession] == nil) {
```



`[self setPossession:[[Possession alloc] init]];`

}

```
if ([[[self possession] name] isEqualToString:[[self nameField] text]] ==
NO) {
    [[self possession] setName:[[self nameField] text]];
}

NSNumber *newValue = [NSNumber numberWithInt:[[[self valueField] text]
intValue]];
if ([[[self possession] value] isEqualToNumber:newValue] == NO) {
    [[self possession] setValue:newValue];
}

[[self delegate] possessionDetailViewController:self
                                didEditPossession:[self possession]];

if ([self isModal]) {
    [self dismissModalViewControllerAnimated:YES];
} else {
    [[self navigationController] popViewControllerAnimated:YES];
}
```

现在，最后一步是将列表视图控制器设置为详情视图控制器的委托。打开 `PossessionListViewController.m`。在通过调用 `[[PossessionDetailViewController alloc] initWithNibName:nil bundle:nil]` 创建 `PossessionDetailViewController` 的两个位置，添加粗体所示的行：

```
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath
*)indexPath
{
    [tableView deselectRowAtIndexPath:indexPath animated:YES];
    PossessionDetailViewController *detailViewController =
        [[PossessionDetailViewController alloc] initWithNibName:nil
                                                        bundle:nil];
    [detailViewController setDelegate:self];
    [detailViewController setPossession:[self possessionAtIndex:[indexPath
        row]]];
    [[self navigationController] pushViewController:detailViewController
                                           animated:YES];
}
```

…

```
- (void)addItemButtonPressed:(id)sender
{
    PossessionDetailViewController *detailViewController =
        [[PossessionDetailViewController alloc] initWithNibName:nil
                                                        bundle:nil];
    [detailViewController setDelegate:self];
    [detailViewController setModal:YES];
    UINavigationController *navigationController =
        [[UINavigationController alloc]
            initWithRootViewController:detailViewController];
    [self presentModalViewController:navigationController
                           animated:YES];
}
```

运行你的应用，并添加一个新项目。如果一切操作正确，该项目就会被添加到列表中！我们现在拥有一个完全可用的方法来向列表中添加项目。

可以发布了。不过，你可能会注意到，应用在两次启动之间不会保存这些物品。我们将在下一章中扩展这个应用，以添加该功能以及其他附加功能。

## 总结

本章深入探讨了视图控制器的诸多内容。我们回顾了它们的生命周期，学习了如何使用它们实现应用逻辑，以及如何通过 nib 文件加载内容。现在，你应该已经熟悉了视图控制器的创建与使用，也应该能够在应用中将数据从一个视图控制器传递到另一个。在下一章中，我们将扩展这一概念，不仅在视图控制器之间传递数据，还要通过将数据持久化到磁盘，使其在应用多次启动之间可用。

