# 第 6 章：为 Web 应用界面增添趣味的 CSS 特性

`图 6-8`：照片应用呈现均匀的画廊效果，所有图片均采用相同的显示尺寸

在开发原生应用时，您可以使用 Cocoa Touch 框架中的 `UIImageView` 对象，通过使用适应比例、填充缩放或填充比例等参数（如图 6-9 所示），让图片适应任意容器尺寸。现在我们将利用全新的 CSS 特性来实现高度相似的设计。Mobile Safari 的缩放算法表现优异，因此您可以获得出色的效果。

`图 6-9`：现在只需为容器应用样式，即可实现适应比例、填充缩放和填充比例模式

`background-size` 属性接受两个值：水平值和垂直值。当然，您也可以只指定一个值，该值将同时应用于两个方向。您可以使用常用的 CSS 单位来赋值；但在本例中，百分比是最佳选择，因为它能让代码更易于重用。

最直接的值是填充缩放，这样您无需了解图片的横纵比。图片将被缩放以填满可用空间，并可能适当地拉伸或压缩：

```css
/* 填充缩放 */
-webkit-background-size: 100%;
```

`background-size` 属性的其他值要求您了解图片的方向。要实现填充比例效果，您应将图片较窄的一边设为 100%，另一边设为 `auto`。这意味着竖版图片将在垂直方向上裁剪，横版图片则在水平方向上裁剪。

```css
/* 横版图片的填充比例... */
-webkit-background-size: auto 100%;

/* ...以及竖版图片 */
-webkit-background-size: 100% auto;
```

将这两个值互换则会产生适应比例缩放的效果，即整张图片会显示在指定区域内，但在垂直或水平方向留下空白区域。

同时明确指定两个值将强制改变图片缩放后的尺寸。

> **警告：** 请注意，图片高度不要超过容器的两倍。Mobile Safari 在处理这种情况时存在一个 Bug，会导致图片被裁剪。避免此问题的唯一方法是使用更小的图片。

CSS3 规范提供了轻松实现预期效果的值。然而，`contain` 和 `cover` 这两个值在 iOS 3.2 版本中并不支持。

### 开发类似照片应用的照片墙

在本节中，我们将通过一个实例展示这些新 CSS 属性和值所提供的可能性，即利用它们重现之前展示的照片应用中的照片墙外观。这种照片墙之所以高效且吸引人，其中一个特点是不论图片的原始格式如何，它们都能完美地适配到分配给它们的方形区域中。

与往常一样，我们将在 Komodo Edit 项目中已有的目录结构基础上进行构建。首先，您需要在项目中添加一个名为 `images` 的新物理文件夹，用于存储照片墙中的所有图片。

在 Komodo Edit 中，转到 **项目** ➤ **添加** ➤ **新建活动文件夹**，然后点击相应的图标创建一个名为 `images` 的新目录，并选择它。

接下来，由于我们将使用 PHP 来读取 `images` 文件夹的内容，并将其中图片的引用动态添加到一些 JavaScript 代码中，因此您需要将 `index.html` 文件重命名为 `index.php`。文件内容应如下所示：

```php
<?php require_once("index_code.php"); ?>

<!DOCTYPE html>
<html>
<head>
  <title>照片墙演示</title>
  ...
  <link rel="stylesheet" href="styles/main.css">
  <link rel="stylesheet" href="styles/gallery.css">
  ...
  <script src="scripts/main.js"></script>
  <script>
    var images = <?php writeImages('images'); ?>;
  </script>
  <script src="scripts/gallery.js"></script>
</head>
<body onload="showImages()">
  ...
  <h1>照片墙</h1>
  ...
  <div id="gallery"></div>
</body>
</html>
```

然后，在与 `index.php` 相同的目录下创建一个 `index_code.php` 文件。该文件将包含照片墙运行所需的函数。换句话说，它允许您从 `images` 文件夹中收集图片列表。文件内容如下：

```php
<?php
#### 解析指定文件夹并返回收集到的文件数组
function getImages($path) {
  $handle = opendir($path);
  $files = array();
  if ($handle) {
    while (($name = readdir($handle)) !== false) {
      if (is_file("$path/$name")) {
        $files[] = "$path/$name";
      }
    }
  }
  closedir($handle);
  return $files;
}
?>
```


#### 将 PHP 数组转换为 JavaScript 数组

```
function writeImages($path) {

  $all = implode('", "', getImages($path));

  echo !$all ? '[]' : '["'. $all . '"]';

}

?>
```

