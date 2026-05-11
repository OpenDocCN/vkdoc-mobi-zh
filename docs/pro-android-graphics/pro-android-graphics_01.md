# 目录

## 第 1 章：Android 数字成像：格式、概念与优化

- Android 的数字图像格式：无损与有损 2
- Android 的`View`和`ViewGroup`类：图像容器 2
- 数字图像的基础：像素与宽高比 3
- 数字图像的颜色：色彩理论与色深 4
- 在 Android 中表示颜色：十六进制表示法 5
- 图像合成：Alpha 通道与混合模式 6
- 数字图像遮罩：Alpha 通道的常见用途 7
- 平滑遮罩边缘：抗锯齿的概念 8
- 优化数字图像：压缩与抖动 9
- 下载 Android 环境：Java 与 ADT Bundle 11
- 安装与更新 Android 开发者 ADT Bundle 15
- 本章小结 22

## 第 2 章：Android 数字视频：格式、概念与优化

- Android 数字视频格式：MPEG4 H.264 与 WebM VP8 26
- Android 的`VideoView`和`MediaPlayer`类：视频播放器 27
- 数字视频的基础：运动、帧与 FPS 28
- 数字视频惯例：比特率、流、标清与高清 29
- 适用于 Android 的数字视频文件：分辨率密度目标 31
- 优化数字视频：编解码器与压缩 32
- 在 Eclipse ADT 中创建 Pro Android Graphics 应用 34
- 创建视频启动画面用户界面设计 42
- 查看你的`MainActivity.java` Activity 子类 43
- 创建视频素材：使用 Terragen 3 3D 软件 44
- 创建未压缩视频：使用 VirtualDub 软件 46
- 压缩视频素材：使用 Sorenson Squeeze 53
- 在 Android 中安装视频素材：使用 Raw 文件夹 59
- 在 Android 应用中引用视频素材 62
- 本章小结 67

## 第 3 章：Android 帧动画：XML、概念与优化

- 帧动画概念：关键帧、帧率与分辨率 70
- 优化帧动画：色深与帧率 71
- 使用 XML 标记在 Android 中创建帧动画 72
- Android 的`<animation-list>`标签：父级帧容器 73
- Android 的`<item>`标签：指定动画帧 73
- 为 GraphicsDesign 应用创建帧动画 74
- 复制分辨率密度目标帧 74
- 使用 XML 创建帧动画定义 76
- 在`ImageView`中引用帧动画定义 80
- 使用 Java 实例化帧动画定义 89
- 本章小结 92

## 第 4 章：Android 过程式动画：XML、概念与优化

- 过程式动画概念：补间与插值器 95
- 过程式动画数据值：范围与枢轴点 98
- 过程式动画变换：旋转、缩放、平移 98
- 过程式动画合成：Alpha 混合 99
- 过程式动画时序：使用持续时间和偏移量 100
- 过程式动画循环：RepeatCount 与 RepeatMode 100
- `<set>`标签：使用 XML 对过程式动画进行分组 101
- 过程式动画 vs. 帧动画：权衡利弊 102
- 在 GraphicsDesign 应用中创建过程式动画 103
- 使用 XML 创建过程式动画定义 104
- 在`MainActivity.java`中实例化动画对象 109
- 使用 Set 创建更复杂的过程式动画 111
- 旋转变换：特效应用稍显过度 115
- 微调变换值：轻松调整 XML 119
- 本章小结 120

## 第 5 章：Android DIP：设备无关像素图形设计

- Android 如何支持设备显示：UI 设计与 UX 121
- 设备显示概念：尺寸、密度、方向、DIP 122
- 密度独立性：创造相似的用户体验 124
- 通过`<supports-screens>`标签实现 Android 多屏支持 126
- 提供设备优化的用户界面布局设计 128
- 使用 Android 的最小宽度屏幕配置修饰符 129
- 使用可用屏幕宽度屏幕配置修饰符 130
- 使用可用屏幕高度屏幕配置修饰符 130
- 提供设备优化的图像可绘制资源 131
- `DisplayMetrics`类：尺寸、密度与字体缩放 132
- 针对 LDPI 至 XXXHDPI 优化 Android 应用图标 133
- 在正确的密度文件夹中安装新的应用图标 139
- 为自定义应用图标配置`AndroidManifest.xml` 141
- 在 Nexus One 上测试新的应用图标和标签 144
- 本章小结 145

## 第 6 章：Android UI 布局：使用`ViewGroup`类进行图形设计

