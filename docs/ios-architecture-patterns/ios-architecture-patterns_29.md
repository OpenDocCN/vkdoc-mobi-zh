# 排版后内容

```
final class HomeViewControllerTest: XCTestCase {
...
func testAddList_whenAddListIsSelected_shoulCallAddList() {
sut.addList()
XCTAssertTrue(interactor.addListRequest)
let viewModel = HomeModel.AddTasksList.ViewModel(addListDelegate: interactor)
sut.showAddListView(viewModel: viewModel)
XCTAssertTrue(router.navigateToAddList)
}
func testSelectedList_whenAListIsSelected_shouldCallTasksList() {
sut.selectedListAt(index: IndexPath(row: 1, section: 1))
XCTAssertEqual(interactor.selectTasksListIndex.row, 1)
XCTAssertEqual(interactor.selectTasksListIndex.section, 1)
let viewModel = HomeModel.SelectTasksList.ViewModel(selectedListDelegate: interactor, tasksList: taskList)
sut.showSelectedList(viewModel: viewModel)
XCTAssertTrue(router.navigateToTaksList)
XCTAssertEqual(router.selectedList.id, taskList.id)
}
func testDeleteList_whenAListIsDelete_shouldBeNoLists() {
sut.deleteListAt(indexPath: IndexPath(row: 1, section: 1))
XCTAssertEqual(interactor.deleteTasksListIndex.row, 1)
XCTAssertEqual(interactor.deleteTasksListIndex.section, 1)
}
}
```

代码清单 6-68：`HomeViewControllerTest` 测试用例开发

在第一个测试中，我们测试了 `AddTaskList` 场景的调用。为此，我们将检查在调用 `addList` 方法后，`MockHomeInteractor` 的 `addListRequest` 变量是否被修改；然后，在调用 `showAddListView` 方法（并向其传递一个 `ViewModel`）后，`MockHomeRouter` 的 `navigateToAddList` 变量是否被修改。

在第二个测试中，我们测试了对 `TaskList` 场景的调用。为此，我们将进行的测试是，一方面验证我们是否正确地将一个任务列表的 `IndexPath` 传递给了 `HomeInteractor`，另一方面验证当我们指示 `HomeRouter` 导航到 `TaskList` 场景时，是否正确传递了指定的 `TaskListModel` 对象，并且该对象修改了 `navigateToAddList` 变量。

在最后一个测试中，我们将检查在执行删除任务列表的操作时，我们传递的 `IndexPath` 是否被正确接收。

## `HomeInteractorTest`

与之前的情况类似，第一步是创建启动测试所需的参数。对于 `HomeInteractor`，除了 `sut` 之外，我们还将为 `Presenter`（`MockHomePresenter`）建立一个模拟对象，以及一个任务列表，我们将把这个列表传递给我们将要创建并传递给交互器的 `MockTaskListService` 实例（代码清单 6-69）。

```
import XCTest
@testable import VIP_MyToDos
final class HomeInteractorTest: XCTestCase {
private var sut: HomeInteractor!
private var presenter: MockHomePresenter!
let taskList = TasksListModel(id: "12345-67890",
title: "Test List",
icon: "test.icon",
tasks: [TaskModel](),
createdAt: Date())
override func setUpWithError() throws {
super.setup()
let mockTaskListService = MockTaskListService(lists: [taskList])
presenter = MockHomePresenter()
sut = HomeInteractor(tasksListService: mockTaskListService)
sut.presenter = presenter
}
...
}
```

代码清单 6-69：`HomeInteractorTest` 设置

`MockHomePresenter` 必须遵循 `HomePresenterInput` 协议。在实现协议方法时，我们将修改最初创建的变量的值，这将使我们能够验证 `HomeInteractor` 是否正确地调用了这些方法（代码清单 6-70）。

```
final class MockHomePresenter: HomePresenterInput {
private(set) var taskLists: [TasksListModel] = [TasksListModel]()
private(set) var showAddList: Bool = false
private(set) var selectedList: TasksListModel = TasksListModel()
func presentTasksLists(response: HomeModel.FetchTasksLists.Response) {
taskLists = response.tasksLists
}
func showAddTaskList(response: HomeModel.AddTasksList.Response) {
showAddList = true
}
func showSelectedList(response: HomeModel.SelectTasksList.Response) {
selectedList = response.tasksList
}
}
```

代码清单 6-70：`MockHomePresenter` 代码

最后，我们为想要测试的不同情况开发测试用例（代码清单 6-71）。

```
final class HomeInteractorTest: XCTestCase {
...
func testPresentList_whenPresentLists_shouldBeOneList() {
let request = HomeModel.FetchTasksLists.Request()
sut.fetchTasksLists(request: request)
XCTAssertEqual(presenter.taskLists.count, 1)
}
func testAddList_whenAddListSelected_shouldShowAddList() {
let request = HomeModel.AddTasksList.Request()
sut.addList(request: request)
XCTAssertTrue(presenter.showAddList)
}
func testSelectList_whenListIsSelected_shouldBeList() {
let request = HomeModel.SelectTasksList.Request(index: IndexPath(row: 0, section: 0))
sut.fetchTasksLists()
sut.selectList(request: request)
XCTAssertEqual(presenter.selectedList.id, taskList.id)
}
func testRemoveList_whenListIsRemoved_shouldBeNoLists() {
let request = HomeModel.RemoveTasksList.Request(index: IndexPath(row: 0, section: 0))
sut.fetchTasksLists()
sut.removeList(request: request)
XCTAssertTrue(presenter.taskLists.isEmpty)
}
}
```

代码清单 6-71：`HomeInteractorTest` 测试用例开发

在第一个测试中，我们将验证是否向 Presenter 传递了一个任务列表（实际上是一个只包含一个列表的数组，即我们在实例化 `MockTaskListService` 时传递的那个列表）。

在第二个测试中，我们验证是否已向 Presenter 指示我们要导航到 `AddTaskList` 场景。

接着，在第三个测试中，我们检查如果选择一个列表（传递其 `IndexPath`），选中的列表是否被传递给了 Presenter。

最后，在第四个也是最后一个测试中，我们将验证当删除一个任务列表（即我们引入 `MockTaskListService` 中的唯一一个列表）时，是否向 Presenter 传递了一个空数组。



