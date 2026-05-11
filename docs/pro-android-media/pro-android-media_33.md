# 第 2 章：构建自定义相机应用

此外，我们还需要告诉相机预览界面的 `SurfaceView` 对象 `cameraView`，使其也按照该尺寸显示。如果不这样做，`SurfaceView` 将不会更改尺寸，相机预览图像会出现变形或画质极低的情况。

```
if (bestHeight != 0 && bestWidth != 0) {
    Log.v("SNAPSHOT", "Using " + bestWidth + " x " + bestHeight);
    parameters.setPreviewSize(bestWidth, bestHeight);
    cameraView.setLayoutParams(new LinearLayout.LayoutParams(bestWidth, bestHeight));
}
```

```
camera.setParameters(parameters);
```

设置完参数后，剩下要做的就是关闭 `surfaceCreated` 方法。

```
} catch (IOException exception) {
    camera.release();
}
```

**图 2–4.** 预览尺寸较小的相机预览画面

## 拍摄并保存图像

要使用 `Camera` 类拍摄图像，我们必须调用 `takePicture` 方法。该方法需要三个或四个参数，它们都是 `Callback` 方法。`takePicture` 方法最简单的形式是将所有参数都设为 `null`。

遗憾的是，虽然可以拍摄照片，但无法获得对其的任何引用。因此，至少应实现其中一个回调方法。最安全的是 `Camera.PictureCallback.onPictureTaken`。该方法保证会被调用，并且会在压缩图像可用时被调用。为了利用这一点，我们将让 Activity 实现 `Camera.PictureCallback` 并添加一个 `onPictureTaken` 方法。

```
public class SnapShot extends Activity implements
    SurfaceHolder.Callback, Camera.PictureCallback {
    public void onPictureTaken(byte[] data, Camera camera) {
    }
}
```

**第 2 章：构建自定义相机应用** **33**

`onPictureTaken` 方法有两个参数；第一个是实际 JPEG 图像数据的字节数组。第二个是对拍摄图像的 `Camera` 对象的引用。

由于我们获得了实际的 JPEG 数据，因此只需将其写入磁盘上的某个位置即可保存。众所周知，利用 `MediaStore` 来指定其位置和元数据是一个好主意。

当我们的 `onPictureTaken` 方法被调用时，我们可以调用 `Camera` 对象的 `startPreview` 方法。当调用 `takePicture` 方法时，预览已自动暂停，而该方法表明现在可以安全地重新启动预览。

```
public void onPictureTaken(byte[] data, Camera camera) {
    Uri imageFileUri = getContentResolver().insert(
        Media.EXTERNAL_CONTENT_URI, new ContentValues());
    try {
        OutputStream imageFileOS = getContentResolver()
            .openOutputStream(imageFileUri);
        imageFileOS.write(data);
        imageFileOS.flush();
        imageFileOS.close();
    } catch (FileNotFoundException e) {
    } catch (IOException e) {
    }
    camera.startPreview();
}
```

在上述代码片段中，我们向 `MediaStore` 插入了一条新记录，并返回了一个 URI。我们可以随后使用这个 URI 获取一个 `OutputStream`，用于写入 JPEG 数据。这会在 `MediaStore` 指定的位置创建一个文件，并将其链接到新记录。

如果我们希望稍后更新存储在 `MediaStore` 记录中的元数据，可以按照第 1 章所述，使用新的 `ContentValues` 对象来更新记录。

```
ContentValues contentValues = new ContentValues(3);
contentValues.put(Media.DISPLAY_NAME, "This is a test title");
contentValues.put(Media.DESCRIPTION, "This is a test description");
getContentResolver().update(imageFileUri, contentValues, null, null);
```

最后，我们需要实际调用 `Camera.takePicture`。为此，我们让预览屏幕变得“可点击”，并在 `onClick` 方法中拍照。

我们将让 Activity 实现 `OnClickListener`，并将 `SurfaceView` 的 `onClickListener` 设置为 Activity 本身。然后，我们使用 `setClickable(true)` 让 `SurfaceView` 变得“可点击”。此外，我们还需要使 `SurfaceView` 变得“可获得焦点”。默认情况下，`SurfaceView` 不可获得焦点，因此我们必须使用 `setFocusable(true)` 进行显式设置。同样，在“触摸模式”下，焦点通常被禁用，因此我们还需要使用 `setFocusInTouchMode(true)` 显式地避免这种情况。

```
public class SnapShot extends Activity implements OnClickListener,
    SurfaceHolder.Callback, Camera.PictureCallback {
    ...
}
```

**34**



