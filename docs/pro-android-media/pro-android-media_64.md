# 第 11 章：视频捕获

如前所述，如果只需录制视频，或希望为用户提供相机应用中的所有控制选项，使用 Intent 触发该功能是一种绝佳方式。

## 添加视频元数据

如第 9 章所述，Android 的 `MediaStore` 内容提供器包含一个专门用于视频的模块 `MediaStore.Video`，此外还包括我们之前探讨过的图片和音频文件及其元数据模块。

通过 Intent 触发相机应用时，返回的新录制视频文件的 `Uri` 是 `content://` 风格的 `Uri`，需要与内容提供器（此处为 `MediaStore`）配合使用。要添加额外元数据，我们可以利用返回的 `Uri` 更新 `MediaStore` 中的视频记录。

与其他内容提供器一样，我们通过从 `Context` 获取的 `ContentResolver` 对象调用 `update` 方法。传入 `content://` 风格的 `Uri` 以及由 `ContentValues` 对象封装的新数据。由于我们已获取指向特定记录的 `Uri`，因此无需为最后两个参数（SQL 风格的 `WHERE` 子句及其参数）指定内容。

`ContentValues` 对象包含键值对，其中键为 `MediaStore.Video` 专用的列名。可用列名以常量形式定义在 `MediaStore.Video.Media` 中，大部分继承自 `android.provider.BaseColumns`、`MediaStore.MediaColumns` 和 `MediaStore.Video.VideoColumns`。

```
ContentValues values = new ContentValues(1);
values.put(MediaStore.MediaColumns.TITLE, titleEditText.getText().toString());
int numRecordsUpdated = getContentResolver().update(videoFileUri, values, null, null);
```

以下是对先前 `VideoCaptureIntent` 示例的改进，允许用户为新拍摄的视频关联标题。

```
package com.apress.proandroidmedia.ch11.videocaptureintent;

import android.app.Activity;
import android.content.ContentValues;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.provider.MediaStore;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class VideoCaptureIntent extends Activity implements OnClickListener {

    public static int VIDEO_CAPTURED = 1;

    Button captureVideoButton;
    Button playVideoButton;
    Button saveVideoButton;
```

该版本将包含一个 `EditText` 对象，用于让用户输入视频标题。

```
    EditText titleEditText;
    Uri videoFileUri;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        captureVideoButton = (Button) this.findViewById(R.id.CaptureVideoButton);
        playVideoButton = (Button) this.findViewById(R.id.PlayVideoButton);
```

我们还将添加一个 `saveVideoButton`，点击时会触发 `MediaStore` 中记录的更新。

```
        saveVideoButton = (Button) this.findViewById(R.id.SaveVideoButton);
        titleEditText = (EditText) this.findViewById(R.id.TitleEditText);

        captureVideoButton.setOnClickListener(this);
        playVideoButton.setOnClickListener(this);
        saveVideoButton.setOnClickListener(this);

        playVideoButton.setEnabled(false);
        saveVideoButton.setEnabled(false);
        titleEditText.setEnabled(false);
    }
```

当任意按钮被点击时触发的 `onClick` 方法执行了大部分操作。点击 `captureVideoButton` 时，通过 Intent 触发系统内置相机应用；点击 `playVideoButton` 时，通过 Intent 触发系统内置媒体播放器应用（而非像之前那样使用 `VideoView`）；最后点击 `saveVideoButton` 时，更新 `MediaStore` 中的视频文件记录。

```
    public void onClick(View v) {
        if (v == captureVideoButton) {
            Intent captureVideoIntent = new Intent(android.provider.MediaStore.ACTION_VIDEO_CAPTURE);
            startActivityForResult(captureVideoIntent, VIDEO_CAPTURED);
        } else if (v == playVideoButton) {
            Intent playVideoIntent = new Intent(Intent.ACTION_VIEW, videoFileUri);
            startActivity(playVideoIntent);
        } else if (v == saveVideoButton) {
```

首先，我们创建一个 `ContentValues` 对象，并用用户在 `EditText` 中输入的文本填充它。

```
            ContentValues values = new ContentValues(1);
            values.put(MediaStore.MediaColumns.TITLE, titleEditText.getText().toString());
```



## 第 11 章：视频捕捉

然后，我们在 `ContentResolver` 对象上调用 `update` 方法，传入捕获到的视频的 `Uri` 以及 `ContentValues` 对象。

