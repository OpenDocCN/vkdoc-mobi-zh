# 8. 动画视图

动画是 iOS 编程的基础部分，有助于改善用户体验，让用户在应用内的不同视图之间切换。根据动画的类型，它还能通过向用户传达其操作信息，为用户提供清晰的指引。

在下面的示例中，我们将使用系统定义的函数 `animate` 来演示动画。我们将绘制一个带有按钮的视图控制器。当用户点击该按钮时，将打开另一个视图，该视图在屏幕上显示时会带有动画效果。

## 定义主视图控制器

```swift
class firstViewController: UIViewController {
```

上述代码定义了一个名为 `firstViewController` 的 `UIViewController`。

## 定义 UI 对象

```swift
private let animatedView: UIView = {
    let view = UIView()
    view.translatesAutoresizingMaskIntoConstraints = false
    view.backgroundColor = .systemGray6
    view.alpha = 0.0
    view.center = view.center
    return view
}()
```

上述代码定义了一个自定义视图，该视图将被动画化以演示动画功能。我们将视图的背景设置为灰色，并将其放置在当前视图控制器的中心位置。

```swift
let viewButton:UIButton = {
    let uiButton  = UIButton()
    uiButton.translatesAutoresizingMaskIntoConstraints = false
    uiButton.setImage(UIImage(systemName: "doc.viewfinder.fill"), for: .normal)
    return uiButton
}()
```

接下来，我们定义按钮。点击按钮后，我们将以动画方式打开自定义视图。该按钮使用系统定义的图像 `doc.viewfinder.fill` 来显示。

## 定义生命周期方法

```swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(true)
    setup()
    drawAnimation()
}
```

上面定义的方法 `viewWillAppear` 是一个系统函数，被归类为 `UIViewController` 的生命周期方法。与每个应用会话仅调用一次的 `viewDidLoad` 不同，`viewWillAppear` 在每次视图控制器被调用时都会执行。

我们在此方法中调用了两个自定义方法。第一个方法是 `setup()`，用于在视图控制器显示之前初始化一些元素；第二个是 `drawAnimation()` 函数，用于在屏幕上绘制按钮。

```swift
setup(){
    viewButton.addTarget(self, action: #selector(animateView), for: .touchUpInside)
}
```

在上述方法中，我们为按钮 `viewButton` 添加了一个名为 `animateView` 的自定义目标函数，该函数将在用户点击按钮（即 `touchUpInside` 事件）时被调用。

```swift
func drawAnimation(){
    let screensize: CGRect = UIScreen.main.bounds
    let screenWidth = screensize.width
    let screenHeight = screensize.height
    view.addSubview(viewButton)
    let constraints = [
        viewButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 25),
        viewButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
        viewButton.widthAnchor.constraint(equalToConstant: 80),
        viewButton.heightAnchor.constraint(equalToConstant: 80)
    ]
    NSLayoutConstraint.activate(constraints)
}
```

上述代码在屏幕上绘制了按钮。按钮的目标操作已定义完毕。用户按下按钮时，动画视图将按照预设的动画效果打开。



### 动画视图

```
@objc func animateView(sender: UIButton) {
    let screensize: CGRect = UIScreen.main.bounds
    let screenWidth = screensize.width
    let screenHeight = screensize.height
    view.addSubview(animatedView)
    let constraints = [
        animatedView.topAnchor.constraint(equalTo: view.topAnchor, constant: 20),
        animatedView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20),
        animatedView.widthAnchor.constraint(equalToConstant: screenWidth - 20),
        animatedView.heightAnchor.constraint(equalToConstant: screenHeight - 20)
    ]
    NSLayoutConstraint.activate(constraints)
    UIView.animate(withDuration: 1.0, delay: 0.0, options: .curveEaseOut, animations: { [self] in
        animatedView.alpha = 1.0
    }, completion: nil)
}
```

当用户点击视图按钮时，会调用上述代码。该代码将执行以下操作：

