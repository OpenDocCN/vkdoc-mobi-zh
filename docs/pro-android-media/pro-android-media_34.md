# 第 2 章：构建自定义相机应用程序

`public void onCreate(Bundle savedInstanceState) {`

```
...

cameraView.setFocusable(true);

cameraView.setFocusableInTouchMode(true);

cameraView.setClickable(true);

cameraView.setOnClickListener(this);

}
```

`public void onClick(View v) {`

```
camera.takePicture(null, null, null, this);
```

`}`

## 其他相机回调方法

除了 `Camera.PictureCallback`，还有几个值得一提的回调方法。

`Camera.PreviewCallback`：定义了一个方法 `onPreviewFrame(byte[] data, Camera camera)`，当预览帧可用时被调用。可能会传入一个包含当前图像像素的字节数组。在 `Camera` 对象上，有三种不同的方式可以使用此回调。

- `setPreviewCallback(Camera.PreviewCallback)`：通过此方法注册一个 `Camera.PreviewCallback`，可确保每当新的预览帧可用并显示在屏幕上时，都会调用 `onPreviewFrame` 方法。传入 `onPreviewFrame` 方法的数据字节数组很可能采用 YUV 格式。不幸的是，Android 2.2 是第一个拥有 YUV 格式解码器（`YuvImage`）的版本；在之前的版本中，解码必须手动完成。

- `setOneShotPreviewCallback(Camera.PreviewCallback)`：在 `Camera` 对象上通过此方法注册 `Camera.PreviewCallback`，会导致 `onPreviewFrame` 在下一次预览图像可用时被调用一次。同样，传递给 `onPreviewFrame` 方法的预览图像数据很可能采用 YUV 格式。这可以通过将 `Camera.getParameters().getPreviewFormat()` 的结果与 `ImageFormat` 中的常量进行比较来确定。

- `setPreviewCallbackWithBuffer(Camera.PreviewCallback)`：此方法在 Android 2.2 中引入，其工作方式与普通的 `setPreviewCallback` 相同，但要求我们指定一个字节数组作为预览图像数据的缓冲区。这样做是为了让我们能够更好地管理处理预览图像时将要使用的内存。

`Camera.AutoFocusCallback`：定义了一个方法 `onAutoFocus`，当自动对焦活动完成时调用。自动对焦可以通过调用 `Camera` 对象上的 `autoFocus` 方法来触发，并传入此回调接口的一个实例。

`Camera.ErrorCallback`：定义了一个 `onError` 方法，当发生 `Camera` 错误时调用。有两个常量可以与传入的错误码进行比较：`CAMERA_ERROR_UNKNOWN` 和 `CAMERA_ERROR_SERVER_DIED`。

`Camera.OnZoomChangeListener`：定义了一个方法 `onZoomChange`，当“平滑变焦”（缓慢放大或缩小）正在进行或完成时调用。这个类和方法是在 Android 2.2（API 级别 8）中引入的。

`Camera.ShutterCallback`：定义了一个方法 `onShutter`，在图像被捕获的那一刻调用。

## 整合所有内容

让我们完整地过一遍这个示例。以下代码是为在 Android 2.2 及更高版本上运行而编写的，但稍作修改，它也应该能在 1.6 及更高版本上运行。需要高于 1.6 版本的章节已用注释标出。

```
package com.apress.proandroidmedia.ch2.snapshot;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Iterator;
import java.util.List;

import android.app.Activity;
import android.content.ContentValues;
import android.content.res.Configuration;
import android.hardware.Camera;
import android.net.Uri;
import android.os.Bundle;
import android.provider.MediaStore.Images.Media;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Toast;

public class SnapShot extends Activity implements OnClickListener,
    SurfaceHolder.Callback, Camera.PictureCallback {

    SurfaceView cameraView;
    SurfaceHolder surfaceHolder;
    Camera camera;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        cameraView = (SurfaceView) this.findViewById(R.id.CameraView);
        surfaceHolder = cameraView.getHolder();
        surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
        surfaceHolder.addCallback(this);

        cameraView.setFocusable(true);
        cameraView.setFocusableInTouchMode(true);
        cameraView.setClickable(true);
        cameraView.setOnClickListener(this);
    }

    public void onClick(View v) {
        camera.takePicture(null, null, this);
    }
```

接下来，我们按照前面描述的那样添加 `onPictureTaken` 方法。

```
    public void onPictureTaken(byte[] data, Camera camera) {
        Uri imageFileUri =
                getContentResolver().insert(Media.EXTERNAL_CONTENT_URI, new ContentValues());

        try {
            OutputStream imageFileOS =
                    getContentResolver().openOutputStream(imageFileUri);
            imageFileOS.write(data);
            imageFileOS.flush();
            imageFileOS.close();
        } catch (FileNotFoundException e) {
            Toast t = Toast.makeText(this, e.getMessage(), Toast.LENGTH_SHORT);
            t.show();
        } catch (IOException e) {
            Toast t = Toast.makeText(this, e.getMessage(), Toast.LENGTH_SHORT);
            t.show();
        }

        camera.startPreview();
    }
```

最后，我们需要各种 `SurfaceHolder.Callback` 方法，在其中设置 `Camera` 对象。

```
    public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {
        camera.startPreview();
    }

    public void surfaceCreated(SurfaceHolder holder) {
        camera = Camera.open();

        try {
            camera.setPreviewDisplay(holder);
            Camera.Parameters parameters = camera.getParameters();

            if (this.getResources().getConfiguration().orientation !=
                    Configuration.ORIENTATION_LANDSCAPE) {
                parameters.set("orientation", "portrait");
                // 适用于 Android 2.2 及以上版本
                camera.setDisplayOrientation(90);
                // 适用于 Android 2.0 及以上版本
                parameters.setRotation(90);
            }

            // 特效适用于 Android 2.0 及以上版本
            List<String> colorEffects = parameters.getSupportedColorEffects();
            Iterator<String> cei = colorEffects.iterator();

            while (cei.hasNext()) {
                String currentEffect = cei.next();
                if (currentEffect.equals(Camera.Parameters.EFFECT_SOLARIZE)) {
                    parameters.setColorEffect(Camera.Parameters.EFFECT_SOLARIZE);
                    break;
                }
            }
            // 特效部分结束（适用于 Android 2.0 及以上版本）

            camera.setParameters(parameters);
        } catch (IOException exception) {
            camera.release();
        }
    }

    public void surfaceDestroyed(SurfaceHolder holder) {
        camera.stopPreview();
        camera.release();
    }

} // 活动结束
```

这样就完成了我们的 `SnapShot` 活动。以下是其所使用的布局 XML 文件。它应位于 `res/layout/main.xml`。

```
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

最后，我们需要将 `CAMERA` 权限添加到 `AndroidManifest.xml` 文件中。以下是完整的清单文件。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch2.snapshot"
    android:versionCode="1"
    android:versionName="1.0">

    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name">
        <activity
            android:name=".SnapShot"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```



