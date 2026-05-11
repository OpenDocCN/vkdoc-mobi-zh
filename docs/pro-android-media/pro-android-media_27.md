# 第 1 章：Android 影像处理入门

提供与`Camera`应用相媲美的图像拍摄功能，而无需自行构建自定义拍摄程序。

Intent 过滤器是应用开发者用来声明其应用提供特定功能的一种手段。在应用的`AndroidManifest.xml`文件中指定 Intent 过滤器，会告知 Android 系统：该应用（尤其是包含该过滤器的 Activity）将在收到指令时执行指定的任务。

`Camera`应用在其清单文件中指定了以下 Intent 过滤器。此处显示的过滤器包含在"`Camera`"Activity 标签内。

```
<intent-filter>
<action android:name="android.media.action.IMAGE_CAPTURE" />
<category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

为了通过 Intent 调用`Camera`应用，我们只需构造一个能被上述过滤器捕获的 Intent 即可。

```
Intent i = new Intent("android.media.action.IMAGE_CAPTURE");
```

在实践中，我们可能不希望直接使用该动作字符串来创建 Intent。在这种情况下，`MediaStore`类中定义了一个常量`ACTION_IMAGE_CAPTURE`。我们应使用常量而非字符串本身的原因是，如果字符串发生变更，常量也极大概率会随之更新，从而使我们的调用更具前瞻性。

```
Intent i = new Intent(android.provider.MediaStore.ACTION_IMAGE_CAPTURE);
startActivity(i);
```

在一个基础 Android Activity 中使用此 Intent，将会启动默认的`Camera`应用并进入静态拍照模式，如图 1–1 所示。

**图 1–1.** *通过 Intent 调用的内置 Camera 应用在模拟器中的运行界面*

## 从 Camera 应用返回数据

当然，如果拍摄照片后`Camera`应用无法将图片返回给调用它的 Activity，那么单纯使用内置`Camera`应用来拍照就没有实际意义。这可以通过将 Activity 中的`startActivity`方法替换为`startActivityForResult`方法来实现。使用此方法，我们就能访问`Camera`应用返回的数据，即用户拍摄的图像（以`Bitmap`形式呈现）。

以下是一个基本示例：

```java
package com.apress.proandroidmedia.ch1.cameraintent;

import android.app.Activity;
import android.content.Intent;
import android.graphics.Bitmap;
import android.os.Bundle;
import android.widget.ImageView;

public class CameraIntent extends Activity {

    final static int CAMERA_RESULT = 0;
    ImageView imv;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        Intent i = new Intent(android.provider.MediaStore.ACTION_IMAGE_CAPTURE);
        startActivityForResult(i, CAMERA_RESULT);
    }

    protected void onActivityResult(int requestCode, int resultCode, Intent intent) {
        super.onActivityResult(requestCode, resultCode, intent);
        if (resultCode == RESULT_OK) {
            Bundle extras = intent.getExtras();
            Bitmap bmp = (Bitmap) extras.get("data");
            imv = (ImageView) findViewById(R.id.ReturnedImageView);
            imv.setImageBitmap(bmp);
        }
    }
}
```

该示例要求在项目的`layout/main.xml`文件中包含以下内容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <ImageView android:id="@+id/ReturnedImageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"></ImageView>
</LinearLayout>
```

为了完善上述示例，以下是`AndroidManifest.xml`的内容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionCode="1"
    android:versionName="1.0"
    package="com.apress.proandroidmedia.ch1.cameraintent">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".CameraIntent"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="4" />
</manifest>
```

在这个例子中，图像是从`Camera`应用通过 Intent 传递的附加数据返回到我们调用 Activity 的`onActivityResult`方法中的。该附加数据的键名为`"data"`，包含一个`Bitmap`对象，需要从通用的`Object`类型进行转换。

```java
// 从 Intent 中获取附加数据
Bundle extras = intent.getExtras();
// 从附加数据中获取返回的图像
Bitmap bmp = (Bitmap) extras.get("data");
```

在我们的布局 XML 文件（`layout/main.xml`）中，有一个`ImageView`。`ImageView`是通用`View`的扩展，支持图像显示。由于我们指定了一个 id 为`ReturnedImageView`的`ImageView`，因此在 Activity 中我们需要获取对它的引用，并通过其`setImageBitmap`方法将获取的`Bitmap`设置给它。这样，我们应用的用户就能查看拍摄的图像了。

要获取`ImageView`对象的引用，我们使用`Activity`类中标准的`findViewById`方法。该方法允许我们通过传递元素的`id`，以编程方式引用在布局 XML（通过`setContentView`加载）中定义的元素。在上述示例中，`ImageView`对象在 XML 中定义如下：

```xml
<ImageView android:id="@+id/ReturnedImageView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"></ImageView>
```

为了引用`ImageView`并让它显示来自`Camera`的`Bitmap`，我们使用以下代码：

```java
imv = (ImageView) findViewById(R.id.ReturnedImageView);
imv.setImageBitmap(bmp);
```

运行此示例时，你可能会注意到生成的图像很小。（在我手机上，它宽 121 像素，高 162 像素。其他设备有不同的默认尺寸。）这并非 bug，而是设计如此。通过 Intent 触发的`Camera`应用不会将全尺寸图像返回给调用 Activity。通常，这样做会占用大量内存，而移动设备在这方面通常受限。相反，`Camera`应用会在返回的 Intent 中附带一张小尺寸的缩略图，如图 1–2 所示。

**图 1–2.** *在 ImageView 中显示的 121x162 像素结果图像*

## 拍摄更大尺寸的图像

为了克服尺寸限制，从 Android 1.5 开始，在大多数设备上，我们可以向启动`Camera`应用的 Intent 中添加一个附加数据。该附加数据的名称在`MediaStore`类中被定义为常量`EXTRA_OUTPUT`。该附加数据的值（附加数据采用键值对形式）会告知`Camera`应用，你希望将拍摄的图像保存到指定的 URI 路径。

以下代码片段指示`Camera`应用将图像以`myfavoritepicture.jpg`的文件名保存到设备的 SD 卡上。

```java
String imageFilePath = Environment.getExternalStorageDirectory().getAbsolutePath()
    + "/myfavoritepicture.jpg";
File imageFile = new File(imageFilePath);
Uri imageFileUri = Uri.fromFile(imageFile);

Intent i = new Intent(android.provider.MediaStore.ACTION_IMAGE_CAPTURE);
i.putExtra(android.provider.MediaStore.EXTRA_OUTPUT, imageFileUri);
startActivityForResult(i, CAMERA_RESULT);
```



