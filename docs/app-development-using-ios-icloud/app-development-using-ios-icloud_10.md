# 5. 图片管理

Xcode 项目通常包含图片，以便为最终用户创造出色的视觉体验。我们在本书的第一个版本中已经了解了基本的图片创建和管理。

在本章中，我们将学习一些新的额外概念：

*   定义 `UIImageView` 来显示图片
*   为图片视图添加手势，以便用户在触摸图片时执行某些操作
*   使用用户定义的约束显示图片视图
*   将图片选择器定义为一个扩展，并添加函数以显示一个操作表，该操作表提供从相册选择照片或激活相机的选项
*   理解允许用户访问相机和相册所需的所有配置更改
*   操控图片属性以赋予其优雅的外观
*   将图片作为 `CKAsset` 管理，以保存到我们的 iCloud 数据库
*   从 iCloud 存储库中检索图片
*   在用户个人资料屏幕上显示图片
*   使用我们在前一章学到的 MVC 设计模式实现这些功能

在我们的示例中，我们将有三个视图控制器，如下所述：

*   `ProfileCreationViewController`：此 `UIViewController` 将帮助从相册上传图片或直接从相机（视图）捕获图片。
*   `Profile`：此 `NSObject` 类代表我们 iCloud 数据库中的个人资料表。
*   `ProfileDisplayViewController`：此 `UIViewController` 将在 iOS 屏幕上显示我们存储的图片。



## 上传图片的视图控制器

在这个视图控制器中，屏幕上会绘制一个图片视图对象。当用户点击该图片时，将弹出一个操作列表，提供从照片库选择图片或使用相机拍摄图片的选项。一旦图片被获取，视图控制器将调用名为 `Profile` 的模态类的 `save` 方法，将图片及其属性保存到存储库中。

```
import UIKit
import CloudKit
class ProfileCreationViewController: UIViewController{
```

我们导入了两个 Xcode 工具包：`UIKit` 用于包含所有生命周期和 UI 对象，`Cloudkit` 用于所有 iCloud 持久化和数据存储。接下来，我们定义 UI 视图控制器 `ProfileCreationViewController` 来创建我们的个人资料图片选择器。

```
let personalProfileImageView: UIImageView = {
let theImageView = UIImageView()
theImageView.image = UIImage(systemName: "person.fill")
theImageView.contentMode = .scaleAspectFit
theImageView.translatesAutoresizingMaskIntoConstraints = false
theImageView.layer.masksToBounds = true
theImageView.layer.borderWidth = 1
theImageView.layer.borderColor = UIColor.systemGray.cgColor
return theImageView
}()
```

我们的视图控制器将包含一个 `UIImageView` 对象。该对象允许交互，点击图片视图将向用户提供一个操作列表，供其从用户照片库中选择图片或从相机拍摄照片。请注意，要实现此功能，我们需要设置应用程序以提示用户弹出权限对话框，以访问照片库和相机。请参考本章末尾的部分，了解如何配置这些属性。

同时请注意，我们的图片视图有一个图片对象，默认显示一个带有灰色背景的系统定义人物图标。

```
let saveButton:UIButton = {
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.contentHorizontalAlignment = .center
button.setTitleColor(.white, for: .normal)
button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
button.titleLabel?.textAlignment = .center
button.setTitle("保存并退出", for: .normal)
button.layer.cornerRadius = 5
button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
button.isEnabled = true
return button
}()
```

除了图片视图，我们还定义了一个保存按钮，点击该按钮将允许用户将图片保存到 iCloud。我们给按钮设置了紫色背景。

```
override func viewDidLoad() {
super.viewDidLoad()
let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
personalProfileImageView.isUserInteractionEnabled = true
personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
personalProfileImageView.layer.cornerRadius =  50
drawPersonalScreen()
}
```

