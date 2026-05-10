# 7. 创建可扩展的文本视图 UI 对象

UI 文本视图默认具有固定大小，只能容纳几行文本。你可以根据应用需求设置高度和宽度来扩展文本视图的尺寸。在本章中，我们将学习如何根据文本长度动态扩展和收缩文本视图。当用户输入的文本长度可变时，此功能非常实用，你可以根据用户提供的输入文本量来合理利用有限的屏幕空间。


## 定义类与类对象

在本节的代码块中，我们将定义用于创建可扩展文本字段功能的类和一些必要的 UI 对象。

```swift
class ChatDetailViewController: UIViewController, UITextViewDelegate {
```

在上述代码中，我们定义了一个 UI 视图控制器类，它继承了以下两个委托：

- `UIViewController`：为类提供所有视图控制器的生命周期方法
- `UITextViewDelegate`：添加 UI 文本视图的内置函数，用于操作文本视图

```swift
let shellTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let shellSaveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
```

上述代码定义了一个 UI 文本视图和一个 UI 按钮。从名称可以看出，这是一个外壳文本视图和外壳按钮，我们仅将其用于显示目的。后续会用通过代码实现展开/收缩的文本视图来替换它们。之所以必须这样做，是因为在保存内容后，我们无法将可展开的文本视图恢复为原始大小。这是 Xcode 中的一个技术显示问题，希望未来的 iOS 版本能解决这个问题。

```swift
let chatTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let saveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
```

上述代码定义了一个 UI 文本视图和一个 UI 按钮，我们将使用代码来展开或收缩它们。请注意，我们为文本视图定义了一些基本属性，例如字体大小、边框宽度、圆角半径（用于使文本视图的边角呈弧形）以及边框颜色。同样，对于按钮，我们设置了按钮标题颜色、按钮内文本的水平和垂直位置以及其字体。

虽然我们定义了一个按钮，但在本章的代码演示中并不会使用它，因为这与本章演示的功能无关。

## 生命周期方法

下面的代码块包含了 `viewDidLoad` 生命周期方法和屏幕绘制的自定义方法。

```swift
override func viewDidLoad() {
super.viewDidLoad()
drawScreen ()
}
```

当视图控制器首次加载时，`viewDidLoad()` 方法会被调用。在这个系统定义的方法中，我们调用了一个自定义方法 `drawScreen()`，该方法将在下一个代码块中描述。

```swift
func drawScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
}
```

在上述代码中，我们在屏幕底部绘制了 `shellTextView` 文本视图，并在 `shellTextView` 旁边绘制了保存按钮。



#### `TextView` 方法

本节包含两种方法。第一种在用户开始编辑文本视图时被调用，第二种则根据文本长度来展开和收缩文本视图。

```
func textViewDidBeginEditing(_ textView: UITextView) {
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
view.addSubview(chatTextView)
NSLayoutConstraint.activate([
chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
chatTextView.heightAnchor.constraint(equalToConstant: 40)
])
chatTextView.becomeFirstResponder()
// 设置一个通知观察者来处理文本变化
NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
view.addSubview(saveButton)
let constraints3 = [
saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
}
```

上述方法是系统定义的方法，当用户开始在文本视图中输入时会被调用。请注意，只有当类扩展了 `UITextViewDelegate` 协议时才会被调用，这一点之前已解释过。以下是该函数的关键要点：

- 我们移除了 `shellTextView` 和 `shellSaveButton`。
- 接下来，我们在 `shellTextView` 和 `shellSaveButton` 所在的确切位置绘制实际的文本视图和按钮，即 `chatTextView` 和 `saveButton`。
- 我们还为按钮定义了触摸内部事件，该事件将调用 `saveChatAction` 方法。

```
@objc private func handleTextChange() {
if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
saveButton.isEnabled = false
}else{
saveButton.isEnabled = true
}
let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
let estimatedSize = chatTextView.sizeThatFits(size)
chatTextView.constraints.forEach { (constraint) in
if constraint.firstAttribute == .height {
constraint.constant = estimatedSize.height
}
}
}
```

上述代码是系统定义的方法，每当用户添加或删除一个字符时都会被调用。这段代码中发生了以下几件事，如下所述：

