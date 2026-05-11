# 第 3 章：图像编辑与处理

在 Android SDK 中，我们可以通过先将一个 `Bitmap` 绘制到 `Canvas` 上，然后将第二个 `Bitmap` 绘制到同一个 `Canvas` 上来实现合成。唯一的区别在于，绘制第二张图像时，我们需要在 `Paint` 上指定一个传输模式（`Xfermode`）。

可作为传输模式使用的类集均派生自 `Xfermode` 基类，其中包含一个名为 `PorterDuffXfermode` 的类。`PorterDuffXfermode` 类以托马斯·波特和汤姆·达夫的名字命名，他们于 1984 年在 ACM SIGGRAPH 计算机图形学出版物上发表了一篇题为“合成数字图像”的论文，详细阐述了一系列在一张图像上绘制另一张图像的不同规则。这些规则定义了最终输出结果中哪些图像的哪些部分将会出现。

波特和达夫设计的规则以及其他更多规则已在 Android 的 `PorterDuff.Mode` 类中枚举。

它们包括以下内容：

- `android.graphics.PorterDuff.Mode.SRC`：此规则意味着只绘制**源**图像，在我们的例子中，即我们应用该绘制的图像。
- `android.graphics.PorterDuff.Mode.DST`：此规则意味着只显示**目标**图像，即已经在画布上的原始图像。

继 `SRC` 和 `DST` 规则之后，还有一组规则与它们配合使用，以确定最终将绘制每张图像的哪些部分。这些规则通常适用于图像尺寸不同或图像具有透明部分的情况。

- `android.graphics.PorterDuff.Mode.DST_OVER`：目标图像将绘制在源图像的上方。
- `android.graphics.PorterDuff.Mode.DST_IN`：目标图像将仅绘制在源图像和目标图像相交的区域。
- `android.graphics.PorterDuff.Mode.DST_OUT`：目标图像将仅绘制在源图像和目标图像不相交的区域。
- `android.graphics.PorterDuff.Mode.DST_ATOP`：目标图像将绘制在其与源图像相交的区域；在其他区域则绘制源图像。
- `android.graphics.PorterDuff.Mode.SRC_OVER`：源图像将绘制在目标图像的上方。
- `android.graphics.PorterDuff.Mode.SRC_IN`：源图像将仅绘制在目标图像和源图像相交的区域。
- `android.graphics.PorterDuff.Mode.SRC_OUT`：源图像将仅绘制在目标图像和源图像不相交的区域。
- `android.graphics.PorterDuff.Mode.SRC_ATOP`：源图像将绘制在其与目标图像相交的区域；在其他区域则绘制目标图像。

