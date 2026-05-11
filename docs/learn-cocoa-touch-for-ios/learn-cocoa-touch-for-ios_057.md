# 第 9 章：用户界面设计

**图 9-7.** *“大小检查器”，其中包含用于设置视图自动调整大小值的弹簧和支柱*

### 视图内容模式

`UIView` 的另一个需要解释的属性是 `contentMode` 属性。当视图调整大小时，此属性定义了其内容的重绘方式。

默认情况下，视图不会被重绘，而是其内容会调整大小，因为这样效率更高。此属性的可能值如下：

- `UIViewContentModeScaleToFill`
- `UIViewContentModeScaleAspectFit`
- `UIViewContentModeScaleAspectFill`
- `UIViewContentModeRedraw`
- `UIViewContentModeCenter`
- `UIViewContentModeTop`
- `UIViewContentModeBottom`
- `UIViewContentModeLeft`
- `UIViewContentModeRight`
- `UIViewContentModeTopLeft`

[www.it-ebooks.info](http://www.it-ebooks.info/)

