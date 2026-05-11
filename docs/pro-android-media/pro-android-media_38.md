# 第 3 章：图像编辑与处理

**图 3–13.** *翻转*

**绘图的替代方案**

前面章节中使用的方法的一个缺点是图像会被截断，因为我们没有计算变换后的结果尺寸，而只是将图像绘制到一个预先设定大小的 `Bitmap` 中。

解决这个问题的一种方法是在创建 `Bitmap` 时就应用 `Matrix`，而不是先绘制到一个空的 `Bitmap`。

采用这种方式就无需再使用 `Canvas` 和 `Paint` 对象。其缺点是，如果我们想对 `Bitmap` 进行更多变换，就无法连续修改该 `Bitmap` 对象，而必须重新创建它。

`Bitmap` 类中提供的静态 `createBitmap` 方法可以实现这一点。第一个参数是源 `Bitmap`，接下来几个参数是从源图像中使用的起始 `x`、`y`、`width` 和 `height` 值，随后是要应用的 `Matrix`，最后是一个 `Boolean` 值，表示是否对图像应用滤镜。由于我们并未应用包含滤镜的矩阵（我们将在本章后面讨论滤镜），因此将其设置为 `false`。

```
Matrix matrix = new Matrix();
matrix.setRotate(15,bmp.getWidth()/2,bmp.getHeight()/2);
alteredBitmap = Bitmap.createBitmap(bmp, 0, 0, bmp.getWidth(), bmp.getHeight(),
matrix, false);
alteredImageView.setImageBitmap(alteredBitmap);
```

![](img/index-79_1.jpg)

### **65**

如您所见，我们以相同的方式处理矩阵，但使用原始 `Bitmap`（`bmp`）作为源来实例化第二个 `Bitmap`（`alteredBitmap`），并传入 `Matrix` 对象。这会从源图像创建一个经过平移并缩放到 `Bitmap` 对象尺寸的 `Bitmap`，如图 3–14 所示。

**图 3–14.** *在创建 Bitmap 时应用矩阵；Bitmap 的尺寸会被调整以匹配实际图像数据。*

**图像处理**

另一种图像编辑或处理方式涉及更改像素本身的颜色值。能够做到这一点，我们就可以改变对比度、亮度、整体色调等。

**ColorMatrix**

与在 `Canvas` 上绘图时使用 `Matrix` 对象的方式类似，我们可以使用 `ColorMatrix` 对象来修改用于在 `Canvas` 上绘图的 `Paint`。

`ColorMatrix` 的工作方式也类似。它是一个由数字组成的数组，作用于图像的像素。不过，它不是对 `x`、`y` 和 `z` 坐标进行操作，而是对每个像素的颜色值（红、绿、蓝和 Alpha 值）进行操作。

### **66**

我们可以通过不带任何参数调用其构造函数来构造一个默认的 `ColorMatrix` 对象。

```
ColorMatrix cm = new ColorMatrix();
```

这个 `ColorMatrix` 可以通过一个使用我们 `ColorMatrix` 对象构造的 `ColorMatrixColorFilter` 对象应用到 `Paint` 对象上，从而改变绘制到 `Canvas` 的方式。

```
paint.setColorFilter(new ColorMatrixColorFilter(cm));
```

只需将其插入到“选择图片”示例的绘图部分，我们就能体验 `ColorMatrix` 的效果。

```
Bitmap bmp = BitmapFactory.decodeStream(getContentResolver().
openInputStream(imageFileUri), null, bmpFactoryOptions);
Bitmap alteredBitmap = Bitmap.createBitmap(bmp.getWidth(),
bmp.getHeight(),bmp.getConfig());
Canvas canvas = new Canvas(alteredBitmap);
Paint paint = new Paint();
ColorMatrix cm = new ColorMatrix();
paint.setColorFilter(new ColorMatrixColorFilter(cm));
Matrix matrix = new Matrix();
canvas.drawBitmap(bmp, matrix, paint);
alteredImageView.setImageBitmap(alteredBitmap);
chosenImageView.setImageBitmap(bmp);
```

