# 第一章：Android 成像入门

**图 1–3.** *最终在 `ImageView` 中显示的全屏图像*

## 图像存储与元数据

Android 提供了一种在应用间共享数据的标准方式。负责此功能的类称为**内容提供器**。内容提供器为存储和检索各类数据提供了标准接口。

图像（以及音频和视频）的标准内容提供器是 `MediaStore`。

`MediaStore` 允许将文件设置在设备的标准位置，并具备存储和检索文件元数据的功能。元数据是关于数据的数据；它可以包含文件本身的数据信息，例如文件大小和名称，但 `MediaStore` 还允许设置多种附加数据，例如标题、描述、经纬度等。

要开始使用 `MediaStore`，让我们修改 `SizedCameraIntent` 活动，使其使用 `MediaStore` 进行图像存储和元数据关联，而不是将图像存储在 SD 卡的任意文件中。

### 获取图像的 URI

要获取图像存储的标准位置，首先需要获得对 `MediaStore` 的引用。为此，我们使用**内容解析器**。内容解析器是访问内容提供器（`MediaStore` 即为此类提供器）的途径。

通过传递特定的 URI，内容解析器即可知道将 `MediaStore` 作为内容提供器进行接口提供。由于我们要插入一张新图像，使用的是 `insert` 方法，而应使用的 URI 包含在 `android.provider.MediaStore.Images.Media` 类的一个常量中，名为 `EXTERNAL_CONTENT_URI`。这表示我们希望将图像存储在设备的主外部存储卷上，通常是 SD 卡。如果希望将其存储在设备的内部存储器中，可以使用 `INTERNAL_CONTENT_URI`。不过，通常对于图像、音频和视频等可能体积较大的媒体文件，建议使用 `EXTERNAL_CONTENT_URI`。

前面所示的 `insert` 调用会返回一个 URI，我们可以用它将图像文件的二进制数据写入。在我们的案例中，与在 `CameraActivity` 中所做的一致，我们只需将其作为附加信息传递给触发相机应用的 Intent。

```
Uri imageFileUri = getContentResolver().insert(
    Media.EXTERNAL_CONTENT_URI, new ContentValues());
Intent i = new Intent(android.provider.MediaStore.ACTION_IMAGE_CAPTURE);
i.putExtra(android.provider.MediaStore.EXTRA_OUTPUT, imageFileUri);
startActivityForResult(i, CAMERA_RESULT);
```

你会注意到我们还传递了一个新的 `ContentValues` 对象。`ContentValues` 对象是我们希望在创建记录时关联的元数据。在上述示例中，我们传递了一个空的 `ContentValues` 对象。

### 预填充关联元数据

如果我们希望预填充元数据，可以使用 `put` 方法向其中添加一些数据。`ContentValues` 以键值对的形式接收数据。这些键名是标准的，并在 `android.provider.MediaStore.Images.Media` 类中定义为常量。（部分常量实际上位于 `Media` 类所实现的 `android.provider.MediaStore.MediaColumns` 接口中。）

```
// 在 ContentValues 映射中保存图像的名称和描述。
ContentValues contentValues = new ContentValues(3);
contentValues.put(Media.DISPLAY_NAME, "这是一个测试标题");
contentValues.put(Media.DESCRIPTION, "这是一个测试描述");
contentValues.put(Media.MIME_TYPE, "image/jpeg");

// 添加一条不包含位图但设置了部分值的新记录。
// insert() 返回新记录的 URI。
Uri imageFileUri = getContentResolver().insert(Media.EXTERNAL_CONTENT_URI,
    contentValues);
```

同样，此调用返回的 URI 可以传递给相机应用（通过 Intent），以指定图像保存的位置。

如果通过 `Log` 命令输出此 URI，它看起来会像这样：

```
content://media/external/images/media/16
```

你可能首先注意到它看起来像常规 URL（例如在浏览器中使用的 URL）；但不同于以用于传输网页的 `http` 等协议开头，它是以 `content` 开头。在 Android 中，当 URI 以 `content` 开头时，表示该 URI 用于内容提供器（如 `MediaStore`）。

