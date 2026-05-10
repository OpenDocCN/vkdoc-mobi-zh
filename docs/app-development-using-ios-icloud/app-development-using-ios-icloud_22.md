# 15. 用户间对话 – 控制器定义

## 控制器类

在模型-视图-控制器（MVC）架构模式中，控制器是模型（Model）与视图（View）之间的中介。在我们的聊天应用中，当用户首次打开应用时，会将用户导航至创建个人资料的视图控制器。对于其他所有情况，则会显示 `ContactViewController`，用于添加联系人或与现有联系人发起聊天。以下为代码分解：

**Import UIKit:**

- 此行导入 UIKit 框架，该框架对于在 iOS 应用中构建用户界面至关重要。

**类声明:**

- `class ControllerViewController: UIViewController`: 定义了一个名为 `ControllerViewController` 的新类，该类继承自 `UIViewController`。这表明此类将管理一个视图及其交互。

**属性:**

- `userid: String = ""`: 一个用于存储用户 ID 的字符串属性。初始化为空字符串。
- `contact: Contact = Contact()`: `Contact` 类的一个实例。
- `profile: Profile?`: 一个可选的 profile 对象。问号表示它可能为 `nil`。

```
import UIKit

class ControllerViewController: UIViewController {
    var userid: String = ""
    let contact: Contact = Contact()
    var profile: Profile?
```

## 生命周期方法

此方法是 UIKit 中的一个生命周期方法，当视图控制器的视图被加载到内存中时会被调用。这是为视图控制器执行初始设置任务（例如设置用户界面、加载数据或注册通知）的常见位置。

我们在 `viewDidLoad()` 中调用了一个名为 `checkProfileExist()` 的自定义方法。该方法负责检查用户个人资料是否存在。

```
override func viewDidLoad() {
    super.viewDidLoad()
    checkProfileExist()
}
```



### 检查个人资料是否存在的方法

此函数用于处理检查用户个人资料是否存在，并根据结果调用不同的视图控制器。代码执行步骤如下：

**获取 iCloud 用户 ID：**

-   调用 `contact.getICloudUserID { ... }` 通过完成处理程序获取用户的 iCloud 用户 ID。
-   完成处理程序接收两个参数：
    -   `userID`：获取到的 iCloud 用户 ID（可选的 `String` 类型）
    -   `error`：获取过程中遇到的任何错误（可选的 `Error` 类型）

**处理错误：**

-   如果 `error` 不为 nil，则打印一条错误信息，表明获取 iCloud 用户 ID 时出现问题。

**处理用户 ID（如果成功获取）：**

-   如果 `userID` 有值，则将其存储在 `self.userid` 属性中。
-   `self.contact.group.enter()` 进入一个调度组以管理异步任务。
-   `self.contact.checkProfileExist(userID: userID)` 检查获取到的 ID 对应的用户资料是否存在。
-   `self.contact.group.notify(queue: .main) { ... }` 设置一个通知，在调度组完成所有任务时于主线程上调用。

**处理资料存在性：**

-   在通知块内部，检查 `contact.profileExists`（一个布尔属性）。
-   如果 `contact.profileExists` 为 true：
    -   将 `self.profile` 设置为获取到的 `contact.profile`（假设其已被填充）
    -   调用 `popUpContact()`（详见下文）
-   如果 `contact.profileExists` 为 false：
    -   调用 `popUpProfile()`（详见下文）

```
func checkProfileExist(){
contact.getICloudUserID { (userID, error) in
if let error = error {
print("Error: \(error)")
} else if let userID = userID {
self.userid = userID
self.contact.group.enter()
self.contact.checkProfileExist(userID: userID)
self.contact.group.notify(queue: .main) { [self] in
if(contact.profileExists){
self.profile = contact.profile
popUpContact()
}else{
popUpProfile()
}
}
}
}
}
```

## 导航到视图控制器

这些函数根据用户资料是否存在，处理模态视图控制器的展示。代码分解如下：

**popUpProfile()**

-   创建 `CreateProfileViewController` 的实例
-   将 `userid` 传递给新的视图控制器
-   设置模态视图控制器的展示样式和过渡样式
-   以模态方式展示 `CreateProfileViewController`

**popUpContact()**

-   创建 `ContactViewController` 的实例
-   将 `profile` 和 `userid` 传递给新的视图控制器
-   设置模态视图控制器的展示样式和过渡样式
-   以模态方式展示 `ContactViewController`

```
func popUpProfile(){
let viewController = CreateProfileViewController()
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

### 完整代码

```
//
//  ControllerViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/15/24.
//
import UIKit
class ControllerViewController: UIViewController {
var userid:String = ""
let contact:Contact = Contact()
var profile:Profile?
override func viewDidLoad() {
super.viewDidLoad()
checkProfileExist()
}
func checkProfileExist(){
contact.getICloudUserID { (userID, error) in
if let error = error {
print("Error: \(error)")
} else if let userID = userID {
self.userid = userID
self.contact.group.enter()
self.contact.checkProfileExist(userID: userID)
self.contact.group.notify(queue: .main) { [self] in
if(contact.profileExists){
self.profile = contact.profile
popUpContact()
}else{
popUpProfile()
}
}
}
}
}
func popUpProfile(){
let viewController = CreateProfileViewController()
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
}
```

## 总结

至此，我们基本聊天应用的构建章节告一段落。提供的代码勾勒了一个基于模型-视图-控制器（MVC）模式的聊天应用的基本结构。

**应用的关键组件如下：**

-   `ControllerViewController`：这是管理应用流程的主控制器类。它负责处理用户认证、资料管理以及不同视图之间的导航。
-   `ContactTableViewCell`：一个自定义的表格视图单元格，用于显示联系人信息，包括头像、姓名以及可能的聊天预览。
-   `ChatDetailsTableViewCell`：另一个自定义的表格视图单元格，专门用于展示单条聊天消息的详细信息，如发送者、消息内容和时间戳。

**我们还探讨了以下核心功能：**

-   **用户认证：** 检查用户资料是否存在，并显示资料创建界面或联系人列表
-   **联系人列表：** 使用 `ContactTableViewCell` 显示带有基本信息的联系人列表
-   **聊天详情：** 使用 `ChatDetailsTableViewCell` 呈现聊天对话的详细视图

**在实现 MVC 模式过程中，我们完成了以下工作：**

-   该代码通过分离控制器、视图和模型（通过 `Contact` 类及潜在的数据结构体现）的关注点，演示了基本的 MVC 原则。
-   使用表格视图单元格来展示数据，符合 MVC 模式。

### 下一步可以做什么

以下是你可以在哪些方面进一步深入，以将学习提升到新高度。

#### 模型层

-   考虑使用网络层从外部源获取数据。

#### 视图层

-   通过动画、过渡和视觉提示增强用户界面。
-   通过为 UI 元素提供适当的标签和提示来改善可访问性。
-   针对不同屏幕尺寸和设备优化布局和性能。

#### 控制器逻辑

-   实现错误处理和边缘情况处理。
-   通过重构改善代码的组织和可读性。
-   对于更大、更复杂的应用程序，考虑使用如 MVVM 或 VIPER 等设计模式。

#### 数据管理

-   实现高效的数据获取和缓存机制。

### 用户体验

-   专注于用户友好的交互和直观的导航。
-   在加载或处理任务期间，向用户提供清晰的反馈。

祝学习愉快。



