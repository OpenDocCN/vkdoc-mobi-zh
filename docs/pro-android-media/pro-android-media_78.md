# 索引

- `setVideoUrl`方法, 286, 288
- `<SurfaceView />`元素, 24
- `setVisibility`方法, 14
- `SurfaceView`类, 24–25
- `Short.MAX_VALUE`常量, 191
- `synth_frequency`变量, 186–187
- `SIZE`列, 116
- 合成音频, 179–187
  - 生成样本, 182–187
  - 播放合成声音, 180–182
- 尺寸限制, 以及使用相机应用捕获图像, 5
- `SizedCameraIntent`活动, 10
- `System.currentTimeMillis()`方法, 17
- `Software`标签, 21
- `src`属性, 288
- `src`目录, 189

---

**索引**

- **T**
  - 标签参数, 257
  - `takePicture`方法, `Camera`类, 32–33
  - `TEMPORARILY_UNAVAILABLE`常量
  - `Test_Movie_iPhone.m4v`文件, 196
  - `TextView`类, 222, 261, 282
  - `Thread.sleep(500)`方法, 165
  - `THREE_GPP`常量, 237
  - 缩略图, 来自`MediaStore`的视频, 212
  - 延时摄影应用, 使用`Camera`类
    - 完整自定义示例, 246–250
    - 使用`Camera`类的定时拍照应用, 38–42
  - `title`变量, 282
  - `toString`方法, 253
  - 触控事件, 用于手指绘画, 93–96
  - `true`（`while`）循环, 170
  - `try catch`块, 247, 253, 256
  - `Typeface`类, 88, 90
  - `Typeface.create`方法, 90
  - `Typeface.createFromAsset`方法, 91
  - `Typeface.DEFAULT`常量, 89
  - `Typeface.DEFAULT_BOLD`常量, 89
  - `Typeface.MONOSPACE`常量, 88
  - `Typeface.SANS_SERIF`常量, 88
  - `Typeface.SERIF`常量, 88

- **U**
  - `update`方法, 232, 234
  - `upx`变量, 95
  - `upy`变量, 95
  - `URI`, 为图像获取, 11
  - `Uri.fromFile`方法, 117
  - `URL`字段, 144
  - `UserComment`标签, 21
  - `UserXMLHandler`类, 275–276
  - `uses-permission`标签, 249, 266
  - 通过意图使用音乐应用, 107

- **V**
  - 视频, 195–228
    - 使用`MediaPlayer`播放, 221
    - `MediaStore`中的视频, 211–218
      - 示例, 212–218
      - 缩略图, 212
    - 网络视频, 218–228
      - HTTP, 218–219
    - 使用`VideoView`播放, 197–200
    - `RTSP`, 219–221
    - 视频捕获, 229–250
      - 添加视频元数据, 232–235
      - 自定义。另请参阅`MediaRecorder`
      - 使用意图录制视频, 229–232
    - 视频帧率, 239
  - `VIDEO_CAPTURED`常量, 229–231, 282
  - `VideoGalleryAdapter`类, 214–215
  - `videoHeight`属性, 206
  - `videoUrl`变量, 283, 285
  - `VideoView`
    - 使用`VideoView`进行网络视频播放, 221
    - 使用`VideoView`播放视频, 197–200
  - `VideoView`类, 214, 221
  - `VideoViewInfo`类, 215
  - `videoWidth`属性, 206
  - `View`类, 93
  - `View.GONE`常量, 14
  - `View.INVISIBLE`常量, 14
  - 频率可视化, 189
  - `VOICE_CALL`常量, 155, 236
  - `VOICE_DOWNLINK`常量, 155, 236
  - `VOICE_RECOGNITION`常量, 155, 236
  - `VOICE_UPLINK`常量, 155, 236

- **W**
  - 网络服务，媒体消费与发布，使用, 251–290
    - HTTP 文件上传
    - 概述, 278–290
    - 上传视频到 Blip.TV, 280
    - HTTP 请求
      - 发起, 278–280
      - 概述, 252–254
      - JSON, 254–272
        - 使用`JSON`从 Flickr 拉取图片, 257–272
        - 在请求中使用位置, 263–272
      - 概述, 251–252
      - REST, 273–278
      - 在 XML 中表示数据, 273
      - SAX 解析, 274–278
  - `what`参数, 240
  - `WHERE`子句, 17, 116, 121
  - `while (true)`循环, 170
  - `while`子句, 214
  - `write`方法, 180, 182, 286
  - `writeTo`方法, 286