- Android `ViewGroup`超类：布局的基础 148
- `ViewGroup.LayoutParams`类：布局参数 149
- 已弃用的布局：`AbsoluteLayout`和`SlidingDrawer` 150
- Android 的实验性布局：`SlidingPaneLayout` 150
- Android `RelativeLayout`类：设计相对布局 152
- Android `LinearLayout`类：设计线性布局 153
- Android `FrameLayout`类：设计帧布局 154
- Android `GridLayout`类：设计 UI 布局网格 155
- `DrawerLayout`类：设计 UI 抽屉布局 158
- 添加菜单项以访问 UI 布局容器 160
- 为你的 UI 设计创建目录 Activity 163
- 创建 XML 目录`LinearLayout` UI 设计 166
- 向 TOC UI 布局容器添加文本 UI 控件 170
- 使用`onOptionsItemSelected()`添加菜单功能 177
- 在 Nexus One 上测试目录 Activity 179
- 本章小结 180

## 第 7 章：Android UI 控件：使用`View`类进行图形设计

- Android `View`类：UI 控件的基础 184
- 视图基本属性：ID、布局定位与尺寸 185
- 视图定位特性：外边距与内边距 186
- 视图图形属性：背景、透明度和可见性 188
- 视图的功能特性：监听器与焦点 189
- 书签工具 UI：使用`RelativeLayout`和`TextView` 190
- 使用`ImageView`控件：图形设计的基石 199
- 在 Nexus One 横屏模式下测试你的 UI 设计 206
- 为`ImageView`图像素材添加投影效果 207
- 修改`ImageView`的 XML 以整合新素材 220
- 在`RelativeLayout`中合成背景图像 223
- 本章小结 226

## 第 8 章：高级`ImageView`：使用`ImageView`进行更多图形设计

- Android 中的图形学：`ImageView`类的起源 230
- `ImageView.ScaleType`嵌套类：缩放控制 231
- 使用`adjustViewBounds`及其与`ScaleType`的关系 233
- `MaxWidth`和`MaxHeight`：控制`adjustViewBounds` 234
- 在`ImageView`中设置基线并控制对齐 235
- 使用`cropToPadding`方法裁剪`ImageView` 235
- 为`ImageView`着色及使用`PorterDuff`进行颜色混合 236
- 对 SkyCloud 图像应用着色以优化阴影对比度 237
- 使用`cropByPadding`裁剪 SkyCloud 图像素材 241
- 更改`ImageView`的基线对齐索引 245
- 执行图像缩放：外边距和内边距属性 248
- 本章小结 253

## 第 9 章：高级`ImageButton`：创建自定义多状态`ImageButton`

- Android 中的按钮图形学：`ImageButton`类概览 256
- `ImageButton`状态：正常、按下、聚焦与悬停 256
- `ImageButton`可绘制素材：合成按钮状态 257
- `ImageButton`的可绘制对象：设置多状态 XML 266
- 创建所有`ImageButton`状态素材：密度分辨率 270
- 将`ImageButton`缩放至 UI 元素级别的比例 276
- 本章小结 280

## 第 10 章：使用 9-Patch 成像技术创建可缩放图像元素

- Android `NinePatchDrawable`：NinePatch 的基础 284
- NinePatch 图形素材：9-Patch 概念概览 285
- Android `NinePatch`类：创建 NinePatch 素材 286
- Draw 9-Patch 工具：创建`NinePatchDrawable`素材 287
- 使用 XML 标记实现 NinePatch 素材 299
- 本章小结 305

## 第 11 章：高级图像混合：使用 Android `PorterDuff`类

- 像素混合：将图像合成提升至新高度 308
- Android 的`PorterDuff`类：混合的基础 308
- `PorterDuff.Mode`类：Android 混合常量 309
- `PorterDuffColorFilter`类：混合你的`ColorFilter` 312
- 使用 PorterDuff 对图像素材应用`ColorFilter`效果 313
- `PorterDuffXfermode`类：应用混合常量 317
- `Paint`类：将混合常量应用到图像上 318
- 使用`Bitmap`类在图像之间应用 PorterDuff 319
- 使用`.setXfermode()`方法应用`PorterDuffXfermode` 320
- `Canvas`类：为合成创建画布 321
- 在 XML 和 Java 中创建`ImageView`以显示画布 323
- 通过`.setBitmapImage()`将画布写入`ImageView` 325
- 本章小结 330

## 第 12 章：高级图像合成：使用`LayerDrawable`类