```
if (getContentResolver().update(videoFileUri, values, null, null) == 1) {
```

如果 `update` 方法的结果为 1，则表示操作成功；有一行数据被更新了，我们将通知用户。

```
Toast t = Toast.makeText(this, "已更新 " + titleEditText.getText().toString(), Toast.LENGTH_SHORT);
t.show();
}
else {
```

如果结果不是 1，我们将告知用户发生了错误。

```
Toast t = Toast.makeText(this, "错误", Toast.LENGTH_SHORT);
t.show();
}
}
}
```

```
protected void onActivityResult (int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == VIDEO_CAPTURED) {
        videoFileUri = data.getData();
        playVideoButton.setEnabled(true);
        saveVideoButton.setEnabled(true);
        titleEditText.setEnabled(true);
    }
}
}
```

以下是由前述 Activity 引用的布局 XML，位于 `res/layout/main.xml` 中。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
>
    <Button android:text="拍摄视频" android:id="@+id/CaptureVideoButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <Button android:text="播放视频" android:id="@+id/PlayVideoButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <TextView android:text="标题:" android:id="@+id/TitleTextView"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></TextView>
    <EditText android:text="" android:id="@+id/TitleEditText"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></EditText>
    <Button android:text="保存元数据" android:id="@+id/SaveVideoButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
