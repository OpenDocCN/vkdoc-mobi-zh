# 排版后的内容

使用 DOM 会在内存中以对象的形式创建 XML 的表示形式，如果 XML 很大，这可能会花费很长时间并占用大量内存。

### SAX 解析

要在 Android 上使用内置的 SAX 解析器，我们首先需要创建一个继承 `DefaultHandler` 的类。这个类包含的方法会在 XML 元素开始、结束以及读取内容时得到通知。以下是一个仅记录输出的极简版本：

```java
private class XMLHandler extends DefaultHandler {

    @Override
    public void startDocument() throws SAXException {
        Log.v("SimpleXMLParser","startDocument");
    }

    @Override
    public void endDocument() throws SAXException {
        Log.v("SimpleXMLParser","endDocument");
    }

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        Log.v("SimpleXMLParser","startElement " + localName);
    }

    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        Log.v("SimpleXMLParser","endElement " + localName);
    }

    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        String stringChars = new String(ch, start, length);
        Log.v("SimpleXMLParser",stringChars);
    }
}
```

有了这个类之后，我们可以创建一个 `SAXParserFactory` 实例，然后再创建一个 `SAXParser` 实例：

```java
SAXParserFactory aSAXParserFactory = SAXParserFactory.newInstance();
SAXParser aSAXParser = aSAXParserFactory.newSAXParser();
```

通过 `SAXParser` 对象，我们可以获取一个 `XMLReader`，用它来决定解析过程中发生什么，并执行实际的解析：

```java
XMLReader anXMLReader = aSAXParser.getXMLReader();
```

然后实例化我们的 `XMLHandler`，并将其传递给 `XMLReader` 的 `setContentHandler` 方法：

```java
XMLHandler anXMLHandler = new XMLHandler();
anXMLReader.setContentHandler(anXMLHandler);
```

最后，在 `XMLReader` 上调用 `parse` 方法。这里我们假设有一个名为 `xmlInputStream` 的 `InputStream`，它包含我们要解析的 XML：

```java
anXMLReader.parse(new InputSource(xmlInputStream));
```

让我们看一个完整的示例，演示如何解析前面的“用户”XML：

```java
package com.apress.proandroidmedia.ch12.simplexmlparser;

import java.io.ByteArrayInputStream;
import java.io.IOException;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;

import org.xml.sax.Attributes;
import org.xml.sax.InputSource;
import org.xml.sax.SAXException;
import org.xml.sax.XMLReader;
import org.xml.sax.helpers.DefaultHandler;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;

public class SimpleXMLParser extends Activity {
```

我们将把 XML 转换为一个名为 `XMLUser` 的类的实例，该类位于代码末尾。这将是我们用 Java 表示的 XML 中定义的数据：

```java
    XMLUser aUser;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
```

在这个示例中，我们将要解析的 XML 作为一个名为 `xml` 的 `String` 包含进来：

```java
        String xml = "<?xml version=\"1.0\"?>\n"
            + "<user>\n"
            + "<user-id>15</user-id>\n"
            + "<username>vanevery</username>\n"
            + "<firstname>Shawn</firstname>\n"
            + "<lastname>Van Every</lastname>\n"
            + "</user>\n";
```

在这里，我们按照前面描述的步骤创建一个 `SAXParserFactory`、一个 `SAXParser` 和一个 `XMLReader`：

```java
        SAXParserFactory aSAXParserFactory = SAXParserFactory.newInstance();
        try {
            SAXParser aSAXParser = aSAXParserFactory.newSAXParser();
            XMLReader anXMLReader = aSAXParser.getXMLReader();
```

我们将使用此处定义的 `UserXMLHandler` 实例作为我们的处理器，它决定了解析过程中发生的事情：

```java
            UserXMLHandler aUserXMLHandler = new UserXMLHandler();
            anXMLReader.setContentHandler(aUserXMLHandler);
```



最后，我们来执行实际的解析。需要额外做一些工作，将位于`xml` `String`中的 XML 转换为`InputStream`和`InputSource`，以便`XMLReader`使用。

