# 12. 平台集成

在移动应用中，集成原生平台是很常见的。你可以编写平台特定代码来使用原生平台 API。有大量的插件可以执行不同的任务。

## 12.1 读写文件

### 问题

你想要读写文件。

### 解决方案

使用 `File` API。

### 讨论

在移动应用中，你可能需要将文件保存到设备上。`dart:io` 库提供了用于读写文件的文件 API。`File` 类拥有读取内容、写入内容和查询文件元数据的方法。文件系统的操作可以是同步的，也可以是异步的。大多数此类操作在 `File` 类中都有成对的方法。异步方法返回一个 `Future` 对象，而同步方法则使用 `Sync` 作为名称后缀，并返回实际值。例如，`readAsString()` 和 `readAsStringSync()` 方法是一对返回字符串的读取操作。表 12-1 显示了 `File` 类的异步方法。

**表 12-1**  
`File` 的异步方法

| 名称 | 描述 |
| --- | --- |
| `copy(String newPath)` | 将此文件复制到新路径。 |
| `create({bool recursive: false})` | 创建此文件。如果 `recursive` 为 true，则会创建所有目录。 |
| `open()` | 使用 `RandomAccessFile` 对象打开文件以供随机访问。 |
| `readAsBytes()` | 将整个文件内容读取为字节列表。 |
| `readAsString({Encoding encoding: utf8})` | 使用指定编码将整个文件内容读取为字符串。 |
| `readAsLines(({Encoding encoding: utf8})` | 使用指定编码将整个文件内容读取为多行文本。 |
| `writeAsBytes(List<int> bytes)` | 将字节列表写入文件。 |
| `writeAsString(String contents)` | 将字符串写入文件。 |
| `rename(String newPath)` | 将此文件重命名到新路径。 |
| `delete({bool recursive: false})` | 删除此文件。 |
| `exists()` | 检查此文件是否存在。 |
| `stat()` | 返回一个描述该文件的 `FileStat` 对象。 |
| `lastAccessed()` | 获取此文件的最后访问时间。 |
| `lastModified()` | 获取此文件的最后修改时间。 |
| `length()` | 获取此文件的长度。 |

`Directory` 类表示文件系统中的目录。给定一个 `Directory` 对象，可以使用 `list()` 或 `listSync()` 方法来列出文件和子目录。

要创建 `File` 对象，可以使用默认构造函数并传入路径。对于 Flutter 应用，该路径可能特定于平台。移动应用存储文件有两个常见位置：

-   临时目录：用于存储可能随时被清除的临时文件
-   文档目录：用于存储应用私有的文件，仅在应用被删除时才会被清除

要获取这两个位置的平台特定路径，可以使用 `path_provider` 包（https://pub.dev/packages/path_provider）。该包提供了 `getTemporaryDirectory()` 函数来获取临时目录的路径，以及 `getApplicationDocumentsDirectory()` 函数来获取应用文档目录。

在清单 12-1 中，`readConfig()` 方法从应用文档目录读取 `config.txt` 文件，而 `writeConfig()` 方法将字符串写入同一个文件。

```
class ConfigFile {
Future get _configFile async {
Directory directory = await getApplicationDocumentsDirectory();
return File('${directory.path}/config.txt');
}
Future readConfig() async {
return _configFile
.then((file) => file.readAsString())
.catchError((error) => 'default config');
}
Future writeConfig(String config) async {
File file = await _configFile;
return file.writeAsString(config);
}
}
```

**清单 12-1**  
读写文件

## 12.2 存储键值对

### 问题

你想要存储类型安全的键值对。

### 解决方案

使用 `shared_preferences` 插件。



### Discussion

