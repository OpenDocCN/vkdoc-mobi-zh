# UISwitch

`UISwitch` 的工作方式与 `UISlider` 非常相似，但它只允许用户选择布尔值，即“开”（如图 3-10 所示）或“关”。这些开关通常用在应用的“设置”区域，允许用户轻松定制自己的偏好。

![Image](img/9781430240051_Fig03-10.jpg)

**图 3-10.** *一个启用的 `UISwitch`*

在添加要在值变化时执行的操作方面，`UISwitch` 的工作方式与 `UISlider` 完全相同，但由于开关的布尔值特性，它要简单得多。与之前一样，使用 `-addTarget:action:forControlEvents:` 将一个方法连接到开关。你也可以通过 `on` 属性访问开关的值，并使用 `-setOn:animated:` 方法动画地改变其值。