```
anXMLReader.parse(
    new InputSource(new ByteArrayInputStream(xml.getBytes())));
} catch (ParserConfigurationException e) {
    e.printStackTrace();
} catch (SAXException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}
```

示例的核心是`UserXMLHandler`。它扩展了`DefaultHandler`，并将在解析 XML 时接收其中的数据。

```
private class UserXMLHandler extends DefaultHandler {
```

我们将使用以下常量与`state`变量配合，来跟踪已读取的元素。

```
static final int NONE = 0;
static final int ID = 1;
static final int FIRSTNAME = 2;
static final int LASTNAME = 3;
int state = NONE;
```

我们将使用以下常量来匹配 XML 中可能出现的元素名称。

```
static final String ID_ELEMENT = "user-id";
static final String FIRSTNAME_ELEMENT = "firstname";
static final String LASTNAME_ELEMENT = "lsatname";
```

当解析器识别到 XML 文档开始时，会调用`startDocument`方法。在此方法中，我们将实例化`XMLUser`对象，用于保存 XML 中表示的数据。

```
@Override
public void startDocument() throws SAXException {
    Log.v("SimpleXMLParser","startDocument");
    aUser = new XMLUser();
}
```

当解析器识别到 XML 文档结束时，会调用`endDocument`方法。我们只需打印出`XMLUser`对象的内容。

```
@Override
public void endDocument() throws SAXException {
    Log.v("SimpleXMLParser","endDocument");
    Log.v("SimpleXMLParser","User Info: " + aUser.user_id + " " +
          aUser.firstname + " " + aUser.lastname);
}
```

当识别到新元素开始时，会调用`startElement`方法。换句话说，也就是找到了 XML 中的开始标签。元素名称通过`localName`变量传入。在此方法中，我们只需将该名称与之前定义的常量进行比较，并据此更改`state`变量。

```
@Override
public void startElement(String uri, String localName, String qName,
                         Attributes attributes) throws SAXException {
    Log.v("SimpleXMLParser","startElement");

    if (localName.equalsIgnoreCase(ID_ELEMENT)) {
        state = ID;
    } else if (localName.equalsIgnoreCase(FIRSTNAME_ELEMENT)) {
        state = FIRSTNAME;
    } else if (localName.equalsIgnoreCase(LASTNAME_ELEMENT)) {
        state = LASTNAME;
    } else {
        state = NONE;
    }
}
```

当找到任何 XML 结束标签时，会调用`endElement`方法。

```
@Override
public void endElement(String uri, String localName, String qName)
       throws SAXException {
    Log.v("SimpleXMLParser","endElement");
}
```

每当在开始和结束标签之间找到文本时，就会调用`characters`方法。在我们的实现中，会根据`state`变量所表示的当前在文档中的位置，获取数据并将其放入`XMLUser`对象。

```
@Override
public void characters(char[] ch, int start, int length) throws SAXException {
    String stringChars = new String(ch, start, length);
    Log.v("SimpleXMLParser",stringChars);

    if (state == ID) {
        aUser.user_id += stringChars.trim();
        Log.v("SimpleXMLParser","user_id:"+aUser.user_id);
    } else if (state == FIRSTNAME) {
        aUser.firstname += stringChars.trim();
        Log.v("SimpleXMLParser","firstname:"+aUser.firstname);
    } else if (state == LASTNAME) {
        aUser.lastname += stringChars.trim();
        Log.v("SimpleXMLParser","lastname:"+aUser.lastname);
    }
}
```

以下是`XMLUser`类，我们用它来保存 XML 提供给我们的数据。

```
class XMLUser {
    String user_id;
    String firstname;
    String lastname;

    public XMLUser() {
        user_id = "";
        firstname = "";
        lastname = "";
    }
}
```

通过这个快速示例，我们可以将其作为任何 XML 的模板。



### Android 的解析需求

在 Android 上，我们可能需要执行一些解析操作，包括处理从 Web 服务请求中收到的数据。

### HTTP 文件上传

我们可能希望允许用户分发由我们开发的应用程序所创建的媒体内容，一种方式是将它们发布到在线视频分享网站，如 YouTube、Vimeo 或 Blip.TV。

