# 视图（UIViewController）

我们可以将视图（+`UIViewController`）视为每个 VIP 循环的起点和终点（它将信息和事件传递给交互器，并接收来自展示器的响应）：

- `UIViewController` 管理场景，并持有对视图的强引用。
- 每个 `UIViewController` 都持有对交互器和路由器的强引用。
- 视图中发生的事件将传递给交互器。
- 视图从展示器接收数据，因为 `UIViewController` 必须遵守展示器用于发送信息的协议。

例如，下面的初始代码展示了如何呈现一个场景的 ViewController 和视图（我们应用程序的*首页*屏幕）（清单 6-1 和清单 6-2）。

```swift
import UIKit
protocol HomeViewDelegate: AnyObject {}
final class HomeView: UIView {
var delegate: HomeSceneViewDelegate?
}
```

*清单 6-2：视图定义了连接到 ViewController 的协议*

```swift
import UIKit
protocol HomeViewControllerInput: AnyObject {}
protocol HomeViewControllerOutput: AnyObject {}
final class HomeViewController: UIViewController {
var interactor: HomeInteractorInput?
var router: HomeRoutingDelegate?
}
extension HomeViewController: HomeViewControllerInput {}
extension HomeViewController: HomeViewDelegate {}
```

*清单 6-1：ViewController 类包含 Input 协议以及对交互器、路由器和视图的引用*

