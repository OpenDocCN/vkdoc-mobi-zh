# 第 5 章：处理用户触摸

```objectivec
- (void)viewWillAppear:(BOOL)animated
{
[super viewWillAppear:animated];
[[self imageView] setImage:[[self possession] image]];
[[self nameField] setText:[[self possession] name]];
[[self valueField] setText:[[[self possession] value] stringValue]];
}
```

构建并运行，添加一个物品，应用应该看起来像图 5-3 所示。

**图 5-3. 我们的新详情视图控制器**

不幸的是，点击标签目前还没有任何反应。为此，我们将向图片视图添加一个轻点手势识别器。我们会在详情视图控制器的 `viewDidLoad` 方法中执行此操作。在较新版本的 Xcode 中，你也可以在 Interface Builder 中通过将手势识别器拖拽到视图上来创建它们。

[www.it-ebooks.info](http://www.it-ebooks.info/)

