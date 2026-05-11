# 第 4 章：图形与触摸事件

**图 4-19.** 使用质量设置 90 保存为 JPEG 的图像

**图 4-20.** 使用质量设置 10 保存为 JPEG 的图像；其文件大小不到质量设置 90 所保存图像的六分之一。

## 小结

正如我们所探讨的，在 Android 上使用基于画布的绘图可以实现很多功能。这为我们的第一部分探索画上了句号，这部分主要处理静态图像。我们讨论的大部分内容仅仅触及了可能性的表面，但这为创建自己的应用程序提供了一个良好的起点，无论是利用相机、执行图像处理，还是具备绘图功能。

接下来，我们将把目光投向音频！

---

## 第 5 章：音频简介



### **Android**

如今，任何名副其实的智能手机都具备与专用便携式媒体设备或 MP3 播放器相媲美的音频播放能力。当然，基于 Android 的设备也不例外。这些能力使我们能够构建音乐播放器、有声读物、播客，或者任何其他以音频播放为核心的应用程序。

在本章中，我们将探讨 Android 在格式和编解码器支持方面的能力，并构建几个不同的播放应用程序。此外，我们还将了解 Android 支持的音频格式和元数据。

## 音频播放

如前所述，Android 支持与 MP3 播放器相媲美的音频播放能力。事实上，它可能更胜一筹，因为它支持相当广泛的音频格式，比大多数硬件播放器都要多。这是智能手机的优势之一，它执行了以前由专用硬件承担的功能，因为它们拥有运行各种软件的强大能力；就像电脑一样，它们可以为不同且不断变化的技术提供广泛的支持，而这些技术简单地集成到以硬件为中心的设备的固件中是不切实际的。

### 支持的音频格式

Android 支持多种用于播放的音频文件格式和编解码器（它支持的录制格式较少，我们将在讲解录制时讨论）。

*   **AAC**：高级音频编码（AAC）编解码器（以及高效率 AAC 的两种配置文件——HE-AAC），文件格式为 `.m4a (audio/m4a)` 或 `.3gp (audio/3gpp)`。AAC 是一种流行的标准，被 iPod 和其他便携式媒体播放器使用。Android 支持在 MPEG-4 音频文件和 3GP 文件（基于 MPEG-4 格式）中使用这种音频格式。AAC 规范的最新补充——高效率 AAC 也得到支持。
*   **MP3**：MPEG-1 音频层 3，文件格式为 `.mp3 (audio/mp3)`。Android 支持 MP3，它可能是使用最广泛的音频编解码器。这使得 Android 能够利用通过各种网站和音乐商店提供的绝大多数在线音频资源。
*   **AMR**：自适应多速率（AMR）编解码器（包括 AMR 窄带 AMR-NB 和 AMR 宽带 AMR-WB），文件格式为 `.3gp (audio/3gpp)` 或 `.amr (audio/amr)`。AMR 是被 3GPP（第三代合作伙伴计划）标准化为主要语音音频编解码器的音频编解码器。3GPP 是一个电信行业组织，为其合作伙伴公司制定规范。换句话说，AMR 编解码器主要用于现代手机上的语音通话应用，并且普遍得到移动手机制造商和运营商的广泛支持。因此，该编解码器通常适用于语音编码，但对于音乐等更复杂的音频类型表现不佳。
*   **Ogg**：Ogg Vorbis，文件格式为 `.ogg (application/ogg)`。Ogg Vorbis 是一种开源、无专利的音频编解码器，其质量可与 MP3、AAC 等商业且受专利保护的编解码器相媲美。它由志愿者开发，目前由 Xiph.Org 基金会维护。
*   **PCM**：脉冲编码调制（PCM），常用于 WAVE 或 WAV 文件（波形音频格式），文件格式为 `.wav (audio/x-wav)`。PCM 是用于在计算机和其他数字音频设备上存储音频的技术。它通常是一种未压缩的音频文件，其数据表示一段时间内音频的振幅。“采样率”是存储振幅读数的频率。“位深度”是用于表示单个样本的位数。例如，采样率为 16kHz、位深度为 32 位的音频数据意味着它将包含 32 位数据来表示音频的振幅，并且每秒会有 16,000 个这样的数据。采样率越高，位深度越高，音频的数字化就越精确。采样率和位深度也决定了音频文件在考虑其长度后的大小。Android 支持 WAV 文件中的 PCM 音频数据。WAV 是个人电脑上历史悠久的音频标准格式。

## 通过 Intent 使用内置音频播放器

与使用相机类似，在应用程序内提供播放音频文件能力的最简单方法是利用内置“音乐”应用程序的功能。这个应用程序可以播放 Android 支持的所有格式，拥有用户熟悉的界面，并且可以通过 Intent 触发播放特定文件。

通用的 `android.content.Intent.ACTION_VIEW` Intent，将其数据设置为音频文件的 `Uri` 并指定 MIME 类型，可以让 Android 选择合适的播放应用程序。这通常是音乐应用程序，但如果用户安装了其他音频播放软件，也可能会看到其他选项。

```java
Intent intent = new Intent(android.content.Intent.ACTION_VIEW);
intent.setDataAndType(audioFileUri, "audio/mp3");
startActivity(intent);
```

**注意：** MIME 代表多用途互联网邮件扩展（Multipurpose Internet Mail Extensions）。它最初是为了帮助电子邮件客户端发送和接收附件而制定的。然而，它的用途已远远超出了电子邮件，扩展到了许多其他通信协议，包括 HTTP 或标准 Web 服务。Android 在解析 Intent 时会使用 MIME 类型，特别是为了帮助确定哪个应用程序应该处理该 Intent。

每种文件类型都有一个特定（有时不止一个）的 MIME 类型。此类型至少使用两部分指定，中间用斜杠分隔。第一部分是更通用的类型，例如“audio”。第二部分是更具体的类型，例如“mpeg”。通用类型“audio”和更具体类型“mpeg”将产生 MIME 类型字符串 `audio/mpeg`，这通常是用于 MP3 文件的 MIME 类型。

以下是通过 Intent 触发内置音频播放应用程序的完整示例：

```java
package com.apress.proandroidmedia.ch5.intentaudioplayer;

import java.io.File;

import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;

我们的 Activity 将监听按钮按下事件，然后才触发音频播放。它实现了 OnClickListener 接口以便做出响应。

public class AudioPlayer extends Activity implements OnClickListener {

    Button playButton;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

将内容视图设置为我们 XML 布局后，我们可以在代码中获取 Button 的引用，并将我们的 Activity（this）设置为 OnClickListener。

        playButton = (Button) this.findViewById(R.id.Button01);
        playButton.setOnClickListener(this);
    }

当我们的按钮被点击时，onClick 方法被调用。在此方法中，我们使用通用的 android.content.Intent.ACTION_VIEW 构造 Intent，然后创建一个 File 对象，该对象是对 SD 卡上存在的音频文件的引用。在这种情况下，音频文件是手动放置在 SD 卡“Music”目录中的文件，该目录是音乐相关音频文件的标准位置。

    public void onClick(View v) {
        Intent intent = new Intent(android.content.Intent.ACTION_VIEW);
        File sdcard = Environment.getExternalStorageDirectory();
        File audioFile = new File(sdcard.getPath() + "/Music/goodmorningandroid.mp3"); 

        接下来，我们将 Intent 的数据设置为从音频文件派生的 Uri，并将类型设置为其 MIME 类型 audio/mp3。最后，我们通过调用 startActivity 并传入我们的 Intent 来触发内置应用程序启动。图 5-1 显示了内置应用程序正在播放该音频文件。

        intent.setDataAndType(Uri.fromFile(audioFile), "audio/mp3");
        startActivity(intent);
    }
}
```