</LinearLayout>
```

## 第 11 章：视频捕捉

前面的示例展示了在 Android 中使用视频的许多方面是多么直接，尤其是当使用 Intent 进行视频捕获并依赖 `MediaStore` 处理元数据时。

需要注意的是，当更新 `MediaStore` 中的元数据时，视频文件本身的数据并不会被更新；相反，数据仅存储在与该视频相关的 `MediaStore` 记录中。Android SDK 并未提供内置类来直接修改媒体文件的元数据。

## 自定义视频捕捉

对于图像和音频，有多种捕获方式。视频捕获也不例外；虽然我们没有像处理音频那样多的选项，但我们有能力使用 `MediaRecorder` 类创建一个自定义视频捕获示例。

在许多方面，构建自定义视频捕获应用程序类似于构建自定义相机应用程序与自定义音频录制应用程序的结合。我们必须创建一个 `SurfaceView`，用于相机绘制预览或取景器图像，就像我们在第 2 章的自定义相机示例中所做的那样，同时我们将使用 `MediaRecorder` 进行实际录制，就像我们在第 7 章音频捕获中所做的那样。

### 用于视频的 MediaRecorder

要使用 `MediaRecorder` 进行视频捕获，我们必须遵循音频捕获的所有步骤，并加上一些视频特有的步骤。此外，`MediaRecorder` 是一个状态机，因此我们必须遵循特定的步骤顺序，从实例化到录制。

首先，我们实例化它，然后按顺序执行这些步骤。

```java
MediaRecorder recorder = new MediaRecorder();
```

#### 音频和视频源

实例化之后，我们可以设置音频和视频源。要设置音频源，我们使用 `setAudioSource` 方法，传入一个代表我们想要使用的源的常量。

```java
recorder.setAudioSource(MediaRecorder.AudioSource.DEFAULT);
```

可能的音频源值是在 `MediaRecorder.AudioSource` 类中定义的常量：

- `CAMCORDER`：如果设备为不同的摄像头（前置、后置）配备了不同的麦克风，使用此值将指定使用合适的麦克风（Android 2.1/API 级别 7 或更高）。



## 第十一章：视频捕获

- `DEFAULT`：指定使用设备上的默认麦克风。
- `MIC`：指定使用专为视频录制设计的普通麦克风。
- `VOICE_CALL`：指定音频应来源于正在进行的通话。目前，大多数手机（甚至全部）均不支持此选项。
- `VOICE_DOWNLINK`：指定音频应来源于通话，具体是传入的音频（对方的语音）。目前，大多数手机（甚至全部）均不支持此选项。
- `VOICE_UPLINK`：指定音频应来源于通话，具体是传出的音频（手机发送的语音）。目前，大多数手机（甚至全部）均不支持此选项。
- `VOICE_RECOGNITION`：指定音频应来源于为手机语音识别功能设置的麦克风。如果未指定此类麦克风，则将使用`DEFAULT`麦克风。

可能的视频源值定义在`MediaRecorder.VideoSource`类中，该类仅包含两个常量：

- `CAMERA`
- `DEFAULT`

两者表示相同含义，即应使用设备的主摄像头进行视频录制。

要设置视频源，我们使用`setVideoSource`方法：

```java
recorder.setVideoSource(MediaRecorder.VideoSource.DEFAULT);
```

**注意：** 按理说，在`MediaRecorder`的`setVideoSource`方法中使用`MediaRecorder.VideoSource.DEFAULT`，或在`MediaRecorder`的`setAudioSource`方法中使用`MediaRecorder.AudioSource.DEFAULT`，与不调用这些方法的结果相同。毕竟它们都是*默认*值。然而事实恰恰相反，必须调用每个方法才能捕获相应数据。换句话说，如果未设置音频源，则不会录制音频；如果未设置视频源，则不会录制视频。

### 输出格式

设置音频和视频源之后，我们可以使用`MediaRecorder`的`setOutputFormat`方法设置输出格式，并传入要使用的格式。

```java
recorder.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
```

可能的格式作为常量列在`MediaRecorder.OutputFormat`类中。

- `DEFAULT`：指定使用默认输出格式。该格式*可能*因设备而异。在运行 Android 2.2 的 Nexus 1 上，默认格式为 MPEG-4，与使用`MPEG_4`常量时的格式相同。
- `MPEG_4`：指定将音频和视频捕获为 MPEG-4 文件格式。该文件扩展名为`.mp4`。MPEG-4 文件通常包含 H.264、H.263 或 MPEG-4 Part 2 编码的视频，以及 AAC 或 MP3 编码的音频（也可能使用其他编解码器）。MPEG-4 文件广泛用于 Flash、iPod 以及许多其他在线视频技术和消费电子设备中。
- `RAW_AMR`：此设置仅用于音频录制，不适用于视频。
- `THREE_GPP`：指定将音频和视频捕获为 3GP 文件格式。该文件扩展名为`.3gp`。3GPP 文件通常包含 H.264、MPEG-4 Part 2 或 H.263 编解码器编码的视频，以及 AMR 或 AAC 编解码器编码的音频。

### 音频与视频编码器

设置输出格式后，我们应指定要使用的音频和视频编码器。使用`MediaRecorder`的`setVideoEncoder`方法，我们可以指定要使用的视频编解码器：

```java
recorder.setVideoEncoder(MediaRecorder.VideoEncoder.DEFAULT);
```

可传入`setVideoEncoder`的常量值定义在`MediaRecorder.VideoEncoder`中：

- `DEFAULT`：这是一个设备相关的设置，指定使用设备默认的编解码器。在许多情况下，该编解码器为 H.263，因为它是 Android 唯一要求支持的编解码器。
- `H263`：指定使用 H.263 编解码器。H.263 于 1995 年发布，专为低比特率视频传输而开发，是 Flash 和 RealPlayer 等许多早期互联网视频技术的基础。Android 要求支持该编解码器进行编码，因此可以确保其可用。
- `H264`：指定使用 H.264 编解码器（也称为 MPEG-4 Part 10 或 AVC，Advanced Video Coding）。H.264 是一种先进的编解码器，广泛应用于从蓝光到 Flash 的各类技术中，于 2003 年发布。大多数 Android 设备支持该编解码器用于视频播放。支持将其用于视频编码的设备则较少。



## 第 11 章：视频捕获

`MPEG_4_SP`：这指定所使用的编解码器将是 MPEG-4 SP（简单画质）编解码器。MPEG-4 SP 在技术上是 MPEG-4 第 2 部分简单画质。它于 1999 年发布，是为需要低比特率视频且无需大量处理器功耗的技术而开发的。

使用 `MediaRecorder` 的 `setAudioEncoder` 方法，我们可以指定将使用的音频编解码器。

```
recorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
```

可以传递给 `setAudioEncoder` 的可能值在 `MediaRecorder.AudioEncoder` 中被定义为常量。

`MediaRecorder.AudioEncoder` 仅包含两个常量：

`AMR_NB`：这指定将用于音频编码的编解码器是 AMR-NB，即自适应多速率窄带（Adaptive Multi-Rate Narrow Band）。AMR-NB 是一种针对语音调校的编解码器，广泛应用于手机中。

`DEFAULT`：由于 AMR-NB 是仅有的另一个选择，`DEFAULT` 常量指定 AMR-NB 将是所使用的音频编解码器。

### 音视频比特率

视频编码比特率可以通过在 `MediaRecorder` 上调用 `setVideoEncodingBitRate` 方法并传入以比特每秒为单位的请求比特率来设置。视频的低比特率设置范围约为每秒 256,000 比特（256 kbps），而高比特率范围约为每秒 3,000,000 比特（3 mbps）。

```
recorder.setVideoEncodingBitRate(150000);
```

我们也可以指定希望用于编码音频数据的最大比特率。通过将以比特每秒为单位的数值传入 `MediaRecorder` 的 `setAudioEncodingBitRate` 方法来实现。作为参考，每秒 8,000 比特（8 kbps）是非常低的比特率，适用于需要通过慢速网络实时传输的音频；而对于 MP3 文件中的音乐，每秒 196,000 比特（196 kbps）及更高的速率并不罕见。目前大多数安卓设备仅支持较低端的比特率，如果你选择了一个过高的比特率，`MediaRecorder` 将自动在其可支持的范围内选择一个比特率。

```
recorder.setAudioEncodingBitRate(8000);
```

### 音频采样率

与比特率一样，音频采样率对于决定捕获和编码的音频质量至关重要。`MediaPlayer` 有一个 `setAudioSampleRate` 方法，允许我们请求特定的采样率。传入的采样率以 Hz（赫兹）为单位，表示每秒的样本数。采样率越高，捕获文件中能表示的音频频率范围就越大。较低的采样率（如 8,000 Hz）适合捕获低质量语音，而较高的采样率（如 48,000 Hz）则用于 DVD 和许多其他高质量视频格式。大多数安卓设备仅支持较低的采样率范围（8,000 Hz）。

```
recorder.setAudioSamplingRate(8000);
```

### 音频声道数

可以通过使用 `setAudioChannels` 方法并传入声道数来指定要捕获的音频声道数量。在大多数安卓设备上，输入音频目前主要限于单声道麦克风，因此使用超过一个声道并无益处。通常，声道数的选择在用于单声道的单声道和用于立体声的双声道之间。

```
recorder.setAudioChannels(1);
```

### 视频帧率

每秒捕获的视频帧数可以通过使用 `setVideoFrameRate` 方法并传入请求的帧率来控制。每秒 12 到 15 帧的值通常足以表现运动。较高标准则是电视采用的每秒 30 帧（实际为 29.97）。实际使用的帧率会根据设备的性能而变化。

```
recorder.setVideoFrameRate(15);
```

### 视频尺寸

捕获视频的宽度和高度可以通过 `setVideoSize` 方法并传入以像素为单位的宽度和高度整数来控制。标准尺寸范围从 176x144 到 640x480，许多设备甚至支持更高分辨率。

**注意：** 许多安卓设备也支持 720x480，但应注意，这与 DV 摄像机捕获的像素宽高比不同，后者捕获的是 *矩形* 像素的 720x480 视频。在安卓设备上，像素通常是 *方形* 的，因此如果来自安卓设备的 720x480 视频被导入到视频编辑软件中，该软件可能因其分辨率而对视频做出错误假设，导致输出变形。要纠正这一点，需要告知软件像素宽高比是方形或 1.0。

```
recorder.setVideoSize(640,480);
```



### 最大文件大小

通过向`setMaxFileSize`方法传入以字节为单位的最大值，可以指定`MediaRecorder`捕获文件的最大大小。

```
recorder.setMaxFileSize(10000000); // 10 兆字节
```

要确定何时达到最大文件大小，我们需要在活动（Activity）中实现`MediaRecorder.OnInfoListener`接口并将其注册到`MediaRecorder`。之后，`onInfo`方法将被调用，并且可以将`what`参数与`MediaRecorder.MEDIA_RECORDER_INFO_FILESIZE_REACHED`常量进行比较。如果它们匹配，则表示达到了最大文件大小。

根据文档，当达到最大文件大小时，`MediaRecorder`应该停止录制，但似乎在 Android 2.2.1 上，它并不会可靠地停止。遗憾的是，我们没有办法检查它是否已经停止。为了真正停止录制，我们必须显式调用`stop`方法。

下面是一个非常简略的示例代码来说明这一点。

```
public class VideoCapture extends Activity implements MediaRecorder.OnInfoListener {

