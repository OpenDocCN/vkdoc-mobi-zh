# 交互器

交互器负责管理视图请求，获取必要的数据（无论是来自数据库还是网络），并将其传递给展示器：

- 交互器包含每个场景的业务逻辑。
- 根据视图的请求，交互器会连接一个或多个工作者/服务（我们稍后将看到，它们负责获取数据）。
- 交互器持有对展示器的强引用。
- 交互器必须遵守视图用于发送事件的协议。

在清单 6-3 中，您可以看到我们开始处理 `HomeInteractor` 的基础代码。

```swift
import Foundation
protocol HomeInteractorOutput: AnyObject {}
typealias HomeInteractorInput = HomeInteractorOutput
final class HomeInteractor {
var presenter: HomePresenterInput?
}
```

*清单 6-3：交互器类定义其输出协议*

