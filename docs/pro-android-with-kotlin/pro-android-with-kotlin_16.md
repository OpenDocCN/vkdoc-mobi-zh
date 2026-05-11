# Android 服务属性

## `android:enabled`
取值为 `true` 或 `false`。默认值为 `true`。若设为 `false`，则服务将被有效禁用。通常情况下，你不会为生产环境中的服务将其设置为 `false`。

## `android:exported`
取值为 `true` 或 `false`。表示其他应用是否可以使用该服务。如果没有定义 intent 过滤器，默认值为 `false`，否则为 `true`。intent 过滤器的存在暗示了外部使用场景，因此有此区分。

## `android:icon`
图标的资源 ID。默认值为应用的图标。

## `android:isolatedProcess`
取值为 `true` 或 `false`。默认值为 `false`。若设为 `true`，服务将无法通过常规途径与系统通信，只能通过服务方法进行交互。使用此标记通常是个好主意，但在大多数情况下，你的服务需要与系统通信，因此除非服务是完全自包含的，否则必须将其保留为 `false`。

## `android:foregroundServiceType`
取值可为以下之一：`"camera"`、`"connectedDevice"`、`"dataSync"`、`"location"`、`"mediaPlayback"`、`"mediaProjection"`、`"microphone"`、`"phoneCall"`。不同的前台服务类型会导致 Android 系统采取不同的行为。如果通过此属性明确指定前台服务的类型，可以改善用户体验。

## `android:label`
向用户显示的服务标签。默认值为应用的标签。

## `android:name`
服务类的名称。如果以点号（`.`）作为首字符，系统会自动在其前面补上 `manifest` 元素中指定的包名。

## `android:permission`
与此服务关联的权限名称。默认值为 `application` 元素中的 `permission` 属性。如果两处均未指定，服务将不受保护。

## `android:process`
服务所属进程的名称。如果指定了此属性，服务将在其自己的进程中运行。若以冒号（`:`）开头，则该进程将为应用私有；若以小写字母开头，则生成的进程将是全局进程。可能存在安全限制。

另请参阅：[`https://developer.android.com/guide/topics/manifest/service-element`](https://developer.android.com/guide/topics/manifest/service-element)

## `<service>` 元素的子元素

`<service>` 元素可以包含以下子元素：

- **`intent-filter`**：零个、一个或多个 intent 过滤器。相关描述见第 3 章“Intent 过滤器”一节。
- **`meta-data`**：任意名称-值对，格式为 `<meta-data android:name="..." android:resource="..." android:value="..." />`。可以有多个此类元素，它们会被存储到一个 `android.os.Bundle` 元素中，可通过 `PackageItemInfo.metaData` 访问。

从 Android 9（API 级别 28）开始，前台服务必须在 `AndroidManifest.xml` 文件中请求额外的权限：

```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```

## 进程理解

作为一名专业开发者，理解 *进程* 的实际含义以及 Android 操作系统如何处理它至关重要；请参阅清单中的 `android:service` 标记以控制进程。这可能很棘手，因为进程的内部机制往往会随着新 Android 版本而变化，如果你浏览博客，会发现它们似乎每分钟都在变化。事实上，*进程* 是一个计算单元，由 Android 操作系统启动以执行计算任务。当 Android 判定系统资源不足时，进程也会被终止。如果你决定停止使用某个特定应用，这并不自动意味着相应的一个或多个进程会被杀死。当你第一次启动一个应用，并且没有明确指示它使用另一个应用的进程时，系统一定会创建一个新进程并启动它。在后续的计算任务中，现有的进程会被复用，或者根据它们各自的设置及相互关系启动新的进程。

如果没有任何预防措施，由应用启动的自身服务将在该应用的进程中运行。这意味着服务可能与应用一同存在，并不可避免地随之消亡。服务需要被启动才能真正存活，但当它运行在应用的主进程中时，它会随应用的终止而终止。这自动意味着服务的资源需求会计入应用的资源需求。在以前资源更为稀缺的时代，这一点比现代设备更强大时更为重要，但了解服务是否需要大量资源仍然是有益的：当出现某种资源短缺时，是杀整个应用还是只杀那个贪心的服务来释放资源，结果会截然不同。

然而，如果你通过清单中的 `android:service` 条目告诉服务使用自己的进程，Android 操作系统就可以独立地管理服务的生命周期。你必须做出决定：要么让服务使用自己的进程，并接受单个应用可能出现进程泛滥的情况；要么让它们运行在同一个进程中，从而更紧密地耦合生命周期。

让多个计算单元运行在同一个进程中还会带来另一个后果：它们不会并发运行！这对 GUI 活动和进程来说至关重要，因为我们知道 GUI 活动必须快速响应以避免阻塞用户交互，而服务在概念上绑定于长时间运行的计算任务。解决此困境的方法是使用异步任务或线程。我们将在第 11 章中进一步讨论并发问题。

## 设备保护存储

如果服务需要访问 *设备保护存储*（例如在由 `android:directBootAware` 标记触发的 *Direct Boot* 模式下），则需要访问一个特殊的上下文：

```kotlin
val directBootContext: Context = appContext.createDeviceProtectedStorageContext()
// 例如，从那里打开一个文件：
val inStream: FileInputStream = directBootContext.openFileInput(filename)
```

通常情况下不应使用此上下文，仅适用于需要在启动后立即激活的特殊服务。

## 服务类

服务必须继承 `android.app.Service` 类或其子类，并且必须像前面描述的那样在应用的 `AndroidManifest.xml` 文件中进行声明。

`android.app.Service` 的接口方法在在线配套文本的“Intent 组成部分”一节中进行了描述。

请注意，要停止通过 `startService()` 或 `startForegroundService()` 显式启动的服务，有两种方式：服务自身通过调用 `stopSelf()` 或 `stopSelfResult()` 停止，或者从外部通过调用 `stopService()` 停止。

## 启动服务

可以从任何继承自 `android.content.Context` 或有权访问 `Context` 的组件（例如 Activity、其他 Service、BroadcastReceiver 和 ContentProvider）显式启动服务。



