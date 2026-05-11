# 第 1 章：Android 图像处理入门

`displayColumnIndex = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA);`

我们需要索引才能从游标中选取该字段。首先，我们通过调用 `moveToFirst` 方法来确保游标有效且包含一些结果。如果游标不包含任何结果，此方法将返回 `false`。我们使用 `Cursor` 类中的几种方法之一来选取实际数据。我们选择哪种方法取决于数据的类型，字符串使用 `getString`，整数使用 `getInt`，以此类推。

```
if (cursor.moveToFirst()) {
  String displayName = cursor.getString(displayColumnIndex);
}
```

## 创建图像查看应用

下面是一个完整的示例，它查询 `MediaStore` 来查找图像，并以幻灯片的形式逐一呈现给用户。

```
package com.apress.proandroidmedia.ch1.mediastoregallery;

import android.app.Activity;
import android.database.Cursor;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Bundle;
import android.provider.MediaStore;
import android.provider.MediaStore.Images.Media;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.ImageButton;
import android.widget.TextView;

public class MediaStoreGallery extends Activity {

public final static int DISPLAYWIDTH = 200;
public final static int DISPLAYHEIGHT = 200;
```

我们不会使用屏幕尺寸来加载和显示图像，而是使用上述常量来决定图像的显示大小。

`TextView titleTextView;`
`ImageButton imageButton;`

在此示例中，我们使用的是 `ImageButton` 而不是 `ImageView`。这同时为我们提供了 `Button`（可点击）和 `ImageView`（可显示图像）的功能。

`Cursor cursor;`
`Bitmap bmp;`
`String imageFilePath;`
`int fileColumn;`
`int titleColumn;`
`int displayColumn;`

```
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    titleTextView = (TextView) this.findViewById(R.id.TitleTextView);
    imageButton = (ImageButton) this.findViewById(R.id.ImageButton);
```

这里我们指定希望返回的列。这必须以字符串数组的形式给出。在下一行中，我们将该数组传递给 `managedQuery` 方法。

```
    String[] columns = { Media.DATA, Media._ID, Media.TITLE, Media.DISPLAY_NAME };
    cursor = managedQuery(Media.EXTERNAL_CONTENT_URI, columns, null, null, null);
```

我们需要知道要从 `Cursor` 对象中获取数据的每个列的索引。在此示例中，我们从 `Media.DATA` 切换到了 `MediaStore.Images.Media.DATA`。这只是为了说明它们是相同的。`Media.DATA` 只是简写，因为我们有一个涉及它的导入语句：`android.provider.MediaStore.Images.Media`。

```
    fileColumn = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA);
    titleColumn = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.TITLE);
    displayColumn = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DISPLAY_NAME);
```

运行查询并获得一个 `Cursor` 对象后，我们在其上调用 `moveToFirst` 以确保它包含结果。

```
    if (cursor.moveToFirst()) {
        //titleTextView.setText(cursor.getString(titleColumn));
        titleTextView.setText(cursor.getString(displayColumn));
        imageFilePath = cursor.getString(fileColumn);
        bmp = getBitmap(imageFilePath);
        // 显示它
        imageButton.setImageBitmap(bmp);
    }
```

然后，我们为 `imageButton` 指定一个新的 `OnClickListener`，它会调用 `Cursor` 对象上的 `moveToNext` 方法。这会遍历结果集，逐一加载并显示返回的每个图像。

```
    imageButton.setOnClickListener(
        new OnClickListener() {
            public void onClick(View v) {
                if (cursor.moveToNext())
                {
                    //titleTextView.setText(cursor.getString(titleColumn));
                    titleTextView.setText(cursor.getString(displayColumn));
                    imageFilePath = cursor.getString(fileColumn);
                    bmp = getBitmap(imageFilePath);
                    imageButton.setImageBitmap(bmp);
                }
            }
        }
    );
}
```



## 第一章：Android 图像处理入门

下面是一个名为 `getBitmap` 的方法，它封装了图像缩放和加载所需的操作，这样我们就能在显示图像时避免本章前面讨论的内存问题。

