# 配置器

这是一个可选组件，但经常被大量使用，因为它允许我们初始化场景的组件并建立它们之间的关系（清单 6-7）：

```swift
import UIKit
final class HomeConfigurator {
func configured(_ viewController: HomeViewController) -> HomeViewController {
let interactor = HomeInteractor()
let presenter = HomePresenter()
let router = HomeRouter()
router.viewController = viewController
presenter.viewController = viewController
interactor.presenter = presenter
viewController.interactor = interactor
viewController.router = router
return viewController
}
}
```

*清单 6-7：`HomeConfigurator` 类代码*

> 注意：正如您在 VIPER 架构中所见，这里我们也使用了基于 Input/Output 术语的协议命名规范，以便于识别和使用。