这是一个简单的布局 XML 文件，指定了文本为“Play Audio”的 Button，用于上述 Activity。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <Button android:text="Play Audio" android:id="@+id/Button01"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
</LinearLayout>
```

**图 5–1.** Android 内置音乐播放器播放通过 Intent 指定的音频文件。

## 创建自定义音频播放应用程序

当然，我们并不局限于使用 Android 内置的音频播放应用程序。我们可以编写自己的应用程序，提供播放能力及更多功能。

为此，Android 包含一个 `MediaPlayer` 类。这个类用于播放和控制音频和视频。现在我们只使用其音频播放能力。

最简单的 `MediaPlayer` 示例是回放与应用程序本身打包在一起的音频文件。为此，需要将音频文件放置在应用程序的原始资源文件夹中。要在 Eclipse 中使用 Android 开发者工具执行此操作，我们需要在项目的 `res` 文件夹中创建一个名为 `raw` 的新文件夹，如图 5-2 所示。Android 开发者工具将在 `R.java` 文件（位于 `gen` 文件夹中）中为此文件生成一个资源 ID，语法为 `R.raw.file_name_without_extension`。

**图 5–2.** 自定义音频播放器的 Eclipse 项目布局，显示了位于 `res` 文件夹内 `raw` 文件夹中的音频文件。

### 启动媒体播放器

为此音频文件创建一个 `MediaPlayer` 很简单。我们使用静态方法 `create` 实例化一个 `MediaPlayer` 对象，将 `this` 作为 `Context`（`Activity` 继承自它）和音频文件生成的资源 ID 传递给它。

```java
MediaPlayer mediaPlayer = MediaPlayer.create(this, R.raw.goodmorningandroid);
```

之后，我们只需在 `MediaPlayer` 对象上调用 `start` 方法来播放它。

```java
mediaPlayer.start();
```

#### 本地资源

在 Android 开发者工具 / Eclipse 项目的 `res` 文件夹中放置资源时，需要考虑两件事：文件扩展名和使用 `Uri`。

**文件扩展名**

扩展名会被移除，因此基本名称相同但扩展名不同的文件会引起问题。

你不应该将名为 `goodmorningandroid.mp3` 和另一个名为 `goodmorningandroid.m4a` 的文件放在里面。相反，如果你想以多种格式提供相同的音频，最好将格式作为文件名的一部分包含在内，这样你就可以区分它们，并且 Android 开发者工具在生成资源 ID 时也不会出现问题。如果你将它们命名为 `goodmorningandroid_mp3.mp3` 和 `goodmorningandroid_m4a.m4a`，你就可以分别将它们引用为 `R.raw.goodmorningandroid_mp3` 和 `R.raw.goodmorningandroid_m4a`。

**资源文件的 Uri**

虽然资源 ID 对某些目的来说很好，但它们并不适用于所有情况。正如我们已经知道的，Android 中的许多事情都可以使用 `Uri` 来完成。幸运的是，为放置在资源中的文件构造一个 `Uri` 很容易。资源 ID 可以附加到一个字符串的末尾，该字符串可用于构造 `Uri`。该字符串必须以 `android.resource://` 开头，后跟资源所在的应用程序的包名，再后跟文件的资源 ID。

以下是一个示例：

```java
Uri fileUri = Uri.parse("android.resource://com.apress.proandroidmedia.ch5.customaudio/"
    + R.raw.goodmorningandroid);
```

要使用 `MediaPlayer` 与 `Uri` 而不是资源 ID（如果文件不是应用程序的一部分，我们将不得不这样做），我们可以调用一个 `create` 方法，传入上下文和 `Uri`。

```java
MediaPlayer mediaPlayer = MediaPlayer.create(this, fileUri);
```

### 控制播放

`MediaPlayer` 类有几个嵌套类，这些类是用于监听 `MediaPlayer` 发送的事件的接口。这些事件与状态变化有关。

例如，当音频文件播放完毕时，`MediaPlayer` 将在实现了 `OnCompletionListener` 并通过 `setOnCompletionListener` 注册的类上调用 `onCompletion` 方法。

以下是一个 Activity 的完整示例，它通过使用 `OnCompletionListener` 无限重复播放同一个音频文件。`MediaPlayer` 对象在 `onStart` 方法中初始化并开始播放，在 `onStop` 方法中停止播放并释放 `MediaPlayer` 对象。这可以防止当 Activity 不再在前台时播放音频，但在 Activity 再次被带到前台时重新开始播放。

```java
package com.apress.proandroidmedia.ch5.customaudio;

import android.app.Activity;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnCompletionListener;
import android.os.Bundle;

public class CustomAudioPlayer extends Activity implements OnCompletionListener {

    MediaPlayer mediaPlayer;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }

    public void onStart() {
        super.onStart();
        mediaPlayer = MediaPlayer.create(this, R.raw.goodmorningandroid);
        mediaPlayer.setOnCompletionListener(this);
        mediaPlayer.start();
    }

    public void onStop() {
        super.onStop();
        mediaPlayer.stop();
        mediaPlayer.release();
    }

    public void onCompletion(MediaPlayer mp) {
        mediaPlayer.start();
    }
}
```

当然，这也可以不使用 `OnCompletionListener` 来实现，只需使用 `setLooping(true)` 方法将 `MediaPlayer` 设置为循环播放即可。

让我们更进一步，让播放由触摸事件控制。这段代码可能是制作 DJ 音频搓盘应用程序的一个良好起点。

```java
package com.apress.proandroidmedia.ch5.customaudio;

import android.app.Activity;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnCompletionListener;
import android.os.Bundle;
import android.util.Log;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.View.OnTouchListener;
import android.widget.Button;

我们的 Activity 将像上一个例子一样实现 OnCompletionListener，但也会实现 OnTouchListener，以便响应触摸事件，以及 OnClickListener，以便在用户点击按钮时做出响应。

public class CustomAudioPlayer extends Activity implements OnCompletionListener,
        OnTouchListener, OnClickListener {

    当然，我们需要一个对 MediaPlayer 对象的引用。我们需要访问一个 View，以便注册我们想要触摸事件，并且我们需要访问我们在布局 XML 文件中定义的任何按钮——在本例中，是一个停止播放的按钮和一个开始播放的按钮。

    MediaPlayer mediaPlayer;
    View theView;
    Button stopButton, startButton;

    我们将声明一个变量，用于保存音频文件中的已保存位置。稍后我们将使用这个位置来确定从何处开始播放音频文件。

    int position = 0;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        使用我们通常的 findViewById 函数，我们将获得对布局 XML 中定义的按钮和视图的访问。

        stopButton = (Button) this.findViewById(R.id.StopButton);
        startButton = (Button) this.findViewById(R.id.StartButton);

        由于我们的 Activity 可以响应点击事件，我们将让它被注册为两个按钮的监听器。

        startButton.setOnClickListener(this);
        stopButton.setOnClickListener(this);

        theView = this.findViewById(R.id.theview);

        然后，我们将让我们的 Activity（this）响应触摸事件。

        theView.setOnTouchListener(this);

        在此应用程序中，我们的音频文件名为 goodmorningandroid.mp3，已放置在项目的 res/raw 文件夹中。我们将使用此文件创建我们的 MediaPlayer 对象。此外，与上一个示例一样，我们将我们的 Activity 设置为 MediaPlayer 的 OnCompletionListener。

        mediaPlayer = MediaPlayer.create(this, R.raw.goodmorningandroid);
        mediaPlayer.setOnCompletionListener(this);
        mediaPlayer.start();
    }

    这里定义了我们 的 onCompletion 方法。每当 MediaPlayer 播放完我们的音频文件时，它就会被调用。在这种情况下，我们将首先调用 start 来播放音频，然后调用 seek 到已保存的位置。音频需要先开始播放，我们才能进行 seek。

    public void onCompletion(MediaPlayer mp) {
        mediaPlayer.start();
        mediaPlayer.seekTo(position);
    }

    当用户触发触摸事件时，onTouch 方法被调用。在此方法中，我们只关注 ACTION_MOVE 触摸事件，该事件在用户将手指拖过视图表面时触发。在这种情况下，我们将确保 MediaPlayer 正在播放，然后根据触摸事件在屏幕上的位置计算我们应该 seek 到哪里。如果它发生在屏幕的右边界附近，我们将 seek 到文件末尾附近。如果它发生在屏幕的左边界附近，我们将 seek 到文件开头附近。我们将此值保存在 position 变量中，以便当音频文件播放完毕，重新开始播放时，它会跳回到那个点（在 onCompletion 方法中）。

    public boolean onTouch(View v, MotionEvent me) {
        if (me.getAction() == MotionEvent.ACTION_MOVE) {
            if (mediaPlayer.isPlaying()) {
                position = (int) (me.getX() *
                        mediaPlayer.getDuration()/theView.getWidth());
                Log.v("SEEK",""+position);
                mediaPlayer.seekTo(position);
            }
        }
        return true;
    }

    最后，我们有一个 onClick 方法来响应按钮点击。这些点击用于暂停和开始音频播放。

    public void onClick(View v) {
        if (v == stopButton) {
            mediaPlayer.pause();
        } else if (v == startButton) {
            mediaPlayer.start();
        }
    }
}
```

以下是上述代码示例中引用的布局 XML 文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <Button android:text="Start" android:id="@+id/StartButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <Button android:text="Stop" android:id="@+id/StopButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <View android:layout_width="fill_parent" android:layout_height="fill_parent"
        android:id="@+id/theview" />
