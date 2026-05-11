# 第 4 章：图形与触摸事件

`Button savePicture;`

在`onCreate`方法中，获取到`choosePicture`按钮的引用后，我们还需要获取`savePicture`按钮的引用：

`savePicture = (Button) this.findViewById(R.id.SavePictureButton);`

之后，我们将它的`onClickListener`设置为当前活动，这与我们对`choosePicture`按钮所做的操作相同。

`savePicture.setOnClickListener(this);`

现在，我们需要修改`onClick`方法，以处理两个不同按钮共用该方法的场景。最简单的方式是将传入的`View`与这两个按钮进行比较。如果`View`与其中一个按钮相等，则说明该按钮被点击。

```
public void onClick(View v) {
    if (v == choosePicture) {
        Intent choosePictureIntent = new Intent(Intent.ACTION_PICK,
            android.provider.MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
        startActivityForResult(choosePictureIntent, 0);
    }
    else if (v == savePicture) {
```

此时我们知道`savePicture`按钮被点击了。接下来需要确保用于绘制的 Bitmap 已经被定义。

```
if (alteredBitmap != null) {
```

确认后，就可以开始保存操作了。与之前的示例类似，我们会查询`MediaStore`来为新图片获取一个`Uri`，并从该`Uri`创建一个`OutputStream`。

```
Uri imageFileUri = getContentResolver().insert(
    Media.EXTERNAL_CONTENT_URI, new ContentValues());

try {
    OutputStream imageFileOS =
        getContentResolver().openOutputStream(imageFileUri);
```

在之前的图片保存示例中，数据已经以 JPEG 格式存在，我们只需将其写入`OutputStream`即可。而在本例中，我们必须使用`Bitmap`对象的`compress`方法，将其转换为 JPEG（或 PNG，如果我们选择的话）。

`compress`方法会压缩`Bitmap`数据并将其写入`OutputStream`。它接收一个定义在`Bitmap.CompressFormat`中的常量，可以是`PNG`或`JPEG`。PNG 非常适合线条艺术和图形，JPEG 则非常适合具有渐变效果的彩色图片，比如照片。由于我们这里处理的是照片，因此使用 JPEG 格式。

下一个参数是质量设置。质量设置仅在压缩为 JPEG 时有用，因为 PNG 图像会始终保留所有数据，质量设置对其无效。JPEG 是一种“有损”编码器，这意味着数据会被丢弃。质量与文件大小成反比。质量设置为 0 将产生很小的文件体积，但图像质量不佳；质量设置为 100 将产生很大的文件体积，但图像效果会非常好。在本例中，如图 4-19 所示，我们使用了 90，这是一个折中的选择。图 4-20 展示了相同图像在质量设置为 10 时的效果，以供对比。

我们需要传入的最后一个参数是实际的`OutputStream`，用于写入文件。

`alteredBitmap.compress(CompressFormat.JPEG, 90, imageFileOS);`

至此就完成了——现在我们只需通过一个 Toast 提示通知用户图片已保存，然后继续执行后续代码。

```
Toast t = Toast.makeText(this, "Saved!", Toast.LENGTH_SHORT);
t.show();

} catch (FileNotFoundException e) {
    Log.v("EXCEPTION", e.getMessage());
}
```

```
    }
}
```

以下是更新后的布局 XML，其中包含了新的保存按钮。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Choose Picture"
        android:id="@+id/ChoosePictureButton" />

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/ChoosenImageView">
    </ImageView>

    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Save Picture"
        android:id="@+id/SavePictureButton" />
</LinearLayout>
```

![图 4-19](img/index-118_1.jpg)
![图 4-20](img/index-118_2.jpg)

