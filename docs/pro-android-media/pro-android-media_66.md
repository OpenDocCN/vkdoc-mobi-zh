# 第 12 章：使用 Web 服务消费与发布媒体 **253**

```java
public class SimpleHTTPRequest extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
```

首先，我们将实例化 `HttpClient` 和 `HttpGet` 对象。

```java
        HttpClient httpclient = new DefaultHttpClient();
        HttpGet httpget = new HttpGet("http://www.apress.com/book/view/9781430232674");
```

`HttpClient` 的 `execute` 方法可能会抛出异常，因此我们需要将其包裹在 try catch 块中。

```java
        try {
            HttpResponse response = httpclient.execute(httpget);
            HttpEntity entity = response.getEntity();
            if (entity != null) {
```

如果存在 `HttpEntity`，我们就可以获取到 `InputStream`，用于读取响应内容。

```java
                InputStream inputstream = entity.getContent();
```

我们将 `InputStream` 包裹在 `BufferedReader` 中，并利用 `StringBuilder` 对象将其转换为最终可操作的普通 `String`。

```java
                BufferedReader bufferedreader =
                    new BufferedReader(new InputStreamReader(inputstream));
                StringBuilder stringbuilder = new StringBuilder();
                String currentline = null;
                try {
                    while ((currentline = bufferedreader.readLine()) != null) {
                        stringbuilder.append(currentline + "\n");
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
```

读取完所有内容后，我们对 `StringBuilder` 对象调用 `toString` 方法以获取最终的 `String`，并通过 `Log` 输出。

```java
                String result = stringbuilder.toString();
                Log.v("HTTP REQUEST", result);
                inputstream.close();
            }
```

**254**

**第 12 章：使用 Web 服务消费与发布媒体**

```java
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

当然，我们需要拥有访问互联网的权限，因此必须在 `AndroidManifest.xml` 中指定该权限。

```xml
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
```

现在我们已经了解了如何对通用网络资源发起 HTTP 请求，接下来让我们看看如何处理向 Web 服务发起请求后可能返回的数据类型。我们将从 JSON 格式的数据开始。

## JSON

JSON 代表 JavaScript 对象表示法。虽然最初是为 JavaScript 设计的，但作为一种数据交换格式，它是与语言无关的。此外，由于多种原因（其中之一是实现相对简单），许多 Web 服务将其作为基于 XML 的数据传输格式的替代方案。与 XML 相比，它更轻量、更紧凑，并且更易于机器解析。

Android 在 `org.json` 包中内置了一个 JSON 解析器。

我们不会深入探讨 JSON 数据的具体语法，但这里是一个典型的 JSON 对象表示。

```json
{"result":{"aname":"value", "anumber":1234, "aboolean":false}}
```

正如你在 JSON 表示中所见，整个表示由花括号 `{` 和 `}` 包围，对象名称首先出现，用引号括起来，后跟冒号：`"result":`。下一组花括号表示该对象所包含的所有数据。每条数据都类似地标注：名称用引号括起来，后跟冒号，然后是实际数据。引号用于表示字符串；不带引号的数字就是普通数字；布尔值用 `true` 或 `false` 表示；最后，数据数组是由方括号 `[` 和 `]` 包围的一系列对象，用逗号分隔。

以下是一个名为 `anarray` 的数组：

```json
{"anarray":[{"arrayelement":"Array Element 1"}, {"arrayelement":"Array Element 2"}]}
```



### JSON 解析示例

该数组包含两个元素，每个元素都是一个由“{”和“}”包围的对象，对象之间用逗号分隔。每个元素都是一个包含名为`"arrayelement"`的字符串的对象。

让我们看看如何使用 Android 内置的 JSON 解析器来解析 JSON 数据。我们将从处理以下简单的 JSON 数据开始：

```json
{"results":{"aname":"value", "anumber":1234, "aboolean":false, "anarray":[{"arrayelement":"Array Element 1"}, {"arrayelement":"Array Element 2"}]}}
```

为了使用 JSON 解析器，数据必须是一个字符串。为此，我们需要对双引号进行转义：

```java
String JSONData = "" +
    "{\"results\":{\"aname\":\"value\", \"anumber\":1234, \"aboolean\":false, " +
    "\"anarray\":[{\"arrayelement\":\"Array Element 1\"}, {\"arrayelement\":\"Array Element 2\"}]}}";
