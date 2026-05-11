# 13. 通信



### 通信

通信是指在组件、应用或设备边界之间发送数据。一种标准化的方式是通过*广播*（*broadcasts*）实现一个或多个应用中各组件间的相互通信，我们已在第 5 章中讨论过。同一设备上应用间通信的另一种可能性是使用 `ResultReceiver` 对象，这些对象通过 `Intent` 传递。尽管其名称如此，但它们不仅可以在被调用的组件完成工作后用于将数据发送回调用方，还可以在被调用组件存活期间的任何时刻使用。我们在本书的多个地方使用了它们，但在本章中，我们将重新审视它们的使用方法，以便将所有通信方式整合在一起。

## `ResultReceiver` 类

`ResultReceiver` 对象可以通过将其分配给 `Intent`，从任何一个组件传递到另一个组件，因此您可以使用它在任意类型的组件之间发送数据，前提是这些组件位于同一设备上。

我们首先定义一个 `ResultReceiver` 的子类，它稍后将从被调用的组件接收消息，并编写：

```
class MyResultReceiver : ResultReceiver(null) {
    companion object {
        val INTENT_KEY = "my.result.receiver"
        val DATA_KEY = "data.key"
    }
    override fun onReceiveResult(resultCode: Int,
                                 resultData: Bundle?) {
        super.onReceiveResult(resultCode, resultData)
        val d = resultData?.get(DATA_KEY) as String
        Log.e("LOG", "Received: " + d)
    }
}
```

当然，您可以在其 `onReceiveResult()` 函数内编写更有意义的内容。

要将 `MyResultReceiver` 的实例传递给被调用的组件，我们现在可以编写：

```
Intent(this, CalledActivity::class.java).apply {
    putExtra(MyResultReceiver.INTENT_KEY,
             MyResultReceiver())
}.run{ startActivity(this) }
```

或使用任何其他方式调用另一个组件。

在被调用的组件内部，您现在可以在任何合适的时机通过类似下面的方式将数据发送回调用组件：

```
var myReceiver:ResultReceiver? = null
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_called)
    ...
    myReceiver = intent.
        getParcelableExtra(
            MyResultReceiver.INTENT_KEY)
}
fun go(v: View) {
    val bndl = Bundle().apply {
        putString(MyResultReceiver.DATA_KEY,
                  "Hello from called component")
    }
    myReceiver?.send(42, bndl) ?:
        throw IllegalStateException("myReceiver is null")
}
```

在生产环境中，您还需要额外注意检查接收方是否仍然存活——为简洁起见，我省略了此项检查。另请注意，在发送方，实际上并不需要持有对 `ResultReceiver` 实现的引用——如果您通过应用边界进行通信，可以直接编写：

```
...
val INTENT_KEY = "my.result.receiver"
val DATA_KEY = "data.key"
...
val myReceiver = intent.
    getParcelableExtra(
        INTENT_KEY)
...
val bndl = Bundle().apply {
    putString(DATA_KEY,
              "Hello from called component")
}
myReceiver?.send(42, bndl)
```

## 与后端通信

使用像 `Firebase` 这样基于云的服务提供商来将您的应用连接到其他设备上的其他应用，如第 10 章所述，当然有其独特的优点。您将拥有一个可靠的消息代理，附带消息备份功能、分析功能等。但使用云服务也有其缺点。您的数据，无论是否加密，都会离开您的“地盘”（即使对于企业应用也是如此），并且您无法 100% 确定提供商将来不会更改 API，从而迫使您更改您的应用。因此，如果您需要更多控制权，可以放弃云服务，转而使用直接网络连接。

对于直接使用网络协议与设备或应用服务器通信，您基本上有兩種选择：

*   **使用 `javax.net.ssl.HttpsURLConnection`。**  
    这提供了底层连接能力，但已包含 TLS、流式处理功能、超时设置以及连接池功能。正如您从其类名中看到的，它是标准 Java API 的一部分，因此您可以在网上找到大量相关信息。不过，我们将在下一节中给出说明。

