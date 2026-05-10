# 14. 用户间对话——表格单元格定义

## 表格单元格的视图类

在定义表格视图的视图类中，我们使用了三个不同的自定义`UITableViewCell`类。`UITableViewCell`类有助于定制表格行，以便以更友好的方式显示数据。它提供了一个结构化的容器，用于在列表式格式中显示数据，这在许多 iOS 应用中很常见，并可用于自定义`UITableViewCell`的外观和布局，以匹配我们应用的设计。

### ContactTableViewCell

以下代码定义了一个名为`ContactTableViewCell`的自定义`UITableViewCell`子类。该子类专门用于在表格视图中显示联系人相关信息。

- **导入 UIKit：** 导入 UIKit 框架，该框架提供了构建用户界面所需的类和协议。
- **自定义 UITableViewCell 类：** 定义了一个名为`ContactTableViewCell`的新类，继承自`UITableViewCell`。这使我们能够创建具有特定布局和内容的自定义表格视图单元格。

```swift
import UIKit
class ContactTableViewCell: UITableViewCell {
```

#### UI 对象详情

以下代码定义了`ContactTableViewCell`类的三个属性：

**profileImageView：**

- 此属性创建一个新的`UIImageView`实例。
- 它使用`UIImage(systemName: "person.fill")`设置默认图像，显示一个人形图标。
- 它配置图像视图并设置以下属性：
  - 内容模式：`.scaleAspectFit`，使图像适配视图边界。
  - 自动布局约束：`translatesAutoresizingMaskIntoConstraints`设置为`false`，以允许程序化布局。
  - 圆角：`layer.masksToBounds`设置为`true`，以实现圆角效果。
  - 边框：`layer.borderWidth`和`layer.borderColor`定义了一个像素宽的系统灰色边框。
- 闭包语法（`{ ... }`）使用这些设置初始化图像视图并返回，将其赋值给`profileImageView`属性。

**nameButton：**

- 此属性创建一个新的`UIButton`实例。
- 它通过`translatesAutoresizingMaskIntoConstraints = false`禁用自动约束。
- 它设置默认按钮属性：
  - 文字颜色：`UIColor.black`，黑色文字。
  - 文字对齐：`.left`用于左对齐文字，`.top`用于顶部对齐文字。
  - 字体：`UIFont.systemFont(ofSize: 20, weight: .semibold)`将字体设置为系统字体，大小 20，粗细为 semibold。

**chatButton：**

