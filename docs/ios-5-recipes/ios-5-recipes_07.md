# UIActivityIndicatorView

通常，应用程序可能正在执行一个不会立即完成的任务。这可能是从服务器下载文件的过程，或者仅仅是处理大量数据、需要相当长的时间才能完成的任务。作为开发者，你应该始终努力让用户了解这类活动的状态。为此，你可以使用 `UIActivityIndicatorView`，如图 3-11 所示。

`UIActivityIndicatorView` 是一个简单的元素，用于显示后台是否有活动正在进行。然而，它只允许两种状态：正在进行和未进行。要在两者之间切换，你可以使用 `-startAnimating` 和 `-stopAnimating` 方法。你也可以通过 `-isAnimating` 方法访问指示器当前是否正在动画。如果你在任务完成后不再需要使用指示器，可以使用 `hidesWhenStopped` 属性。

![Image](img/9781430240051_Fig03-11.jpg)

**图 3-11.** *一个 `UIActivityIndicatorView` 元素*

对于这个元素，你的自定义选项相当有限，只能调整颜色和大小。首先，你有 `activityIndicatorViewStyle` 属性，它有三个可选值：

* `UIActivityIndicatorViewStyleWhiteLarge`
* `UIActivityIndicatorViewStyleWhite`
* `UIActivityIndicatorViewStyleGray`

这三个值仅在大小和颜色上有所不同，应从中选择以最大化视觉显示质量。

如果上述任何一种样式都不太符合你的应用设计，你还可以通过 `UIColor` 类，使用 `color` 属性指定不同的颜色。

