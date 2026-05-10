# 路由器

路由器是负责在 ViewController 之间导航并在它们之间传递信息的组件（清单 6-5）：

- 路由器包含导航逻辑（这使得我们将其从 ViewController 中移除）。
- 它持有对视图（ViewController）的弱引用。
- 这是一个可选组件，因为一个场景的 ViewController 可能没有导航选项。

```swift
protocol HomeRouterDelegate {}
final class HomeRouter {
weak var source: UIViewController?
}
extension HomeRouter: HomeRouterDelegate {}
```

*清单 6-5：`HomeSceneRouterLogic` 结构代码*