现在我们已经定义了这些对象，接下来，我们将定义生命周期方法 `viewDidLoad()`。当用户实例化 `ProfileCreationViewController` 时，会立即调用此方法。在此函数中，我们执行以下描述的几个操作：

- 图片视图控制器没有像 UI 按钮或 UI 文本框那样的预定义事件，因此我们需要手动定义它。`UITapGestureRecognizer` 是我们定义的一个事件，用于实现图片上的点击事件。我们定义了点击时调用的方法 `personalImageTapped`，该方法将点击手势作为默认输入参数。
- 然后，我们确保之前定义的 `UIImageView` 对象（`personalProfileImageView`）的用户交互属性设置为 `true`。这将允许手势捕获事件。
- 最后，我们将用户定义的手势事件附加到 UI 图片对象上。
- 我们还将图片视图的圆角半径设置为 `50`。这将使图片视图显示为圆形。
- 最后，我们调用一个名为 `drawPersonalScreen` 的方法，该方法将在屏幕上绘制图片视图和按钮。

```
func drawPersonalScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
```

这是我们的 `drawPersonalScreen` 方法。我们获取 iPhone 屏幕宽度，这将用于计算 UI 对象在屏幕上的位置。由于 iPhone 有多种外形尺寸，建议动态计算布局以获得更好的对象显示效果。尽管在下面的代码中我们不会使用它，但这是一个很好的功能，当你开始创建复杂屏幕时可以学习。

```
view.addSubview(personalProfileImageView)
let constraints2 = [
personalProfileImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 5),
personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints2)
```

这部分代码将 `UIImageView` 添加到屏幕上。由于图片需要为圆形，高度和宽度必须相同。我们还在顶部留出 `5` 个像素的空间，左侧留出 `140` 个像素的空间。

```
view.addSubview(saveButton)
let constraints = [
saveButton.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 40),
saveButton.leftAnchor.constraint(equalTo: view.rightAnchor, constant: 5),
saveButton.widthAnchor.constraint(equalToConstant: 185),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
}
```

现在，我们将保存按钮添加到屏幕上。请注意，按钮的顶部锚点参照了 `UIImageView`，并在图片下方 `40` 个像素处绘制。

同时，我们还在按钮的触摸事件中添加了 `saveAction` 方法。因此，当用户点击保存按钮时，将调用此方法将图片保存到 iCloud 数据库。

```
@objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
presentPhotoOptions()
}
```

当用户点击图片视图时，会调用此方法。该方法进而调用我们的自定义方法 `presentPhotoOptions`。

```
@objc func saveAction(sender: UIButton){
profileSave()
}
```

当用户点击保存按钮时，会调用此方法，该方法进而调用下一节中定义的 `profileSave` 函数。

```
func profileSave(){
```

这是 `profileSave` 函数，它负责将图片和其他一些个人资料数据（为简化代码，未在屏幕中捕获）保存到 iCloud 存储库。

```
let personalData = personalProfileImageView.image?.pngData()
let personalFilename = commonFunctions.getDocumentsDirectory().appendingPathComponent("copy.png")
try? personalData?.write(to: personalFilename)
let personalProfile : CKAsset?  = CKAsset(fileURL: personalFilename)
```

iCloud 中的数据以 `CKAsset` 类型对象存储。该对象将图片存储为二进制对象。在上述代码中，我们执行以下操作：

- 从 UI 图片对象中，通过使用系统定义的方法 `pngData()` 获取 `png` 格式的数据，并将其存储在本地常量 `personalData` 中。
- 接下来，我们在用户的当前工作空间中创建一个临时对象 `copy.png`。文件路径存储在本地常量 `personalFilename` 中。
- 然后，我们用用户上传的个人资料图片的 `png` 数据覆盖 `copy.png` 文件。
- 最后，我们创建一个 `CKAsset` 对象，提供本地目录路径和文件名。

现在，这个 `CKAsset` 可以存储到 iCloud 存储库中。