```
private Bitmap getBitmap(String imageFilePath)
{
    // 仅加载图像的尺寸，而非图像本身
    BitmapFactory.Options bmpFactoryOptions = new BitmapFactory.Options();
    bmpFactoryOptions.inJustDecodeBounds = true;
    Bitmap bmp = BitmapFactory.decodeFile(imageFilePath, bmpFactoryOptions);

    int heightRatio = (int) Math.ceil(bmpFactoryOptions.outHeight
            / (float) DISPLAYHEIGHT);
    int widthRatio = (int) Math.ceil(bmpFactoryOptions.outWidth
            / (float) DISPLAYWIDTH);

    Log.v("HEIGHTRATIO", "" + heightRatio);
    Log.v("WIDTHRATIO", "" + widthRatio);

    // 如果两个比例都大于 1，说明图像的某一边大于屏幕
    if (heightRatio > 1 && widthRatio > 1) {
        if (heightRatio > widthRatio) {
            // 高度比例更大，按高度比例进行缩放
            bmpFactoryOptions.inSampleSize = heightRatio;
        } else {
            // 宽度比例更大，按宽度比例进行缩放
            bmpFactoryOptions.inSampleSize = widthRatio;
        }
    }

    // 真正地解码图像
    bmpFactoryOptions.inJustDecodeBounds = false;
    bmp = BitmapFactory.decodeFile(imageFilePath, bmpFactoryOptions);
    return bmp;
}
```

以下是配合上述活动使用的布局 XML 文件。它应该放在 `res/layout/main.xml` 文件中。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <ImageButton android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:id="@+id/ImageButton"></ImageButton>
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/TitleTextView"
        android:text="Image Title"/>