*   **使用 Android 附带的 Volley API。**  
    这是一个更高级的封装，围绕基本的网络功能。使用 `Volley` 可以大大简化基于网络的开发，因此它通常是 Android 中进行网络开发的首选方案。

在这两种情况下，您都需要在 `AndroidManifest.xml` 中添加适当的权限。

## 使用 `HttpsURLConnection` 通信

在使用网络通信 API 之前，我们需要确保网络操作在后台进行——现代 Android 版本甚至不允许在 UI 线程中执行网络操作。但即使没有这个限制，也强烈建议始终在后台任务中执行网络操作。我们在第 9 章的“后台任务”部分讨论过后台操作。您首先想了解的一种方法是在挂起函数（属于协程技术的一部分）中运行网络操作，但您也可以自由选择其他方式。以下各节假定其中展示的代码片段在后台运行。

使用基于 `HttpsURLConnection` 类的通信归结为：

```
fun convertStreamToString(istr: InputStream): String {
    val s = Scanner(istr).useDelimiter("\\A")
    return if (s.hasNext()) s.next() else ""
}
// 这是访问模拟设备宿主机（开发 PC）的约定
val HOST_IP = "10.0.2.2"
val url = "https://${HOST_IP}:6699/test/person"
var stream: InputStream? = null
var connection: HttpsURLConnection? = null
var result: String? = null
try {
    connection = (URL(uri.toString()).openConnection()
            as HttpsURLConnection).apply {
        // ！仅用于测试！ 不进行 SSL 主机名验证
        class TrustAllHostNameVerifier : HostnameVerifier {
            override
            fun verify(hostname: String, session: SSLSession):
                    Boolean = true
        }
        hostnameVerifier = TrustAllHostNameVerifier()
        // 设置读取 InputStream 的超时时间为 3000 毫秒
        readTimeout = 3000
        // 设置 connect() 的超时时间为 3000 毫秒。
        connectTimeout = 3000
        // 对于此用例，将 HTTP 方法设置为 GET。
        requestMethod = "GET"
        // 默认已为 true，这里只是说明。需要
        // 为 true，因为此请求承载输入（响应）体。
        doInput = true
        // 打开通信链路
        connect()
        responseCode.takeIf {
            it != HttpsURLConnection.HTTP_OK }?.run {
            throw IOException("HTTP error code: $this")
        }
        // 检索响应体
        stream = inputStream?.also {
            result = it.let { convertStreamToString(it) }
        }
    }
} finally {
    stream?.close()
    connection?.disconnect()
}
Log.e("LOG", result)
```

这个示例尝试访问针对您开发 PC 的 GET URL `https://10.0.2.2:6699/test/person`，并将结果打印到日志中。

请注意，如果您的服务器恰好持有用于 SSL 的自签名证书，则必须在某个初始化位置（例如在 `onCreate()` 回调中）添加：

```
val trustAllCerts =
    arrayOf(object : X509TrustManager {
        override
        fun getAcceptedIssuers():
                Array? = null
        override
        fun checkClientTrusted(
                certs: Array,
                authType: String) {
        }
        override
        fun checkServerTrusted(
                certs: Array,
                authType: String) {
        }
    })
SSLContext.getInstance("SSL").apply {
    init(null, trustAllCerts, java.security.SecureRandom())
}.apply {
    HttpsURLConnection.setDefaultSSLSocketFactory(
        socketFactory)
}
```

否则，前面的代码将会报错并失败。当然，您不应该在生产代码中这样做，而应添加证书有效性检查。

## 使用 Volley 进行网络通信

`Volley` 是一个网络库，它简化了 Android 的网络操作。首先，`Volley` 会自动将工作发送到后台执行；您无需关心这一点。`Volley` 提供的其他好处包括：



*   调度机制
*   多个请求的并行处理
*   JSON 请求与响应的处理
*   缓存
*   诊断工具

要开始使用 Volley 进行开发，请将依赖项添加到模块的 `build.gradle` 文件中：

