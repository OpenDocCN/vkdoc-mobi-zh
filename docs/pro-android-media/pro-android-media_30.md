# 第一章：Android 图像处理入门

```
// 实际解码
bmpFactoryOptions.inJustDecodeBounds = false;
bmp = BitmapFactory.decodeStream(getContentResolver().openInputStream(imageFileUri), null, bmpFactoryOptions);

// 显示图像
returnedImageView.setImageBitmap(bmp);
```

```
catch (FileNotFoundException e)
{
    Log.v("ERROR",e.toString());
}
}
}
}
```

以下是上述示例中使用的布局 XML 文件 `main.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    
    <ImageView android:id="@+id/ReturnedImageView" 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
    
    <TextView android:layout_width="wrap_content" 
        android:layout_height="wrap_content"
        android:text="Title:" 
        android:id="@+id/TitleTextView" />
    
    <EditText android:layout_height="wrap_content" 
        android:id="@+id/TitleEditText"
        android:layout_width="fill_parent" />
    
    <TextView android:layout_width="wrap_content" 
        android:layout_height="wrap_content"
        android:text="Description" 
        android:id="@+id/DescriptionTextView" />
    
    <EditText android:layout_height="wrap_content" 
        android:layout_width="fill_parent"
        android:id="@+id/DescriptionEditText" />
    
    <Button android:layout_width="wrap_content" 
        android:layout_height="wrap_content"
        android:id="@+id/TakePictureButton" 
        android:text="Take Picture" />
    
    <Button android:layout_width="wrap_content" 
        android:layout_height="wrap_content"
        android:id="@+id/SaveDataButton" 
        android:text="Save Data" />
</LinearLayout>
```

与之前的例子相同，当相机应用返回时，会触发 `onActivityResult` 方法。新创建的图像被解码为 `Bitmap` 并显示出来。在这个版本中，还会管理相关的用户界面元素。

## 使用 MediaStore 检索图像

使用 Android 共享内容提供程序的一个强大之处在于，我们可以轻松地利用它们创建类似图库应用这样的功能。

由于内容提供程序（本例中为 `MediaStore`）在应用之间共享，我们无需实际创建相机应用和图像存储机制，就能构建自己的图像查看应用。由于大多数应用都会使用默认的 `MediaStore`，我们可以借此构建自己的图库应用。

从 `MediaStore` 中选择记录非常直接。我们使用与创建新记录时相同的 URI 来从中选择记录。

`Media.EXTERNAL_CONTENT_URI`

`MediaStore`（实际上所有内容提供程序）的操作方式与数据库类似。我们从其中选择记录，并得到一个 `Cursor` 对象，我们可以用它来遍历结果。

为了进行首次选择，我们需要创建一个字符串数组，其中包含我们希望返回的列。`MediaStore` 中图像的标准列在 `MediaStore.Images.Media` 类中表示。

```
String[] columns = { Media.DATA, Media._ID, Media.TITLE, Media.DISPLAY_NAME };
```

要执行实际查询，我们可以使用 activity 的 `managedQuery` 方法。第一个参数是 URI，接着是列名数组，然后是限制性的 `WHERE` 子句，`WHERE` 子句的参数，最后是 `ORDER BY` 子句。

以下查询将选择过去一小时内创建的记录，并按从旧到新的顺序排列。

首先，我们创建一个名为 `oneHourAgo` 的变量，它存储从 1970 年 1 月 1 日到一小时前的秒数。`System.currentTimeMillis()` 返回自同一日期起的毫秒数，因此除以 1000 即可得到秒数。如果减去 60 分钟 * 60 秒，就能得到一小时前的值。

```
long oneHourAgo = System.currentTimeMillis()/1000 - (60 * 60);
```

然后我们将该值放入一个字符串数组中，用作 `WHERE` 子句的参数。

```
String[] whereValues = {""+oneHourAgo};
```

接着我们选择希望返回的列。

```
String[] columns = { Media.DATA, Media._ID, Media.TITLE, Media.DISPLAY_NAME,
    Media.DATE_ADDED };
```

最后我们执行查询。`WHERE` 子句中有一个 `?`，它会被下一个参数中的值替换。如果有多个 `?`，则传入的数组中必须有相应数量的值。这里使用的 `ORDER BY` 子句指定返回的数据将按日期升序排列。

```
cursor = managedQuery(Media.EXTERNAL_CONTENT_URI, columns, Media.DATE_ADDED + " > ?",
    whereValues, Media.DATE_ADDED + " ASC");
```

当然，如果你希望返回所有记录，可以将最后三个参数设为 `null`。

```
Cursor cursor = managedQuery(Media.EXTERNAL_CONTENT_URI, columns, null, null, null);
```

返回的游标可以告诉我们所选各列的索引。



