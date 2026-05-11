# 第 9 章：用户界面设计

- `UIViewContentModeTopRight`
- `UIViewContentModeBottomLeft`
- `UIViewContentModeBottomRight`

别担心，我不会逐一解释每个值。前三个值 `ScaleToFill`、`ScaleAspectFit` 和 `ScaleAspectFill` 都会缩放原始内容。`ScaleToFill` 只是将内容调整大小以完全匹配新视图，必要时会拉伸，不考虑纵横比。这对于渐变或纯色等无需保持纵横比的内容是最佳选择。`ScaleAspectFit` 和 `ScaleAspectFill` 都会保持内容的原始纵横比。`ScaleAspectFit` 确保原始内容完整显示，如果需要会缩小，但会尽可能大地显示而不截断任何部分；任何剩余区域将由视图的背景色填充。`ScaleAspectFill` 会调整原始内容的大小以完全填充新尺寸，必要时会截断顶部、底部或侧边。`UIViewContentModeRedraw` 强制系统为新尺寸重绘内容。这是效率最低的选择，但如果您需要重绘内容以响应尺寸变化，则非常有用。其余的内容模式在视图尺寸变化时不会调整内容大小，而是将其锚定到指定点。

在处理图像时，内容模式最为有用。由于图像内容通常具有特定的纵横比，因此对于图像视图，通常最好使用 `UIViewContentModeScaleAspectFit` 或 `UIViewContentModeScaleAspectFill`。

### UIView 子类中的视图布局

当您创建自己的 `UIView` 子类时，它们可能包含自己的子视图。例如，您可以创建一个包含 `UILabel` 和 `UIImage` 的 `UIView` 对象。虽然子视图的自动调整大小遮罩在很大程度上可以自定义此视图的行为，但您可能希望进行更自定义的设置。例如，如果视图的高度大于宽度，您可能希望将标签显示在图像视图下方；但如果视图的宽度大于高度，则希望将标签显示在图像视图右侧。

要实现此目标，您可以在 `UIView` 子类中实现 `layoutSubviews` 方法，如下所示：

```
- (void)layoutSubviews

{

[super layoutSubviews];

CGFloat width = [self bounds].size.width;

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章：用户界面设计

CGFloat height = [self bounds].size.height;

CGRect imageFrame;

CGRect labelFrame;

if (width > height) {

imageFrame = CGRectMake(0.0f, 0.0f, height, height);

labelFrame = CGRectMake(height, 0.0f, width - height, height);

}

else {

imageFrame = CGRectMake(0.0f, 0.0f, width, width);
```


#### 代码片段

```
labelFrame = CGRectMake(0.0f, width, height - width, width);
}

[[self imageView] setFrame:imageFrame];
[[self label] setFrame:labelFrame];
}
```

此方法假设存在名为 `imageView` 的图像视图属性和名为 `label` 的标签属性，它会使图像呈现为方形：如果视图宽度大于高度，则标签位于其右侧；如果视图高度大于宽度，则标签位于其下方。

#### 在 Retina 显示屏设备上的视图布局

与图像类似，在配备 Retina 显示屏的设备上布局视图时，有一些需要注意的事项。值得庆幸的是，您编写的大部分实际代码无需更改，但在与设计师合作时，了解操作系统如何处理 Retina 显示屏将大有裨益。

在绘制和定位视图时，屏幕尺寸并非以像素为单位，而是以点（points）为单位进行测量。点是一种抽象度量单位，根据设备分辨率对应不同数量的像素。在 iPhone、iPhone 3G 或 iPhone 3GS，以及对应的 iPod touch 型号上，屏幕宽 320 像素、高 480 像素，以点计量时同样为宽 320 点、高 480 点。iPhone 4、iPhone 4S 及后续可能的 iPhone 设备，连同对应的 iPod touch 型号，屏幕宽 640 像素、高 960 像素。然而以点计量时，尺寸完全相同：宽 320 点、高 480 点。因此，当您考虑 iPhone 4S 上 `UIWindow` 的 `frame` 属性时，其 `size` 成员的值是 320 和 480，而非 640 和 960。这对开发者而言，意味着无需对现有应用进行额外调整即可在 Retina 显示屏上正常运行。您只需提供双倍尺寸的图像，布局依然保持不变。

[www.it-ebooks.info](http://www.it-ebooks.info/)