```
dependencies {
...
implementation 'com.android.volley:volley:1.2.1'
}
```

接下来要做的是设置一个 `RequestQueue`，Volley 使用它在后台处理请求。最简单的方法是在 Activity 中编写：

```
val queue = Volley.newRequestQueue(this)
```

但你也可以自定义 `RequestQueue` 的创建方式，改为编写：

```
val CACHE_CAPACITY = 1024 * 1024 // 1MB
val cache = DiskBasedCache(cacheDir, CACHE_CAPACITY)
// ... 或使用其他实现
val network = BasicNetwork(HurlStack())
// ... 或使用其他实现
val requestQueue = RequestQueue(cache, network).apply {
start()
}
```

问题在于：请求队列最好在哪个作用域下定义？我们可以在 Activity 的作用域内创建并运行请求队列，这意味着每次 Activity 重新创建时，该队列也需要重新创建。这是个可行的选项，但文档建议改为使用应用作用域以减少缓存的重新创建。推荐的方法是使用 `Singleton` 模式，结果如下：

```
class RequestQueueSingleton
constructor (context: Context) {
companion object {
@Volatile
private var INSTANCE: RequestQueueSingleton? = null
fun getInstance(context: Context) =
INSTANCE ?: synchronized(this) {
INSTANCE ?: RequestQueueSingleton(context)
}
}
val requestQueue: RequestQueue by lazy {
val alwaysTrusting = object : HurlStack() {
override
fun createConnection(url: URL): HttpURLConnection {
fun getHostnameVerifier():HostnameVerifier {
return object : HostnameVerifier {
override
fun verify(hostname:String,
session:SSLSession):Boolean = true
}
}
return (super.createConnection(url) as
HttpsURLConnection).apply {
hostnameVerifier = getHostnameVerifier()
}
}
}
// 使用应用上下文很重要。
// 此用于测试：
Volley.newRequestQueue(context.applicationContext,
alwaysTrusting)
// ... 生产环境使用：
// Volley.newRequestQueue(context.applicationContext)
}
}
```

其中，出于开发和测试目的，添加了一个接受所有 SSL 主机名验证器。

因此，与其像之前那样编写 `val queue = Volley.newRequestQueue(this)` 或 `val requestQueue = RequestQueue(...)`，你可以使用：

```
val queue = RequestQueueSingleton(this).requestQueue
```

现在，要发送字符串请求，你必须编写：

```
// 这是模拟设备访问主机（开发 PC）的约定
val HOST_IP = "10.0.2.2"
val stringRequest =
StringRequest(Request.Method.GET,
"https://${HOST_IP}:6699/test/person",
Response.Listener { response ->
val shortened =
response.substring(0,
Math.min(response.length, 500))
tv.text = "Response is: ${shortened}"
},
Response.ErrorListener { err ->
Log.e("LOG", err.toString())
tv.text = "That didn't work!"
})
queue.add(stringRequest)
```

其中 `tv` 指向一个 `TextView` UI 元素。要使它正常工作，你需要有一个服务器响应 `https://localhost:6699/test/person`。请注意，响应监听器会自动在 UI 线程中运行，因此你无需自行处理。
要取消单个请求，可以在任何位置对请求对象调用 `cancel()`。你还可以取消一组请求：为相关请求添加一个标签，例如 `val stringRequest = ... .apply {tag = "TheTag"}`，然后编写 `queue?.cancelAll("TheTag")`。Volley 确保一旦请求被取消，响应监听器永远不会被调用。

要请求一个 JSON 对象或 JSON 数组，你需要将之前使用的 `StringRequest` 替换为：

```
val request =
JsonArrayRequest(Request.Method.GET, ...)
或
val request =
JsonObjectRequest(Request.Method.GET, ...)
```

例如，对于 JSON 请求和 POST 方法，你可以编写：

