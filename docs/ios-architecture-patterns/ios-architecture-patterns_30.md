# HomePresenterTest

为了测试`HomePresenter`，我们需要模拟`HomeViewController`（`MockHomeViewController`），并使用之前在测试`HomeViewController`时创建的`MockHomeRouter`（列表 6-72）。

```
import XCTest
@testable import VIP_MyToDos
final class HomePresenterTest: XCTestCase {
private var sut: HomePresenter!
private var viewController: MockHomeViewController!
private var router: MockHomeRouter!
let taskList = TasksListModel(id: "12345-67890",
title: "Test List",
icon: "test.icon",
tasks: [TaskModel](),
createdAt: Date())
override func setUpWithError() throws {
super.setUp()
viewController = MockHomeViewController()
router = MockHomeRouter()
viewController.router = router
sut = HomePresenter()
sut.viewController = viewController
}
...
}
列表 6-72
HomePresenterTest 的设置
```

在设置`MockHomeViewController`时，请记住它必须遵循`HomeViewControllerInput`、`AddListDelegate`和`SelectedListDelegate`协议（列表 6-73）。

```
final class MockHomeViewController: HomeViewControllerInput, AddListDelegate, SelectedListDelegate {
var router: HomeRouterDelegate?
private(set) var lists: [TasksListModel] = [TasksListModel]()
private(set) var selectedList: TasksListModel = TasksListModel()
private(set) var showAddList: Bool = false
func reloadDataWithTaskList(viewModel: HomeModel.FetchTasksLists.ViewModel) {
lists = viewModel.tasksLists
}
func showAddListView(viewModel: HomeModel.AddTasksList.ViewModel) {
showAddList = true
router?.showAddListView(delegate: self)
}
func showSelectedList(viewModel: HomeModel.SelectTasksList.ViewModel) {
selectedList = viewModel.tasksList
router?.showSelectedList(delegate: self, list: viewModel.tasksList)
}
func didAddList() {}
func updateLists() {}
}
列表 6-73
MockHomeViewController 代码
```

最后，我们为`HomePresenter`设置不同的测试（列表 6-74）。

```
final class HomePresenterTest: XCTestCase {
...
func testPresentLists_whenShouldPresentList_reloadDataIsCalled() {
let response = HomeModel.FetchTasksLists.Response(tasksLists: [taskList])
sut.presentTasksLists(response: response)
XCTAssertTrue(!viewController.lists.isEmpty)
}
func testShowAddTaskList_whenAddTaskListIsSelected_shouldNavigateToAddTaskList() {
let response = HomeModel.AddTasksList.Response(addListDelegate: viewController)
sut.showAddTaskList(response: response)
XCTAssertTrue(viewController.showAddList)
XCTAssertTrue(router.navigateToAddList)
}
func testShowSelectedList_whenTaskListIsSelected_shouldNavigateToTasksList() {
let response = HomeModel.SelectTasksList.Response(selectedListDelegate: viewController, tasksList: taskList)
sut.showSelectedList(response: response)
XCTAssertEqual(viewController.selectedList.id, taskList.id)
XCTAssertTrue(router.navigateToTaskList)
XCTAssertEqual(router.selectedList.id, taskList.id)
}
}
列表 6-74
HomePresenterTest 测试用例代码
```

在第一个测试中，我们将验证：如果向 Presenter 传递一个包含列表的数组，它会将其传递给 ViewController，因此 ViewController 的`lists`参数不会为空。

在第二个测试中，我们将证明：调用`HomePresenter`的`showAddTaskList`方法会修改`MockHomeViewController`的`showAddList`参数以及`MockHomeRouter`的`navigateToTaskList`参数。

最后一个`HomePresenter`测试检查：当调用`showSelectedList`方法并向其传递一个任务列表（就像我们已选中该列表一样）时，ViewController 会接收到该列表，Router 也会接收到该列表并导航至`TaskList`场景。

## 总结

Clean Swift 或 VIP 架构的设计初衷，主要是为了减少臃肿的 ViewController，它遵循 Clean Architecture 的原则，并采用单向信息流（我们称之为 VIP 循环）。代码围绕所谓的“场景”进行组织，这些场景对应应用程序的各个界面（类似于 VIPER 架构中的“模块”）。

VIP 架构具有许多优点，例如可扩展、易于维护和可测试。然而，它也有一些缺点。其中一个最重要的缺点可能是：由于使用了大量协议进行通信，该架构在初期可能显得复杂，甚至看起来有些过度设计。

为了在这方面提供帮助，互联网上很容易找到一些模板。一旦将这些模板导入 Xcode，我们只需输入想要创建的场景名称，就能生成所有重复的代码。

通过 VIP 架构，我们完成了对当今五种最常用架构的研究，指出了它们的优缺点，并将其应用于一个简单应用的开发。

在下一章中，我们将从更宏观的角度探讨一些使用频率略低的架构，了解它们的结构基础以及各自的优缺点。

脚注 1

