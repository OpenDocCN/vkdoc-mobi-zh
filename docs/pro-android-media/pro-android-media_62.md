# 第 10 章：高级视频

展示实际运行的示例。如需了解 `MediaPlayer` 和 `MediaController` 的完整说明，请参阅第 9 章的示例。

```
package com.apress.proandroidmedia.ch10.streamingvideoplayer;

import java.io.IOException;

import android.app.Activity;
import android.os.Bundle;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnBufferingUpdateListener;
import android.media.MediaPlayer.OnCompletionListener;
import android.media.MediaPlayer.OnErrorListener;
import android.media.MediaPlayer.OnInfoListener;
import android.media.MediaPlayer.OnPreparedListener;
import android.media.MediaPlayer.OnSeekCompleteListener;
import android.media.MediaPlayer.OnVideoSizeChangedListener;
import android.util.Log;
import android.view.Display;
import android.view.MotionEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.MediaController;
```

`StreamingVideoPlayer` 活动实现了 `MediaPlayer`、`SurfaceHolder` 和 `MediaController` 中许多可用的监听器和回调抽象类。在处理通过网络传输的媒体时，`OnBufferingUpdateListener` 尤其有用。该类指定了一个 `onBufferingUpdate` 方法，该方法在媒体缓冲期间会被重复调用，使我们能够跟踪缓冲区的填充进度。

```
public class StreamingVideoPlayer extends Activity implements
        OnCompletionListener, OnErrorListener, OnInfoListener,
        OnBufferingUpdateListener, OnPreparedListener, OnSeekCompleteListener,
        OnVideoSizeChangedListener, SurfaceHolder.Callback,
        MediaController.MediaPlayerControl {

    MediaController controller;
    Display currentDisplay;
    SurfaceView surfaceView;
    SurfaceHolder surfaceHolder;
    MediaPlayer mediaPlayer;
    View mainView;
```

在此版本中，我们使用名为 `statusView` 的 `TextView` 向用户显示状态消息。这样做的原因是，通过互联网加载视频进行播放可能需要相当长的时间，如果没有某种状态消息，用户可能会认为应用程序已挂起。

```
    TextView statusView;

    int videoWidth = 0;
    int videoHeight = 0;
    boolean readyToPlay = false;

    public final static String LOGTAG = "STREAMING_VIDEO_PLAYER";

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        mainView = this.findViewById(R.id.MainView);
        statusView = (TextView) this.findViewById(R.id.StatusTextView);
        surfaceView = (SurfaceView) this.findViewById(R.id.SurfaceView);
        surfaceHolder = surfaceView.getHolder();
        surfaceHolder.addCallback(this);
        surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);

        mediaPlayer = new MediaPlayer();
        statusView.setText("已创建 MediaPlayer");

        mediaPlayer.setOnCompletionListener(this);
        mediaPlayer.setOnErrorListener(this);
        mediaPlayer.setOnInfoListener(this);
        mediaPlayer.setOnPreparedListener(this);
        mediaPlayer.setOnSeekCompleteListener(this);
        mediaPlayer.setOnVideoSizeChangedListener(this);
```

在 `MediaPlayer` 事件监听器列表中，我们的活动实现了 `OnBufferingUpdateListener` 并进行了注册。

```
        mediaPlayer.setOnBufferingUpdateListener(this);
```

我们不再播放 SD 卡上的文件，而是播放来自 RTSP 服务器的文件。文件的 URL 在以下 `String` 类型的 `filePath` 中指定。然后，我们使用 `MediaPlayer` 的 `setDataSource` 方法，传入 `filePath` 字符串。`MediaPlayer` 知道如何处理加载和播放来自 RTSP 服务器的数据，因此我们无需为此做任何其他特殊处理。

```
        String filePath = "rtsp://v2.cache2.c.youtube.com/CjgLENy73wIaLwm3JbT_9HqWohMYESARFEIJbXYtZ29vZ2xlSARSB3Jlc3VsdHNg96LUzsK0781MDA==/0/0/0/video.3gp";

        try {
            mediaPlayer.setDataSource(filePath);
        } catch (IllegalArgumentException e) {
            Log.v(LOGTAG, e.getMessage());
            finish();
        } catch (IllegalStateException e) {
            Log.v(LOGTAG, e.getMessage());
            finish();
        } catch (IOException e) {
            Log.v(LOGTAG, e.getMessage());
            finish();
        }

        statusView.setText("已设置 MediaPlayer 数据源");

        currentDisplay = getWindowManager().getDefaultDisplay();
        controller = new MediaController(this);
```