- 此属性创建一个新的`UIButton`实例。
- 它通过`translatesAutoresizingMaskIntoConstraints = false`禁用自动约束。
- 它使用`UIImage(systemName: "message.fill")`设置默认图像，显示一个消息图标。

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "message.fill"), for: .normal)
return uiButton
}()
```

## 生命周期方法

此代码定义了`ContactTableViewCell`的布局和配置。它使用自动布局约束将`profileImageView`、`nameButton`和`chatButton`放置在单元格的内容视图中。以下是代码的关键元素：

**awakeFromNib()**

- 当单元格从 nib 文件（如果使用）加载或以编程方式创建时，会调用此方法。
- 在本例中，没有特定的初始化代码，因此该方法仅调用`super.awakeFromNib()`。

**setSelected(_:animated:)**

- 当单元格被选中或取消选中时，会调用此方法。以下是定义单元格内容实际布局的位置：
  - 将`profileImageView`、`nameButton`和`chatButton`添加为`contentView`的子视图。
  - 为每个子视图设置自动布局约束，以在单元格中定位它们：
    - `profileImageView` 放置在左上角，具有固定大小。
    - `nameButton` 放置在`profileImageView`的右侧，具有固定高度。
    - `chatButton` 放置在`nameButton`下方，`profileImageView`的右侧。

```swift
override func awakeFromNib() {
super.awakeFromNib()
// 初始化代码
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(chatButton)
let constraints5 = [
chatButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
chatButton.widthAnchor.constraint(equalToConstant: 80),
chatButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
}
```



### 完整代码

"联系人"TableViewCell 的完整代码如下所示：

```
//
//  ContactTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/7/24.
//
import UIKit
class ContactTableViewCell: UITableViewCell {
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "message.fill"), for: .normal)
return uiButton
}()
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(chatButton)
let constraints5 = [
chatButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
chatButton.widthAnchor.constraint(equalToConstant: 80),
chatButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
}
```

### ChatDetailsTableViewCell

`ChatDetailsTableViewCell` 类是 `UITableViewCell` 的一个自定义子类，用于在对话中显示单条聊天消息。以下是代码的详细说明：

- **导入 UIKit：** 包含 UI 元素和功能所需的 UIKit 框架。
- **类声明：** 定义了一个名为 `ChatDetailsTableViewCell` 的新类，继承自 `UITableViewCell`。

**类属性：**

- **buttonPosition：** 一个 `CGFloat` 属性，用于存储聊天气泡按钮的 x 轴位置。
- **buttonWidth：** 一个 `CGFloat` 属性，用于存储聊天气泡按钮的宽度。
- **rightPosition：** 一个 `CGFloat` 属性，用于调整聊天气泡的右侧位置。

```
import UIKit
class ChatDetailsTableViewCell: UITableViewCell {
var buttonPosition: CGFloat = 40
var buttonWidth:CGFloat = 120
var rightPosition:CGFloat = 5
```

#### UI 对象定义

下面的代码片段为 `ChatDetailsTableViewCell` 类定义了两个额外的属性：

**profileImageView：**

该属性与 `ContactTableViewCell` 中定义的属性相同。它创建了一个带有默认图片、圆角和边框的 `UIImageView`。

**chatButton：**

- 该属性创建了一个新的 `UIButton` 实例，用于显示聊天消息：
  - 禁用自动约束（`translatesAutoresizingMaskIntoConstraints = false`）以实现程序化布局。
  - 设置按钮外观：
    - 文本颜色：`UIColor.black`。
    - 对齐方式：`.left` 和 `.top`，用于左对齐和顶部对齐的文本。
    - 字体：`UIFont.systemFont(ofSize: 14, weight: .regular)` 将字体大小设为 14，粗细为常规。
    - 圆角：`layer.cornerRadius = 8` 将圆角半径设为 8 点。
    - 边框：`layer.borderWidth = 1` 和 `layer.borderColor = UIColor.black.cgColor` 定义了一个黑色、1 像素宽的边框。
    - 背景颜色：`backgroundColor = UIColor.white` 为按钮设置白色背景。
    - 多行文本：`titleLabel?.numberOfLines = 10` 允许按钮标题最多显示 10 行文本，以容纳较长的消息。

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 14, weight: .regular)
uiButton.layer.cornerRadius = 8
uiButton.layer.borderWidth = 1
uiButton.layer.borderColor = UIColor.black.cgColor
uiButton.backgroundColor = UIColor.white
uiButton.titleLabel?.numberOfLines = 10
return uiButton
}()
```



## 生命周期方法

以下代码通过定义单元格的布局和配置，完成了 `ChatDetailsTableViewCell` 类的实现。代码分解如下：

**`awakeFromNib()`**

*   当单元格从 nib 文件加载时调用此方法（如果使用）。
*   在此例中，没有特定的初始化代码，因此该方法仅调用 `super.awakeFromNib()`。

**`init(style:reuseIdentifier:)`**

*   这是单元格的指定初始化器。当单元格以编程方式创建时调用。
*   在此例中，它仅调用 `super.init(style: style, reuseIdentifier: reuseIdentifier)` 来初始化基类。

**`required init?(coder aDecoder: NSCoder)`**

*   这是从故事板或 nib 文件解码单元格所需的初始化器。
*   由于本例中未使用故事板或 nib，因此仅调用 `fatalError` 以指示此初始化器未实现。

**`layoutSubviews()`**

*   此方法在设置单元格的边界后调用，允许进行精确的布局计算。
*   将 `profileImageView` 和 `chatButton` 作为子视图添加到 `contentView`。
*   使用 `NSLayoutConstraint.activate` 为两个视图设置自动布局约束，以将它们定位在单元格内：
    *   `profileImageView` 定位在左上角，并具有指定的尺寸。
    *   `chatButton` 定位在 `profileImageView` 的右侧，其宽度根据 `buttonWidth` 属性动态确定。

```
override func awakeFromNib() {
    super.awakeFromNib()
    // Initialization code
}

override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
    super.init(style: style, reuseIdentifier: reuseIdentifier)
}

required init?(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
}

override func layoutSubviews() {
    super.layoutSubviews()
    let screensize: CGRect = UIScreen.main.bounds
    let screenWidth = screensize.width
    contentView.addSubview(profileImageView)
    let constraints = [
        profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
        profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: rightPosition),
        profileImageView.widthAnchor.constraint(equalToConstant: 30),
        profileImageView.heightAnchor.constraint(equalToConstant: 30)
    ]
    NSLayoutConstraint.activate(constraints)
    contentView.addSubview(chatButton)
    let constraints1 = [
        chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
        chatButton.widthAnchor.constraint(equalToConstant: screenWidth - buttonWidth),
        chatButton.heightAnchor.constraint(equalToConstant:buttonPosition)
    ]
    NSLayoutConstraint.activate(constraints1)
}
```

### 完整代码

`ChatDetailsTableViewCell` 的完整代码如下：

```
//
//  ChatDetailsTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/24/24.
//
import UIKit

class ChatDetailsTableViewCell: UITableViewCell {
    var buttonPosition: CGFloat = 40
    var buttonWidth: CGFloat = 120
    var rightPosition: CGFloat = 5
    
    var profileImageView: UIImageView = {
        let imageView = UIImageView()
        imageView.image = UIImage(systemName: "person.fill")
        imageView.contentMode = .scaleAspectFit
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.layer.masksToBounds = true
        imageView.layer.borderWidth = 1
        imageView.layer.borderColor = UIColor.systemGray.cgColor
        return imageView
    }()
    
    let chatButton: UIButton = {
        let uiButton = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setTitleColor(UIColor.black, for: .normal)
        uiButton.contentHorizontalAlignment = .left
        uiButton.contentVerticalAlignment = .top
        uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 14, weight: .regular)
        uiButton.layer.cornerRadius = 8
        uiButton.layer.borderWidth = 1
        uiButton.layer.borderColor = UIColor.black.cgColor
        uiButton.backgroundColor = UIColor.white
        uiButton.titleLabel?.numberOfLines = 10
        return uiButton
    }()
    
    override func awakeFromNib() {
        super.awakeFromNib()
        // Initialization code
    }
    
    override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        contentView.addSubview(profileImageView)
        let constraints = [
            profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
            profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: rightPosition),
            profileImageView.widthAnchor.constraint(equalToConstant: 30),
            profileImageView.heightAnchor.constraint(equalToConstant: 30)
        ]
        NSLayoutConstraint.activate(constraints)
        contentView.addSubview(chatButton)
        let constraints1 = [
            chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
            chatButton.widthAnchor.constraint(equalToConstant: screenWidth - buttonWidth),
            chatButton.heightAnchor.constraint(equalToConstant: buttonPosition)
        ]
        NSLayoutConstraint.activate(constraints1)
    }
}
```

### ChatTableViewCell

此代码定义了一个名为 `ChatTableViewCell` 的自定义 `UITableViewCell` 子类。该类将用于在表格视图中显示单个聊天消息。代码详情如下：

*   `import UIKit`：导入 UIKit 框架，该框架提供了构建用户界面所需的类和协议。
*   `class ChatTableViewCell: UITableViewCell {`：声明一个继承自 `UITableViewCell` 的新类 `ChatTableViewCell`。这允许自定义单元格的外观和行为。

```
import UIKit

class ChatTableViewCell: UITableViewCell {
```

#### UI 对象定义

下面的代码定义了表示 `ChatTableViewCell` UI 元素的三个属性：

*   `profileImageView`：创建一个带有默认人物图标的圆形图像视图，用于显示发送者的个人资料图片。
*   `nameButton`：创建一个带有黑色文本和较大半粗体字体的按钮，用于显示发送者的姓名。
*   `titleButton`：创建一个带有黑色文本、较小常规字体、最多允许三行文本的按钮，用于显示消息预览或标题。

将这些元素添加到单元格中：

```
var profileImageView: UIImageView = {
    let imageView = UIImageView()
    imageView.image = UIImage(systemName: "person.fill")
    imageView.contentMode = .scaleAspectFit
    imageView.translatesAutoresizingMaskIntoConstraints = false
    imageView.layer.masksToBounds = true
    imageView.layer.borderWidth = 1
    imageView.layer.borderColor = UIColor.systemGray.cgColor
    return imageView
}()

let nameButton: UIButton = {
    let uiButton = UIButton()
    uiButton.translatesAutoresizingMaskIntoConstraints = false
    uiButton.setTitleColor(UIColor.black, for: .normal)
    uiButton.contentHorizontalAlignment = .left
    uiButton.contentVerticalAlignment = .top
    uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
    return uiButton
}()

let titleButton: UIButton = {
    let uiButton = UIButton()
    uiButton.translatesAutoresizingMaskIntoConstraints = false
    uiButton.setTitleColor(UIColor.black, for: .normal)
    uiButton.contentHorizontalAlignment = .left
    uiButton.contentVerticalAlignment = .top
    uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 15, weight: .regular)
    uiButton.titleLabel?.numberOfLines = 3
    uiButton.setTitleColor(.black, for: .normal)
    return uiButton
}()
```



## 生命周期方法

该代码定义了 `ChatDetailsTableViewCell` 的布局，将 `profileImageView`、`nameButton` 和 `titleButton` 定位在单元格的内容视图中。代码分解如下：

**`awakeFromNib()`**

- 当从 nib 文件（若使用）加载单元格时，会调用此方法。
- 在本例中，没有特定的初始化代码，因此它仅调用 `super.awakeFromNib()`。

**`setSelected(_:animated:)`**

- 当单元格被选中或取消选中时，会调用此方法。
- 布局单元格内容的核心逻辑在此处执行：
    - 将 `profileImageView`、`nameButton` 和 `titleButton` 作为子视图添加到 `contentView`。
    - 使用 Auto Layout 约束来定位这些元素：
        - `profileImageView` 位于左上角，具有固定尺寸。
        - `nameButton` 位于 `profileImageView` 的右侧，具有固定高度。
        - `titleButton` 位于 `nameButton` 下方，`profileImageView` 的右侧。

```
override func awakeFromNib() {
    super.awakeFromNib()
    // 初始化代码
}

override func setSelected(_ selected: Bool, animated: Bool) {
    super.setSelected(selected, animated: animated)

    let screensize: CGRect = UIScreen.main.bounds
    let screenWidth = screensize.width

    contentView.addSubview(profileImageView)
    let constraints = [
        profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
        profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
        profileImageView.widthAnchor.constraint(equalToConstant: 60),
        profileImageView.heightAnchor.constraint(equalToConstant: 60)
    ]
    NSLayoutConstraint.activate(constraints)

    contentView.addSubview(nameButton)
    let constraints1 = [
        nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
        nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
        nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
        nameButton.heightAnchor.constraint(equalToConstant: 20)
    ]
    NSLayoutConstraint.activate(constraints1)

    contentView.addSubview(titleButton)
    let constraints2 = [
        titleButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
        titleButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
        titleButton.widthAnchor.constraint(equalToConstant: screenWidth - 100),
        titleButton.heightAnchor.constraint(equalToConstant: 80)
    ]
    NSLayoutConstraint.activate(constraints2)
}
```

### 完整代码

`ChatTableViewCell` 的完整代码如下：

```
//
//  ChatTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//

import UIKit

class ChatTableViewCell: UITableViewCell {

    var profileImageView: UIImageView = {
        let imageView = UIImageView()
        imageView.image = UIImage(systemName: "person.fill")
        imageView.contentMode = .scaleAspectFit
        imageView.translatesAutoresizingMaskIntoConstraints = false
        imageView.layer.masksToBounds = true
        imageView.layer.borderWidth = 1
        imageView.layer.borderColor = UIColor.systemGray.cgColor
        return imageView
    }()

    let nameButton: UIButton = {
        let uiButton = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setTitleColor(UIColor.black, for: .normal)
        uiButton.contentHorizontalAlignment = .left
        uiButton.contentVerticalAlignment = .top
        uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
        return uiButton
    }()

    let titleButton: UIButton = {
        let uiButton = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setTitleColor(UIColor.black, for: .normal)
        uiButton.contentHorizontalAlignment = .left
        uiButton.contentVerticalAlignment = .top
        uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 15, weight: .regular)
        uiButton.titleLabel?.numberOfLines = 3
        uiButton.setTitleColor(.black, for: .normal)
        return uiButton
    }()

    override func awakeFromNib() {
        super.awakeFromNib()
        // 初始化代码
    }

    override func setSelected(_ selected: Bool, animated: Bool) {
        super.setSelected(selected, animated: animated)

        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width

        contentView.addSubview(profileImageView)
        let constraints = [
            profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
            profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
            profileImageView.widthAnchor.constraint(equalToConstant: 60),
            profileImageView.heightAnchor.constraint(equalToConstant: 60)
        ]
        NSLayoutConstraint.activate(constraints)

        contentView.addSubview(nameButton)
        let constraints1 = [
            nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
            nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
            nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
            nameButton.heightAnchor.constraint(equalToConstant: 20)
        ]
        NSLayoutConstraint.activate(constraints1)

        contentView.addSubview(titleButton)
        let constraints2 = [
            titleButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
            titleButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
            titleButton.widthAnchor.constraint(equalToConstant: screenWidth - 100),
            titleButton.heightAnchor.constraint(equalToConstant: 80)
        ]
        NSLayoutConstraint.activate(constraints2)
    }
}
```

