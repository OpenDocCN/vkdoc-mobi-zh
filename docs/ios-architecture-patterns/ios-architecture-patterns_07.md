# 控制器测试

控制器是 MVC 的核心，它们是视图与模型之间的中介，同时还负责管理屏幕间的导航。因此，我们将在控制器中进行的测试将侧重于与模型层的交互以及从一个屏幕到另一个屏幕的导航。

例如，在 `HomeViewControllerTest.swift` 文件中，我们有一个测试，用于验证当执行 `deleteList` 方法时，选定的任务列表将从数据库中删除（代码清单 2-34）。

```
class HomeViewControllerTest: XCTestCase {
var sut: HomeViewController!
var navigationController: MockNavigationController!
var tasksListService: MockTaskListService!
var taskService: MockTaskService!
let list = TasksListModel(id: ProcessInfo().globallyUniqueString,
title: "Test title",
icon: "test.icon",
tasks: [TaskModel](),
createdAt: Date())
override func setUpWithError() throws {
tasksListService = MockTaskListService(lists: [list])
taskService = MockTaskService()
sut = HomeViewController(tasksListService: tasksListService, taskService: taskService)
navigationController = MockNavigationController(rootViewController: UIViewController())
navigationController.pushViewController(sut, animated: false)
navigationController.vcIsPushed = false
}
override func tearDownWithError() throws {
sut = nil
navigationController = nil
taskService = nil
super.tearDown()
}
func testDeleteList_whenDeletedActionIsCalled_shouldBeNoneOnDatabase() {
sut.deleteList(list)
XCTAssertEqual(tasksListService.fetchLists().count, 0)
}
func testPushVC_whenAddListIsCalled_thenPushAddListVCCalled() {
sut.addListAction()
XCTAssertTrue(navigationController.vcIsPushed)
}
func testPushVC_whenTaskListIsCalled_thenPushTaskListVCCalled() {
sut.selectedList(TasksListModel())
XCTAssertTrue(navigationController.vcIsPushed)
}
}
代码清单 2-34
HomeViewControllerTest 代码测试
```

这里我们可以看到，在初始化控制器时注入 `MockTasksListService` 和 `MockTaskService` 类的依赖关系，如何使我们能够使用这些具有可控结果的“模拟”类（从而加速测试）。

我们在 `testDeleteList_whenDeletedActionIsCalled_shouldBeNoneOnDatabase` 测试中所做的是，首先创建一个任务列表并保存到数据库，然后执行 `deleteList` 方法，并检查该列表是否已从数据库中删除。

在 `testPushVC_whenAddListIsCalled_thenPushAddListVCCalled` 测试中，我们执行 `addListAction` 方法，并检查 `vcIsPushed` 参数是否为 `true`，该参数已在 `MockNavigationController` 中定义。

应用程序中的其余控制器也包含类似的单元测试，正如你在项目代码中所见。



### 视图测试

在 `Views` 中，我们要测试的是不同组件是否显示了正确的信息，以及用户交互是否产生了预期结果。

例如，对于 `HomeView`，我们有一个按钮用于进入添加任务列表界面。因此，我们将测试该按钮是否以 `addListAction` 方法作为其目标（列表 2-35）。

```
func testButtonAction_whenAddListButtonIsTapped_shouldBeCalledAddListAction() {
    let addListButton = sut.addListButton
    XCTAssertNotNil(addListButton, "UIButton does not exist")
    guard let addListButtonAction = addListButton.actions(forTarget: sut, forControlEvent: .touchUpInside) else {
        XCTFail("Not actions assigned for .touchUpInside")
        return
    }
    XCTAssertTrue(addListButtonAction.contains("addListAction"))
}
```

*列表 2-35* – `HomeView` 的 `addListButton` 测试代码

我们在测试中所做的是，首先检查按钮是否存在，然后检查它是否关联了一个方法，并且该方法是 `addListAction`。

在 `HomeView` 中，还有一个用于显示用户创建的任务列表的表格。表格带有多种方法，我们都需要进行测试。因此，例如，如果在 `setUpWithError` 方法中我们在数据库中创建了一个列表，我们必须验证该列表有一行，该行显示了一个单元格，或者，当我们删除该列表时，列表数量不为零（列表 2-36）。

```
// UITableView 有一行
func testTableView_whenModelHasAList_shouldBeOneRow() {
    XCTAssertEqual(sut.tableView.numberOfRows(inSection: 0), 1)
}

// 在 IndexPath(row: 0, section: 0) 处的 UITableView 有一个 UITableViewCell
func testTableView_whenModelHasAList_shoulBeACellAtIndexPath() {
    let indexPath = IndexPath(row: 0, section: 0)
    let cell = sut.tableView.dataSource?.tableView(sut.tableView, cellForRowAt: indexPath)
    XCTAssertNotNil(cell)
}

// 在 TasksList 对象被删除后，tasksList 包含 0 个元素
func testTableView_whenListIsDeleted_shouldBeNoneOnModel() {
    let indexPath = IndexPath(row: 0, section: 0)
    sut.tableView.dataSource?.tableView?(sut.tableView, commit: .delete, forRowAt: indexPath)
    XCTAssertEqual(sut.tasksList.count, 0)
}
```

*列表 2-36* – 用于 `UITableView` 方法的测试

---

## 总结

模型-视图-控制器架构可能是最常用的，也是开始开发应用程序时通常首先使用的架构，因为它最简单。

MVC 有清晰的职责分离（模型、视图和控制器），尽管很多时候视图与控制器紧密耦合，这降低了其可重用性。这使得在没有视图干预的情况下测试控制器变得更加困难。

另一方面，必须注意不要使控制器过载，因为作为 MVC 的核心部分，通常会将职责添加到其中，例如业务逻辑、导航到其他屏幕、视图本身及其组件。

在我们开发的应用程序示例中，由于它是一个相当简单的应用程序，所以控制器很小，但想想如果我们开始添加额外的功能会发生什么。因此，最好采取一些有助于减小其规模的措施（其中一些我们已经应用了）：

-   将视图代码移动到其自己的类中，并在控制器本身中保留一个引用。
-   将所有与数据相关的内容（例如获取和处理数据，或数据排序）传递给模型。
-   通过协调器管理屏幕之间的导航。

从现在开始，我们将看到更复杂的架构，这些架构旨在解决 MVC 可能存在的问题，并且其中展示的一些解决方案可以迁移到这种架构中。

---