    public void onCreate(Bundle savedInstanceState) {
        recorder.setOnInfoListener(this);
    }

    public void onInfo(MediaRecorder mr, int what, int extra) {
        if (what == MediaRecorder.MEDIA_RECORDER_INFO_MAX_FILESIZE_REACHED) {
            Log.v("VIDEOCAPTURE","Maximum Filesize Reached");
        }
    }
}
```

### 最大时长

通过向`setMaxDuration`方法传入以毫秒为单位的最大时长，可以设置`MediaRecorder`捕获文件的最大时长。

```
recorder.setMaxDuration(10000); // 10 秒
```

要确定何时达到最大时长，我们需要实现`MediaRecorder.OnInfoListener`接口并将其注册到`MediaRecorder`。然后，当达到最大时长时，我们的`onInfo`方法将被触发，其中`what`整数被设置为`MediaRecorder.MEDIA_RECORDER_INFO_MAX_DURATION_REACHED`常量。

根据文档，当达到最大时长时，`MediaRecorder`应该停止录制，但似乎它在 Android 2.2.1 上并不能可靠地停止。遗憾的是，我们也没有办法检查它是否已经停止。为了真正停止录制，我们必须显式调用`stop`方法。

下面是一个简略的示例说明。

```
public class VideoCapture extends Activity implements MediaRecorder.OnInfoListener {