你可以使用文件 API 在设备上存储任何数据。使用通用文件 API 意味着你需要自己处理数据的序列化和反序列化。如果你需要存储的数据是简单的键值对，使用 `shared_preferences` 插件（[`https://pub.dev/packages/shared_preferences`](https://pub.dev/packages/shared_preferences)）是更好的选择。该插件提供了基于 Map 的 API 来管理类型安全的键值对。键的类型始终是 `String`，只有几种类型可以作为值使用，包括 `String`、`bool`、`double`、`int` 和 `List<String>`。

要管理键值对，你需要使用静态方法 `SharedPreferences.getInstance()` 来获取 `SharedPreferences` 对象。表 12-2 展示了 `SharedPreferences` 类的方法。对于每种支持的数据类型，都有一对用于获取和设置值的方法。例如，`getBool()` 和 `setBool()` 方法用于获取和设置 `bool` 值。

表 12-2  
`SharedPreference` 的方法

| 名称 | 描述 |
| --- | --- |
| `get(String key)` | 读取指定键的值。 |
| `containsKey(String key)` | 检查指定键是否存在。 |
| `getKeys()` | 获取所有键的集合。 |
| `remove(String key)` | 移除指定键所对应的键值对。 |
| `clear()` | 移除所有键值对。 |
| `setString(String key, String value)` | 写入一个 `String` 值。 |
| `getString()` | 读取一个 `String` 值。 |

在代码清单 12-2 中，`SharedPreferences` 类用于读写一个键值对。

```dart
class AppConfig {
  Future _getPrefs() async {
    return await SharedPreferences.getInstance();
  }

  Future getName() async {
    SharedPreferences prefs = await _getPrefs();
    return prefs.getString('name') ?? "";
  }

  Future setName(String name) async {
    SharedPreferences prefs = await _getPrefs();
    return prefs.setString('name', name);
  }
}
```
代码清单 12-2  
使用 `SharedPreferences`

## 12.3 编写平台特定代码

### 问题

你想编写平台特定代码。

### 解决方案

使用平台通道在 Flutter 应用与底层宿主平台之间传递消息。



### 讨论

在 Flutter 应用中，大部分代码都是用平台无关的 Dart 语言编写的。Flutter SDK 提供的功能是有限的。有时你可能仍然需要编写特定平台的代码来使用原生平台 API。一个生成的 Flutter 应用在 `android` 和 `ios` 目录中已经包含了平台特定的代码。构建原生包需要这两个目录中的代码。

Flutter 使用消息传递来调用平台特定的 API 并获取返回结果。消息通过平台通道传递。Flutter 代码通过平台通道向宿主发送消息。宿主代码监听平台通道并接收消息。然后使用平台特定的 API 生成响应，并通过同一通道将响应发送回 Flutter 代码。传递的消息实际上是异步方法调用。

在 Flutter 代码中，平台通道是使用 `MethodChannel` 类创建的。一个应用中的所有通道名称必须唯一。建议使用域名作为通道名称的前缀。要通过通道发送方法调用，这些方法调用在被发送前必须编码为二进制格式，接收到的结果会被解码为 Dart 值。编码和解码是使用 `MethodCodec` 类的子类完成的：

- `StandardMethodCodec` 类使用标准二进制编码。
- `JSONMethodCodec` 类使用 UTF-8 JSON 编码。

`MethodChannel` 构造函数有 `name` 参数用于指定通道名称，`codec` 参数用于指定 `MethodCodec` 对象。默认使用的 `MethodCodec` 对象是 `StandardMethodCodec` 对象。

给定一个 `MethodChannel` 对象，`invokeMethod()` 方法使用指定的参数在通道上调用一个方法。返回值是一个 `Future<T>` 对象。这个 `Future` 对象可能以不同的值完成：

- 如果方法调用成功，它会以结果值完成。
- 如果方法调用失败，它会以 `PlatformException` (平台异常) 完成。
- 如果该方法尚未实现，它会以 `MissingPluginException` (缺少插件异常) 完成。

`invokeListMethod()` 方法也会调用一个方法，但返回一个 `Future<List<T>>` 对象。`invokeMapMethod()` 方法调用一个方法并返回一个 `Future<Map<K, V>>` 对象。`invokeListMethod()` 和 `invokeMapMethod()` 方法都在内部使用了 `invokeMethod()`，但增加了额外的类型转换。

在代码清单 12-3 中，通过通道调用了 `getNetworkOperator` 方法，并返回网络运营商。

```
class NetworkOperator extends StatefulWidget {
@override
_NetworkOperatorState createState() => _NetworkOperatorState();
}
class _NetworkOperatorState extends State {
static const channel = const MethodChannel('flutter-recipes/network');
String _networkOperator = ";
@override
void initState() {
super.initState();
_getNetworkOperator();
}
Future _getNetworkOperator() async {
String operator;
try {
operator = await channel.invokeMethod('getNetworkOperator') ?? 'unknown';
} catch (e) {
operator = 'Failed to get network operator: ${e.message}';
}
setState(() {
_networkOperator = operator;
});
}
@override
Widget build(BuildContext context) {
return Container(
child: Center(
child: Text(_networkOperator),
),
);
}
}
代码清单 12-3
获取网络运营商
```

`getNetworkOperator` 方法调用的处理器需要在 Android 和 iOS 平台上都实现。代码清单 12-4 展示了 Java 实现。`getNetworkOperator()` 方法使用 Android API 来获取网络运营商。在通道的方法调用处理器中，如果方法名是 `getNetworkOperator`，则使用 `Result.success()` 方法将 `getNetworkOperator()` 方法的结果作为成功响应发送回去。如果你想发送错误响应，可以使用 `Result.error()` 方法。如果方法未知，你应该使用 `Result.notImplemented()` 将此方法标记为未实现。

```
public class MainActivity extends FlutterActivity {
private static final String CHANNEL = "flutter-recipes/network";
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
GeneratedPluginRegistrant.registerWith(this);
new MethodChannel(getFlutterView(), CHANNEL)
.setMethodCallHandler((methodCall, result) -> {
if ("getNetworkOperator".equals(methodCall.method)) {
result.success(getNetworkOperator());
} else {
result.notImplemented();
}
});
}
private String getNetworkOperator() {
TelephonyManager telephonyManager =
((TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE));
return telephonyManager.getNetworkOperatorName();
}
}
代码清单 12-4
getNetworkOperator 的 Android 实现
```

代码清单 12-5 展示了 iOS 平台的 `AppDelegate.swift` 文件。`receiveNetworkOperator()` 函数使用 iOS API 来获取运营商名称，并使用 `FlutterResult` 将其作为响应发送回去。

```
import UIKit
import Flutter
import CoreTelephony
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
override func application(
_ application: UIApplication,
didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?
) -> Bool {
GeneratedPluginRegistrant.register(with: self)
guard let controller = window?.rootViewController as? FlutterViewController else {
fatalError("rootViewController is not type FlutterViewController")
}
let networkChannel = FlutterMethodChannel(name: "flutter-recipes/network", binaryMessenger: controller)
networkChannel.setMethodCallHandler({
[weak self] (call: FlutterMethodCall, result: FlutterResult) -> Void in
guard call.method == "getNetworkOperator" else {
result(FlutterMethodNotImplemented)
return
}
self?.receiveNetworkOperator(result: result)
})
return super.application(application, didFinishLaunchingWithOptions: launchOptions)
}
private func receiveNetworkOperator(result: FlutterResult) {
let networkInfo = CTTelephonyNetworkInfo()
let carrier = networkInfo.subscriberCellularProvider
result(carrier?.carrierName)
}
}
代码清单 12-5
getNetworkOperator 的 Swift 实现
```

## 12.4 创建插件

### 问题

你希望创建包含平台特定代码的可共享插件。

### 解决方案

使用插件模板创建 Flutter 项目。



### 讨论

食谱 12-4 展示了如何在 Flutter 应用中添加平台特定代码。添加到 Flutter 应用的代码无法在不同应用之间共享。若要使平台特定代码可复用，可以创建 Flutter 插件。插件是 Flutter SDK 中支持的另一种项目类型。插件可以像其他 Dart 包一样使用 Dart pub 工具（`https://pub.dev/`）进行共享。

要创建新的 Flutter 插件，可使用`flutter create --template=plugin`命令。`template=plugin`参数表示使用`plugin`模板创建 Flutter 项目。你可以分别为 Android 选择 Java 或 Kotlin，为 iOS 选择 Objective-C 或 Swift。默认情况下，Android 使用 Java，iOS 使用 Objective-C。通过`-a`参数（值为`java`或`kotlin`）可指定 Android 语言，`-i`参数（值为`objc`或`swift`）可指定 iOS 语言。以下命令展示了如何使用 Swift 为 iOS 创建插件：

```
$ flutter create --template=plugin -i swift network
```

你也可以使用 Android Studio 或 VS Code 创建新插件。

新创建的插件包含获取平台版本的骨架代码。我们可以使用食谱 12-3 中的代码实现插件，添加获取网络运营商的新方法。在生成的插件目录中，包含以下几个子目录：

*   `lib`目录包含插件的公开 Dart API。
*   `android`目录包含公开 API 的 Android 实现。
*   `ios`目录包含公开 API 的 iOS 实现。
*   `example`目录包含使用该插件的示例 Flutter 应用。
*   `test`目录包含测试代码。

我们首先在`lib/network_plugin.dart`文件中定义公开 Dart API。在清单 12-6 中，通过方法通道调用`getNetworkOperator`方法来获取`networkOperator`属性的值。

```
class NetworkPlugin {
static const MethodChannel _channel =
const MethodChannel('network_plugin');
static Future get networkOperator async {
return await _channel.invokeMethod('getNetworkOperator');
}
}
清单 12-6
插件 Dart API
```

清单 12-7 中的`NetworkPlugin.java`文件是插件的 Android 实现。`NetworkPlugin`类实现了`MethodCallHandler`接口，用于处理从平台通道接收的方法调用。

```
public class NetworkPlugin implements MethodCallHandler {
public static void registerWith(Registrar registrar) {
final MethodChannel channel = new MethodChannel(registrar.messenger(), "network_plugin");
channel.setMethodCallHandler(new NetworkPlugin(registrar));
}
NetworkPlugin(Registrar registrar) {
this.registrar = registrar;
}
private final PluginRegistry.Registrar registrar;
@Override
public void onMethodCall(MethodCall call, Result result) {
if (call.method.equals("getNetworkOperator")) {
result.success(getNetworkOperator());
} else {
result.notImplemented();
}
}
private String getNetworkOperator() {
Context context = registrar.context();
TelephonyManager telephonyManager =
((TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE));
return telephonyManager.getNetworkOperatorName();
}
}
清单 12-7
Android 实现
```

清单 12-8 中的`SwiftNetworkPlugin.swift`文件是插件的 Swift 实现。

```
public class SwiftNetworkPlugin: NSObject, FlutterPlugin {
public static func register(with registrar: FlutterPluginRegistrar) {
let channel = FlutterMethodChannel(name: "network_plugin",
binaryMessenger: registrar.messenger())
let instance = SwiftNetworkPlugin()
registrar.addMethodCallDelegate(instance, channel: channel)
}
public func handle(_ call: FlutterMethodCall,
result: @escaping FlutterResult) {
if (call.method == "getNetworkOperator") {
self.receiveNetworkOperator(result: result)
} else {
result(FlutterMethodNotImplemented)
}
}
private func receiveNetworkOperator(result: FlutterResult) {
let networkInfo = CTTelephonyNetworkInfo()
let carrier = networkInfo.subscriberCellularProvider
result(carrier?.carrierName)
}
}
清单 12-8
Swift 实现
```

示例项目和测试代码也需要使用新的 API 进行更新。

## 12.5 显示网页

### 问题

你想要显示网页。

### 解决方案

使用`webview_flutter`插件。



### 讨论

如果你想在 Flutter 应用中显示网页，可以使用 `webview_flutter` 插件（`https://pub.dartlang.org/packages/webview_flutter`）。在 `pubspec.yaml` 文件的依赖项中添加 `webview_flutter: ⁰.3.6` 后，你就可以使用 `WebView` 组件来显示网页并与之交互。对于 iOS 系统，你需要在 `ios/Runner/Info.plist` 文件中添加键 `io.flutter.embedded_views_preview`，其值为 `YES`。

表 12-3 展示了 `WebView` 构造函数的参数。要控制网页视图，你需要使用 `onWebViewCreated` 回调来获取 `WebViewController` 对象。`javascriptMode` 的值可以是 `JavascriptMode.disabled` 或 `JavascriptMode.unrestricted`。要启用在网页中执行 JavaScript，应将值设置为 `JavascriptMode.unrestricted`。`navigationDelegate` 的类型为 `NavigationDelegate`，它是一个函数，接收一个 `NavigationRequest` 对象，并返回 `NavigationDecision` 枚举值。如果返回值是 `NavigationDecision.prevent`，则导航请求会被阻止。如果返回值是 `NavigationDecision.navigate`，则导航请求可以继续。你可以使用导航委托来阻止用户访问受限页面。`onPageFinished` 回调接收已加载页面的 URL。

**表 12-3** `WebView` 构造函数参数

| 名称 | 描述 |
| --- | --- |
| `initialUrl` | 要加载的初始 URL。 |
| `onWebViewCreated` | 当 `WebView` 创建时的回调。 |
| `javascriptMode` | 是否启用 JavaScript。 |
| `javascriptChannels` | 用于接收在网页视图中运行的 JavaScript 代码所发送消息的通道。 |
| `navigationDelegate` | 决定是否应处理导航请求。 |
| `onPageFinished` | 页面加载完成时的回调。 |
| `gestureRecognizers` | 网页视图识别的手势。 |

获取 `WebViewController` 对象后，你可以使用表 12-4 中所示的方法与网页视图进行交互。所有这些方法都是异步的，并返回 `Future` 对象。例如，`canGoBack()` 方法返回一个 `Future<bool>` 对象。

**表 12-4** `WebViewController` 的方法

| 名称 | 描述 |
| --- | --- |
| `evaluateJavascript(String javascriptString)` | 在当前页面的上下文中执行 JavaScript 代码。 |
| `loadUrl(String url, { Map<String, String> headers })` | 加载指定的 URL。 |
| `reload()` | 重新加载当前 URL。 |
| `goBack()` | 在导航历史中后退。 |
| `canGoBack()` | 检查在历史中后退是否有效。 |
| `goForward()` | 在导航历史中前进。 |
| `canGoForward()` | 检查在历史中前进是否有效。 |
| `clearCache()` | 清除缓存。 |
| `currentUrl()` | 获取当前的 URL。 |

清单 12-9 展示了一个使用 `WebView` 组件与谷歌搜索页面交互的示例。由于 `WebView` 组件的创建是异步的，因此使用 `Completer<WebViewController>` 对象来捕获 `WebViewController` 对象。在 `onWebViewCreated` 回调中，用已创建的 `WebViewController` 对象来填充 `Completer<WebViewController>` 对象。在 `onPageFinished` 回调中，使用 `WebViewController` 对象的 `evaluateJavascript()` 方法来执行 JavaScript 代码，该代码向输入框设置一个值并点击搜索按钮。这会导致 `WebView` 组件加载搜索结果页面。

`JavascriptChannel` 对象通过一个通道名称和一个 `JavascriptMessageHandler` 函数创建，用于处理从网页中运行的 JavaScript 代码发送来的消息。清单 12-9 中的消息处理器使用一个 `SnackBar` 组件来显示接收到的消息。通道名称 `Messenger` 成为一个全局对象，它有一个 `postMessage` 函数，可供 JavaScript 代码用于发送消息回来。

```dart
class GoogleSearch extends StatefulWidget {
  @override
  _GoogleSearchState createState() => _GoogleSearchState();
}

class _GoogleSearchState extends State<GoogleSearch> {
  final Completer<WebViewController> _controller = Completer<WebViewController>();

  @override
  Widget build(BuildContext context) {
    return WebView(
      initialUrl: 'https://google.com',
      javascriptMode: JavascriptMode.unrestricted,
      javascriptChannels: <JavascriptChannel>[_javascriptChannel(context)].toSet(),
      onWebViewCreated: (WebViewController webViewController) {
        _controller.complete(webViewController);
      },
      onPageFinished: (String url) {
        _controller.future.then((WebViewController webViewController) {
          webViewController.evaluateJavascript(
              'Messenger.postMessage("Loaded in " + navigator.userAgent);');
          webViewController.evaluateJavascript(
              'document.getElementsByName("q")[0].value="flutter";'
              'document.querySelector("button[aria-label*=Search]").click();');
        });
      },
    );
  }

  JavascriptChannel _javascriptChannel(BuildContext context) {
    return JavascriptChannel(
      name: 'Messenger',
      onMessageReceived: (JavascriptMessage message) {
        Scaffold.of(context).showSnackBar(
          SnackBar(content: Text(message.message)),
        );
      },
    );
  }
}
```

**清单 12-9** 使用 `WebView`

## 12.6 播放视频

### 问题

你想要播放视频。

### 解决方案

使用 `video_player` 插件。



### 讨论

如果你想从资源、文件系统或网络播放视频，可以使用 `video_player` 插件（[`pub.dev/packages?q=video_player`](https://pub.dev/packages%253Fq%253Dvideo_player)）。要使用此插件，你需要将 `video_player: ⁰.10.0+5` 添加到 `pubspec.yaml` 文件的依赖项中。对于 iOS，你需要使用真机而非模拟器进行开发和测试。如果你想从任意位置加载视频，需要将代码清单 12-10 中的代码添加到 `ios/Runner/Info.plist` 文件中。使用 `NSAllowsArbitraryLoads` 会降低应用的安全性。建议查阅苹果的网络安防指南（[`developer.apple.com/documentation/security/preventing_insecure_network_connections`](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)）。

```
NSAppTransportSecurity
NSAllowsArbitraryLoads

代码清单 12-10
iOS HTTP 安全配置
```

如果在 Android 上需要从网络加载视频，你需要将代码清单 12-11 中的代码添加到 `android/app/src/main/AndroidManifest.xml` 文件中。

```
代码清单 12-11
Android
```

要播放视频，你需要使用表 12-5 中所示的构造函数来创建 `VideoPlayerController` 对象。

**表 12-5**
`VideoPlayerController` 的构造函数

| 名称 | 描述 |
| --- | --- |
| `VideoPlayerController.asset(String dataSource, { String package })` | 从资源播放视频。 |
| `VideoPlayerController.file(File file)` | 从本地文件系统播放视频。 |
| `VideoPlayerController.network(String dataSource)` | 播放从网络加载的视频。 |

创建 `VideoPlayerController` 对象后，你可以使用表 12-6 中所示的方法来控制视频播放。所有这些方法都返回 `Future` 对象。必须首先调用 `initialize()` 方法来初始化控制器。只有在 `initialize()` 方法返回的 `Future` 对象成功完成后，才能调用其他方法。

**表 12-6**
`VideoPlayerController` 的方法

| 名称 | 描述 |
| --- | --- |
| `play()` | 播放视频。 |
| `pause()` | 暂停视频。 |
| `seekTo(Duration moment)` | 跳转到指定位置。 |
| `setLooping(bool looping)` | 是否循环播放视频。 |
| `setVolume(double volume)` | 设置音频音量。 |
| `initialize()` | 初始化控制器。 |
| `dispose()` | 释放控制器并清理资源。 |

`VideoPlayerController` 类继承自 `ValueNotifier<VideoPlayerValue>`。你可以通过为其添加监听器来获知状态变更的通知。`VideoPlayerValue` 类包含多个属性，用于访问视频的状态。`VideoPlayer` 是实际显示视频的组件，它需要一个 `VideoPlayerController` 对象。

代码清单 12-12 中的 `VideoPlayerView` 类是一个用于播放指定 URL 视频的组件。在 `initState()` 方法中，使用 `VideoPlayerController.network()` 构造函数创建了 `VideoPlayerController` 对象。`FutureBuilder` 组件使用 `initialize()` 方法返回的 `Future` 对象来构建 UI。由于 `VideoPlayerController` 对象也是一个 `Listenable` 对象，我们可以将 `AnimatedBuilder` 与 `VideoPlayerController` 对象一起使用。`AspectRatio` 组件使用 `aspectRatio` 属性确保播放视频时使用正确的宽高比。`VideoProgressIndicator` 组件显示一个进度条，用于指示视频播放进度。

```
class VideoPlayerView extends StatefulWidget {
  VideoPlayerView({Key key, this.videoUrl}) : super(key: key);
  final String videoUrl;

  @override
  _VideoPlayerViewState createState() => _VideoPlayerViewState();
}

class _VideoPlayerViewState extends State<VideoPlayerView> {
  VideoPlayerController _controller;
  Future _initializedFuture;

  @override
  void initState() {
    super.initState();
    _controller = VideoPlayerController.network(widget.videoUrl);
    _initializedFuture = _controller.initialize();
  }

  @override
  Widget build(BuildContext context) {
    return FutureBuilder(
      future: _initializedFuture,
      builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
          return AnimatedBuilder(
            animation: _controller,
            child: VideoProgressIndicator(_controller, allowScrubbing: true),
            builder: (context, child) {
              return Column(
                children: [
                  AspectRatio(
                    aspectRatio: _controller.value.aspectRatio,
                    child: VideoPlayer(_controller),
                  ),
                  Row(
                    children: [
                      IconButton(
                        icon: Icon(_controller.value.isPlaying
                            ? Icons.pause
                            : Icons.play_arrow),
                        onPressed: () {
                          if (_controller.value.isPlaying) {
                            _controller.pause();
                          } else {
                            _controller.play();
                          }
                        },
                      ),
                      Expanded(child: child),
                    ],
                  ),
                ],
              );
            },
          );
        } else {
          return Center(child: CircularProgressIndicator());
        }
      },
    );
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}

代码清单 12-12
播放视频
```

## 12.7 使用相机

### 问题

你想使用相机拍照或录制视频。

### 解决方案

使用 `camera` 插件。



### 讨论

如果你想访问设备上的摄像头，可以使用`camera`插件（[`https://pub.dev/packages/camera`](https://pub.dev/packages/camera)）。要安装此插件，需要在`pubspec.yaml`文件的依赖项中添加`camera: ⁰.5.0`。对于 iOS，需要将列表 12-13 中的代码添加到`ios/Runner/Info.plist`文件中。这两个键值对描述了访问摄像头和麦克风的目的。这是保护用户隐私所必需的。

```
NSCameraUsageDescription
APPNAME requires access to your phone's camera.
NSMicrophoneUsageDescription
APPNAME requires access to your phone's microphone.
列表 12-13
iOS 的隐私要求
```

对于 Android，需要在 `android/app/build.gradle` 文件中将最低 Android SDK 版本设置为 21。

要访问摄像头，你需要创建 `CameraController` 对象。`CameraController` 构造函数需要 `CameraDescription` 和 `ResolutionPreset` 类型的参数。`CameraDescription` 类描述了一个摄像头。`ResolutionPreset` 枚举描述了屏幕分辨率的质量。`ResolutionPreset` 是一个枚举，包含 `low`、`medium` 和 `high` 三个值。要获取 `CameraDescription` 对象，你可以使用 `availableCameras()` 函数来获取可用摄像头的列表，其类型为 `List<CameraDescription>`。

表格 12-7 展示了 `CameraController` 类的方法。所有这些方法都返回 `Future` 对象。首先需要初始化一个 `CameraController` 对象。只有在 `initialize()` 返回的 `Future` 对象成功完成后，才能调用其他方法。`CameraController` 类继承自 `ValueNotifier<CameraValue>` 类，因此你可以为其添加监听器，以获取状态变更的通知。

**表 12-7** `CameraController` 的方法

| 名称 | 描述 |
| --- | --- |
| `takePicture(String path)` | 拍摄一张照片并保存到文件。 |
| `prepareForVideoRecording()` | 准备视频录制。 |
| `startVideoRecording(String filePath)` | 开始视频录制并保存到文件。 |
| `stopVideoRecording()` | 停止当前视频录制。 |
| `startImageStream()` | 开始图像流。 |
| `stopImageStream()` | 停止当前的图像流。 |
| `initialize()` | 初始化控制器。 |
| `dispose()` | 释放控制器并清理资源。 |

在列表 12-14 中，使用传入的 `CameraDescription` 对象创建了 `CameraController` 对象。`FutureBuilder` 微件在 `CameraController` 对象初始化完成后构建实际的 UI。`CameraPreview` 微件显示摄像头的实时预览。当按下图标时，会拍摄一张照片并保存到临时目录。

```
class CameraView extends StatefulWidget {
CameraView({Key key, this.camera}) : super(key: key);
final CameraDescription camera;
@override
_CameraViewState createState() => _CameraViewState();
}
class _CameraViewState extends State {
CameraController _controller;
Future _initializedFuture;
@override
void initState() {
super.initState();
_controller = CameraController(widget.camera, ResolutionPreset.high);
_initializedFuture = _controller.initialize();
}
@override
Widget build(BuildContext context) {
return FutureBuilder(
future: _initializedFuture,
builder: (context, snapshot) {
if (snapshot.connectionState == ConnectionState.done) {
return Column(
children: [
Expanded(child: CameraPreview(_controller)),
IconButton(
icon: Icon(Icons.photo_camera),
onPressed: () async {
String path = join((await getTemporaryDirectory()).path,
'${DateTime.now()}.png');
await _controller.takePicture(path);
Scaffold.of(context).showSnackBar(
SnackBar(content: Text('Picture saved to $path')));
},
),
],
);
} else {
return Center(child: CircularProgressIndicator());
}
},
);
}
@override
void dispose() {
_controller.dispose();
super.dispose();
}
}
列表 12-14
使用摄像头
```

在列表 12-15 中，`availableCameras()` 函数获取一个 `CameraDescription` 对象列表，并且只使用第一个对象来创建 `CameraView` 微件。

```
class CameraSelector extends StatelessWidget {
final Future _cameraFuture =
availableCameras().then((list) => list.first);
@override
Widget build(BuildContext context) {
return FutureBuilder(
future: _cameraFuture,
builder: (context, snapshot) {
if (snapshot.connectionState == ConnectionState.done) {
if (snapshot.hasData) {
return CameraView(camera: snapshot.data);
} else {
return Center(child: Text('No camera available!'));
}
} else {
return Center(child: CircularProgressIndicator());
}
},
);
}
}
列表 12-15
选择摄像头
```

## 12.8 使用系统共享菜单

### 问题

你想要允许用户使用系统共享菜单分享项目。

### 解决方案

使用 `share` 插件。

### 讨论

如果你想允许用户在应用中分享项目，可以使用 `share` 插件（[`https://pub.dev/packages/share`](https://pub.dev/packages/share)）来显示系统共享菜单。要使用此插件，需要在 `pubspec.yaml` 文件的依赖项中添加 `share: ⁰.6.1`。

`share` 插件提供的 API 非常简单。它只有一个静态的 `share()` 方法用于分享文本。你可以分享纯文本或 URL。列表 12-16 展示了如何使用 `share()` 方法分享一个 URL。

```
Share.share('https://flutter.dev');
列表 12-16
分享一个 URL
```

## 12.9 总结

Flutter 应用可以使用平台特定的代码来调用原生平台 API。有大量的社区插件可用于原生平台上的不同功能，包括摄像头、麦克风、传感器等。在下一章中，我们将讨论 Flutter 中的杂项主题。

