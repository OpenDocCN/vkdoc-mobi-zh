# 第 11 章：视频采集

`recorder.prepare();`

} catch (`IllegalStateException` e) {

`e.printStackTrace();`

`finish();`

} catch (`IOException` e) {

`e.printStackTrace();`

`finish();`

}

当`SurfaceView`被点击时，我们 Activity 的 `onClick` 方法将被调用。

```
public void onClick(View v) {
```

如果 `recording` 布尔值为 true，我们将对 `recorder` 调用 `stop` 方法，并将 `recording` 布尔值设为 false。此外，如果我们不再需要使用 `MediaRecorder`，应调用 `release` 方法来释放其资源，因为同一时间只能有一个应用使用它。

```
if (recording) {
    recorder.stop();
    //recorder.release();
    recording = false;
    Log.v(TAG,"Recording Stopped");
```

在本例中，我们将允许用户再次录制，因此会调用 `initRecorder` 和 `prepareRecorder` 来重新设置一切。我们需要这样做，因为使用 `stop` 方法停止录制后，其状态就像刚刚初始化一样，因此无法立即再次录制。

```
    // 让我们重新 initRecorder，以便可以再次录制
    initRecorder();
    prepareRecorder();
} else {
```

如果 `recording` 布尔值为 false，我们将对 `MediaRecorder` 调用 `start` 方法，并更新该布尔值。

```
    recording = true;
    recorder.start();
    Log.v(TAG,"Recording Started");
}
```

正如之前所解释的，一旦 `Surface` 被创建，我们将调用 `prepareRecorder`。

```
public void surfaceCreated(SurfaceHolder holder) {
    Log.v(TAG,"surfaceCreated");
    prepareRecorder();
}

public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
}
```

如果 `Surface` 被销毁时我们正在录制，将停止录制。这通常发生在 Activity 不再可见时。由于 `MediaRecorder` 使用共享资源（例如摄像头和麦克风），我们将调用 `release` 方法，以便其他应用可以使用它们。此外，没有理由让此应用继续运行；我们将调用 `finish`，这样下次启动时，一切都会重新初始化。

```
public void surfaceDestroyed(SurfaceHolder holder) {
    Log.v(TAG,"surfaceDestroyed");
    if (recording) {
        recorder.stop();
        recording = false;
    }
    recorder.release();
    finish();
}
```

以下是我们使用的布局 XML。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <SurfaceView android:id="@+id/CameraView" 
        android:layout_width="640px"
        android:layout_height="480px" />

</LinearLayout>
```

以下是本例中 `AndroidManifest.xml` 的内容。值得注意的是三个必需的 `uses-permission` 标签。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch11.videocapture"
    android:versionCode="1"
    android:versionName="1.0">

    <application android:icon="@drawable/icon" 
        android:label="@string/app_name">
        <activity android:name=".VideoCapture"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

    <uses-sdk android:minSdkVersion="8" />
    
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

</manifest>
```

正如我们所看到的，编写一个执行自定义视频采集的应用并非特别困难，但如果我们不注意 `Surface` 的创建和步骤顺序，可能会有些棘手。创建我们自己的应用，在更改设置（如用于录制的编解码器、比特率和格式）方面提供了极大的灵活性。图 11-2 显示了上述示例在设备上的外观。

![自定义视频采集 Activity](img/index-264_1.jpg)

**图 11-2.** *自定义视频采集 Activity*

## 本章小结

至此，我们对 Android 视频功能进行了探索。在前面的章节中，我们研究了播放、存储和网络视频。在本章中，我们将视频采集纳入其中，并看到与 Android 上存在的其他媒体采集功能一样，我们可以通过 Intent 使用内置功能，也可以创建自己的采集应用。这两种方式都有效；前者提供了丰富的功能集，但控制较少，而后者则提供了更可控的体验。



### 使用网络服务消费与发布媒体

若本未涉及从诸如`Flickr`（提供照片与视频）和以丰富用户生成视频闻名的`YouTube`等网站在线消费媒体的多种可能性，那便是一种疏忽。

同样合理的是，既然本书大量篇幅都在探讨构建允许用户创作或生成媒体的应用程序的方法与途径，我们也应涵盖将这些媒体发布到相同或类似平台（例如用于视频的`Blip.TV`和用于图片的`Flickr`）的方法。

此外，在媒体消费与发布的过程中，我们将完成两件事：学习并使用网络服务及网络服务协议（如`REST`和`JSON`），同时探讨如何利用位置信息。我们将学习如何向`Flickr`等平台查询用户周边被创建的内容。

## 网络服务

我们都熟悉网页及构成网站的站点，例如`Yahoo`、`Google`、`Hulu`和`Apress.com`。但你可能不太熟悉“网络服务”的概念。简而言之，网络服务是一种以编程方式访问网站所提供内容和服务的途径。

网站允许内容以这种方式被访问，是为了让像我们这样的第三方开发者能够将其内容和功能嵌入到应用程序中。例如，Android 手机通常预装了一个`YouTube`应用程序。该应用程序通过网络服务协议从`YouTube`网站获取数据，并在应用内显示。这与在浏览器中访问`YouTube`移动网站不同——在这种场景下，我们获取的不是`YouTube`网站的布局和格式数据，而仅仅是数据本身，例如最受欢迎和评分最高的视频列表，这些数据随后会在应用程序的布局中显示。

有几种网络服务技术用于实现这种幕后数据传输。我们将介绍其中两种：`JSON`和`REST`。不过，首先我们需要了解使用网络服务的基础之一——即发起网络或 HTTP 请求。

### HTTP 请求

如果我们不使用 HTTP 来访问网络服务，它也就不能被称为“网络”服务。在 Android 上发起 HTTP 请求非常简单，只需使用 Android 中`org.apache.http`包内由 Apache 提供的`HttpClient`类即可。

首先，我们需要创建一个`HttpClient`对象，实际上它将是一个`DefaultHttpClient`对象。

```
HttpClient httpclient = new DefaultHttpClient();
```

接下来，我们可以构造请求。在本例中，我们将发起一个 HTTP GET 请求，该请求会将所有参数以查询字符串的形式附加在 URL 上。这与 HTTP POST 请求不同，后者将附加数据作为请求消息的主体（而非 URL 的一部分）发送。

要创建 HTTP GET 请求，我们将实例化一个`HttpGet`对象，并传入想要获取的页面的 URL。此处，我们传入的是 Apress 网站上关于本书的页面 URL。

```
HttpGet httpget = new HttpGet("http://www.apress.com/book/view/9781430232674");
```

然后，我们通过`execute`方法将`HttpGet`对象传递给`HttpClient`对象来执行请求。这将返回一个`HttpResponse`对象。

```
HttpResponse response = httpclient.execute(httpget);
```

`HttpResponse`将包含一个`HttpEntity`，它本质上就是一条 HTTP 消息。请求和响应都包含实体。

```
HttpEntity entity = response.getEntity();
```

`HttpEntity`的`getContent`方法会返回一个`InputStream`，我们可以用它来读取响应中发送的实际内容。

```
InputStream inputstream = entity.getContent();
```

让我们通过一个简短示例来说明。



```java
package com.apress.proandroidmedia.ch12.simplehttprequest;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
```

