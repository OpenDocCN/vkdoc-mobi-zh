# 高级视频

一种与 RTSP 一同在 Android 中受支持的媒体传输形式是 RTP（实时传输协议），但仅在与 RTSP 配对时使用。换句话说，Android 上的 RTP 不能独立于 RTSP 工作。

`RTSP` 和 `RTP` 专用于实时流媒体，这与 HTTP 渐进式下载有很大不同，因为媒体在通过网络接收的同时进行播放。

这也意味着需要专门的服务器来传输媒体。市面上有多种 RTSP 服务器：Apple 的开源 Darwin Streaming Server、RealNetwork 的 Helix Server 以及 Wowza Media Server。遗憾的是，搭建和使用服务器超出了本书的讨论范围。幸运的是，存在一个通过 RTSP 传输媒体的高度可靠的服务可供我们测试（YouTube 的移动网站，可通过 [`m.youtube.com`](http://m.youtube.com) 访问）。

与渐进式下载类似，在准备通过 RTSP 传输的媒体时，需要考虑几件事。首先，媒体需要使用 Android 支持的编解码器和文件格式进行编码，并且该文件格式应能被 RTSP 服务器流式传输。通常，移动设备的流媒体被编码为 3GP 容器中的 MP4 视频和 AAC 音频，尽管也支持其他编解码器（`H.264`）和容器（`MP4`）。

**注意：** Android 目前有两个底层媒体框架，PacketVideo 的 `OpenCORE` 和 Android 特有的 `Stagefright`。`OpenCORE` 是 Android 中一直使用的原始框架，并且在 Android 2.2（引入 `Stagefright` 时）之前一直独占使用。

在 Android 2.2（及所有先前版本）中，`OpenCORE` 是用于流媒体视频（RTSP）的框架，尽管未来可能会改变。哪个框架被使用将由手机制造商决定，并且两个框架在 API 层面应该是兼容的。由于这一切都在后台进行，幸运的是，作为开发者，我们不需要关心底层使用的是哪个框架。

关于哪些协议、编解码器、容器格式和流媒体协议受支持，更多信息可以在 [www.opencore.net](http://www.opencore.net) 找到。具体来说，`OpenCORE` 多媒体框架能力文档可在 `www.opencore.net/files/opencore_framework_capabilities.pdf` 获取。（遗憾的是，目前尚无关于 `Stagefright` 能力的公开文档。）

最后，媒体的比特率必须能够根据用户的网络连接实时传输给最终用户。这些速度根据网络类型的不同而有很大差异。第二代网络（GPRS）提供的数据速度最高在 50 到 100 kbps 范围内。要在此类网络上实时传输直播视频，需要将视频编码在 30 kbps 左右，以应对开销和变化的连接质量。升级到 EDGE 网络应能可靠地传输 50 kbps 左右的视频，而目前 3G 网络的保守比特率大约在 100 kbps，许多网络能够支持更高的比特率。

与 HTTP 渐进式下载不同，RTSP 也可以用于直播流媒体，这是其相对于传统 HTTP 传输的主要优势之一。RTSP 还支持在点播媒体中定位搜索。这意味着用户可以直接跳转到视频的特定位置，而无需下载直到该位置之前的所有媒体。服务器只负责向播放器提供该文件位置处的媒体。

## 网络视频播放

Android 在以第 9 章讨论的三种视频播放方式中支持 HTTP 和 RTSP 视频播放。通过 Intent 使用内置的 `MediaPlayer Activity` 或使用 `VideoView` 类来播放任何一种网络视频都不需要修改源代码。只需使用 HTTP 或 RTSP URL 作为视频的 `Uri`，只要格式受支持，它就能正常工作。

### VideoView 网络视频播放器

以下是第 9 章中的 `ViewTheVideo` Activity 示例，它使用 `VideoView` 和来自 YouTube 移动网站的 RTSP URL 播放视频。唯一的改变是用于构造 `videoUri` 的字符串。

```java
package com.apress.proandroidmedia.ch10.videoview;

import android.app.Activity;
import android.net.Uri;
import android.os.Bundle;
import android.widget.MediaController;
import android.widget.VideoView;

public class ViewTheVideo extends Activity {

    VideoView vv;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        vv = (VideoView) this.findViewById(R.id.VideoView);

        Uri videoUri =
                Uri.parse("rtsp://v2.cache2.c.youtube.com/CjgLENy73wIaLwm3JbT_9HqWohMYESARFEIJbXYtZ29vZ2xlSARSB3Jlc3VsdHNg_vSmsbeSyd5JDA==/0/0/0/video.3gp");

        vv.setMediaController(new MediaController(this));
        vv.setVideoURI(videoUri);
        vv.start();
    }
}
```

### MediaPlayer 网络视频播放器

使用 `MediaPlayer` 进行网络视频播放与我们在第 9 章中介绍的使用 `MediaPlayer` 和 `MediaController` 的代码类似。在下面的示例中，我们将突出显示与网络播放相关的部分。