```

Android 中的 JSON 包包含一个`JSONObject`类，可以通过传入 JSON 格式的数据（如`JSONData`字符串中的内容）来构建其实例：

```java
JSONObject overallJSONObject = new JSONObject(JSONData);
```

拿到`JSONObject`对象后，我们可以从中提取任何 JSON 对象、JSON 数组或普通字段。由于`results`是直接位于外层对象内部的 JSON 对象，我们可以使用`getJSONObject`方法并传入要提取的对象名称来获取其引用：

```java
JSONObject resultsObject = overallJSONObject.getJSONObject("results");
```

获得`resultsObject`后，就可以提取其中包含的任何数据。每种数据类型都有不同的提取方法。

要提取字符串，我们使用`getString`方法，并传入 JSON 数据中指定的名称：

```java
String aname = resultsObject.getString("aname");
```

要提取整数，我们使用`getInt`方法，并传入 JSON 数据中指定的名称。相应地，我们可以使用`getDouble`提取 JSON 数字为`double`类型，或使用`getLong`提取为`long`类型：

```java
int anumber = resultsObject.getInt("anumber");
```

要提取布尔值，我们使用`getBoolean`方法，并传入 JSON 数据中指定的名称：

```java
boolean aboolean = resultsObject.getBoolean("aboolean");
```

示例数据在`resultsObject`中还包含一个名为`anarray`的 JSON 对象数组。我们可以使用`getJSONArray`方法并传入数组名称来获取其引用：

```java
JSONArray anarray = resultsObject.getJSONArray("anarray");
```

我们可以遍历`JSONArray`对象，使用`JSONArray`提供的`getJSONObject`方法并传入元素的索引号来提取单个 JSON 对象元素：

```java
for (int i = 0; i < anarray.length(); i++) {
    JSONObject arrayElementObject = anarray.getJSONObject(i);
    String arrayelement = arrayElementObject.getString("arrayelement");
}
```

以下是综合以上所有内容的完整示例：

```java
package com.apress.proandroidmedia.ch12.simplejson;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;