</LinearLayout>
```

## 内部元数据

EXIF（可交换图像文件格式）是一种在图像文件中保存元数据的标准方式。许多数码相机和桌面应用程序都支持使用 EXIF 数据。由于 EXIF 数据实际上是文件的一部分，因此在文件从一个地方传输到另一个地方时，它不应丢失。例如，当将文件从 Android 设备的 SD 卡复制到家用电脑时，这些数据将保持完整。如果你在 iPhoto 等应用程序中打开该文件，这些数据也会存在。

通常，EXIF 数据具有很强的技术导向性；该标准中的大多数标签都与图像本身的捕获数据有关，例如 `ExposureTime` 和 `ShutterSpeedValue`。

不过，有些标签值得我们考虑填写或修改。其中可能包括以下内容：

- `UserComment`：用户生成的评论
- `ImageDescription`：标题
- `Artist`：图像的创作者或拍摄者
- `Copyright`：图像的版权持有者
- `Software`：用于创建图像的软件

幸运的是，Android 为我们提供了一种便捷的方式来读取和写入 EXIF 数据。实现这一功能的主要类是 `ExifInterface`。

以下是使用 `ExifInterface` 从图像文件中读取特定 EXIF 数据的方法：

```
ExifInterface ei = new ExifInterface(imageFilePath);
String imageDescription = ei.getAttribute("ImageDescription");
if (imageDescription != null)
{
    Log.v("EXIF", imageDescription);
}
```

以下是使用 `ExifInterface` 将 EXIF 数据保存到图像文件的方法：

```
ExifInterface ei = new ExifInterface(imageFilePath);
ei.setAttribute("ImageDescription","Something New");
```

`ExifInterface` 包含一组常量，这些常量定义了相机应用程序自动包含在捕获图像中的典型数据集。

EXIF 规范的最新版本是 2010 年 4 月发布的 2.3 版。可在此处在线获取：[www.cipa.jp/english/hyoujunka/kikaku/pdf/DC-008-2010_E.pdf](http://www.cipa.jp/english/hyoujunka/kikaku/pdf/DC-008-2010_E.pdf)。

## 本章小结

在本章中，我们了解了 Android 上图像捕获和存储的基础知识。我们看到了使用 Android 内置相机应用程序的强大之处，以及如何通过 Intent 有效利用其功能。我们发现，相机应用程序为在任何 Android 应用程序中添加图像捕获功能提供了一个出色且一致的接口。

我们还讨论了在处理大图像时需要注意内存使用的问题。我们了解到 `BitmapFactory` 类可以帮助我们加载图像的缩放版本，以节省内存。对内存的重视提醒我们，手机并非拥有看似无限内存的台式电脑。

我们介绍了如何使用 Android 内置的图像内容提供程序 `MediaStore`。我们学习了如何使用它将图像保存到设备的标准位置，以及如何查询它以快速构建利用已捕获图像的应用程序。

最后，我们探讨了如何通过名为 EXIF 的标准将某些元数据与图像关联起来。EXIF 是可移植的，并广泛应用于各种设备和软件应用程序中。

这为我们探索如何在 Android 上对媒体做更多事情提供了一个很好的起点。

我很期待！

## 第二章：构建自定义相机应用程序

在上一章中，我们了解了如何利用 Android 内置的相机应用程序，在任何其他应用程序中提供一个现成的照片捕获组件。虽然这为最终用户提供了一个标准接口，并且对我们程序员来说也很直接，但它并没有提供太多的灵活性。例如，如果我们希望照片捕获应用程序支持延时摄影，使用内置应用程序很难做到。

幸运的是，Android 并没有限制我们只能使用内置应用程序来访问硬件相机。我们对底层硬件和可用方法的访问权限与相机应用程序本身一样多，这使我们能够在我们想要的任何类型的应用程序中使用这些功能。

在本章中，我们将探索构建一个利用底层 `Camera` 类的拍照应用程序，并学习如何利用我们获得的能力。我们将逐步完成构建几个不同应用程序所需的步骤：

- 一个简单的即拍即得照片应用程序
- 一个倒计时定时器
- 一个延时摄影应用程序

## 使用 Camera 类

Android 中的 `Camera` 类是我们用来访问设备上相机硬件的工具。它允许我们实际捕获图像，并通过其嵌套的 `Camera.Parameters` 类，我们可以更改各种属性，例如是否应激活闪光灯以及白平衡应设置为何值。

[`developer.android.com/reference/android/hardware/Camera.html`](http://developer.android.com/reference/android/hardware/Camera.html)



### 相机权限

为了使用`Camera`类来拍摄图像，我们需要在`AndroidManifest.xml`文件中声明我们要求`CAMERA`权限。

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

### 预览表面

另外，在开始使用相机之前，我们需要创建某种类型的`Surface`，供`Camera`在其上绘制取景器或预览图像。`Surface`是 Android 中的一个抽象类，代表一个绘制图形或图像的地方。提供绘制`Surface`的一种直接方法是使用`SurfaceView`类。`SurfaceView`是一个具体类，在标准`View`内提供了一个`Surface`。

要在布局中指定`SurfaceView`，我们只需在任何常规布局 XML 中使用`<SurfaceView />`元素。这是一个基本的布局，它只是在`LinearLayout`中实现了一个用于相机预览的`SurfaceView`。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <SurfaceView 
        android:id="@+id/CameraView" 
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

在我们的代码中，为了将此`SurfaceView`与`Camera`类配合使用，我们需要添加一个`SurfaceHolder`。`SurfaceHolder`类可以充当`Surface`的监视器，通过回调为我们提供一个接口，让我们知道`Surface`何时被创建、销毁或更改。`SurfaceView`类方便地为我们提供了一个方法`getHolder()`，用来获取其`Surface`对应的`SurfaceHolder`。

以下是一段代码片段，它访问了布局 XML 中声明的`SurfaceView`并从中获取了一个`SurfaceHolder`。它还将`Surface`设置为“推送”类型的`Surface`，这意味着绘图缓冲区由`Surface`本身外部维护。在本例中，缓冲区由`Camera`类管理。`Camera`预览需要使用“推送”类型的`Surface`。

```java
SurfaceView cameraView = (SurfaceView) this.findViewById(R.id.CameraView);
SurfaceHolder surfaceHolder = cameraView.getHolder();
surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
```

此外，我们还需要在我们的 Activity 中实现`SurfaceHolder.Callback`。这使得当`Surface`被创建、更改或销毁时，我们的 Activity 能够收到通知。为了实现`Callback`，我们将添加以下方法。

```java
public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {}
public void surfaceCreated(SurfaceHolder holder) {}
public void surfaceDestroyed(SurfaceHolder holder) {}
```

最后，我们需要告诉我们的`SurfaceHolder`将此 Activity 用作`Callback`处理器。

```java
surfaceHolder.addCallback(this);
```

现在我们的 Activity 应该看起来像这样。

```java
package com.apress.proandroidmedia.ch2.snapshot;