### 检索已保存的图像

之前用于保存图像的同一 URI 也可以用作访问图像的途径。我们无需将文件的完整路径传递给 `BitmapFactory`，而是可以通过内容解析器为图像打开一个 `InputStream`，并将其传递给 `BitmapFactory`。

```
Bitmap bmp = BitmapFactory.decodeStream(
    getContentResolver().openInputStream(imageFileUri), null, bmpFactoryOptions);
```

### 后续添加元数据

如果我们希望在将图像捕获到 `MediaStore` 后关联更多元数据，可以使用内容解析器的 `update` 方法。这与之前使用的 `insert` 方法非常相似，只不过我们是直接通过图像的 URI 来访问图像文件。

```
// 更新记录的标题和描述
ContentValues contentValues = new ContentValues(3);
contentValues.put(Media.DISPLAY_NAME, "这是一个测试标题");
contentValues.put(Media.DESCRIPTION, "这是一个测试描述");
getContentResolver().update(imageFileUri,contentValues,null,null);
```

### 更新我们的 `CameraActivity`，使用 `MediaStore` 进行图像存储并关联元数据

以下是我们之前示例的更新版，它将图像保存到 `MediaStore` 中，并让我们有机会添加标题和描述。此外，此版本包含多个 UI 元素，其可见性会根据用户在应用中的进度进行管理。

```
package com.apress.proandroidmedia.ch1.mediastorecameraintent;

import java.io.FileNotFoundException;
import android.app.Activity;
import android.content.Intent;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;
import android.provider.MediaStore.Images.Media;
import android.content.ContentValues;

public class MediaStoreCameraIntent extends Activity {

    final static int CAMERA_RESULT = 0;
    Uri imageFileUri;

    // 用户界面元素，在 res/layout/main.xml 中定义
    ImageView returnedImageView;
    Button takePictureButton;
    Button saveDataButton;
    TextView titleTextView;
    TextView descriptionTextView;
    EditText titleEditText;
    EditText descriptionEditText;
```

我们包含了一些 UI 元素。它们按照常规方式在 `layout/main.xml` 中指定，其对象已在上述代码中声明。

```
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);

        // 将内容视图设置为 res/layout/main.xml 文件中定义的内容
        setContentView(R.layout.main);

        // 获取 UI 元素的引用
        returnedImageView = (ImageView) findViewById(R.id.ReturnedImageView);
        takePictureButton = (Button) findViewById(R.id.TakePictureButton);
        saveDataButton = (Button) findViewById(R.id.SaveDataButton);
        titleTextView = (TextView) findViewById(R.id.TitleTextView);
        descriptionTextView = (TextView) findViewById(R.id.DescriptionTextView);
        titleEditText = (EditText) findViewById(R.id.TitleEditText);
        descriptionEditText = (EditText) findViewById(R.id.DescriptionEditText);
```



在标准的 `Activity` 的 `onCreate` 方法中，在调用 `setContentView` 之后，我们实例化在代码中需要控制的用户界面元素。在通过 `findViewById` 方法获取它们后，我们必须将每个元素强制转换为适当的类型。

```java
// Set all except takePictureButton to not be visible initially
// View.GONE is invisible and doesn't take up space in the layout
returnedImageView.setVisibility(View.GONE);
saveDataButton.setVisibility(View.GONE);
titleTextView.setVisibility(View.GONE);
descriptionTextView.setVisibility(View.GONE);
titleEditText.setVisibility(View.GONE);
descriptionEditText.setVisibility(View.GONE);
```

## 第 1 章：Android 图像处理介绍

继续，我们将所有用户界面元素设置为不可见且不在布局中占据空间。`View.GONE` 是可以用于 `setVisibility` 方法来实现此目的的常量。另一个选项 `View.INVISIBLE` 隐藏它们，但它们仍然在布局中占据空间。