    public void onCreate(Bundle savedInstanceState) {
        recorder.setOnInfoListener(this);
    }

    public void onInfo(MediaRecorder mr, int what, int extra) {
        if (what == MediaRecorder.MEDIA_RECORDER_INFO_MAX_DURATION_REACHED) {
            Log.v("VIDEOCAPTURE","Maximum Duration Reached");
            mr.stop();
        }
    }
}
```

### 配置文件

从 Android 2.2（API 级别 8）开始，`MediaRecorder` 有一个名为 `setProfile` 的方法，它接收一个 `CamcorderProfile` 实例。`CamcorderProfile` 有一个静态方法 `CamcorderProfile.get`，它接收一个整数，该整数的可能值被定义为常量，即 `CamcorderProfile.QUALITY_HIGH` 或 `CamcorderProfile.QUALITY_LOW`。使用此方法，我们可以根据预设值设置一整组配置变量。当然，`QUALITY_HIGH` 指的是高质量视频捕获的设置，而 `QUALITY_LOW` 指的是低质量视频捕获的设置。

`QUALITY_HIGH` 具有以下设置：

- **音频比特率**：每秒 12,200 比特
- 音频通道数：1
- 音频编解码器：AMR-NB
- 音频采样率：8000 Hz
- **时长**：60 秒
- 文件格式：MP4
- **视频比特率**：每秒 3,000,000 比特
- 视频编解码器：H.264
- 视频帧宽度：720 像素
- 视频帧高度：480 像素
- **视频帧率**：每秒 24 帧

请注意，如前所述，许多设置是最大值或请求值，实际结果将取决于设备的性能。例如，在运行 Android 2.2.1 的 Nexus 1 上使用 `QUALITY_HIGH` 设置捕获的示例视频，其帧率为每秒 12.6 帧，总比特率为 1,617.34 kb/秒。

以下是 `QUALITY_LOW` 的设置：

- **音频比特率**：每秒 12,200 比特
- 音频通道数：1
- 音频编解码器：AMR-NB
- 音频采样率：8000 Hz
- **时长**：30 秒
- 文件格式：3GPP
- **视频比特率**：每秒 256,000 比特
- 视频编解码器：3
- 视频帧宽度：176 像素
- 视频帧高度：144 像素
- **视频帧率**：每秒 15 帧

与 `QUALITY_HIGH` 版本一样，这些设置也会产生略有不同的结果。最终捕获的视频帧率为每秒 16.06 帧，比特率为 207.96 kb/秒。

### 输出文件

完成上述所有设置后，我们可以设置输出文件的位置。我们可以传入一个 `FileDescriptor` 或一个表示文件路径的 `String`。

```
recorder.setOutputFile("/sdcard/recorded_video.mp4");
```

### 预览表面

在我们继续之前，我们需要为 `MediaRecorder` 预览（取景器）图像绘制指定一个 `Surface`。我们将以一种类似于第 2 章中自定义相机示例中处理预览图像的方式来处理这个问题。

让我们看一个简略的示例。首先，我们将在布局中创建一个 `Surface`。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
>
    <SurfaceView android:id="@+id/CameraView" android:layout_width="640px"
        android:layout_height="480px"></SurfaceView>
</LinearLayout>
```

