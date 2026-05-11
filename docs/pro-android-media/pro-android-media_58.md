# 第 9 章：视频简介

`} else if (whatInfo == MediaPlayer.MEDIA_INFO_METADATA_UPDATE) {`

`MEDIA_INFO_METADATA_UPDATE` 在 Android 2.0 及以上版本中可用。当新的元数据可用时，会触发该事件。

`Log.v(LOGTAG, "MediaInfo, Media Info Metadata Update " + extra);`

`}`

`return false;`

`}`

在 `MediaPlayer` 成功准备好开始播放后，`onPrepared` 方法将被调用。这是我们所实现的 `OnPreparedListener` 接口的一部分。一旦调用此方法，`MediaPlayer` 便进入了“已准备”状态，并可以开始播放。

```
public void onPrepared(MediaPlayer mp) {
    Log.v(LOGTAG, "onPrepared Called");
```

在播放视频之前，我们应该设置 `Surface` 的尺寸，使其与视频尺寸或显示尺寸匹配，具体取决于哪个更小。首先，我们使用 `MediaPlayer` 对象提供的 `getVideoWidth` 和 `getVideoHeight` 方法获取视频的尺寸。

```
videoWidth = mp.getVideoWidth();
videoHeight = mp.getVideoHeight();
```

如果视频的宽度或高度大于屏幕显示尺寸，那么我们需要计算应使用的缩放比例。

```
if (videoWidth > currentDisplay.getWidth() ||
    videoHeight > currentDisplay.getHeight()) {
    float heightRatio = (float) videoHeight / (float) currentDisplay.getHeight();
    float widthRatio = (float) videoWidth / (float) currentDisplay.getWidth();
    if (heightRatio > 1 || widthRatio > 1) {
```

我们将使用两者中较大的比例，并通过将视频尺寸除以该较大比例来设置 `videoHeight` 和 `videoWidth`。

```
        if (heightRatio > widthRatio) {
            videoHeight = (int) Math.ceil((float) videoHeight / (float) heightRatio);
            videoWidth = (int) Math.ceil((float) videoWidth / (float) heightRatio);
        } else {
            videoHeight = (int) Math.ceil((float) videoHeight / (float) widthRatio);
            videoWidth = (int) Math.ceil((float) videoWidth / (float) widthRatio);
        }
    }
}
```

现在，我们可以设置用于显示视频的 `SurfaceView` 的尺寸，要么是视频的实际尺寸，要么是当视频大于显示尺寸时调整后的尺寸。

```
surfaceView.setLayoutParams(
    new LinearLayout.LayoutParams(videoWidth, videoHeight));
```

最后，我们可以通过调用 `MediaPlayer` 对象的 `start` 方法来开始播放视频。

```
mp.start();
}
```

`onSeekComplete` 是我们所实现的 `OnSeekListener` 接口的一部分，我们的 Activity 是所注册的 `MediaPlayer` 监听器。当搜索命令完成时，会调用此方法。

```
public void onSeekComplete(MediaPlayer mp) {
    Log.v(LOGTAG, "onSeekComplete Called");
}
```

`onVideoSizeChanged` 是我们所实现的 `OnVideoSizeChangedListener` 接口的一部分，我们的 Activity 是所注册的 `MediaPlayer` 监听器。当尺寸发生变化时，会调用此方法。在指定数据源并读取视频元数据后，该方法至少会被调用一次。

```
public void onVideoSizeChanged(MediaPlayer mp, int width, int height) {
    Log.v(LOGTAG, "onVideoSizeChanged Called");
}
}
```

以下是用于上述 Activity 的布局 XML 文件 `main.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:id="@+id/MainView">
    <SurfaceView
        android:id="@+id/SurfaceView"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content" />
</LinearLayout>
```

**图 9–5.** *在 CustomVideoPlayer Activity 中播放视频*

## 带 MediaController 的 MediaPlayer

我们在 `VideoView` 示例中使用的 `MediaController` 视图也可以与 `MediaPlayer` 一起使用，如图 9-6 所示。不幸的是，要使其正常工作，需要付出更多的努力。

首先，我们的类除了已经实现的其它接口外，还需要实现 `MediaController.MediaPlayerControl`。

```
import android.widget.MediaController;

public class CustomVideoPlayer extends Activity
        implements OnCompletionListener, OnErrorListener, OnInfoListener,
        OnPreparedListener, OnSeekCompleteListener, OnVideoSizeChangedListener,
        SurfaceHolder.Callback, MediaController.MediaPlayerControl {
```

该接口定义了一系列 `MediaController` 用于控制播放的函数，我们需要在我们的 Activity 中实现它们。以下是这些函数及其在我们的 `CustomVideoPlayer` 示例中的实现。对于其中几个函数，我们只需返回 `true`，表示该功能已具备。对于其余函数，我们在 `MediaPlayer` 对象上调用相应的函数。

```
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
}
```

现在，我们可以自由地添加实际的 `MediaController` 对象。我们将其与其他实例变量一起声明。

```
MediaController controller;
```

在 `onCreate` 方法中，我们将实例化它。

```
controller = new MediaController(this);
```

实际上，我们将在 `MediaPlayer` 准备就绪后才设置并使用它。在 `onPrepared` 方法的末尾，我们可以添加以下代码。首先，我们通过调用 `setMediaPlayer` 方法来指定实现了 `MediaController.MediaPlayerControl` 的对象。在本例中，该对象是我们的 Activity，所以我们传入 `this`。然后，我们设置 Activity 的根视图，以便 `MediaController` 可以确定如何显示自身。在前面的布局 XML 中，我们为根 `LinearLayout` 对象设置了 ID `MainView`，以便在此处引用它。最后，我们启用并显示它。

```
controller.setMediaPlayer(this);
controller.setAnchorView(this.findViewById(R.id.MainView));
controller.setEnabled(true);
controller.show();
```

为了在控制器消失后（`MediaController` 的默认行为是在超时后自动隐藏）重新显示它，我们可以在 Activity 中重写 `onTouchEvent` 来显示或隐藏它。

```
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

**图 9–6.** *带 MediaController 的 CustomVideoPlayer Activity*

## 总结

就像 Android 中的许多事情一样，完成一项任务有许多不同的方式。在本章中，我们研究了播放视频文件的三种不同方式。通过 Intent 简单地使用内置应用程序是最简单但最不灵活的方式。使用 `VideoView` 允许我们在自己的 Activity 中播放视频，但在控制能力方面并没有提供太多。`MediaPlayer` 提供了最大的控制范围，但需要做最多的工作。