默认的 `ColorMatrix` 被称为单位矩阵，就像默认的 `Matrix` 对象一样，应用它时不会改变图像。查看其数组内容有助于我们理解它的工作原理。

```
1 0 0 0 0
0 1 0 0 0
0 0 1 0 0
0 0 0 1 0
```

如您所见，这是一个由 20 个浮点数组成的数组。第一行五个数字表示对单个像素红色部分的操作，第二行影响绿色部分，第三行操作蓝色部分，最后一行操作像素的 Alpha 值。

在每一行中，第一个数字是与像素红色值相乘的乘数，第二个是与绿色值相乘的乘数，第三个与蓝色值相乘，第四个与 Alpha 值相乘，最后一个数字不与任何值相乘。这些值全部相加，以改变其所操作的像素。

假设我们有一个中灰色的像素，其红色值为 128，蓝色值为 128，绿色值为 128，Alpha 值为 0（不透明）。如果我们将该像素通过这个颜色矩阵处理，计算过程如下：

```
新红色值 = 1*128 + 0*128 + 0*128 + 0*0 + 0
新蓝色值 = 0*128 + 1*128 + 0*128 + 0*0 + 0
新绿色值 = 0*128 + 0*128 + 1*128 + 0*0 + 0
```

### **67**

```
新 Alpha 值 = 0*128 + 0*128 + 0*128 + 1*0 + 0
```

如您所见，所有值都将保持不变，仍为 128。对于我们用于像素的任何颜色变体来说都是如此，因为每一行在其对应颜色的操作位置上是 1，而在其他位置上是 0。

如果我们简单地想让图像看起来比以前红两倍，我们可以将作用于所有像素红色值的数字从 1 增加到 2。这将使所有像素的红色值加倍。

```
2 0 0 0 0
0 1 0 0 0
0 0 1 0 0
0 0 0 1 0
```

要在代码中实现这一点，我们可以执行以下操作：

```
ColorMatrix cm = new ColorMatrix();
cm.set(new float[] {
2, 0, 0, 0, 0,
0, 1, 0, 0, 0,
0, 0, 1, 0, 0,
0, 0, 0, 1, 0
});
paint.setColorFilter(new ColorMatrixColorFilter(cm));
```

手动操作，我们可以对任何颜色进行类似的处理。

**调整对比度和亮度**

调整图像的亮度和对比度可以通过增加或减少颜色值来实现。

以下代码将使每个颜色通道的强度加倍，这会同时影响图像的亮度和对比度，如图 3–15 所示。

```
ColorMatrix cm = new ColorMatrix();
float contrast = 2;
cm.set(new float[] {
contrast, 0, 0, 0, 0,
0, contrast, 0, 0, 0,
0, 0, contrast, 0, 0,
0, 0, 0, 1, 0 });
paint.setColorFilter(new ColorMatrixColorFilter(cm));
```

![](img/index-82_1.jpg)

### **68**

**图 3–15.** *使用 ColorMatrix 将每种颜色的强度加倍，从而增加亮度和对比度*

在此示例中，两种效果是关联的。如果我们只想增加对比度而不增加亮度，实际上必须降低亮度以补偿颜色强度的增加。

Download from Wow! eBook <www.wowebook.com>

通常，调整亮度时，仅使用矩阵中每种颜色的最后一列会更简单。该列的值是直接加到颜色值上的数值，而不对现有颜色值进行任何乘法运算。

因此，要降低亮度，我们可以使用如下矩阵代码。

```
ColorMatrix cm = new ColorMatrix();
float brightness = -25;
cm.set(new float[] {
1, 0, 0, 0, brightness,
0, 1, 0, 0, brightness,
0, 0, 1, 0, brightness,
0, 0, 0, 1, 0 });
paint.setColorFilter(new ColorMatrixColorFilter(cm));
```

将这两种变换结合在一起，会得到以下结果。

```
ColorMatrix cm = new ColorMatrix();
float contrast = 2;
float brightness = -25;
cm.set(new float[] {
contrast, 0, 0, 0, brightness,
```

![](img/index-83_1.jpg)



