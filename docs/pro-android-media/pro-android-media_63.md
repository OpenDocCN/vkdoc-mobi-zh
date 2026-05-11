# 第 10 章：进阶视频处理

```
public void surfaceCreated(SurfaceHolder holder) {
    Log.v(LOGTAG, "surfaceCreated Called");
    mediaPlayer.setDisplay(holder);
    statusView.setText("MediaPlayer Display Surface Set");
}
```

我们将使用 `MediaPlayer` 的 `prepareAsync` 方法而不是 `prepare`。`prepareAsync` 方法在后台的独立线程中进行准备工作。这样做可以避免用户界面卡顿。这使用户能够执行其他操作，或者允许我们开发者显示加载动画或类似效果。

```
try {
    mediaPlayer.prepareAsync();
} catch (IllegalStateException e) {
    Log.v(LOGTAG, "IllegalStateException " + e.getMessage());
    finish();
}
```

为了让用户了解 `prepareAsync` 方法运行期间的情况，我们将更新 `statusView` `TextView` 显示的状态信息。

```
statusView.setText("MediaPlayer Preparing");
```

```
public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
    Log.v(LOGTAG, "surfaceChanged Called");
}

public void surfaceDestroyed(SurfaceHolder holder) {
    Log.v(LOGTAG, "surfaceDestroyed Called");
}

public void onCompletion(MediaPlayer mp) {
    Log.v(LOGTAG, "onCompletion Called");
    statusView.setText("MediaPlayer Playback Completed");
}

public boolean onError(MediaPlayer mp, int whatError, int extra) {
    Log.v(LOGTAG, "onError Called");
    statusView.setText("MediaPlayer Error");
    if (whatError == MediaPlayer.MEDIA_ERROR_SERVER_DIED) {
        Log.v(LOGTAG, "Media Error, Server Died " + extra);
    } else if (whatError == MediaPlayer.MEDIA_ERROR_UNKNOWN) {
        Log.v(LOGTAG, "Media Error, Error Unknown " + extra);
    }
    return false;
}

public boolean onInfo(MediaPlayer mp, int whatInfo, int extra) {
    statusView.setText("MediaPlayer onInfo Called");
    if (whatInfo == MediaPlayer.MEDIA_INFO_BAD_INTERLEAVING) {
        Log.v(LOGTAG, "Media Info, Media Info Bad Interleaving " + extra);
    } else if (whatInfo == MediaPlayer.MEDIA_INFO_NOT_SEEKABLE) {
        Log.v(LOGTAG, "Media Info, Media Info Not Seekable " + extra);
    } else if (whatInfo == MediaPlayer.MEDIA_INFO_UNKNOWN) {
        Log.v(LOGTAG, "Media Info, Media Info Unknown " + extra);
    } else if (whatInfo == MediaPlayer.MEDIA_INFO_VIDEO_TRACK_LAGGING) {
        Log.v(LOGTAG, "MediaInfo, Media Info Video Track Lagging " + extra);
    } else if (whatInfo == MediaPlayer.MEDIA_INFO_METADATA_UPDATE) {
        Log.v(LOGTAG, "MediaInfo, Media Info Metadata Update " + extra);
    }
    return false;
}

public void onPrepared(MediaPlayer mp) {
    Log.v(LOGTAG, "onPrepared Called");
    statusView.setText("MediaPlayer Prepared");
    videoWidth = mp.getVideoWidth();
    videoHeight = mp.getVideoHeight();
    Log.v(LOGTAG, "Width: " + videoWidth);
    Log.v(LOGTAG, "Height: " + videoHeight);
    if (videoWidth > currentDisplay.getWidth()
        || videoHeight > currentDisplay.getHeight()) {
        float heightRatio = (float) videoHeight
            / (float) currentDisplay.getHeight();
        float widthRatio = (float) videoWidth
            / (float) currentDisplay.getWidth();
        if (heightRatio > 1 || widthRatio > 1) {
            if (heightRatio > widthRatio) {
                videoHeight = (int) Math.ceil((float) videoHeight
                    / (float) heightRatio);
                videoWidth = (int) Math.ceil((float) videoWidth
                    / (float) heightRatio);
            } else {
                videoHeight = (int) Math.ceil((float) videoHeight
                    / (float) widthRatio);
                videoWidth = (int) Math.ceil((float) videoWidth
                    / (float) widthRatio);
            }
        }
    }
    surfaceView.setLayoutParams(
        new LinearLayout.LayoutParams(videoWidth, videoHeight));
    controller.setMediaPlayer(this);
    controller.setAnchorView(this.findViewById(R.id.MainView));
    controller.setEnabled(true);
    controller.show();
    mp.start();
    statusView.setText("MediaPlayer Started");
}

public void onSeekComplete(MediaPlayer mp) {
    Log.v(LOGTAG, "onSeekComplete Called");
}

public void onVideoSizeChanged(MediaPlayer mp, int width, int height) {
    Log.v(LOGTAG, "onVideoSizeChanged Called");
    videoWidth = mp.getVideoWidth();
    videoHeight = mp.getVideoHeight();
    Log.v(LOGTAG, "Width: " + videoWidth);
    Log.v(LOGTAG, "Height: " + videoHeight);
    if (videoWidth > currentDisplay.getWidth()
        || videoHeight > currentDisplay.getHeight()) {
        float heightRatio = (float) videoHeight
            / (float) currentDisplay.getHeight();
        float widthRatio = (float) videoWidth
            / (float) currentDisplay.getWidth();
        if (heightRatio > 1 || widthRatio > 1) {
            if (heightRatio > widthRatio) {
                videoHeight = (int) Math.ceil((float) videoHeight
                    / (float) heightRatio);
                videoWidth = (int) Math.ceil((float) videoWidth
                    / (float) heightRatio);
            } else {
                videoHeight = (int) Math.ceil((float) videoHeight
                    / (float) widthRatio);
                videoWidth = (int) Math.ceil((float) videoWidth
                    / (float) widthRatio);
            }
        }
    }
    surfaceView.setLayoutParams(
        new LinearLayout.LayoutParams(videoWidth, videoHeight));
}
```

