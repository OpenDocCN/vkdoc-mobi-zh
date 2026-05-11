# 本章内容

作为本书的最后一章，我将向你展示如何通过响应关键的 Windows 事件来控制应用生命周期。我将向你展示如何修复 Visual Studio 添加到项目中的代码，如何正确处理应用的挂起与恢复，以及如何实现契约，让你的应用融入 Windows 8 提供的更广泛用户体验中。在此过程中，我将演示地理位置功能的使用，并展示如何设置和管理重复执行的异步任务。表 5-1 提供了本章的总结。

表 5-1. 本章总结

| 问题 | 解决方案 | 清单编号 |
| --- | --- | --- |
| 确保你的应用接收生命周期事件。 | 处理 `Application` 类中定义的 `Suspending` 和 `Resuming` 事件。 | 1–2 |
| 确保应用挂起时后台任务能够干净地终止。 | 请求延迟，并利用这五秒的宽限期为挂起做准备。 | 3-6 |
| 实现一个契约。 | 在你的代码中添加功能实现，并重写 `Application` 方法，以便在契约被调用时接收生命周期事件。 | 7–9 |

## 处理应用生命周期

在第 1 章中，我向你展示了 Visual Studio 放置在 `App.xaml.cs` 文件中的骨架代码，以便快速启动我的示例项目。这段代码处理 Windows 应用生命周期事件，确保我能够对操作系统发送给我的信号做出适当的响应。Windows 应用的生命周期有三个关键阶段。

第一阶段是**激活**，发生在你的应用启动时。Windows 运行时将加载并处理你的内容，并在一切准备就绪时发出信号。例如，正是在激活阶段，我生成了示例应用的动态内容。

用户通常不会关闭 Windows 应用；他们只是切换到另一个应用，让 Windows 来处理后续事宜。这就是为什么 Windows 应用的用户界面上没有关闭按钮或菜单栏。一个不再需要的应用会被转入第二阶段，即**挂起**。当应用挂起时，不会执行任何代码，也不会与用户发生任何交互。

如果用户切换回一个已挂起的应用，那么第三阶段就会发生：**恢复**应用。应用会显示给用户，并且应用代码的执行会恢复。挂起的应用并不总是会被恢复；例如，如果设备内存不足，Windows 可能直接终止一个挂起的应用。

## 修正 Visual Studio 事件代码

我需要做的第一个更改是从 `MainPage.xaml.cs` 代码隐藏文件中移除视图模型。一直以来，我逐步移动视图模型，以展示它在 Windows 应用中的核心作用。在本章中，我将向你展示一些功能，这些功能要求视图模型作为生命周期的一部分被创建。你可以在清单 5-1 中看到简化后的 `MainPage.xaml.cs` 文件。

**清单 5-1.** 从 `MainPage.xaml.cs` 文件中移除视图模型实例化

```
using GrocerApp.Data;
using System;
using Windows.Data.Xml.Dom;
using Windows.UI.Notifications;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

namespace GrocerApp.Pages {

public sealed partial class MainPage : Page {
        private ViewModel viewModel;

public MainPage() {
            this.InitializeComponent();
        }

private void UpdateBadge() {
            // . . .为简洁起见，省略了语句。. . .
        }

private void UpdateTile() {
            // . . .为简洁起见，省略了语句。. . .
        }

protected override void OnNavigatedTo(NavigationEventArgs e) {
            viewModel = (ViewModel)e.Parameter;
            this.DataContext = viewModel;

MainFrame.Navigate(typeof(ListPage), viewModel);

viewModel.GroceryList.CollectionChanged += (sender, args) => {
                UpdateTile();
                UpdateBadge();
            };

UpdateTile();
            UpdateBadge();
        }

private void NavBarButtonPress(object sender, RoutedEventArgs e) {
            Boolean isListView = (Button)sender == ListViewButton;
            MainFrame.Navigate(isListView ? typeof(ListPage)
                : typeof(DetailPage), viewModel);

}
    }
}
```

现在，我可以转向 `App.xaml.cs` 文件，并在模板代码的基础上构建，以响应应用生命周期的变化。不幸的是，Visual Studio 添加到项目中的用于处理生命周期事件的代码并不奏效。它可以很好地处理激活和挂起，但无法在应用被恢复时通知应用。幸运的是，解决方案非常简单，你可以在清单 5-2 中看到需要对 `App.xaml.cs` 进行的更改。

**清单 5-2.** 处理生命周期通知事件

```
using GrocerApp.Data;
using System;
using Windows.ApplicationModel;
using Windows.ApplicationModel.Activation;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace GrocerApp {

sealed partial class App : Application {
        private ViewModel viewModel;

public App() {
            this.InitializeComponent();

viewModel = new ViewModel();

viewModel.StoreList.Add("Whole Foods");
            viewModel.StoreList.Add("Kroger");
            viewModel.StoreList.Add("Costco");
            viewModel.StoreList.Add("Walmart");

viewModel.GroceryList.Add(new GroceryItem {
                Name = "Apples",
                Quantity = 4, Store = "Whole Foods"
            });
            viewModel.GroceryList.Add(new GroceryItem {
                Name = "Hotdogs",
                Quantity = 12, Store = "Costco"
            });
            viewModel.GroceryList.Add(new GroceryItem {
                Name = "Soda",
                Quantity = 2, Store = "Costco"
            });
            viewModel.GroceryList.Add(new GroceryItem {
                Name = "Eggs",
                Quantity = 12, Store = "Kroger"
            });

this.Suspending += OnSuspending;
            this.Resuming += OnResuming;
        }

protected override void OnLaunched(LaunchActivatedEventArgs args) {
            Frame rootFrame = Window.Current.Content as Frame;

if (rootFrame == null) {
                rootFrame = new Frame();
                if (args.PreviousExecutionState == ApplicationExecutionState.
                 Terminated)
                {
                    //TODO: 从先前挂起的应用程序加载状态
                }
                Window.Current.Content = rootFrame;
            }

if (rootFrame.Content == null) {

if (!rootFrame.Navigate(typeof(Pages.MainPage), viewModel)) {
                    throw new Exception("无法创建初始页面");
                }
            }
            Window.Current.Activate();
        }

private void OnResuming(object sender, object e) {
            viewModel.GroceryList[1].Name = "Resume";
        }

private void OnSuspending(object sender, SuspendingEventArgs e) {
            var deferral = e.SuspendingOperation.GetDeferral();
            viewModel.GroceryList[0].Name = "Suspend";
            deferral.Complete();
        }
    }
}
```

我已高亮显示最重要的更改，即注册 `Suspending` 和 `Resuming` 事件，并向这些事件的处理方法中添加了语句。Visual Studio 在创建类时包含了一个用于处理 `Suspending` 事件的处理程序，但我必须添加 `Resuming` 处理程序才能收到事件通知。我移除了 `OnLaunched` 方法中的代码，这些代码试图（但未能）判断应用何时被恢复。