public class SimpleJSON extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        String JSONData = "" +
            "{\"results\":{\"aname\":\"value\", \"anumber\":1234, \"aboolean\":false, " +
            "\"anarray\":[{\"arrayelement\":\"Array Element 1\"}, {\"arrayelement\":\"Array Element 2\"}]}}";

        // 许多 JSONObject 和 JSONArray 方法需要放在 try catch 块中，因为它们可能会抛出异常，包括 JSONObject 和 JSONArray 的构造函数。
        try {
            JSONObject overallJSONObject = new JSONObject(JSONData);
            Log.v("SIMPLEJSON", overallJSONObject.toString());

            JSONObject resultsObject = overallJSONObject.getJSONObject("results");
            Log.v("SIMPLEJSON", resultsObject.toString());

            String aname = resultsObject.getString("aname");
            Log.v("SIMPLEJSON", aname);

            int anumber = resultsObject.getInt("anumber");
            Log.v("SIMPLEJSON", "" + anumber);

        } catch (JSONException e) {
            // 处理异常
        }
    }
}
```



```java
boolean aboolean = resultsObject.getBoolean("aboolean");
Log.v("SIMPLEJSON", "" + aboolean);
JSONArray anarray = resultsObject.getJSONArray("anarray");
for (int i = 0; i < anarray.length(); i++) {
    JSONObject arrayElementObject = anarray.getJSONObject(i);
    String arrayelement = arrayElementObject.getString("arrayelement");
    Log.v("SIMPLEJSON", arrayelement);
}
} catch (JSONException e) {
```

大多数情况下，我们需要捕获的异常是 `JSONException` 的实例。这表示解析过程中出现了错误。这里我们只是打印了堆栈跟踪。在你的应用中，你可能希望采取更智能的处理方式，例如向用户显示错误消息，或者在不执行导致错误的步骤的情况下尝试重新解析。

```
e.printStackTrace();
```

## 第 12 章：使用 Web 服务消费和发布媒体

如上所述，解析 JSON 数据的基础知识非常简单。现在，让我们来看一个实际示例，了解如何组合一个用于获取 JSON 数据的 HTTP 请求。

### 使用 JSON 获取 Flickr 图片

Flickr 是一个流行的在线照片和视频分享网站，它提供了一个功能非常完备的 Web 服务 API，并将 JSON 作为输出格式之一。

与许多通过 Web 服务 API 提供功能的网站一样，Flickr 要求开发者注册并请求一个 API 密钥。API 密钥用于在 Flickr 系统中唯一标识应用程序，以便进行跟踪；如果应用程序造成问题，可以对其进行处理（例如禁用）。

要向 Flickr 请求 API 密钥，您必须登录并访问页面 [www.flickr.com/services/apps/create/apply/](http://www.flickr.com/services/apps/create/apply/?)。随后，系统会显示您的 API 密钥和一个额外的“Secret”字符串，某些功能（例如用户登录）需要用到该字符串。

在下面的示例中，我们将使用 Flickr API 来搜索标记为“waterfront”的图片。为此，我们将使用 `flickr.photos.search` 方法。您可以在 [www.flickr.com/services/api/](http://www.flickr.com/services/api) 在线查阅 Flickr API 中所有可用方法的文档。

要调用此方法，我们只需构造一个 URL，并传入需要指定的参数。我们将指定 `flickr.photos.search` 作为方法，`waterfront` 作为标签，`json` 作为格式，`5` 作为每页返回的图片数量（我们只查看一页，默认是第一页）。我们还需要传入 `nojsoncallback` 参数，其值为 `1`，这告诉 Flickr 返回纯 JSON，而不是包裹在 JavaScript 方法调用中的 JSON。最后，您需要指定您的 API 密钥（在示例中显示为 `YOUR_API_KEY`）。

```
http://api.flickr.com/services/rest/?method=flickr.photos.search&tags=waterfront&format=json&api_key=YOUR_API_KEY&per_page=5&nojsoncallback=1
```

要查看此 API 调用返回的内容，我们只需将其放入桌面浏览器中并查看响应。以下是当前返回的内容。如果您尝试运行，数据会有所不同，因为它显示的是最近五张标记为“waterfront”的图片。然而，数据结构将保持不变，这一点很重要，因为我们需要它保持一致才能围绕它构建应用程序。

```json
{"photos":{"page":1, "pages":69200, "perpage":5, "total":"345999",
"photo":[{"id":"5224082852", "owner":"43034272@N03", "secret":"9c694fa5f",
"server":"4130", "farm":5, "title":"_G8J1792", "ispublic":1, "isfriend":0,
"isfamily":0}, {"id":"5124084164", "owner":"43034272@N03", "secret":"64c867f86",
"server":"4051", "farm":5, "title":"_G8J1798", "ispublic":1, "isfriend":0,
"isfamily":0}, {"id":"5123480013", "owner":"43034272@N03", "secret":"b571b786e",
"server":"4061", "farm":5, "title":"_G8J1781", "ispublic":1, "isfriend":0,
"isfamily":0}, {"id":"5124083470", "owner":"43034272@N03", "secret":"537b42326",
```



```json
{
  "server": "4070",
  "farm": 5,
  "title": "_G8J1783",
  "ispublic": 1,
  "isfriend": 0,
  "isfamily": 0
}, {
  "id": "5124082142",
  "owner": "43034272@N03",
  "secret": "288b74481",
  "server": "1381",
  "farm": 2,
  "title": "_G8J1774",
  "ispublic": 1,
  "isfriend": 0,
  "isfamily": 0
}
]
, "stat": "ok"}
```