由于我们的活动实现了 `OnBufferingUpdateListener` 并注册为 `MediaPlayer` 的监听器，以下方法将在媒体下载和缓冲期间被定期调用。缓冲将发生在准备阶段（调用 `onPrepareAsync` 或 `onPrepare` 之后）。

```
public void onBufferingUpdate(MediaPlayer mp, int bufferedPercent) {
    statusView.setText("MediaPlayer Buffering: " + bufferedPercent + "%");
    Log.v(LOGTAG, "MediaPlayer Buffering: " + bufferedPercent + "%");
}

public boolean canPause() {
    return true;
}

public boolean canSeekBackward() {
    return true;
}

public boolean canSeekForward() {
    return true;
}

public int getBufferPercentage() {
    return 0;
}

public int getCurrentPosition() {
    return mediaPlayer.getCurrentPosition();
}

public int getDuration() {
    return mediaPlayer.getDuration();
}

public boolean isPlaying() {
    return mediaPlayer.isPlaying();
}

public void pause() {
    if (mediaPlayer.isPlaying()) {
        mediaPlayer.pause();
    }
}

public void seekTo(int pos) {
    mediaPlayer.seekTo(pos);
}

public void start() {
    mediaPlayer.start();
}

@Override
public boolean onTouchEvent(MotionEvent ev) {
    if (controller.isShowing()) {
        controller.hide();
    } else {
        controller.show();
    }
    return false;
}
```

![图 10–3. 流式 MediaPlayer 活动播放来自 YouTube 的视频文件](img/index-242_1.jpg)

## 总结

在本章中，我们探讨了 Android 上一些更进阶的视频播放和流媒体功能。正如我们所学到的，`MediaStore` 可以像之前用于图片和音频那样用于视频。它为我们开发者提供了一种真正全面的手段，用于在 Android 上进行媒体元数据的存储和检索。我们还了解到，媒体视频播放的三种方法也可用于实现网络视频播放，包括流媒体和基于网络的播放。



### 视频捕获

在前几章中，我们已经讨论了图像捕获和音频捕获。现在，我们将把注意力转向视频捕获。在本章中，我们将探讨如何通过 Intent 使用 Android 内置的相机应用来捕获视频。我们将了解 Android 支持的视频捕获格式和编解码器，最后还会构建一个自定义的视频捕获应用。

## 使用 Intent 录制视频

虽然有些老生常谈，但正如之前讨论的那样，在 Android 上执行某些功能最快捷、最简单的方法通常是利用已有的应用，通过从我们的应用发送 Intent 来触发它。使用内置相机应用通过 Intent 触发来录制视频，也不例外。

