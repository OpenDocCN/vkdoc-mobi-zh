# 4. 模型-视图-控制器设计框架

模型-视图-控制器（MVC）架构是软件开发中最常用的设计模式之一。MVC 根据代码所代表的功能，将代码划分为可管理的组件，从而有助于提高编写代码的可读性、可用性和可维护性。MVC 设计模式将应用程序中的对象分配为以下三个角色：

**模型（Model）**：模型对象是应用程序所需的数据。该类包含数据对象的所有定义以及管理数据的方法，包括将数据持久化到数据库。所需的数据封装在模型对象中，并且可以与其他模型对象建立一对多关系。从典型的设计角度来看，任何需要持久化到数据库的内容都应在模型对象中。

**视图（View）**：视图对象包含应用程序的屏幕和用户交互部分。屏幕渲染和绘制功能位于视图对象中。视图对象可以利用数据对象来显示或捕获应用程序的数据。

**控制器（Controller）**：控制器连接不同的视图对象，使应用程序能够交互并执行所需功能。它解释在视图对象中执行的用户操作，并将数据更改（新增/更新/删除）传达给模型对象。



## 示例代码

我们将通过下面的示例来解释这个概念。

在我们的应用程序中，系统会检查用户是否已存在，如果不存在，则该用户被视为首次使用，系统会提示他们创建新的登录资料。如果是现有用户，则用户将被重定向到用户主屏幕。要实现此功能，请执行以下操作：

**模型**

* 创建一个`Model UIViewController`类，并查找 iCloud 用户 ID 详情。
* 在**模型**`UIViewController`类中，我们将调用 iCloud 数据库检查用户 ID 是否作为记录存在于资料数据库中。如果记录存在，则向调用函数返回一个 `true` 布尔值，否则返回 `false` 布尔值。

**视图**

* 创建一个**视图**`UIViewController`类，用于显示新资料创建屏幕。我们将显示一个简单的标题标签来展示这个概念。
* 创建一个**视图**`UIViewController`类，用于显示用户主屏幕。

**控制器**

* 创建一个**控制器**`UIViewController`类，并通过调用在用户定义方法中定义的相应模型方法来获取 iCloud 用户 ID。
* 在**控制器**`UIViewController`类中，该类将包含一个方法，该方法会调用一个函数来检查 iCloud 数据库中是否存在用户 ID（该函数的定义将在我们的模型`UIViewController`中，如下一个要点所述）。
* 在**控制器**`UIViewController`类中，我们将检查用户是否存在的返回布尔值，如果为 `false`，则调用一个函数来显示新资料屏幕。如果返回值是 `true`，它将调用另一个函数来显示用户主屏幕。

现在让我们看看实现同样功能的代码。

### 模型定义

```
import UIKit
import CloudKit
class Contact: NSObject {
```

模型类导入了两个框架：用于所有用户界面定义的 `UIKit`（这是视图控制器的默认框架）以及 `CloudKit`，因为我们使用 iCloud 来持久化数据。我们的模型类名为 `Contact`，类型为 `NSObject`，这是用于开发 iOS 应用程序的基础对象。

`UIKit` 包含以下重要组件：