</LinearLayout>
```

正如你所看到的，在 Android 上构建自定义音频播放器开辟了一些有趣的 possibilities。可以构建的应用程序不仅仅是直接播放音频。将其变成一个功能齐全的手机 DJ 应用程序可能会很有趣。

## 用于音频的 MediaStore

我们在本书前面探讨了如何使用 `MediaStore` 处理图像。我们学到的大部分知识都可以用于其他类型媒体（包括音频）的存储和检索。为了提供一种健壮的浏览和搜索音频的机制，Android 包含一个 `MediaStore.Audio` 包，它定义了标准的内容提供器。

### 从 MediaStore 访问音频

访问使用 `MediaStore` 提供器存储的音频文件与我们之前使用 `MediaStore` 的方式是一致的。在本例中，我们将使用 `android.provider.MediaStore.Audio` 包。

通过一个示例应用程序来说明 `MediaStore` 在音频方面的使用是最简单的方法之一。以下代码创建了一个 Activity，它查询 `MediaStore` 获取任何音频文件，并简单地播放返回的第一个文件。

```java
package com.apress.proandroidmedia.ch5.audioplayer;

import java.io.File;

import android.app.Activity;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.provider.MediaStore;

public class AudioPlayer extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        要使用 MediaStore，我们需要指定要返回哪些数据。我们通过使用位于 android.provider.MediaStore.Audio.Media 类中的常量创建一个字符串数组来实现这一点。这些常量是保存在 MediaStore 中用于音频的所有标准字段。

        在本例中，我们请求 DATA 列，该列包含实际音频文件的路径。我们还请求内部 ID、标题、显示名称、MIME 类型、艺术家、专辑以及音频文件的类型——是闹钟、音乐、铃声还是通知类型。

        其他列，例如添加日期（DATE_ADDED）、修改日期（DATE_MODIFIED）、文件大小（SIZE）等也可用。

        String[] columns = {
            MediaStore.Audio.Media.DATA,
            MediaStore.Audio.Media._ID,
            MediaStore.Audio.Media.TITLE,
            MediaStore.Audio.Media.DISPLAY_NAME,
            MediaStore.Audio.Media.MIME_TYPE,
            MediaStore.Audio.Media.ARTIST,
            MediaStore.Audio.Media.ALBUM,
            MediaStore.Audio.Media.IS_RINGTONE,
            MediaStore.Audio.Media.IS_ALARM,
            MediaStore.Audio.Media.IS_MUSIC,
            MediaStore.Audio.Media.IS_NOTIFICATION
        };

        我们通过调用 Activity 中的 managedQuery 方法来查询 MediaStore。managedQuery 方法接受内容提供器的 Uri，在本例中是音频 MediaStore，即 android.provider.MediaStore.Audio.Media.EXTERNAL_CONTENT_URI。这个 Uri 指定我们想要存储在 SD 卡上的音频。如果我们想要存储在内部存储器中的音频文件，我们将使用 android.provider.MediaStore.Audio.Media.INTERNAL_CONTENT_URI。

        除了 MediaStore 的 Uri，managedQuery 方法还接受我们想要返回的列数组、一个 SQL WHERE 子句、WHERE 子句的值以及一个 SQL ORDER BY 子句。

        在这个例子中，我们没有使用 WHERE 和 ORDER BY 子句，所以我们将为这些参数传递 null。

        Cursor cursor = managedQuery(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI,
                columns, null, null, null);

        managedQuery 方法返回一个 Cursor 对象。Cursor 类允许与数据库查询返回的数据集进行交互。

        我们要做的第一件事是创建几个变量来保存我们想要从结果中访问的一些列的列号。这不是绝对必要的，但拥有索引很方便，这样我们就不必在每次需要时都调用 Cursor 对象的方法。我们通过将要获取的列的常量值传递给 Cursor 的 getColumnIndex 方法来获取它们。

        int fileColumn = cursor.getColumnIndex (MediaStore.Audio.Media.DATA);

        第一个是包含实际音频文件路径的列的索引。我们通过传递表示该列的常量 android.provider.MediaStore.Audio.Media.DATA 来获取上述索引。

        接下来我们获取其他几个索引，并非所有索引我们都实际使用，所以额外的索引仅用于说明目的。

        int titleColumn = cursor.getColumnIndex (MediaStore.Audio.Media.TITLE);
        int displayColumn = cursor.getColumnIndex (MediaStore.Audio.Media.DISPLAY_NAME);
        int mimeTypeColumn = cursor.getColumnIndex (MediaStore.Audio.Media.MIME_TYPE);

        MediaStore 返回的数据在 Cursor 中按行和列组织。我们可以通过调用 moveToFirst 方法并从中检索结果来获取第一个返回的结果。如果没有返回任何行，该方法将返回布尔值 false，因此我们可以将其包装在 if 语句中以确保有数据。

        if (cursor.moveToFirst()) {

            要获取实际数据，我们在 Cursor 上调用一个“get”方法，并传入我们要检索的列的索引。如果数据预期是 String，我们调用 getString。如果预期是整数，我们调用 getInt。对于所有原始数据类型，都有相应的“get”方法。

            String audioFilePath = cursor.getString(fileColumn);
            String mimeType = cursor.getString(mimeTypeColumn);

            Log.v("AUDIOPLAYER",audioFilePath);
            Log.v("AUDIOPLAYER",mimeType);

            一旦我们有了文件的路径和 MIME 类型，我们就可以使用它们来构造 Intent 以启动内置音频播放应用程序并播放该文件。（或者，我们可以像前面说明的那样使用 MediaPlayer 类来直接播放音频文件。）为了将音频文件的路径转换为可以传递给 Intent 的 Uri，我们构造一个 File 对象并使用 Uri.fromFile 方法来获取 Uri。还有其他方法可以做到同样的事情，但这可能是最直接的方法。

            Intent intent = new Intent(android.content.Intent.ACTION_VIEW);
            File newFile = new File(audioFilePath);
            intent.setDataAndType(Uri.fromFile(newFile), mimeType);
            startActivity(intent);
        }
    }
}
```

这完成了我们对使用 `MediaStore` 处理音频的基本说明。

现在，让我们更进一步，创建一个允许我们缩小返回结果范围并浏览它们，从而允许用户选择要播放的音频文件的应用程序。

### 在 MediaStore 中浏览音频

音频文件，尤其是音乐文件，可以通过专辑、艺术家和流派以及直接在 `MediaStore` 中找到。每一个都有一个可以与 `managedQuery` 一起使用的 `Uri` 来搜索。

*   **专辑**：`android.provider.MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI`
*   **艺术家**：`android.provider.MediaStore.Artists.EXTERNAL_CONTENT_URI`
*   **流派**：`android.provider.MediaStore.Genres.EXTERNAL_CONTENT_URI`

以下是您如何使用专辑 `Uri` 查询设备上所有专辑的方法：

```java
String[] columns = { android.provider.MediaStore.Audio.Albums._ID,
        android.provider.MediaStore.Audio.Albums.ALBUM};
Cursor cursor = managedQuery(MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI, columns,
        null, null, null);
if (cursor != null) {
    while (cursor.moveToNext()) {
        Log.v("OUTPUT",
                cursor.getString(cursor.getColumnIndex(MediaStore.Audio.Albums.ALBUM)));
    }
}
```

在上面的代码片段中，您会看到我们要求 `MediaStore` 返回 `_ID` 和 `ALBUM` 列。`ALBUM` 常量表示我们希望返回专辑的名称。其他可用的列在 `android.provider.MediaStore.Audio.Albums` 类中列出，并继承自 `android.provider.BaseColumns` 和 `android.provider.MediaStore.Audio.AlbumColumns`。

我们调用 `managedQuery` 方法，只提供 `Uri` 和列列表，其他参数保留为 `null`。这将为我们提供设备上所有可用的专辑。

最后，我们输出专辑列表。要遍历 `Cursor` 对象内返回的列表，我们首先检查 `Cursor` 是否包含结果（`cursor != null`），然后使用 `moveToNext` 方法。

#### 专辑浏览应用示例

以下是一个示例，以上述内容为起点，允许用户查看所有专辑的名称。用户可以指示他想看哪张专辑上的歌曲。然后它将显示歌曲列表，如果用户选择了其中一首，它将播放该歌曲。

```java
package com.apress.proandroidmedia.ch5.audiobrowser;

import java.io.File;

import android.app.ListActivity;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.MediaStore;
import android.util.Log;
import android.view.View;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;

我们不扩展一个通用的 Activity，而是扩展 ListActivity。这使我们能够呈现和管理一个基本的 ListView。

public class AudioBrowser extends ListActivity {

    Cursor cursor;