在 `android.provider.MediaStore` 类中，有一个名为 `ACTION_VIDEO_CAPTURE` 的常量，它包含字符串 `"android.media.action.VIDEO_CAPTURE"`。相机应用将此字符串注册为 Intent 过滤器，因此通过 `Content.startActivity` 或 `Context.startActivityForResult` 方法发送的 Intent 将激活它。其他应用也可能注册相同的字符串，这会导致提示用户选择他/她想要用来执行该操作的应用。

```
Intent captureVideoIntent = new Intent(android.provider.MediaStore.ACTION_VIDEO_CAPTURE);
startActivityForResult(captureVideoIntent, VIDEO_CAPTURED);
```

`VIDEO_CAPTURED` 是一个应定义为类变量的常量，用于识别相机应用何时通过调用我们的 `onActivityResult` 方法将结果返回到我们的 Activity：

```
public static int VIDEO_CAPTURED = 1;
```

在 `onActivityResult` 方法中返回给我们的 Activity 的 Intent（以下代码示例中的 `data`）包含一个指向相机应用创建的视频文件的 `Uri`。

```
protected void onActivityResult (int requestCode, int resultCode, Intent data) {
  if (resultCode == RESULT_OK && requestCode == VIDEO_CAPTURED) {
    Uri videoFileUri = data.getData();
  }
}
```

以下是使用 Intent 触发内置相机应用进行视频捕获的完整示例。

```
package com.apress.proandroidmedia.ch11.videocaptureintent;

import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.VideoView;

public class VideoCaptureIntent extends Activity implements OnClickListener {
```

创建 `VIDEO_CAPTURED` 常量，用于标识通过调用 `onActivityResult` 返回的 Activity。

```
  public static int VIDEO_CAPTURED = 1;
```

我们将使用两个按钮：一个用于触发发送 Intent，`captureVideoButton`；另一个用于在视频捕获后播放它，`playVideoButton`。

```
  Button captureVideoButton;
  Button playVideoButton;
```

我们将使用一个带有 `Uri` 的标准 `VideoView` 对象来回放已捕获的视频。

```
  VideoView videoView;
  Uri videoFileUri;

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    captureVideoButton = (Button) this.findViewById(R.id.CaptureVideoButton);
    playVideoButton = (Button) this.findViewById(R.id.PlayVideoButton);

    captureVideoButton.setOnClickListener(this);
    playVideoButton.setOnClickListener(this);
```

`playVideoButton` 最初将不可用，这意味着它不能被点击。我们将在捕获视频后将其设置为可用。

```
    playVideoButton.setEnabled(false);

    videoView = (VideoView) this.findViewById(R.id.VideoView);
  }
```

我们的 Activity 实现了 `OnClickListener`，并注册为每个 `Button` 的 `OnClickListener`。因此，当按下或点击按钮时，将调用我们的 `onClick` 方法。

```
  public void onClick(View v) {
    if (v == captureVideoButton) {
```

如果按下了 `captureVideoButton`，我们将创建 Intent 并将其连同我们的 `VIDEO_CAPTURED` 常量一起传递给 `startActivityForResult` 方法，该方法将启动内置的相机应用。

```
      Intent captureVideoIntent = new Intent(android.provider.MediaStore.ACTION_VIDEO_CAPTURE);
      startActivityForResult(captureVideoIntent, VIDEO_CAPTURED);
    } else if (v == playVideoButton) {
```

如果按下了 `playVideoButton`，我们将设置要播放的 `Uri` 并开始播放。

```
      videoView.setVideoURI(videoFileUri);
      videoView.start();
    }
  }
```

当相机（或任何触发的应用/Activity）返回时，将调用以下 `onActivityResult` 方法。我们检查 `resultCode` 是否为常量 `RESULT_OK`，并且 `requestCode` 是否为我们传递给 `startActivityForResult` 的 `VIDEO_CAPTURED`，然后获取录制视频文件的 `Uri`。之后，我们将启用 `playVideoButton`，以便用户可以按下它并触发视频播放。

```
  protected void onActivityResult (int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == VIDEO_CAPTURED) {
      videoFileUri = data.getData();
      playVideoButton.setEnabled(true);
    }
  }
}
```

以下是前述 Activity 引用的 `res/layout/main.xml` 中包含的布局 XML。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
>
    <Button android:text="Capture Video" android:id="@+id/CaptureVideoButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content">
    </Button>
    <Button android:text="Play Video" android:id="@+id/PlayVideoButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content">
    </Button>
    <VideoView android:id="@+id/VideoView" android:layout_width="wrap_content"
        android:layout_height="wrap_content">
    </VideoView>
</LinearLayout>
```



