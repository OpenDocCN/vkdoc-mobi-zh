# 展示器

展示器（清单 6-4）的功能是接收来自交互器的信息，并将其转换为视图可以呈现的信息（即，我们将信息转换为 ViewModel）：

- 展示器包含展示逻辑。
- 它持有对视图的弱引用（视图充当展示器的输出）。
- 展示器将交互器的结果转换为视图可渲染的对象。

```swift
import UIKit
typealias HomePresenterInput = HomeInteractorOutput
typealias HomePresenterOutput = HomeViewControllerInput
final class HomePresenter {
weak var viewController: HomePresenterOutput?
}
extension HomePresenter: HomePresenterInput {}
```

*清单 6-4：在展示器代码中，我们将 `HomeInteractorOutput` 和 `HomeViewControllerInput` “重命名”为“展示器”命名规范*