import android.app.Activity;
import android.os.Bundle;
import android.view.SurfaceHolder;
import android.view.SurfaceView;

public class SnapShot extends Activity implements SurfaceHolder.Callback {
    SurfaceView cameraView;
    SurfaceHolder surfaceHolder;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        cameraView = (SurfaceView) this.findViewById(R.id.CameraView);
        surfaceHolder = cameraView.getHolder();
        surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
        surfaceHolder.addCallback(this);
    }

    public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {}

    public void surfaceCreated(SurfaceHolder holder) {}

    public void surfaceDestroyed(SurfaceHolder holder) {}
}
```

### 实现相机

现在我们已经设置好了 Activity 和预览`Surface`，可以开始使用实际的`Camera`对象了。

当`Surface`被创建时（由于`SurfaceHolder.Callback`，这会触发调用我们代码中的`surfaceCreated()`方法），我们可以通过调用`Camera`类的静态`open()`方法来获取一个`Camera`对象。

```java
Camera camera;

public void surfaceCreated(SurfaceHolder holder) {
    camera = Camera.open();
```

接着，我们需要将预览显示设置为当前使用的`SurfaceHolder`（该`SurfaceHolder`通过回调提供给我们的方法）。这个方法需要包裹在`try`/`catch`块中，因为它可能会抛出`IOException`。如果出现这种情况，我们需要释放相机，否则可能会占用相机硬件资源，影响其他应用程序。

```java
try {
    camera.setPreviewDisplay(holder);
} catch (IOException exception) {
    camera.release();
}
```

最后，我们需要启动相机预览。

```java
camera.startPreview();
}
```

同样，在`surfaceDestroyed()`中，我们也需要释放相机。我们将首先调用`stopPreview()`，以确保一切按预期清理。

```java
public void surfaceDestroyed(SurfaceHolder holder) {
    camera.stopPreview();
    camera.release();
}
```

运行这段代码，你可能会注意到预览画面有些奇怪。预览图像逆时针旋转了 90 度，如图 2-1 所示。

**图 2–1.** *相机预览旋转了 90 度*

发生这种旋转的原因是`Camera`假设方向是水平的（横屏模式）。纠正旋转最简单的方法是将我们的 Activity 设置为横屏模式。为此，我们可以在 Activity 的`onCreate()`方法中添加以下代码。

```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
```

现在，我们的`Camera`预览画面显示正确，如图 2-2 所示。遗憾的是，我们的应用程序现在被锁定在横屏模式。

**图 2–2.** *横屏模式下的相机预览*

### 设置相机参数

如前所述，`Camera`类有一个内嵌的`Camera.Parameters`类。此类包含一系列重要的属性或设置，可用于更改`Camera`的操作方式。其中一个能立刻帮到我们的设置是处理预览中遇到的旋转/横屏问题。

可以通过以下方式修改`Camera`要使用的`Parameters`：

```java
Camera.Parameters parameters = camera.getParameters();
parameters.set("some parameter", "some value");
// 或
parameters.set("some parameter", some_int);
camera.setParameters(parameters);
```

有两个不同的通用`Parameters.set()`方法。第一个方法接受两个字符串参数，分别为参数名称和值；第二个方法的参数名称是字符串，但值是整数。