在我们的活动（Activity）中，我们需要实现 `SurfaceHolder.Callback` 接口，以便在 `Surface` 被创建、更改或销毁时得到通知。

```
...

public class VideoCapture extends Activity implements SurfaceHolder.Callback{
    MediaRecorder recorder;
    SurfaceHolder holder;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        recorder = new MediaRecorder();

        // 在代码的此点，我们将执行 MediaRecorder 对象的所有常规设置，
        // 例如设置音频和视频源、输出文件位置等。
        // 我们在此省略这些部分，仅为了说明如何在此代码片段中处理 Surface。

        setContentView(R.layout.main);

        // 设置内容视图后，我们可以获取对 SurfaceView 的引用，
        // 并获取对其 SurfaceHolder 的引用。
        SurfaceView cameraView = (SurfaceView)
            findViewById(R.id.CameraView);
        holder = cameraView.getHolder();

        // 我们将添加我们的活动作为其 SurfaceHolder.Callback 的实现者。
        holder.addCallback(this);

        // 与第 2 章中的相机示例一样，预览 Surface 的缓冲区将由
        // 底层的 MediaRecorder 所使用的 Camera 对象在外部管理；
        // 因此我们需要将类型设置为 SURFACE_TYPE_PUSH_BUFFERS。
        holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
    }

    // 因为我们的活动已经实现了 SurfaceHolder.Callback 并被注册为该接口，
    // 所以当 Surface 实际创建时，我们的 surfaceCreated 方法将被调用。
    // 当此情况发生时，我们可以使用 setPreviewDisplay 方法将 Surface
    // 设置为 MediaRecorder 对象的预览显示。在 Surface 创建之前，
    // 我们不能执行此操作。
    public void surfaceCreated(SurfaceHolder holder) {
        recorder.setPreviewDisplay(holder.getSurface());
    }

    // 由于我们实现了 SurfaceHolder.Callback，因此还必须拥有
    // surfaceChanged 和 surfaceDestroyed 方法，它们的名称对应了
    // 各自的调用时机：
    // surfaceChanged 在 Surface 发生更改时调用，例如其高度和宽度被改变时；
    // surfaceDestroyed 在 Surface 不再使用时调用，例如当活动不再可见时。
    //
    // 当 surfaceDestroyed 被调用时，我们应该停止录制视频，因为录制
    // 只有在有 Surface 可供绘制预览图像时才能工作。
    public void surfaceChanged(SurfaceHolder holder, int format, int width,
                               int height) {
    }

    public void surfaceDestroyed(SurfaceHolder holder) {
        recorder.stop();
    }
}
```



### 准备录制

在设置好所有 `MediaRecorder` 的属性后，我们就可以使用 `prepare` 方法了。这个方法是必需的，它会执行所有内部功能，让 `MediaRecorder` 进入录制准备状态。

```
recorder.prepare();
```

### 开始录制

当 `MediaRecorder` 完成"准备"后，就可以开始录制了。`start` 方法正是用来开始录制的。

```
recorder.start();
```

### 停止录制

录制开始后，可以使用 `stop` 方法来停止录制。

```
recorder.stop();
```

### 释放资源

最后，当我们不再需要使用 `MediaRecorder` 时，应该调用 `release` 方法来释放其占用的资源。这一点很重要，因为许多底层资源（如硬件摄像头和麦克风）同一时间只能被一个应用程序使用。

```
recorder.release();
```

### 状态机

就像刚才描述的那样，与 `MediaPlayer` 对象一样，`MediaRecorder` 具有多种状态，并且某些方法只能在特定状态下调用。图 11-1 展示了可能的状态以及调用特定方法后进入的状态（此处为方便参考再次引用）。

**图 11-1.** *Android API 参考文档中的 MediaRecorder 状态图*

### 权限

使用 `MediaRecorder` 进行音频和视频捕捉，并将文件保存到 SD 卡上，需要在 `AndroidManifest.xml` 文件中设置以下权限。