    让我们创建几个常量，帮助我们跟踪用户在应用程序中的位置，并在用户执行操作时做出适当响应。这将通过 currentState 变量来跟踪，该变量初始设置为 STATE_SELECT_ALBUM。

    public static int STATE_SELECT_ALBUM = 0;
    public static int STATE_SELECT_SONG = 1;
    int currentState = STATE_SELECT_ALBUM;

    与普通的 Activity 一样，我们有一个 onCreate 方法，可以在其中执行初始命令。

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        设置布局（通过 main.xml 布局 XML 文件）后，我们创建一个字符串数组，表示在运行查询时我们希望从 MediaStore 返回的列。在本例中，它与上面的代码片段相同——我们想要 _ID 和专辑名称 ALBUM。两者都是 MediaStore.Audio.Albums 类中的常量。

        String[] columns = {
            android.provider.MediaStore.Audio.Albums._ID,
            android.provider.MediaStore.Audio.Albums.ALBUM
        };

        我们调用 managedQuery 方法，只传入代表专辑搜索的 Uri 和列，其他所有内容都设为 null。这应该会给我们一个所有可用专辑的列表。

        cursor = managedQuery(MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI, columns,
                null, null, null);

        完成此操作后，我们会返回一个 Cursor 对象，其中包含我们的查询结果。

        由于我们使用 ListActivity，我们可以让它自动为我们管理数据列表。我们可以使用 setListAdapter 方法将我们的 Cursor 对象绑定到 ListView。

        首先，我们创建一个字符串数组，该数组是我们要显示的 Cursor 中的列名。在我们的例子中，我们只想要专辑的名称——MediaStore.Audio.Albums.ALBUM 是它的常量。

        接下来，我们列出将显示来自这些列数据的视图对象。因为我们只有一列，所以只需要一个视图对象。它是 android.R.id.text1。这个视图是我们可以使用的，因为它是我们下一步将要使用的 android.R.layout.simple_list_item_1 布局的一部分。

        最后，我们调用 setListAdapter 方法，传入一个 SimpleCursorAdapter，我们正在内联创建它。SimpleCursorAdapter 是一个简单的适配器，用于将包含数据的 Cursor 对象适配到 ListActivity。在创建 SimpleCursorAdapter 时，我们传入我们的 Activity（this）作为 Context，一个已经为我们定义好的标准 ListView 布局（android.R.layout.simple_list_item_1），包含数据的 Cursor 对象，以及我们刚刚定义的两个数组。