* 用户界面组件，例如文本字段、标签和按钮
* 事件处理，用于处理用户对象上的用户交互
* 绘图和动画
* 应用程序生命周期管理，从应用程序启动到终止

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profileExists = false
```

我们定义了一个名为 `group` 的常量，它存储了一个名为 `DispatchGroup` 的同步对象的实例化对象。

接下来，我们实例化一个 `DispatchQueue` 类型的对象，并将其存储在一个名为 `syncQueue` 的常量中。该队列的名称为 `com.domain.app.sections`。这将帮助我们按顺序处理许多异步调用。

`profileExists` 是一个变量，初始设置为 `false`。如果用户的资料存在于我们的持久化 iCloud 存储中，则该值将更新为 `true`。

```
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
```

现在我们在 `Contact` 类内部定义一个嵌套类 `Profile`。该类模拟了持久化在 iCloud 存储中的自定义表 `profile`。`Profile` 类包含以下元素：

* `record`：这是类型为 `CKRecord` 的对象，定义在 iCloud 工具包中。实例化后，它将存储记录引用 ID。
* `userid`：一个字符串值，表示用户的唯一 ID。
* `personalprofile`：一个 `CKAsset`（定义在 iCloud 工具包中），用于存储用户资料图片。
* `name`：一个字符串值，用于存储用户名。
* `personalphone`：一个字符串值，表示用户的个人电话号码。
* `init` 方法允许调用类传入值进行实例化，这些值将存储在类的对象中，以备将来使用。

```
var profile:Profile?
```

现在我们定义了一个类级别的变量 `profile`，类型为我们的 `Profile` 类，如果用户资料存在，我们将在此存储它。

```
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
```

`getICloudUserID` 是我们 `Contact` 类中的一个自定义方法，它将帮助获取应用程序用户的 iCloud ID。注意，每个苹果用户都有一个唯一的用户 ID。此方法将获取该用户 ID 作为唯一标识。

`completion` 处理程序帮助该方法在完成时向调用方法返回两个值：一个代表用户 ID 的字符串字面量，以及可能出现的错误。如果没有错误，它将返回 `nil`。

`@escaping` 关键字表示闭包可能会在函数返回之后被调用。这是必要的，因为检索用户 ID 可能需要一些时间（异步）。

`-> Void`：这指定了闭包不返回任何值。

```
let container = CKContainer.default()
```

定义了一个名为 `container` 的**常量**，它存储了对默认 iCloud 容器的引用。

```
container.fetchUserRecordID { (recordID, error) in
```

现在我们执行系统定义的方法 `fetchUserRecordID`，该方法存在于 `CKContainer.default()` 方法中。此方法将获取苹果用户 ID 的详细信息（如果存在）；否则，它将抛出一个错误。

```
if let error = error {
```



在这个语句中，我们检查方法是否成功执行。如果没有，将返回一个错误，该错误将存储在`error`常量中。

```
completion(nil, error)
```

如果有错误，将执行此语句。注意，我们正在调用`completion`处理程序。我们将第一个参数设为`nil`（即用户 ID）并将错误作为第二个参数返回。这将告诉调用函数存在错误。

```
} else if let recordID = recordID {
```

如果方法成功检索到用户 ID，它将被存储在`recordID`常量中。

```
let userID = recordID.recordName
```

我们从`recordID`常量中检索用户的名称，该常量有一个名为`recordName`的属性用于存储用户 ID，并将其存储在`userID`常量中。

```
completion(userID, nil)
}
}
}
```

注意，我们再次调用`completion`处理程序。在这次调用中，我们传回用户 ID 并将错误设为`nil`。

```
func checkProfileExist(userID:String) {
```

现在，我们定义下一个函数，该函数将检查`profile`iCloud 数据库中是否存在配置文件记录。注意，该函数接收用户 ID（从上一个函数成功执行时检索到的），该 ID 将用作查询`profile`数据库的唯一标识符。

```
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
```

以上三个语句正在准备 iCloud 查询操作。

*   第一个语句定义了一个名为`pred`的常量。这将存储我们查询的条件。在我们的示例中，我们正在创建一个`NSPredicate`，其中`userid`字段等于传递给方法的`userID`。

*   接下来，我们定义查询并将其存储在名为`query`的常量中。注意，该查询使用作为变量（`pred`）传递的条件执行，并且它针对`profile`数据库执行。

*   最后，我们创建操作对象，该对象将`query`常量作为单个参数。

```
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

在执行查询之前，我们现在将设置一些配置元素。虽然这些配置元素对我们的示例来说并不重要，但当你有大量结果集返回时，它们是关键元素：

*   定义一个名为`operationConfiguration`的操作配置常量，它存储一个`CKOperation.Configuration()`对象。

*   接下来，我们将请求的超时间隔设置为 10 秒。

*   我们还将资源（针对`CKAsset`对象）的超时间隔设置为 10 秒。

*   最后，我们将配置对象添加到`operation`的`configuration`属性。

```
CKContainer.default().publicCloudDatabase.add(operation)
```

在这个语句中，我们将操作添加到公共云数据库（配置文件对象所在的位置）并执行查询。

```
operation.recordMatchedBlock = { recordID, result in
```

这段代码在查询完成时执行（注意，我们的查询是异步的）。

`recordMatchedBlock`是 CloudKit 中新引入（iOS 15）的闭包，专门设计用于处理异步调用。`recordID`是正在处理的唯一标识记录；它的类型是`CKRecord.ID`。

`result`变量表示一个 Swift 结构体，定义为`Result<CKRecord, Error>`，它封装了获取记录的结果。它可以是包含获取到的记录（作为`CKRecord`对象）的`success`，也可以是包含遇到的`error`的`failure`。

```
switch result {
```

我们现在使用`switch`语句来决定如何处理上面定义的`result`结构体的每种情况。

```
case .success(let record):
```

如果有可用的记录，将调用`success`，并且当前记录存在于`record`对象中。

```
self.profileExists = true
```

我们将类级别的变量`profileExists`设为`true`，因为我们发现用户配置文件存在。

```
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
```

我们还从`record`对象中检索各个字段的值，并将其存储在我们的`profile`对象中。注意我们是如何将检索到的字段转换为相应类型的。

```
self.profile!.record = record
```

最后，我们还将完整的记录存储在`profile`对象的`record`属性中，以便将来在需要时引用其他属性。

```
case .failure(let error):
```

`switch`块的这一部分用于处理失败情况。如果失败，将返回一个`error`对象。

```
print(error.localizedDescription)
```

我们在控制台打印错误的本地化描述。

```
self.profileExists = false
}
}
```

并将类变量`profileExists`设为`false`，因为无法检索到记录。

```
operation.queryResultBlock = { result in
```

操作对象有一个名为`queryResultBlock`的新方法。这个闭包在 iOS 15 中定义，非常适合同步调用。此块在`recordMatchedBlock`之后调用，在这里我们可以在离开方法之前进行一些最终调整。`queryResultBlock`具有与`recordMatchedBlock`相同的`result`结构。

```
DispatchQueue.main.async {
```

`checkProfileExist`函数在一个组调度主队列线程上调用。我们现在将退出该函数，通知调用函数线程已结束，它可以恢复其余代码的处理。

```
switch result {
```

我们使用`switch`来检查成功和失败条件。

```
case .failure(let error):
```

如果失败，则执行此块。它包含一个`error`对象作为参数，其中包含错误定义。

```
debugPrint(error)
```

我们在调试控制台上打印错误。

```
self.profileExists = false
```

由于出现错误，将类变量`profileExists`设为`false`。

```
self.syncQueue.async {
self.group.leave()
}
```

并离开线程返回到主队列线程以恢复函数。

```
case .success(_):
```

如果用户配置文件记录存在，则执行此块。

```
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

我们正在退出函数，返回主线程以从异步等待中恢复。

完整的模型视图代码如下所示：



```swift
import UIKit
import CloudKit
class Contact: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profileExists = false
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
var profile:Profile?
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
let container = CKContainer.default()
container.fetchUserRecordID { (recordID, error) in
if let error = error {
completion(nil, error)
} else if let recordID = recordID {
let userID = recordID.recordName
completion(userID, nil)
}
}
}
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
}
```

### 视图定义

```swift
class ProfileViewController: UIViewController{
```

这是我们 MVC 架构设计中的视图部分。我们需要展示两个视图。在这个例子中，我们展示的是个人资料视图控制器的显示。当用户个人资料不存在时，控制器会调用该视图。这个视图将帮助捕获用户个人资料。我们不会定义一个完整的个人资料捕获界面，因为目的是学习 MVC 概念。

我们将 `ProfileViewController` 定义为 `UIViewController` 类。

```swift
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Profile Page"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
```

为了显示，我们定义了一个标题标签作为 UI 标签展示。我们为标签设置了一些值，并提供了一些属性，例如颜色、文本对齐和字体。请注意，属性 `translatesAutoresizingMaskIntoConstraints`。如果你想通过代码绘制对象，需要将其设置为 false。

```swift
func drawProfileCreationScreen(){
```

`drawProfileCreationScreen` 是一个自定义函数，用于在屏幕上绘制标签。

```swift
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
```

定义两个常量，用于获取 iPhone 屏幕的高度和宽度。在我们绘制标签到屏幕上时，这两个常量都需要注意。

```swift
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
}
```

在这段代码中，我们将之前定义的 `headerLabel` 对象作为子视图添加到当前视图中。请注意约束是如何定义的，以便将标签定位在距顶部 30 像素的位置，占据整个屏幕宽度，并且高度为 40 像素。我们需要激活这些约束，才能让标题在屏幕上可见。

```swift
override func viewDidLoad() {
super.viewDidLoad()
drawProfileCreationScreen()
}
```

这是 UI 视图控制器的生命周期部分。当控制器类实例化视图时，会调用 `viewDidLoad()`。除了调用视图的父类方法外，我们还调用了自己的函数 `drawProfileCreationScreen()`，以便在屏幕上绘制标题。

个人资料视图的完整代码如下：

```swift
class ProfileViewController: UIViewController{
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "InTribe"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
}
override func viewDidLoad() {
super.viewDidLoad()
drawProfileCreationScreen ()
}
}
```

请注意，我没有展示主页面的代码。目的是展示 MVC 架构布局。



### 控制器定义

```
class MainViewController: UIViewController{
```

我们正在创建一个名为 `MainViewController` 的控制器类。请注意，我们的类是 `UIViewController` 类型。

```
var userid:String = ""
```

定义一个类级别的变量，如果用户在数据库中存在，该变量将存储用户 ID。

```
func checkProfileExist(){
```

`checkProfileExist` 是我们的自定义函数，用于检查用户是否存在于持久化数据库中。

```
var contact = Contact()
```

现在我们定义一个名为 `contact` 的变量，类型是我们自定义的模型类 `Contact`。请注意，`Contact` 没有任何初始化参数。该模型类将包含用于检查用户配置文件是否存在于 iCloud 数据库中的实际代码。

```
contact.getICloudUserID { (userID, error) in
```

现在我们调用自定义方法 `getICloudUserID`，该方法有一个完成处理程序，返回用户 ID 和可能发生的错误。

```
if let error = error {
print("Error: \(error)")
}
```

如果该方法返回错误，它将被分配给常量 `error`，并打印到屏幕上。

```
else if let userID = userID {
```

否则，如果用户 ID 存在，则将其分配给常量 `userID`。

```
self.userid = userID
```

现在，我们位于 else if 语句内部。仅当用户 ID 存在时才会执行此语句。我们将 `userID` 常量赋值给之前定义的类级别变量 `userID`。

```
self.contact.group.enter()
```

从 `contact` 模型类中，我们实例化一个名为 `group` 的常量。这是 Apple Dispatch 框架中的一个同步对象。它有助于协调多个异步任务，并确保它们全部完成后再继续执行。

```
self.contact.checkProfileExist(userID: userID)
```

调用存在于 `contact` 变量模型对象中的 `checkProfileExist` 方法。它以用户 ID 作为输入。

```
self.contact.group.notify(queue: .main) { [self] in
```

`Contact` 模型类中的 `checkProfileExist` 方法是一个异步方法。当它完成时，它会通知 `group` 对象中的主线程其已完成。这一点很重要，因为在异步方法中，我们需要等待方法完成其操作。

```
if(contact.profileExists){
```

现在 `checkProfileExist` 方法已完成，我们检查布尔变量 `profileExists` 是否为 true（请注意，`profileExists` 的值在 `contact` 类中根据用户 ID 是否存在被设置为 true 或 false）。

```
popUpHome()
```

如果存在，我们将调用方法 `popUpProfile()` 来实例化主屏幕。

```
}else{
popUpProfile()
}
}
}
}
```

否则，它将弹出“个人资料创建”视图控制器。

```
func popUpHome(){
let viewController = HomeViewController()
viewController.profile = contact.profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

这是控制器中的 `popUpHome` 函数。当调用此函数时，它将：

*   创建一个自定义 `HomeViewController` 类的新实例，并将引用存储在常量 `viewController` 中
*   将 `profile` 对象传递给 `HomeViewController` 对象
*   将 `HomeViewController` 的呈现样式设置为在当前视图控制器之上呈现
*   设置动画以创建平滑的溶解效果
*   呈现新的视图控制器

```
func popUpProfile(){
let viewController = ProfileViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

这是控制器中的 `popUpProfile` 函数。当调用此函数时，它将：

*   创建一个自定义 `ProfileViewController` 类的新实例，并将引用存储在常量 `viewController` 中
*   将 `ProfileViewController` 的呈现样式设置为在当前视图控制器之上呈现
*   设置动画以创建平滑的溶解效果
*   呈现新的视图控制器

以下是控制器视图控制器的完整代码：

```
class MainViewController: UIViewController{
var userid:String = ""
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
popUpHome()
}else{
popUpProfile()
}
}
}
}
}
func popUpHome(){
let viewController = HomeViewController()
viewController.profile = contact.profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpProfile(){
let viewController = ProfileViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
}
```

