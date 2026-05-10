# Mock 服务

为了便于测试那些依赖 Model 层访问的类，我们可以使用 Model 层的“模拟”（mock）对象，这将使我们能够模拟真实类的行为，同时更轻松地验证结果。

在本例中，我们将设置两个这样的类来“扮演”服务角色：`MockTaskListService` 和 `MockTaskService`（代码清单 2-32 和代码清单 2-33）。

```
class MockTaskService: TaskServiceProtocol {
private var list: TasksListModel!
required init(coreDataManager: CoreDataManager) {}
convenience init(list: TasksListModel) {
self.init(coreDataManager: CoreDataManager.shared)
self.list = list
}
override func saveTask(_ task: TaskModel, in taskList: TasksListModel) {
list = taskList
list.tasks.append(task)
}
override func fetchTasksForList(_ taskList: TasksListModel) -> [TaskModel] {
return list.tasks
}
override func updateTask(_ task: TaskModel) {
guard let tasks = list.tasks else { return }
var updatedTasks = [TaskModel]()
tasks.forEach({
var updatedTask = $0
if $0.id == task.id {
updatedTask.done.toggle()
}
updatedTasks.append(updatedTask)
})
list.tasks = updatedTasks
}
override func deleteTask(_ task: TaskModel) {
list.tasks = list.tasks.filter({ $0.id != task.id })
}
}
代码清单 2-33
MockTaskService 代码
```

```
class MockTaskListService: TasksListServiceProtocol {
private var lists: [TasksListModel] = [TasksListModel]()
required init(coreDataManager: CoreDataManager) {}
convenience init(lists: [TasksListModel]) {
self.init(coreDataManager: CoreDataManager.shared)
self.lists = lists
}
override func saveTasksList(_ list: TasksListModel) {
lists.append(list)
}
override func fetchLists() -> [TasksListModel] {
return lists
}
override func fetchListWithId(_ id: String) -> TasksListModel? {
return lists.filter({ $0.id == id }).first
}
override func deleteList(_ list: TasksListModel) {
lists = lists.filter({ $0.id != list.id })
}
}
代码清单 2-32
MockTaskListService 代码
```

正如我们所看到的，这些类重写了原始类的方法，但它们仅操作我们传入的数据，而不访问数据库。