- 图层可绘制对象：将图像合成提升至新水平 334
- Android 的`LayerDrawable`类：图层的基础 334
- `<layer-list>`父标签：使用 XML 设置图层 335
- 实例化用于 PorterDuff 合成的`LayerDrawable` 346
- 创建`Drawable`对象以持有`LayerDrawable`素材 347
- 将`Drawable`转换为`BitmapDrawable`并提取`Bitmap` 348
- 修改 PorterDuff 流水线以使用`LayerDrawable` 351
- 切换`LayerDrawable`图像素材：从源到目标 353
- 更改流水线中使用的`LayerDrawable`图层 354
- 读者练习：使用两个`LayerDrawable`素材 358
- Android 中数字图像合成的最终注意点 358
- 本章小结 359

## 第 13 章：数字图像过渡：使用`TransitionDrawable`类

- 过渡：图像混合创造运动错觉 362
- Android 的`TransitionDrawable`类：过渡引擎 362
- `<transition>`父标签：在 XML 中设置过渡 363
- 实例化`ImageButton`和`TransitionDrawable`对象 370
- 使用`.reverseTransition()`方法实现 Pong 过渡 379
- 通过`ImageView`实现高级`TransitionDrawable`合成 381
- 本章小结 383

## 第 14 章：基于帧的动画：使用`AnimationDrawable`类

- `AnimationDrawable`类：帧动画引擎 386
- `DrawableContainer`类：多可绘制对象的可绘制对象 386
- 使用 Java 创建你的`AnimationDrawable`闪屏 387
- 使用 Android `Runnable`类运行你的动画 389
- 为你的动画创建`setUpAnimation()`方法 390
- 创建新的`AnimationDrawable`对象并引用其帧 391
- 使用`AnimationDrawable`类的`.addFrame()`方法 392
- 通过使用`.setOneShot()`配置`AnimationDrawable` 394
- 使用 Handler 类调度`AnimationDrawable` 395
- 设计一个循环回第一帧的`AnimationDrawable` 398
- 添加事件处理以允许通过点击触发帧动画 399
- 本章小结 408

## 第 15 章：过程式动画：使用动画类

- Animation 类：你的过程式动画引擎 412
- `TranslateAnimation`类：用于移动的动画子类 413
- `ScaleAnimation`类：用于缩放的动画子类 413
- 放大你的 Logo：使用`ScaleAnimation`类 414
- `AlphaAnimation`类：用于混合的动画子类 419
- 淡入你的 PAG Logo：使用`AlphaAnimation`类 420
- `AnimationSet`类：创建复杂的动画集合 423
- 为你的 PAG Logo 动画创建`AnimationSet` 425
- `RotateAnimation`类：用于旋转的动画子类 431
- 旋转你的 PAG Logo：使用`RotateAnimation`类 432
- 使用 Android `Runnable`类运行`AnimationSet` 436
- 为你的`AnimationSet`创建`TranslateAnimation`对象 436
- 本章小结 436

## 第 16 章：高级图形学：精通`Drawable`类

- Android 可绘制资源：可绘制对象的类型 439
- 创建`ShapeDrawable`对象：XML `<shape>`父标签 441
- Android `Drawable`类：图形学的蓝图 454
- 创建你的自定义 Drawable：`ImageRoundingDrawable` 456
- 创建用于绘制 Drawable 画布的`Paint`对象 459
- Android `Shader`超类：用于绘画的纹理贴图 460
- `Shader.TileMode`嵌套类：着色器平铺模式 461
- `BitmapShader`类：使用位图进行纹理映射 462
- 为 Drawable 创建并配置`BitmapShader` 462
- Android `Rect`和`RectF`类：定义绘制区域 467
- 定义你的`RectF`对象并调用`.drawRoundRect()` 468
- Java `InputStream`类：读取原始数据流 473
- 本章小结 477

## 第 17 章：交互式绘图：交互式使用`Paint`和`Canvas`类

- Android `onDraw()`方法：在屏幕上进行绘制 480
- Android `Canvas`类：数字工匠的画布 480
- Android `Paint`类：数字工匠的画笔 481
- 为你的 SketchPad 设置 GraphicsDesign 项目 484
- 创建自定义视图类：你的`SketchPadView`类 489
- Android `Context`类：告诉你的 Activity 它所处的位置 492
- 配置你的`SketchPadView()`构造方法 493
- 创建坐标类以跟踪触摸点的 X 和 Y 坐标 496
- Java `List`工具类：整理你的集合 497
- Java 的`ArrayList`工具类：集合列表的数组 497
- 创建`ArrayList`对象来保存触摸点数据 498
- 实现你的`.onDraw()`方法：绘制你的画布 500
- 创建你的`OnTouchListener()`方法：事件处理 501
- Android 的`MotionEvent`类：Android 中的运动数据 502
- [处理你的运动数据：使用`.getX()`和`.getY()`](#A978



