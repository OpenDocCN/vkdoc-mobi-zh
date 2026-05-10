# TasksListServiceTest

该类包含对 `TasksListService` 类的测试（我们不会列出 `TaskService` 类的测试，因为两者非常相似），如代码清单 2-31 所示。

```
class TasksListServiceTest: XCTestCase {
var sut:TasksListServiceProtocol!
var list: TasksListModel!
override func setUpWithError() throws {
sut = TasksListService(coreDataManager: InMemoryCoreDataManager())
list = TasksListModel(id: "12345-67890",
title: "Test List",
icon: "test.icon",
tasks: [TaskModel](),
createdAt: Date())
}
override func tearDownWithError() throws { ... }
func testSaveOnDB_whenSavesAList_shouldBeOneOnDatabase() {
sut.saveTasksList(list)
XCTAssertEqual(sut.fetchLists().count, 1)
}
func testSaveOnDB_whenSavesAList_shouldBeOneWithGivenIdOnDatabase() { ... }
func testDeleteOnDB_whenSavesAListAndThenDeleted_shouldBeNoneOnDatabase() {
sut.saveTasksList(list)
XCTAssertNotNil(sut.fetchListWithId("12345-67890"))
sut.deleteList(list)
XCTAssertEqual(sut.fetchLists().count, 0)
}
}
代码清单 2-31
TaskListServiceTest 文件代码测试
```

在测试方法 `testSaveOnDB_whenSavesAList_shouldBeOneOnDatabase` 中，我们首先将一个列表保存到数据库，然后检查该列表是否存在于数据库中（我们使用 `XCTAssertEqual` 函数来完成此操作）。

在测试方法 `testDeleteOnDB_whenSavesAListAndThenDeleted_shouldBeNoneOnDatabase` 中，我们首先将一个列表保存到数据库，检查该列表是否存在，然后将其删除，最后验证它在数据库中已不存在。此例中，我们结合使用了两个 `XCTAssert` 函数（`XCTAssertNotNil` 和 `XCTAssertEqual`）。