- 如果用户输入了非空格的字符，保存按钮将被启用。如果用户输入后又通过退格键删除该字符，保存按钮将再次禁用。这样做是为了确保用户不会意外地保存空白字符串。
- 接下来，我们定义了一个尺寸对象，其宽度为屏幕宽度。减去三十像素，这是保存按钮的宽度。本质上，我们将初始尺寸定义为显示的文本视图的原始大小，该大小可以无限增长。
- 然后，我们根据用户提供的文本长度来估算新文本视图的大小。这是通过系统定义的函数 `sizeThatFits` 完成的，该函数适用于任何文本视图。
- 最后，我们通过遍历所有约束并查找高度约束来动态更改 `chatTextView` 的高度约束。一旦找到，我们将 `chatTextView` 的高度设置为 `estimatedSize` 对象的高度。

#### 按钮方法

这是系统定义的触摸内部事件。我们已将 `saveChatAction()` 设置为用户点击按钮时的目标函数。

```
@objc func saveChatAction(sender: UIButton){
}
```

此函数有意留空。你可以根据应用程序的需求来实现它，例如将文本保存到持久化数据库中。

#### `TextView` 展开的完整代码

以下是上面讨论的文本视图展开函数的完整代码。



## 代码类 `ChatDetailViewController`

```
class ChatDetailViewController: UIViewController, UITextViewDelegate {
    let shellTextView: UITextView = {
        let textView = UITextView()
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.isScrollEnabled = false
        textView.font = UIFont.systemFont(ofSize: 17)
        textView.layer.borderWidth = 1
        textView.layer.borderColor = UIColor.gray.cgColor
        textView.layer.cornerRadius = 10
        return textView
    }()
    let shellSaveButton:UIButton = {
        let uiButton  = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setTitleColor(UIColor.black, for: .normal)
        uiButton.contentHorizontalAlignment = .left
        uiButton.contentVerticalAlignment = .top
        uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
        uiButton.isEnabled = false
        return uiButton
    }()
    let chatTextView: UITextView = {
        let textView = UITextView()
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.isScrollEnabled = false
        textView.font = UIFont.systemFont(ofSize: 17)
        textView.layer.borderWidth = 1
        textView.layer.borderColor = UIColor.gray.cgColor
        textView.layer.cornerRadius = 10
        return textView
    }()
    let saveButton:UIButton = {
        let uiButton  = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setTitleColor(UIColor.black, for: .normal)
        uiButton.contentHorizontalAlignment = .left
        uiButton.contentVerticalAlignment = .top
        uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
        uiButton.isEnabled = false
        return uiButton
    }()
    override func viewDidLoad() {
        super.viewDidLoad()
        drawScreen ()
    }
    func drawScreen(){
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        let screenHeight = screensize.height
        shellTextView.delegate = self
        view.addSubview(shellTextView)
        NSLayoutConstraint.activate([
            shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
            shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
            shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
            shellTextView.heightAnchor.constraint(equalToConstant: 40)
        ])
        view.addSubview(shellSaveButton)
        let constraints3 = [
            shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
            shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
            shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter:shellTextView.rightAnchor,  multiplier: 40),
            shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
        ]
        NSLayoutConstraint.activate(constraints3)
        shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
    }
    func textViewDidBeginEditing(_ textView: UITextView) {
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        shellTextView.removeConstraints(shellTextView.constraints)
        shellTextView.removeFromSuperview()
        shellSaveButton.removeConstraints(shellSaveButton.constraints)
        shellSaveButton.removeFromSuperview()
        view.addSubview(chatTextView)
        NSLayoutConstraint.activate([
            chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
            chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
            chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
            chatTextView.heightAnchor.constraint(equalToConstant: 40)
        ])
        chatTextView.becomeFirstResponder()
        // Set up a notification observer to handle text changes
        NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
        view.addSubview(saveButton)
        let constraints3 = [
            saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
            saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
            saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
            saveButton.heightAnchor.constraint(equalToConstant: 40)
        ]
        NSLayoutConstraint.activate(constraints3)
        saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
        saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
    }
    @objc private func handleTextChange() {
        if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
            saveButton.isEnabled = false
        }else{
            saveButton.isEnabled = true
        }
        let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
        let estimatedSize = chatTextView.sizeThatFits(size)
        chatTextView.constraints.forEach { (constraint) in
            if constraint.firstAttribute == .height {
                constraint.constant = estimatedSize.height
            }
        }
    }
    @objc func saveChatAction(sender: UIButton){
    }
}
```