为了将文件发布到这类服务，我们需要执行 HTTP 文件上传。在 Android 上完成 HTTP 文件上传有几种方法。能给我们最大灵活性的方法是引入并使用 Apache HTTP Components（[`hc.apache.org/`](http://hc.apache.org)）中的库，这些库并未随 Android 完全打包。

我们需要 `httpmime-4.0.x.jar`，它包含在可从 [`hc.apache.org/downloads.cgi`](http://hc.apache.org/downloads.cgi) 下载的 `HttpClient 4.0.x (GA)` 包中。（版本号中的“x”当前为 3；当你下载时可能更高。）我们还需要 Apache Mime4J 0.6 版本（`apache-mime4j-0.6.jar`）或更高版本，可从 [`james.apache.org/download.cgi`](http://james.apache.org/download.cgi) 下载。

在构建应用时，只需将这些文件拖入 Eclipse 项目中的项目文件夹，即可将它们引入你的 Eclipse 项目。然后我们需要在项目属性中编辑 Java 构建路径。要将它们包含在构建路径中，请前往 Java 构建路径对话框中的**库**选项卡，选择“添加 JARs”，最后选中它们。

通过导入上述库，我们获得了一个 `MultipartEntity`，它可以用于 `HttpClient` 使用的 `HttpPost` 请求中。`MultipartEntity` 允许向服务器发送 multipart/form-data 类型的 POST 请求。这与浏览器从允许用户选择文件的表单进行上传时使用的机制相同。

### 发起 HTTP 请求

下面是一个如何使用它的简要说明。

首先，我们通过实例化 `DefaultHttpClient` 来创建一个 `HttpClient` 对象。

```
HttpClient httpclient = new DefaultHttpClient();
```

接着，我们创建一个 `HttpPost` 对象，它代表一个指向特定 URL 的 POST 请求，我们将该 URL 传入。

```
HttpPost httppost = new HttpPost("http://webserver/file-upload-app");
```

然后，我们可以实例化 `MultipartEntity`。如前述，这种类型的实体可以包含多个部分。

```
MultipartEntity multipartentity = new MultipartEntity();
```

我们需要的主要部分是要上传的实际文件。为此，我们使用 `addPart` 方法，传入一个字符串作为名称，以及一个 `FileBody` 对象作为值。这个 `FileBody` 对象接收一个 `File` 对象，该对象代表我们想要上传的实际文件——在本例中，是 SD 卡根目录下的一个视频文件。

```
multipartentity.addPart("file", new FileBody(new File("/sdcard/video_h264_640x480.mp4")));
```

如果需要添加其他元素，如用户名、密码等，我们使用相同的 `addPart` 方法，传入名称和值。在这种情况下，值应该是一个包含实际字符串值的 `StringBody` 对象。

```
multipartentity.addPart("username", new StringBody("myusername"));
multipartentity.addPart("password", new StringBody("mypassword"));
multipartentity.addPart("title", new StringBody("A Title"));
```

一旦我们的 `MultipartEntity` 设置完毕，我们通过调用 `setEntity` 方法将它传递给 `HttpPost` 对象。

```
httppost.setEntity(multipartentity);
```

现在我们可以执行请求并获取响应。

```
HttpResponse httpresponse = httpclient.execute(httppost);
HttpEntity responseentity = httpresponse.getEntity();
```

我们可以通过调用所得到的 `HttpEntity` 上的 `getContent` 方法获取一个 `InputStream`，从而读取响应。

```
InputStream inputstream = responseentity.getContent();
```



为了从`InputStream`读取数据，我们会将其包装在`InputStreamReader`和`BufferedReader`中，然后执行常规的读取过程。

```java
BufferedReader bufferedreader = new BufferedReader(new InputStreamReader(inputstream));
```

我们将使用`StringBuilder`来保存所有读取到的数据。

```java
StringBuilder stringbuilder = new StringBuilder();
```

然后我们会逐行从`BufferedReader`读取，直到其返回`null`。

```java
String currentline = null;
while ((currentline = bufferedreader.readLine()) != null) {
    stringbuilder.append(currentline + "\n");
}
```

读取完成后，我们会将`StringBuilder`对象转换为普通字符串并输出到日志中。

```java
String result = stringbuilder.toString();
Log.v("HTTP UPLOAD REQUEST", result);
```

最后，我们会关闭`InputStream`。

```java
inputstream.close();
```

当然，我们的应用需要拥有访问互联网的权限。因此，我们需要在`AndroidManifest.xml`文件中添加以下`uses-permission`行。

```xml
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
```

一旦我们下载并导入了所需的库，使用`HttpClient`类进行文件上传并不比执行普通的 HTTP 请求困难多少。

## 向 Blip.TV 上传视频

Blip.TV 是一个流行的视频分享网站，它提供了一个基于 REST 的文件上传 API，我们可以利用它来为采集应用甚至独立应用构建视频分享机制。

Blip.TV 的上传 API 文档可以在线查阅，地址为`http://wiki.blip.tv/index.php/REST_Upload_API`。该文档详细说明了请求中可以包含的各种元素。特别地，我们需要将上传的文件元素命名为`"file"`。要运行示例代码，你需要一个常规的 Blip.TV 用户登录名（用户名）和密码（在真实应用中，这应由用户提供）。我们还需要一个标题，并且需要包含值为`"1"`的`post`参数和值为`api`的`skin`参数，以便获得 XML 格式的响应。

一旦通过 API 将视频上传到 Blip.TV，他们会返回类似如下的 XML 响应：

```xml
<response>
    <current_time>2010-10-30T00:13:00Z</current_time>
    <timestamp>1288397581081</timestamp>
    <status>OK</status>
    <payload>
        <asset>
            <timestamp>1288397580</timestamp>
            <id>4332695</id>
            <item_type>file</item_type>
            <item_id>4314031</item_id>
            <links>
                <link rel="alternate" type="text/html" href="http://blip.tv/file/4314031/" />
                <link rel="alternate" type="application/rss+xml" href="http://blip.tv/rss/4332695" />
                <link rel="alternate" type="application/atom+xml" href="http://blip.tv/file/4314031/?skin=atom" />
                <link rel="service.edit" type="text/html" href="http://blip.tv/file/post/4314031/" />
                <link rel="service.edit" type="text/xml" href="http://blip.tv/file/post/4314031/?skin=api" />
            </links>
            <files>
                <file src="Username-AVideo562.3gp" submitted_as="VID_20101029_200900.3gp" role='Source' />
            </files>
        </asset>
    </payload>
</response>
```

值得注意的是，如果上传成功，XML 会给出状态`OK`，并在`file`元素中提供原始文件的链接。我们可以使用 SAX 解析器解析这个 XML，查找这些项目，并将视频信息呈现给用户以验证上传是否成功。

如果上传失败，XML 会给出状态`ERROR`，并包含一个包含代码和消息的错误标签。以下是用户名/密码组合输入错误的示例：

```xml
<response>
    <current_time>2010-10-30T00:38:32Z</current_time>
    <timestamp>1288399112662</timestamp>
    <status>ERROR</status>
    <error>
        <code>AUTHENTICATION_REQUIRED</code>
        <message>The operation you attempted to perform require authentication,</message>
    </error>
</response>
```



### Blip.TV 上传代码解析

但您的身份验证信息无效、缺失或不足以执行您尝试的操作。

让我们完整地看一下用于捕获视频并上传到`Blip.TV`的完整代码：

```java
package com.apress.proandroidmedia.ch12.blipuploader;

import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.FilterOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.MultipartEntity;
import org.apache.http.entity.mime.content.FileBody;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;

import org.xml.sax.Attributes;
import org.xml.sax.InputSource;
import org.xml.sax.SAXException;
import org.xml.sax.XMLReader;
import org.xml.sax.helpers.DefaultHandler;

import android.app.Activity;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;

public class BlipTVUploader extends Activity {
```

我们的 Activity 将使用`Intent`来触发用于视频录制的`Camera Activity`和用于视频播放的默认 Activity，因此我们需要设置两个常量来识别返回的是哪个 Activity。

```java
    final static int VIDEO_CAPTURED = 0;
    final static int VIDEO_PLAYED = 1;
```

我们有几个变量：一个`File`对象用于表示 SD 卡上捕获的视频，一个`String title`将作为上传到`Blip.TV`时的标题，以及`Blip.TV`的用户名和密码。在实际应用程序中，标题、用户名和密码应从用户处获取。

```java
    File videoFile;
    String title = "A Video";
    String username = "BLIPTV_USERNAME";
    String password = "BLIPTV_PASSWORD";
```

`postingResult`变量将包含上传后从`Blip.TV`返回的 XML 响应结果。

```java
    String postingResult = "";
```

`fileLength`变量将在视频录制完成后设置，以便我们跟踪上传进度。

```java
    long fileLength = 0;
```

我们将使用`TextView`向用户显示上传进度和其他信息。

```java
    TextView textview;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        textview = (TextView) findViewById(R.id.textview);
```

在首次启动时，我们将使用`Intent`触发默认的视频捕获应用程序，通常是内置的`Camera Activity`，以启动并允许用户捕获视频。

我们将`VIDEO_CAPTURED`常量连同`Intent`一起传递给`startActivityForResult`，以便在`onActivityResult`中知道返回给我们的是什么内容。

```java
        Intent captureVideoIntent =
            new Intent(android.provider.MediaStore.ACTION_VIDEO_CAPTURE);
        startActivityForResult(captureVideoIntent, VIDEO_CAPTURED);
    }

    protected void onActivityResult (int requestCode, int resultCode, Intent data) {
```

当`Camera Activity`返回时，我们可以按照第 11 章中描述的方式获取视频文件的`Uri`。

```java
        if (resultCode == RESULT_OK && requestCode == VIDEO_CAPTURED) {
            Uri videoFileUri = data.getData();
```

为了获取 SD 卡上实际的视频文件，我们需要查询`MediaStore`，请求代表文件的`DATA`列。

```java
            String[] columns = { android.provider.MediaStore.Video.Media.DATA };
            Cursor cursor = managedQuery(videoFileUri, columns, null, null, null);
            int fileColumn =
```


```java
cursor.getColumnIndexOrThrow(android.provider.MediaStore.Video.Media.DATA);

if (cursor.moveToFirst()) {

String videoFilePath = cursor.getString(fileColumn);

Log.v("VIDEO FILE PATH",videoFilePath);
```

获取文件路径后，我们将构造一个`File`对象，获取其长度，并实例化一个`BlipTVFilePoster`对象。`BlipTVFilePoster`继承自`AsyncTask`，因此要启动其工作，只需调用它的`execute`方法。

```java
videoFile = new File(videoFilePath);

fileLength = videoFile.length();

BlipTVFilePoster btvfp = new BlipTVFilePoster();

btvfp.execute();

}
```

如果是视频播放器活动返回给我们，我们将直接结束。至此，我们的任务已完成。

```java
} else if (requestCode == VIDEO_PLAYED) {

finish();

}

}
```

如前所述，`BlipTVFilePoster`类继承自`AsyncTask`。这样它就能在后台线程中执行任务，而不会阻塞用户界面。同时，该类还将实现`ProgressListener`（我们为此设计的接口，用于处理上传类的进度回调）和`BlipXMLParserListener`（用于处理 XML 解析类的回调）。

```java
class BlipTVFilePoster extends AsyncTask<Void, String, Void> implements

ProgressListener, BlipXMLParserListener {
```

`videoUrl`变量将包含视频文件上传至 Blip.TV 后的网址。

```java
String videoUrl;

@Override

protected Void doInBackground(Void... params) {
```

如前所述，我们可以使用带`MultipartEntity`的`HttpClient`来执行 HTTP 文件上传。

```java
HttpClient httpclient = new DefaultHttpClient();

HttpPost httppost = new HttpPost("http://blip.tv/file/post");
```

在本例中，我们使用了一个名为`ProgressMultipartEntity`的类，它继承自`MultipartEntity`，但允许我们跟踪上传进度。我们将自身作为监听器传入，这之所以可行，是因为我们实现了`ProgressListener`接口。

```java
ProgressMultipartEntity multipartentity = new ProgressMultipartEntity(this);
```

根据 Blip.TV 的要求，我们需要在 POST 请求中添加多个部分。当然，我们需要包含文件，此外还需提供用户登录名（`username`）、密码、标题，以及值为`"1"`的`post`字段以确保实际发布内容，还有值为`api`的`skin`字段，这样服务器会返回 XML 响应而非普通网页。

```java
try {

multipartentity.addPart("file", new FileBody(videoFile));

multipartentity.addPart("userlogin", new StringBody(username));

multipartentity.addPart("password", new StringBody(password));

multipartentity.addPart("title", new StringBody(title));

multipartentity.addPart("post", new StringBody("1"));

multipartentity.addPart("skin", new StringBody("api"));

httppost.setEntity(multipartentity);

HttpResponse httpresponse = httpclient.execute(httppost);

HttpEntity responseentity = httpresponse.getEntity();

if (responseentity != null) {
```

执行 HTTP 文件上传后，我们可以获取一个`InputStream`来读取服务器的响应。在本例中，我们只需将这个`InputStream`交给 SAX 解析器实现，以便判断上传是否成功。

```java
InputStream inputstream = responseentity.getContent();

SAXParserFactory aSAXParserFactory = SAXParserFactory.newInstance();

try {

SAXParser aSAXParser = aSAXParserFactory.newSAXParser();

XMLReader anXMLReader = aSAXParser.getXMLReader();
```

我们将使用此处定义的`BlipResponseXMLHandler`，专门处理文件上传后 Blip.TV 返回的 XML 响应。

```java
BlipResponseXMLHandler xmlHandler =

new BlipResponseXMLHandler(this);

anXMLReader.setContentHandler(xmlHandler);

anXMLReader.parse(new InputSource(inputstream));

} catch (ParserConfigurationException e) {

e.printStackTrace();

} catch (SAXException e) {

e.printStackTrace();

} catch (IOException e) {

e.printStackTrace();

}

inputstream.close();

}

} catch (ClientProtocolException e) {

e.printStackTrace();

} catch (IOException e) {

e.printStackTrace();

}

return null;

}
```

```java
protected void onProgressUpdate(String... textToDisplay) {
    textview.setText(textToDisplay[0]);
}
```

`onPostExecute()`方法在`doInBackground()`方法完成时触发。此时，如果我们的`videoUrl`变量已被 XML 解析器填充，我们只需创建一个`Intent`，使用默认视频播放器 Activity 来播放已上传的文件。

```java
protected void onPostExecute(Void result) {
    if (videoUrl != null) {
        Intent viewVideoIntent = new Intent(Intent.ACTION_VIEW);
        Uri uri = Uri.parse("http://blip.tv/file/get/" + videoUrl);
        viewVideoIntent.setDataAndType(uri, "video/3gpp");
        startActivityForResult(viewVideoIntent, VIDEO_PLAYED);
    }
}
```

此处定义的`transferred()`方法是`ProgressListener`接口实现的一部分。它将在文件上传过程中由`ProgressMultipartEntity`调用。该方法会调用`publishProgress()`方法，从而触发`onProgressUpdate()`方法来更新 UI。

```java
public void transferred(long num) {
    double percent = (double)num/(double)fileLength;
    int percentInt = (int) (percent * 100);
    publishProgress("" + percentInt + "% Transferred");
}
```

`parseResult()`方法是`BlipXMLParserListener`接口实现的一部分。当解析需要报告给用户的 XML 进度时，将调用此方法。它只是调用`publishProgress()`，从而触发`onProgressUpdate()`将结果显示给用户。

```java
public void parseResult(String result) {
    publishProgress(result);
}
```

`setVideoUrl()`也是`BlipXMLParserListener`接口实现的一部分。它只是用上传后的视频 URL 填充`videoUrl`变量。

```java
public void setVideoUrl(String url) {
    videoUrl = url;
}
```

以下是`ProgressMultipartEntity`，它扩展了`MultipartEntity`。此类托管`ProgressListener`以报告进度，并覆盖`writeTo()`方法，使用能够统计传出字节数的`OutputStream`。

```java
class ProgressMultipartEntity extends MultipartEntity {
    ProgressListener progressListener;

    public ProgressMultipartEntity(ProgressListener pListener) {
        super();
        this.progressListener = pListener;
    }

    @Override
    public void writeTo(OutputStream outstream) throws IOException {
        super.writeTo(new ProgressOutputStream(outstream, this.progressListener));
    }
}
```

`ProgressListener`接口非常简单——它只指定实现类中需要有一个`transferred()`方法。

```java
interface ProgressListener {
    void transferred(long num);
}
```

以下是`ProgressOutputStream`，它重写了`FilterOutputStream`中的`write()`方法，并跟踪已传输的字节数。

```java
static class ProgressOutputStream extends FilterOutputStream {
    ProgressListener listener;
    int transferred;

    public ProgressOutputStream(final OutputStream out, ProgressListener listener) {
        super(out);
        this.listener = listener;
        this.transferred = 0;
    }

    public void write(byte[] b, int off, int len) throws IOException {
        out.write(b, off, len);
        this.transferred += len;
        this.listener.transferred(this.transferred);
    }

    public void write(int b) throws IOException {
        out.write(b);
        this.transferred++;
        this.listener.transferred(this.transferred);
    }
}
```

最后，我们有`BlipXMLParserListener`接口和`BlipResponseXMLHandler`，它们处理 Blip.TV 响应文件上传时返回的 XML 数据。

```java
interface BlipXMLParserListener {
    void parseResult(String result);
    void setVideoUrl(String url);
}

class BlipResponseXMLHandler extends DefaultHandler {
```

以下常量与`state`变量结合使用，以跟踪我们在 XML 中的位置。

```java
    int NONE = 0;
    int ONSTATUS = 1;
    int ONFILE = 2;
}
```

```java
int ONERRORMESSAGE = 3;

int state = NONE;
```

下一组常量定义了 XML 可能返回的状态值。我们将用这些常量来跟踪直接在下方定义的 `status` 整数中的状态值。

```java
int STATUS_UNKNOWN = 0;
int STATUS_OK = 1;
int STATUS_ERROR = 2;
int status = STATUS_UNKNOWN;
```

`message` 变量将用于保存 XML 返回的错误消息，或者在上传成功时保存视频的 URL。

```java
String message = "";
```

当然，我们需要持有通过构造函数传入的 `BlipXMLParserListener` 实例。

```java
BlipXMLParserListener listener;

public BlipResponseXMLHandler(BlipXMLParserListener bxpl) {
    super();
    listener = bxpl;
}

@Override
public void startDocument() throws SAXException {
}

@Override
public void endDocument() throws SAXException {
}
```

大部分工作将在 `startElement` 标签内完成。系统会检查包含 XML 标签名称的 `localName` 变量，看它是否与我们需要关注的任何标签匹配；如果匹配，我们会将 `state` 变量设置为相应的常量。

```java
@Override
public void startElement(String uri, String localName, String qName,
        Attributes attributes) throws SAXException {
    if (localName.equalsIgnoreCase("status")) {
        state = ONSTATUS;
    } else if (localName.equalsIgnoreCase("file")) {
        state = ONFILE;
    }
```

如果是 `file` 元素，我们会通知监听器，并提取 `src` 属性，该属性值等于上传后 Blip.TV 赋予的文件名。

```java
listener.parseResult("onFile");
message = attributes.getValue("src");
listener.parseResult("filemessage:" + message);
```

然后，我们通过 `setVideoUrl` 方法将其传递给 `BlipXMLParserListener`。

```java
listener.setVideoUrl(message);
```

```java
    } else if (localName.equalsIgnoreCase("message")) {
        state = ONERRORMESSAGE;
        listener.parseResult("onErrorMessage");
    }
}

@Override
public void endElement(String uri, String localName, String qName)
        throws SAXException {
    if (localName.equalsIgnoreCase("status")) {
        state = NONE;
    } else if (localName.equalsIgnoreCase("file")) {
        state = NONE;
    } else if (localName.equalsIgnoreCase("message")) {
        state = NONE;
    }
}
```

当在元素内发现任何内容时，会触发 `characters` 方法。如果 `state` 变量指示我们正在读取一个需要关注的元素，我们将采取相应操作。

```java
@Override
public void characters(char[] ch, int start, int length) throws SAXException {
    String stringChars = new String(ch, start, length);
```

如果正在读取 `status` 元素，我们会将 `status` 变量设置为相应的常量。

```java
    if (state == ONSTATUS) {
        if (stringChars.equalsIgnoreCase("OK")) {
            status = STATUS_OK;
        } else if (stringChars.equalsIgnoreCase("ERROR")) {
            status = STATUS_ERROR;
        } else {
            status = STATUS_UNKNOWN;
        }
```

如果正在读取错误消息元素，我们会获取文本内容，将其设置为 `message` 变量，并将其发送给监听器。

```java
    } else if (state == ONERRORMESSAGE) {
        message += stringChars.trim();
        listener.parseResult(message);
    }
}
```

上述示例中用于跟踪上传进度的方法，基于用户 "tuler" 提供的答案，并由用户 ColinD 在 Stack Overflow 上 SoaperGEM 提出的问题中进行了编辑，链接如下：

[`stackoverflow.com/questions/254719/file-upload-with-java-with-progress-bar/470047#470047`](http://stackoverflow.com/questions/254719/file-upload-with-java-with-progress-bar/470047#470047)

以下是上述活动所使用的布局 XML。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <TextView
        android:id="@+id/textview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        />
</LinearLayout>
```


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch12.blipuploader"
    android:versionCode="1"
    android:versionName="1.0">

    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name">
        <activity
            android:name=".BlipTVUploader"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

    <uses-sdk android:minSdkVersion="4" />
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

最后，这是`AndroidManifest.xml`，其中包含`uses-permission`标签，指定了我们需要能够访问互联网。

这个示例展示了允许用户将其创作直接发布到在线视频分享平台的方法。类似的代码也可用于发布到其他分享平台，当然，并不仅限于视频。我们可以将图片上传到 Flickr 或 Picasa，也可以将音频文件上传到音频分享网站。

## 总结

正如我们在本章中所见，利用在线服务来获取媒体以及允许用户发布媒体，为我们开启了广泛的可能性。我们发现，在 Android 中使用 HTTP、REST、JSON 和 XML 并不困难，并且能够让我们访问几乎任何网络服务。此外，将位置信息融入其中，又为我们的应用程序增添了新的动态维度。



`android.content.Intent.ACTION_VIEW`

`android.provider.MediaStore.Audio.Media`

`android.graphics.PorterDuff.Mode.DARKEN`

`android.provider.MediaStore.Audio.Media.D`

`android.graphics.PorterDuff.Mode.DST` 规则

`android.provider.MediaStore.Images.Media`

`android.graphics.PorterDuff.Mode.DST_ATO`

`android.provider.MediaStore.MediaColumns`

`android.graphics.PorterDuff.Mode.DST_IN`

`android.graphics.PorterDuff.Mode.DST_OU`

`android.R.layout.simple_list_item_1` 布局

`android.graphics.PorterDuff.Mode.DST_OVE`

`apache-mime[4j-0.6.jar` 文件

`android.graphics.PorterDuff.Mode.LIGHTEN`

`ARGB_4444` 常量
`ARGB_8888` 常量

`AsyncTask` 类

`AudioTrack` 类

音频
分析
捕捉声音
可视化频率

背景播放
本地服务示例
本地服务与远程服务

`BaseAdapter` 类
`bestHeight` 变量
`bestWidth` 变量
`bindService` 方法

`Bitmap`
创建时应用 `Matrix` 类
配置
将 `Bitmap` 绘制到
`Bitmap.Config` 类
`Bitmap.Config.ARGB_8888` 常量
`BitmapFactory` 类

`AVAILABLE` 常量
AVC（高级视频编码）

本地服务示例
`MediaPlayer` 类
`MediaPlayer` 类的绑定
`MediaStore` 用于
实现 `MediaPlayer` 类
本地服务与远程服务

网络化
HTTP 播放
RTSP 流媒体
通过 HTTP 流式传输音频
支持的格式
合成
生成样本
播放合成声音
通过 `Intent` 使用音乐应用

音频和视频比特率
音频和视频编码器