```
val reqObj:JSONObject =
JSONObject("""{"a":7, "b":"Hello"}""")
val json1 = JsonObjectRequest(Request.Method.POST,
"https://${HOST_IP}:6699/test/json",
reqObj,
Response.Listener { response ->
Log.e("LOG", "Response: ${response}")
},
Response.ErrorListener{ err ->
Log.e("LOG", "Error: ${err}")
})
```

Volley 还能为你做更多事情。你可以使用其他 HTTP 方法（如 `PUT`），也可以编写自定义请求来处理和返回其他数据类型。更多详情请参阅 Volley 的在线文档或 API 文档。

## 设置测试服务器

这实际上不是 Android 主题，甚至不一定与 Kotlin 有关，但要测试通信，你需要运行某种 Web 服务器。为了方便起见，我通常配置一个基于 Groovy 和 `Spark` 的非常简洁但功能强大的服务器——不是 Apache Spark，而是来自 [`http://sparkjava.com/`](http://sparkjava.com/) 的 Java Spark。

例如，要在 Eclipse 中使用它，首先安装 Groovy 插件。然后创建一个 Maven 项目，并添加依赖项：

```
com.sparkjava
spark-core
2.9.4

org.slf4j
slf4j-simple
1.7.25
test
```

之后，创建一个 Java 密钥库文件并编写一个 Groovy 脚本：

```
import static spark.Spark.*
def keystoreFilePath = "keystore.jks"
def keystorePassword = "passw7%d"
def truststoreFilePath = null
def truststorePassword = null
secure(keystoreFilePath, keystorePassword,
truststoreFilePath, truststorePassword)
port(6699)
get("/test/person", { req, res -> "Hello World" })
post("/test/json", { req, res ->
println(req.body())
'{ "msg":"Hello World", "val":7 }'
})
```

然后启动它。

**注意**：为了避免在 Eclipse 内出现 Servlet API 版本冲突，请在项目中右键点击 Groovy Libraries，在属性中弹出的 Groovy 设置对话框中移除对 Servlet API 的依赖。

例如，在 Linux 下，可以使用以下 Bash 脚本来创建密钥库文件：

```
#!/bin/bash
export JAVA_HOME=/opt/jdk
$JAVA_HOME/bin/keytool -genkey -keyalg RSA \
-alias selfsigned -keystore keystore.jks \
-storepass passw7%d -validity 360 -keysize 2048
```

请根据实际情况调整 Java 路径。

## Android 与 NFC

NFC 是一种短距离无线连接技术，用于在支持 NFC 的设备之间传输小型数据包。通信双方之间的通信距离限制在几厘米之内。典型的用例包括：

*   连接并读取或写入 NFC 标签
*   连接并与其他支持 NFC 的设备通信（点对点模式）
*   模拟 NFC 卡：连接并与 NFC 读卡器/写入器通信

要开始开发支持 NFC 的应用，你需要在 `AndroidManifest.xml` 中获取相应的权限：

```
<uses-permission android:name="android.permission.NFC" />
```

为了在 Google Play 商店中限制应用可见性，请在同一文件中添加：

```
<uses-feature android:name="android.hardware.nfc" android:required="true" />
```

## 与 NFC 标签通信

一旦启用 NFC 的设备在附近发现 NFC 标签，它会尝试按照特定算法分发该标签：如果系统确定存在 NDEF 数据并找到能够处理 NDEF 的 Intent 过滤器，则调用相应的组件。如果标签不提供 NDEF 数据，但通过提供技术和/或有效载荷信息来标识自身，则此数据集会映射到“tech”记录，系统会尝试找到能够处理该记录的组件。如果两者都失败，则发现信息仅限于检测到 NFC 标签这一事实。在这种情况下，系统会尝试找到能够处理无 NDEF 且无“tech”类型数据的 NFC 标签的组件。
根据 NFC 标签上的信息，Android 还会创建一个 URI 和 MIME 类型，你可以将其用于 Intent 过滤器。有关该过程的更详细描述，请参阅 Android 在线开发者文档中的“NFC 基础”页面——例如，在您喜欢的搜索引擎中输入“Android 开发 NFC 基础”即可找到。