```
let profile:Contact.Profile = Contact.Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, personalphone: personalPhone)
```



现在我们正在用所有必需的参数（`ID`、`image`、`name`、`phone`）来实例化我们自定义对象 `Profile` 的一个实例。这是一个模态类，我们将在下一节中学习。`Profile` 类将包含用于定义、存储和从 iCloud 数据库中检索 profile 对象的方法和属性。

```
self.contact.group.enter()
self.contact.saveProfile(profile: profile)
self.contact.group.notify(queue: .main) {
}
}
}
```

任何 iCloud 操作都是作为异步方法运行的。`group` 帮助我们将这些方法聚合在一起，并在主线程上等待其完成。我们正在调用模态类的 `saveProfile` 方法，该方法将包含将对象保存到 iCloud 存储库的例程。请注意，`saveProfile` 将 `Profile` 对象作为一个`IN`参数。

```
extension ProfileCreationViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
```

对于所有图像处理功能，我们将为视图控制器类定义一个扩展。我们也可以不使用扩展来实现，但扩展提高了模块化程度，并且可以帮助您在需要上传图像的其他区域中包含此功能。以下是一些关键的说明：

*   我们正在扩展 `ProfileCreationViewController` 类。
*   该类扩展了两个委托。第一个是 `UIImagePickerControllerDelegate`，用于调用所有内置的图像选择和显示功能；第二个是 `UINavigationControllerDelegate`，用于操作表导航。

```
func presentPhotoOptions(){
```

如果您还记得上一节的内容，当点击图像并触发点击手势时，会调用此方法，进而调用该方法。

```
var message:String = "Personal Profile Picture"
```

这是操作表上的一条显示消息。我们将其标记为“个人头像图片”。

```
let actionSheet = UIAlertController(title: message, message: "Select your Option", preferredStyle: .actionSheet)
```

接下来，我们定义操作表。

```
actionSheet.addAction(UIAlertAction(title: "cancel", style: .cancel, handler: nil))
```

操作表中的第一个操作是取消。选择此选项将关闭操作表，不执行任何功能。

```
actionSheet.addAction(UIAlertAction(title: "Take Photo", style: .default, handler: { [weak self]_ in
self?.presentCamera()
}))
```

第二个操作允许用户调出相机来拍照。请注意，当选择此选项时，我们调用了一个自定义函数 `presentCamera()`。

```
actionSheet.addAction(UIAlertAction(title: "Choose Photo", style: .default, handler: {[weak self]_ in
self?.presentPhoto()
}))
```

最后一个操作允许用户从相册中选择照片。请注意，当选择此选项时，我们调用了一个自定义函数 `presentPhoto()`。

```
present(actionSheet, animated: true)
}
```

`present` 方法将向用户呈现操作表。操作表默认显示在屏幕底部。

```
func presentCamera(){
let ip = UIImagePickerController()
ip.sourceType = .camera
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
```

`presentCamera` 方法将向用户呈现一个相机，并提供一个 `UIImagePicker` 来捕获图像。

```
func presentPhoto(){
let ip = UIImagePickerController()
ip.sourceType = .photoLibrary
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
```

`presentPhoto` 方法将向用户呈现一个相机，并提供一个 `UIImagePicker` 来捕获图像。

```
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
let selectedImage  = info[UIImagePickerController.InfoKey.editedImage]
self.personalProfileImageView.image = selectedImage as? UIImage
picker.dismiss(animated: true)
}
```

`imagePickerController` 函数是一个类型为 `UIImagePicker` 的系统定义方法。选中的图像存储在常量 `selectedImage` 中，然后赋值给 `UIImageView`。这将在屏幕上显示选中的个人资料图片。

```
func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
picker.dismiss(animated: true)
}
}
}
```

当用户选择取消选项时，会调用此函数，这将使选择器消失。

用于上传图像的视图控制器的完整代码如下所示：