        String[] displayFields = new String[] {MediaStore.Audio.Albums.ALBUM};
        int[] displayViews = new int[] {android.R.id.text1};
        setListAdapter(new SimpleCursorAdapter(this,
                android.R.layout.simple_list_item_1, cursor, displayFields,
                displayViews));
    }

    如果我们就这样运行，我们会得到一个简单的设备上可用专辑列表，如图 5-3 所示。不过，我们将更进一步，允许用户选择一个专辑。

    **图 5–3.** 在基本 ListView 中列出的专辑。

    为了允许用户实际选择一个专辑，我们需要重写由我们的 ListActivity 父类提供的默认 onListItemClick 方法。

    protected void onListItemClick(ListView l, View v, int position, long id) {
        if (currentState == STATE_SELECT_ALBUM) {

            当列表中的专辑被选中时，这个方法将被调用。由于我们的 currentState 变量最初是 STATE_SELECT_ALBUM，第一次调用此方法时它应为 true。

            所选专辑在列表中的位置将被传入，并且可以通过调用 moveToPosition 方法与 Cursor 对象一起使用，以获取关于是哪个专辑的数据。

            if (cursor.moveToPosition(position)) {

                假设 moveToPosition 成功，我们将重新开始查询 MediaStore。不过这一次，我们将在 MediaStore.Audio.Media.EXTERNAL_CONTENT_URI 上运行我们的 managedQuery，因为我们想要访问单个媒体文件。

                首先我们选择我们想要返回的列。

                String[] columns = {
                    MediaStore.Audio.Media.DATA,
                    MediaStore.Audio.Media._ID,
                    MediaStore.Audio.Media.TITLE,
                    MediaStore.Audio.Media.DISPLAY_NAME,
                    MediaStore.Audio.Media.MIME_TYPE,
                };

                接下来，我们需要为查询构建一个 SQL WHERE 子句。因为我们只想要属于特定专辑的媒体文件，所以我们的 WHERE 子句应该表明这一点。

                在普通 SQL 中，WHERE 子句看起来像这样：

                WHERE album = ‘专辑名称’

                由于我们使用 managedQuery，我们不需要单词 WHERE，也不需要传入它应该等于什么。相反，我们替换为一个 '?'。因此对于上述版本，字符串如下所示：

                album = ?

                由于我们使用常量工作，我们不知道列的实际名称。我们将使用它来构建 WHERE 子句。

                String where = android.provider.MediaStore.Audio.Media.ALBUM + "=?";

                完成 WHERE 子句，我们需要将替换 WHERE 子句中 ? 的数据。这将是一个字符串数组，每个 ? 对应一个。在我们的例子中，我们想使用被选中的专辑的名称。由于我们将 Cursor 定位在了正确的位置，我们只需在正确的列上调用 Cursor 的 getString 方法，该列通过调用 Cursor 在列名上的 getColumnIndex 方法获得。

                String whereVal[] = {cursor.getString(cursor.getColumnIndex(MediaStore.Audio.Albums.ALBUM))};

                最后，我们可以指定我们希望结果按特定列的值排序。为此，让我们创建一个字符串变量，其中包含我们希望结果按之排序的列的名称。

                String orderBy = android.provider.MediaStore.Audio.Media.TITLE;

                最终，我们可以运行我们的 managedQuery 方法，传入 Uri、列、WHERE 子句变量、WHERE 子句数据和 ORDER BY 变量。

                cursor = managedQuery(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI,
                        columns, where, whereVal, orderBy);

                同样，我们将使用 ListActivity 方法来管理 Cursor 对象，并在列表中呈现结果。

                String[] displayFields = new String[] {MediaStore.Audio.Media.DISPLAY_NAME};
                int[] displayViews = new int[] {android.R.id.text1};
                setListAdapter(new SimpleCursorAdapter(this,
                        android.R.layout.simple_list_item_1, cursor, displayFields,
                        displayViews));

                我们要做的最后一件事是将 currentState 变量更改为 STATE_SELECT_SONG，以便下次执行此方法时，我们跳过所有这些，因为用户将选择一首歌而不是一张专辑。

                currentState = STATE_SELECT_SONG;
            }
        } else if (currentState == STATE_SELECT_SONG) {

            当用户在选择专辑后从列表中选择一首歌时，因为 currentState 将等于 STATE_SELECT_SONG，他将进入此方法的这一部分。

            if (cursor.moveToPosition(position)) {

                使用与之前相同的对 Cursor 对象的 moveToPosition 调用，我们可以访问实际被选中的歌曲。在这种情况下，我们获取包含文件路径的列，并



# 章节

## 背景与网络音频

在上一章中，我们探讨了 Android 的基本音频播放功能。虽然这些功能非常出色，但我们需要进一步挖掘，使其更具通用性。在本章中，我们将研究如何实现诸如后台播放音频文件之类的操作，这样播放音频的应用无需保持在前台运行。我们还将探讨如何合成声音，而不仅仅是播放音频文件，并了解如何利用互联网上的流媒体音频。

## 后台音频播放

到目前为止，我们一直专注于构建以前台为中心的应用，这些应用的用户界面始终呈现在用户面前。在上一章中，我们学习了如何为这类应用添加音频播放功能。

然而，如果我们想要构建一个播放音乐或有声书的应用程序，同时希望用户在收听过程中能够使用手机执行其他操作，这时会发生什么呢？如果我们只局限于构建 Activity，可能会遇到一些困难。Android 操作系统有权终止不在前台且未被用户使用的 Activity。这样做是为了释放内存，为其他应用程序的运行腾出空间。如果操作系统终止了正在播放音频的 Activity，音频就会停止播放，这会严重影响用户体验。

幸运的是，有一个解决方案。我们可以使用 Service，而不是在 Activity 中播放音频。

## 服务

为了确保当应用程序不再处于前台且其 Activity 未被使用时音频仍能继续播放，我们需要创建一个 Service。Service 是 Android 应用程序的一个组件，旨在后台运行任务，无需用户进行任何交互。

### 本地服务与远程服务

Android 使用两种不同类型的 Service。第一种是我们将要探讨的**本地服务**。本地服务作为特定应用程序的一部分存在，仅由该应用程序访问和控制。另一种是**远程服务**。它们可以与其他应用程序通信、被访问和控制。如前所述，我们将专注于使用本地服务来实现音频播放功能。开发远程服务是一个非常大的主题，遗憾的是超出了本书的范围。

### 简单本地服务

为了演示 Service，让我们通过这个非常简单的例子来了解。

首先，我们需要一个包含界面的 Activity，该界面能让我们启动和停止 Service。

```
package com.apress.proandroidmedia.ch06.simpleservice;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;

public class SimpleServiceActivity extends Activity implements OnClickListener {
```

我们的 Activity 将包含两个按钮——一个用于启动 Service，另一个用于停止 Service。

```
Button startServiceButton;
Button stopServiceButton;
```

为了启动或停止 Service，我们使用标准的 intent。

```
Intent serviceIntent;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
```

我们的 Activity 实现了`OnClickListener`，因此我们将每个按钮的`OnClickListener`设置为`this`。

```
startServiceButton = (Button) this.findViewById(R.id.StartServiceButton);
stopServiceButton = (Button) this.findViewById(R.id.StopServiceButton);
startServiceButton.setOnClickListener(this);
stopServiceButton.setOnClickListener(this);
```

在实例化用于启动和停止 Service 的 intent 时，我们将 Activity 作为`Context`传入，随后是 Service 的类。

```
serviceIntent = new Intent(this, SimpleServiceService.class);
}

public void onClick(View v) {
    if (v == startServiceButton) {
```

当点击`startServiceButton`时，我们调用`startService`方法，并传入刚刚创建好的用于引用我们 Service 的 intent。`startService`方法是`Context`类的一部分，而 Activity 是`Context`的子类。

```
startService(serviceIntent);
    }
    else if (v == stopServiceButton) {
```

当点击`stopServiceButton`时，我们调用`stopService`方法，并传入同一个引用我们 Service 的 intent。与`startService`一样，`stopService`也是`Context`类的一部分。

```
stopService(serviceIntent);
    }
}
}
```

这是定义上述 Activity 布局的`main.xml`文件。它包含`StartService`和`StopService`按钮以及一个`TextView`。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
>
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Simple Service"
    />
    <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:id="@+id/StartServiceButton" android:text="Start Service"></Button>
    <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="Stop Service" android:id="@+id/StopServiceButton"></Button>
</LinearLayout>
```

现在我们可以继续编写 Service 本身的代码。在这个例子中，我们并没有在 Service 中完成任何实际任务，只是使用`Toast`来告诉我们 Service 何时启动和停止。

```
package com.apress.proandroidmedia.ch06.simpleservice;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.util.Log;
```

Services 继承自`android.app.Service`类。`Service`类是一个抽象类，因此要继承它，我们至少需要实现`onBind`方法。



# 第 6 章：背景与网络音频

在这个简单示例中，我们不会“绑定”到`Service`。因此我们只需在`onBind`方法中返回`null`。

```java
public class SimpleServiceService extends Service {

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
```

接下来的三个方法代表了`Service`的生命周期。与`Activity`中的`onCreate`方法类似，该方法会在`Service`被实例化时调用。它将是第一个被调用的方法。

```java
    @Override
    public void onCreate() {
        Log.v("SIMPLESERVICE", "onCreate");
    }
```

每当调用`startService`并传入匹配此`Service`的 intent 时，都会调用`onStartCommand`方法。因此它可能被多次调用。`onStartCommand`返回一个整数值，表示操作系统在杀死`Service`后应执行的操作。我们这里使用的`START_STICKY`表示如果`Service`被杀死，它会重新启动。

```java
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.v("SIMPLESERVICE", "onStartCommand");
        return START_STICKY;
    }
```

### `onStartCommand` 与 `onStart` 的区别

`onStartCommand`方法是在 Android 2.0（API Level 5）中引入的。在此之前，使用的是`onStart`方法。`onStart`的参数是一个`Intent`和一个`startId`整型值。它不包含`int flags`参数，也没有返回值。如果你的应用面向运行 2.0 之前版本系统的手机，可以使用`onStart`方法。

```java
    @Override
    public void onStart(Intent intent, int startid) {
        Log.v("SIMPLESERVICE", "onStart");
    }
```

当操作系统销毁`Service`时会调用`onDestroy`方法。在本例中，它由我们的 Activity 调用`stopService`方法时触发。此方法应执行关闭`Service`时所需的任何清理工作。

```java
    public void onDestroy() {
        Log.v("SIMPLESERVICE", "onDestroy");
    }

}
```

最后，为了让这个示例正常运行，我们需要在清单文件（`AndroidManifest.xml`）中添加一个声明`Service`的条目。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch06.simpleservice"
    android:versionCode="1"
    android:versionName="1.0">

    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".SimpleServiceActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <service android:name=".SimpleServiceService" />
    </application>

    <uses-sdk android:minSdkVersion="5" />

</manifest>
```

当然，这个示例除了在日志中输出`Service`何时启动和停止外，并没有做其他任何事情。让我们继续前进，让我们的`Service`真正做一些事情。

## 本地服务加 MediaPlayer

既然我们已经创建了一个简单的`Service`示例，就可以将其作为模板来创建一个在后台播放音频文件的应用程序。下面是一个`Service`和一个用于控制该`Service`的 Activity，它们共同实现了在后台播放音频文件的功能。其工作原理与上一章的自定义音频播放器示例类似，因为它使用了 Android 提供的同一个底层`MediaPlayer`类。

```java
package com.apress.proandroidmedia.ch06.backgroundaudio;

import android.app.Service;
import android.content.Intent;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnCompletionListener;
import android.os.IBinder;
import android.util.Log;
```

`Service`实现了`OnCompletionListener`接口，以便在`MediaPlayer`完成音频文件播放时得到通知。

```java
public class BackgroundAudioService extends Service implements OnCompletionListener {

    // 声明一个 MediaPlayer 类型的对象。
    // 该对象将处理音频的播放，如上一章自定义音频播放器示例所示。
    MediaPlayer mediaPlayer;

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
```



# 第 6 章：后台与网络音频

```
public void onCreate() {
Log.v("PLAYERSERVICE","onCreate");
```

在`onCreate`方法中，我们实例化了`MediaPlayer`。我们将一个对名为`goodmorningandroid.mp3`的音频文件的特定引用传递给它，该文件应放置在我们项目的原始资源（`res/raw`）目录中。如果该目录不存在，则应创建它。将音频文件放在该位置，使我们能够通过生成的`R`类中的常量`R.raw.goodmorningandroid`来引用它。关于将音频放置在原始资源目录中的更多细节，请参见第 5 章的“创建自定义音频播放应用程序”一节。

```
mediaPlayer = MediaPlayer.create(this, R.raw.goodmorningandroid);
```

我们还设置了我们的 Service（即这个类）作为`MediaPlayer`对象的`OnCompletionListener`。

```
mediaPlayer.setOnCompletionListener(this);
}
```

当对此 Service 发出`startService`命令时，将触发`onStartCommand`方法。在此方法中，我们首先检查`MediaPlayer`对象是否已经在播放，因为该方法可能会被多次调用，如果它没有播放，我们就启动它。由于我们使用的是`onStartCommand`方法而不是`onStart`方法，因此此示例仅在 Android 2.0 及以上版本中运行。

```
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
Log.v("PLAYERSERVICE","onStartCommand");
if (!mediaPlayer.isPlaying()) {
mediaPlayer.start();
}
return START_STICKY;
}
```

当 Service 被销毁时，会触发`onDestroy`方法。由于这并不能保证`MediaPlayer`会停止播放，因此如果它正在播放，我们在此处调用其`stop`方法，并调用其`release`方法以释放所有内存占用和/或资源锁。

```
public void onDestroy() {
if (mediaPlayer.isPlaying())
{
mediaPlayer.stop();
}
mediaPlayer.release();
Log.v("SIMPLESERVICE","onDestroy");
}
```

由于我们实现了`OnCompletionListener`，并且 Service 本身被设置为`MediaPlayer`的`OnCompletionListener`，因此当`MediaPlayer`播放完一个音频文件时，会调用下面的`onCompletion`方法。由于此 Service 仅用于播放一首歌曲，因此我们调用`stopSelf`，这类似于在我们的 Activity 中调用`stopService`。

```
public void onCompletion(MediaPlayer _mediaPlayer) {
stopSelf();
}
}
```

以下是对应于上述 Service 的 Activity。它有一个非常简单的界面，包含两个按钮用于控制 Service 的启动和停止。在每种情况下，它都会立即调用`finish`，以说明 Service 不依赖于 Activity 并独立运行。

```
package com.apress.proandroidmedia.ch06.backgroundaudio;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;

public class BackgroundAudioActivity extends Activity implements OnClickListener {

Button startPlaybackButton, stopPlaybackButton;
Intent playbackServiceIntent;

@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);

startPlaybackButton = (Button) this.findViewById(R.id.StartPlaybackButton);
stopPlaybackButton = (Button) this.findViewById(R.id.StopPlaybackButton);

startPlaybackButton.setOnClickListener(this);
stopPlaybackButton.setOnClickListener(this);

playbackServiceIntent = new Intent(this,BackgroundAudioService.class);
}

public void onClick(View v) {
if (v == startPlaybackButton) {
startService(playbackServiceIntent);
finish();
} else if (v == stopPlaybackButton) {
stopService(playbackServiceIntent);
finish();
}
}
}
```

以下是上述 Activity 使用的`main.xml`布局 XML 文件。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent"
>
<TextView
```


  
# 第六章：后台音频与网络音频

`android:layout_width="fill_parent"`

`android:layout_height="wrap_content"`

`android:text="后台音频播放器"`

`/>`

`<Button android:text="开始播放" android:id="@+id/StartPlaybackButton"`  
`android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>`  
`<Button android:text="停止播放" android:id="@+id/StopPlaybackButton"`  
`android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>`  
`</LinearLayout>`

最后，这是该项目的 `AndroidManifest.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch06.backgroundaudio"
    android:versionCode="1"
    android:versionName="1.0">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".BackgroundAudioActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <service android:name=".BackgroundAudioService" />
    </application>
    <uses-sdk android:minSdkVersion="5" />
</manifest>
```

如上所示，简单地在 `Service` 中使用 `MediaPlayer` 来启动和停止音频播放是非常直接的。接下来，我们看看如何在此基础上更进一步。

## 在 Service 中控制 MediaPlayer

不幸的是，当使用 `Service` 时，从面向用户的活动（Activity）向 `MediaPlayer` 发送命令会变得更加复杂。

为了让 `MediaPlayer` 能够被控制，我们需要将 Activity 和 `Service` 绑定在一起。一旦绑定完成，由于 Activity 和 `Service` 运行在同一个进程中，我们可以直接调用 `Service` 中的方法。如果我们要创建一个远程 `Service`，那还需要采取进一步的措施。

让我们在上述 Activity 中添加一个名为“找乐子”的 `Button`。当这个 `Button` 被按下时，我们将让 `MediaPlayer` 往回跳转几秒，然后继续播放音频文件。

我们首先在布局 XML 中添加这个 `Button`：

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
>
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="后台音频播放器"
    />
    <Button android:text="开始播放" android:id="@+id/StartPlaybackButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <Button android:text="停止播放" android:id="@+id/StopPlaybackButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
    <Button android:text="找乐子" android:id="@+id/HaveFunButton"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></Button>
</LinearLayout>
```

然后在 Activity 中，我们会获取对这个 Button 的引用，并将其 `onClickListener` 设置为 Activity 本身，就像已有的 `Button` 一样。

```
package com.apress.proandroidmedia.ch06.backgroundaudiobind;

import android.app.Activity;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;

public class BackgroundAudioActivity extends Activity implements OnClickListener {

    Button startPlaybackButton, stopPlaybackButton;
    Button haveFunButton;
    Intent playbackServiceIntent;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        startPlaybackButton = (Button) this.findViewById(R.id.StartPlaybackButton);
        stopPlaybackButton = (Button) this.findViewById(R.id.StopPlaybackButton);
        haveFunButton = (Button) this.findViewById(R.id.HaveFunButton);

        startPlaybackButton.setOnClickListener(this);
        stopPlaybackButton.setOnClickListener(this);
        haveFunButton.setOnClickListener(this);

        playbackServiceIntent = new Intent(this, BackgroundAudioService.class);
    }
}
```

为了让这个 `Button` 能够与 `Service` 中运行的 `MediaPlayer` 交互，我们必须绑定到 `Service`。实现这一点的方法是使用 `bindService` 方法。该方法接收一个 `Intent`（实际上，我们可以复用启动 `Service` 时使用的 `playbackServiceIntent`）、一个 `ServiceConnection` 对象，以及一些用于指定当 `Service` 未运行时的处理方式的标志位。




# 第 6 章：后台与网络音频

在活动（Activity）的以下`onClick`方法中，当按下`startPlaybackButton`按钮时，我们在启动`Service`后立即与其绑定。当调用`stopPlaybackButton`时，我们解除与`Service`的绑定。

```java
public void onClick(View v) {
    if (v == startPlaybackButton) {
        startService(playbackServiceIntent);
        bindService(playbackServiceIntent, serviceConnection,
                Context.BIND_AUTO_CREATE);
    } else if (v == stopPlaybackButton) {
        unbindService(serviceConnection);
        stopService(playbackServiceIntent);
    }
```

你可能会注意到，我们使用了一个尚未定义的新对象`serviceConnection`。我们稍后会处理这个问题。

我们还需要完成`onClick`方法。由于我们的新`Button`的`onClickListener`设置为当前活动，我们也应处理这种情况并关闭`onClick`方法。

```java
    else if (v == haveFunButton) {
        baService.haveFun();
    }
}
```

在这个新片段中，我们使用了另一个新对象`baService`。`baService`是一个`BackgroundAudioService`类型的对象。我们现在声明它，并在创建`ServiceConnection`对象时负责实例化。

```java
private BackgroundAudioService baService;
```

如前所述，我们仍然缺少名为`serviceConnection`的对象的声明和实例化。`serviceConnection`将是`ServiceConnection`类型的对象，`ServiceConnection`是一个用于监控绑定`Service`状态的接口。

现在让我们创建`serviceConnection`：

```java
private ServiceConnection serviceConnection = new ServiceConnection() {
```

这里展示的`onServiceConnected`方法将在通过`bindService`命令与`Service`建立连接时被调用，该命令将此对象指定为`ServiceConnection`（正如我们在`bindService`调用中所做的那样）。

传递给此方法的一个参数是`IBinder`对象，该对象实际上是由`Service`本身创建并传递的。在本例中，这个`IBinder`对象将是`BackgroundAudioServiceBinder`类型，我们将在`Service`中创建它。它有一个返回`Service`本身的方法，名为`getService`。该方法返回的对象可以被直接操作，就像我们在点击`haveFunButton`时所做的那样。

```java
    public void onServiceConnected(ComponentName className, IBinder baBinder) {
        baService =
            ((BackgroundAudioService.BackgroundAudioServiceBinder)baBinder).getService();
    }
```

我们还需要一个`onServiceDisconnected`方法来处理`Service`终止的情况。

```java
    public void onServiceDisconnected(ComponentName className) {
        baService = null;
    }
};
}
```

现在我们可以将注意力转向需要在`Service`本身中修改的内容。

```java
package com.apress.proandroidmedia.ch06.backgroundaudiobind;

import android.app.Service;
import android.content.Intent;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnCompletionListener;
import android.os.Binder;
import android.os.IBinder;
import android.util.Log;

public class BackgroundAudioService extends Service implements OnCompletionListener {

    MediaPlayer mediaPlayer;
```

我们首先需要在`Service`中做出的修改是创建一个内部类，该类扩展`Binder`，并能在被请求时返回`Service`本身。

```java
    public class BackgroundAudioServiceBinder extends Binder {
        BackgroundAudioService getService() {
            return BackgroundAudioService.this;
        }
    }
```

接着，我们将其实例化为一个名为`basBinder`的对象。

```java
    private final IBinder basBinder = new BackgroundAudioServiceBinder();
```

并重写`onBind`的实现以返回该对象。

```java
    @Override
    public IBinder onBind(Intent intent) {
        // 返回 BackgroundAudioServiceBinder 对象
        return basBinder;
    }
```

绑定部分就完成了。现在我们只需要处理“找点乐子”的功能。

如前所述，当点击`haveFunButton`时，我们希望`MediaPlayer`向后回退几秒。在此实现中，它将回退 2500 毫秒，即 2.5 秒。

```java
    public void haveFun() {
        if (mediaPlayer.isPlaying()) {
            mediaPlayer.seekTo(mediaPlayer.getCurrentPosition() - 2500);
        }
    }
```

`Service`的更新就到这里。以下是完整的其余代码。

```java
    @Override
    public void onCreate() {
        Log.v("PLAYERSERVICE","onCreate");
        mediaPlayer = MediaPlayer.create(this, R.raw.goodmorningandroid);
        mediaPlayer.setOnCompletionListener(this);
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.v("PLAYERSERVICE","onStartCommand");
        if (!mediaPlayer.isPlaying()) {
            mediaPlayer.start();
        }
        return START_STICKY;
    }

    public void onDestroy() {
        if (mediaPlayer.isPlaying()) {
            mediaPlayer.stop();
        }
        mediaPlayer.release();
        Log.v("SIMPLESERVICE","onDestroy");
    }

    public void onCompletion(MediaPlayer _mediaPlayer) {
        stopSelf();
    }
}
```

最后，这是上述示例所需的`AndroidManifest.xml`文件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionCode="1"
    android:versionName="1.0" package="com.apress.proandroidmedia.ch06.backgroundaudiobind">

    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".BackgroundAudioActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <service android:name=".BackgroundAudioService" />
    </application>

    <uses-sdk android:minSdkVersion="5" />
</manifest>
```

现在基础已经打好，我们可以向`Service`中添加任何我们想要的功能，并通过绑定从活动（Activity）中直接调用诸如`haveFun`之类的方法。如果不绑定到`Service`，我们除了启动和停止`Service`之外，将无法执行任何其他操作。

上述示例应该为构建后台播放音频文件的应用程序提供了一个良好的起点，允许用户在音频继续播放的同时执行其他任务。第二个示例可以扩展为一个功能齐全的音频播放应用。

## 网络音频

接下来，让我们关注如何进一步利用 Android 的音频播放能力来获取位于别处的媒体资源，特别是网络上的音频。随着 MP3 文件发布、播客和流媒体变得越来越流行，构建能够利用这些服务的音频播放应用显得合情合理。

幸运的是，Android 在处理网络上的各种音频类型方面具有强大的能力。

让我们从研究如何利用基于网络的音频或通过 HTTP 传输的音频开始。

### HTTP 音频播放

最简单的例子就是播放位于网络上且可通过 HTTP 访问的音频文件。

其中一个文件在我的服务器上，地址如下：  
[`www.mobvcasting.com/android/audio/goodmorningandroid.mp3`](http://www.mobvcasting.com/android/audio/goodmorningandroid.mp3)

下面是一个示例活动，它使用`MediaPlayer`来演示如何播放通过 HTTP 获取的音频。

```java
package com.apress.proandroidmedia.ch06.audiohttp;

import java.io.IOException;
import android.app.Activity;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.util.Log;

public class AudioHTTPPlayer extends Activity {

    MediaPlayer mediaPlayer;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
```

当我们的活动被创建时，我们通过无参调用`MediaPlayer`构造函数来通用实例化一个`MediaPlayer`对象。这是一种不同的使用方式...



# 第六章：后台与网络音频

我们先前见过的 `MediaPlayer` 要求我们在播放音频之前先完成一些额外步骤。

`mediaPlayer = new MediaPlayer();`

具体来说，我们需要调用 `setDataSource` 方法，传入想要播放的音频文件的 HTTP 地址。该方法可能会抛出 `IOException` 异常，因此我们必须捕获并处理该异常。

```
try {
    mediaPlayer.setDataSource(
        "http://www.mobvcasting.com/android/audio/goodmorningandroid.mp3");
```

随后我们调用 `prepare` 方法，再调用 `start` 方法，之后音频应开始播放。

```
    mediaPlayer.prepare();
    mediaPlayer.start();
} catch (IOException e) {
    Log.v("AUDIOHTTPPLAYER", e.getMessage());
}
```

运行此示例时，你可能会注意到从应用加载到音频播放之间存在明显的延迟时间。延迟的长短取决于手机用于联网的数据网络速度（以及其他变量）。

如果在代码中添加 `Log` 或 `Toast` 消息，我们会发现这个延迟发生在调用 `prepare` 方法和调用 `start` 方法之间。在 `prepare` 方法运行期间，`MediaPlayer` 正在填充缓冲区，以便即使网络较慢也能流畅播放音频。

`prepare` 方法在执行此操作时实际上会阻塞。这意味着使用此方法的应用程序在 `prepare` 方法完成之前可能会变得无响应。幸运的是，有一种解决方法，那就是使用 `prepareAsync` 方法。该方法会立即返回，并在后台进行缓冲和其他工作，使应用程序能够继续运行。

接下来要注意的是关注 `MediaPlayer` 对象的状态，并实现各种有助于我们追踪其状态的回调函数。

为了掌握 `MediaPlayer` 对象可能处于的各种状态，查看 Android API 参考中 `MediaPlayer` 页面的状态图会很有帮助，如图 6-1 所示。

**图 6-1.** *来自 Android API 参考的 MediaPlayer 状态图*

以下是一个完整的 `MediaPlayer` 示例，它使用了 `prepareAsync` 并实现了多个监听器来追踪其状态。

```
package com.apress.proandroidmedia.ch06.audiohttpasync;

import java.io.IOException;
import android.app.Activity;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnBufferingUpdateListener;
import android.media.MediaPlayer.OnCompletionListener;
import android.media.MediaPlayer.OnErrorListener;
import android.media.MediaPlayer.OnInfoListener;
import android.media.MediaPlayer.OnPreparedListener;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;
```

在这个版本的 HTTP 音频播放器中，我们实现了多个接口。其中两个接口——`OnPreparedListener` 和 `OnCompletionListener`——将帮助我们追踪 `MediaPlayer` 的状态，以便我们避免在不适当的时候尝试播放或停止音频。

```
public class AudioHTTPPlayer extends Activity
        implements OnClickListener, OnErrorListener, OnCompletionListener,
        OnBufferingUpdateListener, OnPreparedListener {

    MediaPlayer mediaPlayer;
```

这个活动（Activity）的界面包含开始和停止 `Button`、一个用于显示状态的 `TextView`，以及一个用于显示缓冲区已填充百分比的 `TextView`。

```
    Button stopButton, startButton;
    TextView statusTextView, bufferValueTextView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
```

在 `onCreate` 方法中，我们将 `stopButton` 和 `startButton` 设置为禁用状态。它们将在应用程序运行过程中根据情况被启用或禁用。这是为了说明它们触发的方法何时可用、何时不可用。

```
        stopButton = (Button) this.findViewById(R.id.EndButton);
        startButton = (Button) this.findViewById(R.id.StartButton);
        stopButton.setOnClickListener(this);
        startButton.setOnClickListener(this);
        stopButton.setEnabled(false);
        startButton.setEnabled(false);

        bufferValueTextView = (TextView) this.findViewById(R.id.BufferValueTextView);
        statusTextView = (TextView) this.findViewById(R.id.StatusDisplayTextView);
        statusTextView.setText("onCreate");
```

在实例化 `MediaPlayer` 对象后，我们将该活动注册为 `OnCompletionListener`、`OnErrorListener`、`OnBufferingUpdateListener` 和 `OnPreparedListener`。

```
        mediaPlayer = new MediaPlayer();
        mediaPlayer.setOnCompletionListener(this);
        mediaPlayer.setOnErrorListener(this);
        mediaPlayer.setOnBufferingUpdateListener(this);
        mediaPlayer.setOnPreparedListener(this);
        statusTextView.setText("MediaPlayer created");
```

接下来，我们使用音频文件的 URL 调用 `setDataSource`。

```
        try {
            mediaPlayer.setDataSource(
                "http://www.mobvcasting.com/android/audio/goodmorningandroid.mp3");
            statusTextView.setText("setDataSource done");
            statusTextView.setText("calling prepareAsync");
```

最后，我们将调用 `prepareAsync`，它会在后台开始缓冲音频文件并立即返回。当准备工作完成时，由于我们的活动已注册为 `MediaPlayer` 的 `OnPreparedListener`，活动中的 `onPrepared` 方法将被调用。

```
            mediaPlayer.prepareAsync();
        } catch (IOException e) {
            Log.v("AUDIOHTTPPLAYER", e.getMessage());
        }
    }
```

接下来是这两个 `Button` 的 `onClick` 方法的实现。当按下 `stopButton` 时，会调用 `MediaPlayer` 的 `pause` 方法。当按下 `startButton` 时，会调用 `MediaPlayer` 的 `start` 方法。

```
    public void onClick(View v) {
        if (v == stopButton) {
            mediaPlayer.pause();
            statusTextView.setText("pause called");
            startButton.setEnabled(true);
        } else if (v == startButton) {
            mediaPlayer.start();
            statusTextView.setText("start called");
            startButton.setEnabled(false);
            stopButton.setEnabled(true);
        }
    }
```

如果 `MediaPlayer` 进入错误状态，则会在注册为 `MediaPlayer` 的 `OnErrorListener` 的对象上调用 `onError` 方法。下面的 `onError` 方法展示了 `MediaPlayer` 类中指定的各种常量。

```
    public boolean onError(MediaPlayer mp, int what, int extra) {
        statusTextView.setText("onError called");
        switch (what) {
            case MediaPlayer.MEDIA_ERROR_NOT_VALID_FOR_PROGRESSIVE_PLAYBACK:
                statusTextView.setText(
                    "MEDIA ERROR NOT VALID FOR PROGRESSIVE PLAYBACK " + extra);
```


# 第 6 章：后台与网络音频

```
Log.v("ERROR","MEDIA ERROR NOT VALID FOR PROGRESSIVE PLAYBACK " + extra);
break;
case MediaPlayer.MEDIA_ERROR_SERVER_DIED:
statusTextView.setText("MEDIA ERROR SERVER DIED " + extra);
Log.v("ERROR","MEDIA ERROR SERVER DIED " + extra);
break;
case MediaPlayer.MEDIA_ERROR_UNKNOWN:
statusTextView.setText("MEDIA ERROR UNKNOWN " + extra);
Log.v("ERROR","MEDIA ERROR UNKNOWN " + extra);
break;
}
return false;
```

当`MediaPlayer`完成音频文件播放时，注册为`OnCompletionListener`对象的`onCompletion`方法将被调用。这表示我们可以重新开始播放。

```
public void onCompletion(MediaPlayer mp) {
statusTextView.setText("onCompletion called");
stopButton.setEnabled(false);
startButton.setEnabled(true);
}
```

当`MediaPlayer`正在缓冲时，注册为`MediaPlayer`的`onBufferingUpdateListener`对象的`onBufferingUpdate`方法将被调用。缓冲填充的百分比会作为参数传入。

```
public void onBufferingUpdate(MediaPlayer mp, int percent) {
bufferValueTextView.setText("" + percent + "%");
}
```

当`prepareAsync`方法完成时，注册为`OnPreparedListener`对象的`onPrepared`方法将被调用。这表示音频已准备好播放，因此，在以下方法中，我们将启用`startButton`按钮。

```
public void onPrepared(MediaPlayer mp) {
statusTextView.setText("onPrepared called");
startButton.setEnabled(true);
}
```

以下是上述活动相关的布局 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView android:text="Status" android:id="@+id/TextView01"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></TextView>
    <TextView android:text="Unknown" android:id="@+id/StatusDisplayTextView"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></TextView>
    <TextView android:text="0%" android:id="@+id/BufferValueTextView"
        android:layout_width="wrap_content" android:layout_height="wrap_content"></TextView>
    <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:text="Start" android:id="@+id/StartButton"></Button>
    <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:id="@+id/EndButton" android:text="Stop"></Button>
</LinearLayout>
```

如上所示，`MediaPlayer`拥有一套出色的功能，用于处理通过 HTTP 在线获取的音频文件。

### 通过 HTTP 流式传输音频

在线传输实时音频的一种常见方式是通过 HTTP 流式传输。有多种流媒体方法属于 HTTP 流媒体范畴，从服务器推送（历史上曾用于在浏览器中显示持续刷新的网络摄像头图像）到 Apple、Adobe 和 Microsoft 为其各自的媒体播放应用程序提出的一系列新方法。

通过 HTTP 传输实时音频的主要方法是由一家名为 Nullsoft 的公司于 1999 年开发的，该公司随后被 AOL 收购。Nullsoft 是流行 MP3 播放器 WinAMP 的创建者，他们开发了一款使用 HTTP 的实时音频流服务器，名为 SHOUTcast。SHOUTcast 使用 ICY 协议，该协议扩展了 HTTP。目前，大量服务器和播放软件产品支持该协议，事实上数量如此之多，以至于它可能被视为网络电台的事实标准。

幸运的是，Android 上的 `MediaPlayer` 类能够播放 ICY 流，而无需我们开发人员费尽周折。不幸的是，网络电台通常不会公布其流的直接 URL。这是有充分理由的；遗憾的是，浏览器不支持直接播放 ICY 流，需要辅助应用程序或插件才能播放该流。为了知道要打开一个辅助应用程序，网络电台会提供一个具有特定 MIME 类型的中间文件，其中包含指向实际直播流的指针。对于 ICY 流，这通常是 PLS 文件或 M3U 文件。

PLS 文件是一种多媒体播放列表文件，其 MIME 类型为 `audio/x-scpls`。M3U 文件也是存储多媒体播放列表的文件，但采用更基本的格式。其 MIME 类型为 `audio/x-mpegurl`。以下显示了一个指向虚假直播流的 M3U 文件的内容：

```
#EXTM3U
#EXTINF:0,Live Stream Name
http://www.nostreamhere.org:8000/
```

第一行 `#EXTM3U` 是必需的，用于指定后续内容为扩展 M3U 文件，可包含额外信息。播放列表条目的额外信息在该条目上方一行指定，以 `#EXTINF:` 开头，后跟持续时间（秒）、逗号，然后是媒体名称。


# 背景与网络音频

M3U 文件也可以包含多个条目，依次指定一个文件或流。

```
#EXTM3U
#EXTINF:0,直播流名称
http://www.nostreamhere.org:8000/
#EXTINF:0,其他直播流名称
http://www.nostreamthere.org/
```

遗憾的是，Android 上的 `MediaPlayer` 并不会帮我们解析 M3U 文件。因此，要在 Android 上创建 HTTP 流式音频播放器，我们必须自行处理解析工作，并使用 `MediaPlayer` 进行实际的媒体播放。

以下是一个示例 Activity，用于解析并播放来自网络电台或 URL 字段中输入的任何 M3U 文件。

```java
package com.apress.proandroidmedia.ch06.httpaudioplaylistplayer;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.Vector;

import org.apache.http.HttpResponse;
import org.apache.http.HttpStatus;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;

import android.app.Activity;
import android.media.MediaPlayer;
import android.media.MediaPlayer.OnCompletionListener;
import android.media.MediaPlayer.OnPreparedListener;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
```

与之前的示例一样，这个 Activity 会实现 `OnCompletionListener` 和 `OnPreparedListener` 来跟踪 `MediaPlayer` 的状态。

```java
public class HTTPAudioPlaylistPlayer extends Activity
        implements OnClickListener, OnCompletionListener, OnPreparedListener {

    Vector playlistItems;
```

我们将使用一个 `Vector` 来保存播放列表中的项目列表。每个项目都是一个 `PlaylistFile` 对象，该类定义在本类末尾的一个内部类中。

界面上会有几个 `Button` 以及一个 `EditText` 对象，用于包含 M3U 文件的 URL。

```java
    Button parseButton;
    Button playButton;
    Button stopButton;
    EditText editTextUrl;
    String baseURL = "";
    MediaPlayer mediaPlayer;
```

下面的整型变量用于跟踪我们当前正在处理 `playlistItems` 这个 `Vector` 中的哪一项。

```java
    int currentPlaylistItemNumber = 0;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        parseButton = (Button) this.findViewById(R.id.ButtonParse);
        playButton = (Button) this.findViewById(R.id.PlayButton);
        stopButton = (Button) this.findViewById(R.id.StopButton);
```

我们将 `editTextUrl` 对象的文本设置为某个网络电台的 M3U 文件 URL。第一个被注释掉的是俄勒冈州波特兰市社区电台 KBOO（[www.kboo.fm](http://www.kboo.fm)）的 URL。第二个未被注释掉的是德克萨斯州奥斯汀市古典音乐电台 KMFA（[www.kmfa.org](http://www.kmfa.org)）的 URL。用户可以编辑此字段，将其设置为互联网上可用的任何 M3U 文件的 URL。

```java
        editTextUrl = (EditText) this.findViewById(R.id.EditTextURL);
        //editTextUrl.setText("http://live.kboo.fm:8000/high.m3u");
        editTextUrl.setText("http://pubint.ic.llnwd.net/stream/pubint_kmfa.m3u");

        parseButton.setOnClickListener(this);
        playButton.setOnClickListener(this);
        stopButton.setOnClickListener(this);
```

初始状态下，`playButton` 和 `stopButton` 将不可用，用户无法点击它们。而 `parseButton` 则是可用的。在获取并解析 M3U 文件后，`playButton` 将变为可用；音频开始播放后，`stopButton` 将变为可用。我们通过这种方式引导用户遵循应用的流程。

```java
        playButton.setEnabled(false);
        stopButton.setEnabled(false);

        mediaPlayer = new MediaPlayer();
        mediaPlayer.setOnCompletionListener(this);
        mediaPlayer.setOnPreparedListener(this);
    }
```

每个 `Button` 的 `OnClickListener` 都设置为这个 Activity。因此，当其中任何一个按钮被点击时，都会调用下面的 `onClick` 方法。这驱动了应用的大部分流程。当 `parseButton` 被按下时，会调用 `parsePlaylistFile` 方法。当 `playButton` 被按下时，会调用 `playPlaylistItems` 方法。


```markdown