```
<uses-permission android:name="android.permission.RECORD_AUDIO"></uses-permission>
<uses-permission android:name="android.permission.CAMERA"></uses-permission>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE">
</uses-permission>
```

## 完整的自定义视频捕捉示例

以下是一个将上述所有步骤整合在一起的完整示例。它使用了 `CamcorderProfile`，因此需要 Android 2.2 或更高版本。

```
package com.apress.proandroidmedia.ch11.videocapture;

import java.io.IOException;
import android.app.Activity;
import android.content.pm.ActivityInfo;
import android.media.CamcorderProfile;
import android.media.MediaRecorder;
import android.os.Bundle;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.view.Window;
import android.view.WindowManager;
import android.view.View.OnClickListener;
```

在这个示例中，我们的 Activity 将实现 `OnClickListener`（以便用户可以点击开始和停止录制）和 `SurfaceHolder.Callback`（用于处理与 Surface 相关的事件）。

```
public class VideoCapture extends Activity implements OnClickListener,
    SurfaceHolder.Callback {

    MediaRecorder recorder;
    SurfaceHolder holder;
```

这里有一个布尔值，表示我们当前是否正在录制。当未录制时为 `false`，录制时为 `true`。

```
    boolean recording = false;
    public static final String TAG = "VIDEOCAPTURE";

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
```

该 Activity 将全屏并以横屏模式运行，我们将通过以下方法进行设置。

```
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                WindowManager.LayoutParams.FLAG_FULLSCREEN);
        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
```

接下来，实例化我们的 `MediaRecorder` 对象。

```
        recorder = new MediaRecorder();
```

稍后定义的 `initRecorder` 方法将处理所有 `MediaRecorder` 的设置。

```
        initRecorder();
        setContentView(R.layout.main);
```

继续往下，我们将获取 `SurfaceView` 和 `SurfaceHolder` 的引用，并将我们的 Activity 注册为 `SurfaceHolder.Callback`。

```
        SurfaceView cameraView = (SurfaceView) findViewById(R.id.CameraView);
        holder = cameraView.getHolder();
        holder.addCallback(this);
        holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
```

我们将 `SurfaceView` 设置为可点击，并将我们的 Activity 注册为其 `OnClickListener`。这样，当触摸 `SurfaceView` 时，就会调用我们的 `onClick` 方法。

```
        cameraView.setClickable(true);
        cameraView.setOnClickListener(this);
    }
```

这里定义的 `initRecorder` 方法负责处理所有 `MediaRecorder` 的设置。

```
    private void initRecorder() {
```

我们将使用默认的音频和视频源。

```
        recorder.setAudioSource(MediaRecorder.AudioSource.DEFAULT);
        recorder.setVideoSource(MediaRecorder.VideoSource.DEFAULT);
```

为了简化操作，我们不逐一设置所有参数，而是使用内置的 `CamcorderProfile`——在本例中，使用由 `QUALITY_HIGH` 常量指定的配置。

```
        CamcorderProfile cpHigh = CamcorderProfile.get(CamcorderProfile.QUALITY_HIGH);
        recorder.setProfile(cpHigh);
```

我们指定录制文件的保存路径。在本例中，是直接保存在 SD 卡上的文件。

```
        recorder.setOutputFile("/sdcard/videocapture_example.mp4");
```

然后指定最大录制时长（以毫秒为单位）。

```
        recorder.setMaxDuration(50000); // 50 秒
```

最后指定最大文件大小（以字节为单位）。

```
        recorder.setMaxFileSize(5000000); // 大约 5 MB
    }
```

以下 `prepareRecorder` 方法的作用是将 `setPreviewDisplay` 方法与其他 `MediaRecorder` 方法区分开来。这样做是因为此步骤需要在 Surface 创建之后执行，而其他步骤可以在 `MediaRecorder` 实例化之后或停止录制之后的任何时间执行。

```
    private void prepareRecorder() {
        recorder.setPreviewDisplay(holder.getSurface());
```

设置预览显示后，我们可以调用 `MediaRecorder` 对象的 `prepare` 方法。这一步让一切就绪以便开始捕捉。由于此方法会抛出一些异常，我们需要将其包裹在 `try/catch` 块中。如果捕获到任何异常，我们将直接结束。在实际的应用中，您可能希望以更优雅的方式处理这些异常。

```
        try {
```