```swift
import UIKit
import CloudKit
class ProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
    var contact = Contact()
    let personalProfileImageView: UIImageView = {
        let theImageView = UIImageView()
        theImageView.image = UIImage(systemName: "person.fill")
        theImageView.contentMode = .scaleAspectFit
        theImageView.translatesAutoresizingMaskIntoConstraints = false
        theImageView.layer.masksToBounds = true
        theImageView.layer.borderWidth = 1
        theImageView.layer.borderColor = UIColor.systemGray.cgColor
        return theImageView
    }()
    let saveButton:UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.contentHorizontalAlignment = .center
        button.setTitleColor(.white, for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
        button.titleLabel?.textAlignment = .center
        button.setTitle("保存并退出", for: .normal)
        button.layer.cornerRadius = 5
        button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
        button.isEnabled = true
        return button
    }()
    override func viewDidLoad() {
        super.viewDidLoad()
        let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
        personalProfileImageView.isUserInteractionEnabled = true
        personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
        personalProfileImageView.layer.cornerRadius = 50
        drawPersonalScreen()
    }
    func drawPersonalScreen(){
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        view.addSubview(personalProfileImageView)
        let constraints2 = [
            personalProfileImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 5),
            personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
            personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
            personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
        ]
        view.addSubview(saveButton)
        NSLayoutConstraint.activate(constraints2)
        let constraints = [
            saveButton.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 40),
            saveButton.leftAnchor.constraint(equalTo: view.rightAnchor, constant: 5),
            saveButton.widthAnchor.constraint(equalToConstant: 185),
            saveButton.heightAnchor.constraint(equalToConstant: 40)
        ]
        NSLayoutConstraint.activate(constraints)
        saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
    }
    @objc func saveAction(sender: UIButton){
        profileSave()
    }
    // 图片点击事件
    @objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
        presentPhotoOptions()
    }
    func profileSave(){
        let personalData = personalProfileImageView.image?.pngData()
        let personalFilename = commonFunctions.getDocumentsDirectory().appendingPathComponent("copy.png")
        try? personalData?.write(to: personalFilename)
        let personalProfile : CKAsset? = CKAsset(fileURL: personalFilename)
        let profile:Contact.Profile = Contact.Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, personalphone: personalPhone)
        self.contact.group.enter()
        self.contact.saveProfile(profile: profile)
        self.contact.group.notify(queue: .main) {
        }
    }
}
extension ProfileViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
    func presentPhotoOptions(fromwhere: Int){
        var message:String = " 个人资料图片 "
        let actionSheet = UIAlertController(title: message, message: "选择您的选项", preferredStyle: .actionSheet)
        actionSheet.addAction(UIAlertAction(title: "取消", style: .cancel, handler: nil))
        actionSheet.addAction(UIAlertAction(title: "拍照", style: .default, handler: { [weak self]_ in
            self?.presentCamera(fromwhere: fromwhere)
        }))
        actionSheet.addAction(UIAlertAction(title: "选择照片", style: .default, handler: {[weak self]_ in
            self?.presentPhoto(fromWhere: fromwhere)
        }))
        present(actionSheet, animated: true)
    }
    func presentCamera(fromwhere:Int){
        let ip = UIImagePickerController()
        ip.sourceType = .camera
        ip.delegate = self
        ip.allowsEditing = true
        ip.navigationBar.tag = fromwhere
        present(ip, animated: true)
    }
    func presentPhoto(fromWhere:Int){
        let ip = UIImagePickerController()
        ip.sourceType = .photoLibrary
        ip.delegate = self
        ip.allowsEditing = true
        ip.navigationBar.tag = fromWhere
        present(ip, animated: true)
    }
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        let selectedImage = info[UIImagePickerController.InfoKey.editedImage]
        self.personalProfileImageView.image = selectedImage as? UIImage
        picker.dismiss(animated: true)
    }
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        picker.dismiss(animated: true)
    }
}
```