- **X, Y, Z**
  - `Xfermode`类, 70
  - XML, 在 XML 中表示数据, 273
  - `XMLHandler`类, 274
  - `XMLReader`类, 274–276
  - `XMLUser`类, 275, 277



*   前言
*   内容概览
*   目录
*   关于作者
*   关于技术审校者
*   致谢
*   序言
*   Android 成像简介
    *   使用内置相机应用拍摄图像
        *   从相机应用返回数据
        *   拍摄更大的图像
        *   显示大图像
    *   图像存储与元数据
        *   获取图像的 URI
        *   更新 CameraActivity 以使用 MediaStore 存储图像并关联元数据
        *   使用 MediaStore 检索图像
        *   创建图像查看应用
        *   内部元数据
    *   本章小结
*   构建自定义相机应用
    *   使用 Camera 类
        *   相机权限
        *   预览界面
        *   实现相机功能
        *   整合所有内容
    *   扩展自定义相机应用
        *   构建基于定时器的相机应用
        *   构建延时摄影应用
    *   本章小结
*   图像编辑与处理
    *   使用内置图库应用选择图像
    *   将一张位图绘制到位图上
    *   基本的图像缩放与旋转
        *   矩阵入门
        *   矩阵方法
        *   绘图的替代方案
    *   图像处理
        *   ColorMatrix
        *   修改对比度和亮度
        *   修改饱和度
    *   图像合成
    *   本章小结
*   图形与触摸事件
    *   Canvas 绘图
        *   创建位图
        *   位图配置
        *   创建 Canvas
        *   使用 Paint
        *   绘制形状
        *   绘制文本
    *   手指绘画
        *   触摸事件
        *   在现有图像上绘图
        *   保存基于位图的 Canvas 绘图
    *   本章小结
*   Android 音频简介
    *   音频播放
        *   支持的音频格式
        *   通过 Intent 使用内置音频播放器
        *   创建自定义音频播放应用
        *   用于音频的 MediaStore
    *   本章小结
*   后台与网络音频
    *   后台音频播放
        *   服务
        *   本地服务加 MediaPlayer
        *   控制服务中的 MediaPlayer
    *   网络音频
        *   HTTP 音频播放
        *   通过 HTTP 流式播放音频
        *   RTSP 音频流
    *   本章小结
*   音频捕获
    *   使用 Intent 捕获音频
    *   自定义音频捕获
        *   MediaRecorder 音频源
        *   MediaRecorder 输出格式
        *   MediaRecorder 音频编码器
        *   MediaRecorder 输出与录制
        *   MediaRecorder 状态机
        *   MediaRecorder 示例
        *   其他 MediaRecorder 方法
    *   将音频插入 MediaStore
    *   使用 AudioRecord 进行原始音频录制
    *   使用 AudioTrack 进行原始音频播放
    *   原始音频捕获与播放示例
    *   本章小结
*   音频合成与分析
    *   数字音频合成
        *   播放合成声音
        *   生成采样
    *   音频分析
        *   捕获用于分析的声音
        *   可视化频率
    *   本章小结
*   视频简介
    *   视频播放
        *   支持的格式
        *   使用 Intent 播放
        *   使用 VideoView 播放
        *   使用 MediaController 添加控件
        *   使用 MediaPlayer 播放
    *   本章小结
*   高级视频
    *   用于检索视频的 MediaStore
        *   来自 MediaStore 的视频缩略图
        *   完整的 MediaStore 视频示例
    *   网络视频
        *   支持的网络视频类型
        *   网络视频播放
    *   本章小结
*   视频捕获
    *   使用 Intent 录制视频
    *   添加视频元数据
    *   自定义视频捕获
        *   用于视频的 MediaRecorder
        *   完整的自定义视频捕获示例
    *   本章小结
*   使用 Web 服务的媒体消费与发布
    *   Web 服务
    *   HTTP 请求
    *   JSON
        *   使用 JSON 拉取 Flickr 图像
        *   位置信息
        *   使用 JSON 和位置信息拉取 Flickr 图像
    *   REST
        *   以 XML 表示数据
        *   SAX 解析
    *   HTTP 文件上传
        *   发起 HTTP 请求
        *   将视频上传到 Blip.TV
    *   本章小结
*   索引
    *   A
    *   B
    *   C
    *   D
    *   E, F
    *   G, H
    *   I, J, K
    *   L
    *   M
    *   N, O
    *   P, Q, R
    *   S, T
    *   U, V
    *   W, X, Y, Z