- 在`UIViewController`上添加自定义的`UIView` `animatedView`。我们将`animatedView`的大小设置为与视图控制器大致相同，并从各边预留 20 像素的间距。
- 紧接着，我们调用系统在`UIView`上定义的动画方法。我们应用的效果是曲线缓出，持续时间为 1 秒且无延迟，在完成块中，我们将透明度设置为 1.0。这将使`animatedView`以缩放效果呈现。要了解更多关于`UIView`动画方法的信息，请参考下一节。

#### UIView.animate 函数详解

`UIView.animate`有多个属性，可用于控制并设置你期望的特定动画行为。关键属性如下所列。

##### Duration (TimeInterval)

一个必需参数，指定动画持续的总时间（以秒为单位）。它是一个`TimeInterval`值，即一个表示时长的双精度浮点数。

##### Animations

`@escaping () -> Void`：这也是一个必需的闭包，包含实际的动画代码。在此闭包内部，你可以修改要动画化的视图的属性。常见的可更改属性包括`frame`（位置和大小）、`alpha`（透明度）、`center`、`transform`（用于旋转和缩放）以及`background color`。

##### Delay (TimeInterval)

这个可选参数指定动画开始前的延迟时间（以秒为单位）。它是一个`TimeInterval`值，即一个表示时长的双精度浮点数。

##### Options

`UIView.AnimationOptions`：这个可选参数允许你配置动画的各个方面。它是一组来自`UIView.AnimationOptions`枚举的标志。以下是一些常用选项：

- `.curveEaseInOut`：为动画定义缓入缓出的曲线（平滑开始和结束）
- `.curveEaseIn`：定义缓入曲线（开始慢，然后加速）
- `.curveEaseOut`：定义缓出曲线（开始快，然后减速）
- `.repeat`：使动画重复一定次数（使用`repeatCount`指定次数）
- `.autoreverse`：动画完成后反向播放（从头到尾反向运行）
- `.beginFromCurrentState`：从视图的当前状态开始动画，而不是从故事板中的初始状态开始

##### Completion: ((Bool) -> Void)?

这个可选闭包会在动画完成时被调用。它接收一个`Bool`参数，指示动画是否成功完成或被中断。你可以使用此闭包执行任何所需的动画后操作。

### 完整代码

```
class firstViewController: UIViewController {
    private let animatedView: UIView = {
        let view = UIView()
        view.translatesAutoresizingMaskIntoConstraints = false
        view.backgroundColor = .systemGray6
        view.alpha = 0.0
        view.center = view.center
        return view
    }()
    let viewButton: UIButton = {
        let uiButton = UIButton()
        uiButton.translatesAutoresizingMaskIntoConstraints = false
        uiButton.setImage(UIImage(systemName: "doc.viewfinder.fill"), for: .normal)
        return uiButton
    }()
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(true)
        setup()
        drawAnimation()
    }
    setup() {
        viewButton.addTarget(self, action: #selector(animateView), for: .touchUpInside)
    }
    func drawAnimation() {
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        let screenHeight = screensize.height
        view.addSubview(viewButton)
        let constraints = [
            viewButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 25),
            viewButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
            viewButton.widthAnchor.constraint(equalToConstant: 80),
            viewButton.heightAnchor.constraint(equalToConstant: 80)
        ]
        NSLayoutConstraint.activate(constraints)
    }
    @objc func animateView(sender: UIButton) {
        let screensize: CGRect = UIScreen.main.bounds
        let screenWidth = screensize.width
        let screenHeight = screensize.height
        view.addSubview(animatedView)
        let constraints = [
            animatedView.topAnchor.constraint(equalTo: view.topAnchor, constant: 20),
            animatedView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20),
            animatedView.widthAnchor.constraint(equalToConstant: screenWidth - 20),
            animatedView.heightAnchor.constraint(equalToConstant: screenHeight - 20)
        ]
        NSLayoutConstraint.activate(constraints)
        UIView.animate(withDuration: 1.0, delay: 0.0, options: .curveEaseOut, animations: { [self] in
            animatedView.alpha = 1.0
        }, completion: nil)
    }
}
```