```java
// When the Take Picture Button is clicked
takePictureButton.setOnClickListener(new OnClickListener() {
    public void onClick(View v)
    {
        // Add a new record without the bitmap
        // returns the URI of the new record
        imageFileUri = getContentResolver().insert(Media.EXTERNAL_CONTENT_URI,
                new ContentValues());

        // Start the Camera App
        Intent i = new Intent(android.provider.MediaStore.ACTION_IMAGE_CAPTURE);
        i.putExtra(android.provider.MediaStore.EXTRA_OUTPUT, imageFileUri);
        startActivityForResult(i, CAMERA_RESULT);
    }
});
```

在 `takePictureButton` 的 `OnClickListener` 中，我们创建内置相机的标准 `Intent` 并调用 `startActivityForResult`。在此处执行此操作，而不是直接在 `onCreate` 方法中执行，可以带来稍微更好的用户体验。

```java
saveDataButton.setOnClickListener(new OnClickListener() {
    public void onClick(View v)
    {
        // Update the MediaStore record with Title and Description
        ContentValues contentValues = new ContentValues(3);
        contentValues.put(Media.DISPLAY_NAME,
                titleEditText.getText().toString());
        contentValues.put(Media.DESCRIPTION,
                descriptionEditText.getText().toString());
        getContentResolver().update(imageFileUri,contentValues,null,null);

        // Tell the user
        Toast bread = Toast.makeText(MediaStoreCameraIntent.this, "Record Updated", Toast.LENGTH_SHORT);
        bread.show();

        // Go back to the initial state, set Take Picture Button Visible
        // hide other UI elements
        takePictureButton.setVisibility(View.VISIBLE);
        returnedImageView.setVisibility(View.GONE);
        saveDataButton.setVisibility(View.GONE);
        titleTextView.setVisibility(View.GONE);
        descriptionTextView.setVisibility(View.GONE);
        titleEditText.setVisibility(View.GONE);
        descriptionEditText.setVisibility(View.GONE);
    }
});
```

## 第 1 章：Android 图像处理介绍

`saveDataButton` 的 `OnClickListener` 在相机应用程序返回图像后变为可见，它负责将元数据与图像关联起来。它获取用户在各个 `EditText` 元素中键入的值，并创建一个 `ContentValues` 对象，用于更新 `MediaStore` 中此图像的记录。

```java
protected void onActivityResult(int requestCode, int resultCode, Intent intent)
{
    super.onActivityResult(requestCode, resultCode, intent);
    if (resultCode == RESULT_OK)
    {
        // The Camera App has returned
        // Hide the Take Picture Button
        takePictureButton.setVisibility(View.GONE);

        // Show the other UI Elements
        saveDataButton.setVisibility(View.VISIBLE);
        returnedImageView.setVisibility(View.VISIBLE);
        titleTextView.setVisibility(View.VISIBLE);
        descriptionTextView.setVisibility(View.VISIBLE);
        titleEditText.setVisibility(View.VISIBLE);
        descriptionEditText.setVisibility(View.VISIBLE);

        // Scale the image
        int dw = 200; // Make it at most 200 pixels wide
        int dh = 200; // Make it at most 200 pixels tall
        try
        {
            // Load up the image's dimensions not the image itself
            BitmapFactory.Options bmpFactoryOptions = new BitmapFactory.Options();
            bmpFactoryOptions.inJustDecodeBounds = true;
            Bitmap bmp = BitmapFactory.decodeStream(getContentResolver().
                    openInputStream(imageFileUri), null, bmpFactoryOptions);

            int heightRatio = (int)Math.ceil(bmpFactoryOptions.outHeight/(float)dh);
            int widthRatio = (int)Math.ceil(bmpFactoryOptions.outWidth/(float)dw);

            Log.v("HEIGHTRATIO",""+heightRatio);
            Log.v("WIDTHRATIO",""+widthRatio);

            // If both of the ratios are greater than 1,
            // one of the sides of the image is greater than the screen
            if (heightRatio > 1 && widthRatio > 1)
            {
                if (heightRatio > widthRatio)
                {
                    // Height ratio is larger, scale according to it
                    bmpFactoryOptions.inSampleSize = heightRatio;
                }
                else
                {
                    // Width ratio is larger, scale according to it
                    bmpFactoryOptions.inSampleSize = widthRatio;
                }
            }
        }
    }
}
```



