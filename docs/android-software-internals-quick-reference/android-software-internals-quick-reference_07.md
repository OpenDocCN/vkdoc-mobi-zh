# Intent

Intent 是 Android 中核心的进程间通信（IPC）机制之一，允许应用程序与其他 Android 组件（包括应用程序）进行通信（例如，发送数据或启动操作），即使接收方当前未在运行。Android 中有两类主要的 Intent：

*   **显式 Intent** - 显式 Intent 是指定应用程序，或同时指定应用程序和将执行请求的组件的 Intent。
*   **隐式 Intent** - 隐式 Intent 较为模糊，指定了所需操作的类型（例如，打开相机或位置应用）。

除了这两种主要类别的 Intent（其中消息直接发送到特定应用程序或服务）之外，还可以发送广播 Intent。这些广播消息（可由 Android 系统或应用程序发送）会被设备上所有先前已注册特定广播操作类型的应用程序同时接收。在某些情况下，可能需要特殊权限才能注册特定的广播（例如，对于 `BOOT_COMPLETE` 权限，该权限允许在 Android 系统启动完成后接收 `ACTION_BOOT_COMPLETED` Intent）。

## 启动组件

除了这两类 Intent 之外，发送 Intent 主要有三种方法：

*   **启动 Activity** - 可以通过将已初始化的 `Intent` 对象传递给 `startActivity()` 或 `startActivityForResult()` 上下文方法来启动一个 Activity 实例。
*   **启动后台服务** - 在 Android 5.0（API 级别 21）之前，可以通过将已初始化的 `Intent` 对象传递给 `startService()` 上下文方法来启动后台 `Service`。在 API 级别 21 之后，这可用于启动 `JobScheduler` 组件。
*   **发送广播** - 虽然系统会定期发送许多广播，例如 `TIME_TICK` 和 `BOOT_COMPLETE`，但普通应用程序也可以发送自己的广播。通常，广播是一种可以被多个应用程序同时接收的 Intent。此类广播可以使用 `sendBroadcast()` 或 `sendOrderedBroadcast()` 上下文方法发送。

## Intent 属性

一个 Intent 由几个属性组成。根据正在发送的 Intent 和接收者，以下某些属性可能是必需的；然而，在某些情况下，并非必需。所有标准的 Intent 属性都作为常量存储在 `Intent` 对象中，例如 `Intent.FLAG_ACTIVITY_NO_HISTORY`，其常量值为 `1073741824`。

### 核心属性

*   **Action（操作）** - 可以使用已初始化的 `Intent` 对象的 `setAction` 方法设置。操作定义了要执行的高级操作。
*   **Data（数据）** - 可以使用已初始化的 `Intent` 对象的 `setData` 方法设置。数据字段包括此 Intent 正在操作的数据（例如，要在图像编辑应用中打开的文件的路径）。

### 附加属性

*   **Category（类别）** - 可以使用已初始化的 `Intent` 对象的 `addCategory` 方法设置。类别提供了有关 Intent 要执行的操作的附加上下文。只有能够处理所有指定类别的 Activity 才能被选择来接收 Intent。
*   **Type（类型）** - 可以使用已初始化的 `Intent` 对象的 `setType` 方法设置。通常，类型是从数据本身推断出来的；但是，此属性可用于设置特定的 MIME（多用途互联网邮件扩展）类型（例如 `audio/mpeg` 或 `audio/*`），例如，对于要返回的数据。
*   **Component（组件）** - 可以使用已初始化的 `Intent` 对象的 `setComponent` 方法设置。此属性标识用于 Intent 的组件类的名称。这是一个可选属性，因为它通常根据 Intent 的内容来标识。
*   **Extras（附加信息）** - 可以使用已初始化的 `Intent` 对象的 `putExtra` 方法设置。Extra 是一个 Bundle（一组包含不同类型的键值对），接收者可以使用它（例如，当将笔记分享到您的社交媒体应用时，笔记文本将作为 Bundle 中的一个字段发送）。此 Bundle 可以包含专有键或 Android 系统中设置的键，例如 `EXTRA_ALARM_COUNT`。
*   **Flags（标志）** - 可以使用已初始化的 `Intent` 对象的 `setFlag` 方法设置。标志表示正在启动的组件的行为（例如，不将启动的 Activity 包含在任务堆栈中）。

### 操作（Actions）

可以使用已初始化的 `Intent` 对象的 `setAction` 方法设置。`getAction` 方法也可用于检索接收到的 Intent 的操作。操作定义了要执行的高级操作。

下面是一个示例：

```
Intent intent = new Intent();
intent.setAction(Intent.ACTION_MAIN);
```

在撰写本文时，Android 文档^(¹¹) 列出了超过 135 种标准 Intent 操作类型。表 4-1 列出了最常见的 Intent 操作的一个子集。

**表 4-1** Intent 操作

| 名称 | API 级别 | 描述 |
| --- | --- | --- |
| `ACTION_ALL_APPS` | 1+ | 列出所有可用的应用程序。 |
| `ACTION_APP_ERROR` | 14+ | 当用户在崩溃对话框警告消息中选择“报告”按钮时发生。然后此 Intent 将被发送到安装出现错误的应用程序的安装程序应用。 |
| `ACTION_CAMERA_BUTTON` | 1+ | 表示设备的“相机按钮”已被按下的广播。 |
| `ACTION_CHOOSER` | 1+ | 系统提供的默认标准 Activity 选择器的替代方案。此操作将显示一个 Activity 选择器。 |
| `ACTION_DEFAULT` / `ACTION_VIEW` | 1+ | 向用户显示数据的简单操作。 |
| `ACTION_MY_PACKAGE_SUSPENDED` | 28+ | 一个受保护的广播 Intent，只能由系统发送，当应用程序进入暂停状态时发送给该应用程序。 |
| `ACTION_MY_PACKAGE_UNSUSPENDED` | 28+ | 一个受保护的广播 Intent，只能由系统发送，当应用程序离开暂停状态时发送给该应用程序。 |
| `ACTION_PACKAGE_ADDED` | 1+ | 一个受保护的广播 Intent，只能由系统发送，当设备上安装了新应用程序时发送，包括两个附加信息：`EXTRA_UID` 和 `EXTRA_REPLACING`。 |
| `ACTION_PACKAGE_CHANGED` | 1+ | 一个受保护的广播 Intent，只能由系统发送，当设备上的现有应用程序发生更改时发送，包括附加信息：`EXTRA_UID`、`EXTRA_CHANGED_COMPONENT_NAME_LIST` 和 `EXTRA_DONT_KILL_APP`。 |
| `ACTION_PACKAGE_DATA_CLEARED` | 3+ | 一个受保护的广播 Intent，只能由系统发送，应在 `ACTION_PACKAGE_RESTARTED` 之前发送。当应用程序的持久数据被擦除时发送此广播，请记住此 Intent 不会发送给应用程序本身，包括附加信息：`EXTRA_UID` 和 `EXTRA_PACKAGE_NAME`。 |
| `ACTION_PACKAGE_FIRST_LAUNCH` | 12+ | 一个受保护的广播 Intent，只能由系统发送，当应用程序首次启动时发送给应用程序的安装程序（例如 Google Play 商店）。不包括附加信息；但是，数据包括已启动包的名称。 |
| `ACTION_PACKAGE_RESTARTED` | 1+ | 一个受保护的广播 Intent，只能由系统发送，当用户杀死应用程序及其所有进程时触发。数据将包含包名称，并且还将包含附加信息 `EXTRA_UID`。 |
| `ACTION_PACKAGE_REMOVED` | 1+ | 一个受保护的广播 Intent，只能由系统发送，当应用程序已从设备中移除时触发。由于应用程序已被移除，它将不会接收 Intent。除了包含应用程序名称的数据属性外，还将包含以下附加信息：`EXTRA_UID`、`EXTRA_DATA_REMOVED` 和 `EXTRA_REPLACING`。 |
| `ACTION_POWER_CONNECTED` | 4+ | 一个受保护的广播 Intent，只能由系统发送，表示设备已连接到电源。 |
| `ACTION_POWER_DISCONNECTED` | 4+ | 一个受保护的广播 Intent，只能由系统发送，表示设备已断开电源连接。 |
| `ACTION_REBOOT` | 1+ | 一个受保护的广播 Intent，只能由系统发送，指示设备重启。 |
| `ACTION_RUN` | 1+ | 运行已定义数据的高级操作。 |
| `ACTION_SCREEN_OFF` | 1+ | 一个受保护的广播 Intent，只能由系统发送，当设备屏幕进入休眠或变为非活动状态时发送。 |
| `ACTION_SHUTDOWN` | 4+ | 一个受保护的广播 Intent，只能由系统发送，在设备关机过程中触发。 |
| `ACTION_SEND` | 1+ | 向收件人发送数据的高级操作。此操作通常与选择器配对使用。 |
| `ACTION_SET_WALLPAPER` | 1+ | 用于显示选择壁纸的设置。 |
| `ACTION_TIMEZONE_CHANGED` | 1+ | 一个受保护的广播 Intent，只能由系统发送，表示系统时区已更改。包含附加信息 `EXTRA_TIMEZONE`。 |
| `ACTION_VOICE_COMMAND` | 1+ | 用于启动语音命令的 Intent 操作。 |
| `ACTION_WEB_SEARCH` | 1+ | 用于启动网络搜索的 Intent 操作。 |

### 类别（Categories）

可以使用已初始化的 `Intent` 对象的 `addCategory` 方法设置。`getCategories` 方法也可用于检索接收到的 Intent 的类别。类别提供了有关 Intent 要执行的操作的附加上下文。只有能够处理所有指定类别的 Activity 才能被选择来接收 Intent。

下面是一个示例：

```
Intent intent = new Intent();
intent.addCategory(Intent.APP_CALCULATOR);
```

在撰写本文时，Android 文档^(¹²) 列出了超过 39 种标准 Intent 类别属性。表 4-2 列出了这些常见 Intent 类别的一个子集。

**表 4-2** Intent 类别

| 名称 | API 级别 | 描述 |
| --- | --- | --- |
| `CATEGORY_ALTERNATIVE` | 1+ | 用于标识标准 Activity 或用户当前正在查看的数据的替代操作。 |
| `CATEGORY_APP_BROWSER` | 15+ | 用于打开应能够浏览互联网的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开首选的浏览器应用。 |
| `CATEGORY_APP_CALCULATOR` | 15+ | 用于打开应能够执行标准算术运算的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开计算器应用。 |
| `CATEGORY_APP_CALENDAR` | 15+ | 用于打开应能够执行标准日历操作的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开日历应用。 |
| `CATEGORY_APP_CONTACTS` | 15+ | 用于打开应能够查看和操作通讯录条目的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开联系人应用。 |
| `CATEGORY_APP_EMAIL` | 15+ | 用于打开应能够发送和接收电子邮件的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开电子邮件应用。 |
| `CATEGORY_APP_FILES` | 15+ | 用于打开应能够管理存储在设备上的文件的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开文件应用。 |
| `CATEGORY_APP_GALLERY` | 15+ | 用于打开应能够查看和操作存储在设备上的图像和视频文件的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开图库应用。 |
| `CATEGORY_APP_MAPS` | 15+ | 用于打开应能够显示用户当前位置的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开地图应用。 |
| `CATEGORY_APP_MARKET` | 11+ | 用于打开应允许用户浏览和安装新应用程序的 Activity。 |
| `CATEGORY_APP_MESSAGING` | 15+ | 用于打开应能够发送和接收短信的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开消息应用。 |
| `CATEGORY_APP_MUSIC` | 15+ | 用于打开应能够在设备上播放音乐的 Activity。它可以与 `ACTION_MAIN` 一起使用以打开音乐应用。 |
| `CATEGORY_BROWSABLE` | 1+ | 指示可以直接从浏览器调用和启动的 Activity（例如，用户选择指向 Google Play 商店网站的链接，然后被带到 Google Play 商店应用而不是网站）。 |
| `CATEGORY_CAR_MODE` | 8+ | 指示 Activity 已针对在车载环境中的使用进行了优化。 |
| `CATEGORY_DEFAULT` | 1+ | 用于标识可用作默认操作的 Activity。 |
| `CATEGORY_HOME` | 1+ | 设备启动时以及当用户返回起始 Activity 时启动的 Activity。 |
| `CATEGORY_LAUNCHER` | 1+ | 用于标识可用作设备上初始 Activity（即主屏幕）的 Activity。 |
| `CATEGORY_MONKEY` | 1+ | 指示可由 Monkey（一个 Android UI 模糊测试工具）或其他自动化工具测试的 Activity。 |

### 附加信息（Extras）

可以使用已初始化的 `Intent` 对象的 `putExtra` 方法设置。`getExtras` 方法也可用于检索接收到的 Intent 的附加信息。Extra 是一个 Bundle（一组包含不同类型的键值对），接收者可以使用它（例如，当将笔记分享到您的社交媒体应用时，笔记文本将作为 Bundle 中的一个字段发送）。此 Bundle 可以包含专有键或 Android 系统中设置的键，例如 `EXTRA_ALARM_COUNT`。

下面是一个示例：

```
Intent intent = new Intent();
intent.putExtra(Intent.EXTRA_TEXT, "这是一个文本 extra 示例");
```

在撰写本文时，Android 文档^(¹³) 列出了超过 80 种标准 Intent 附加属性。表 4-3 列出了这些常见 Intent 附加信息的一个子集。

**表 4-3** Intent 附加信息

| 名称 | API 级别 | 描述 |
| --- | --- | --- |
| `EXTRA_CHANGED_COMPONENT_NAME_LIST` | 7+ | 属于 Intent 操作 `ACTION_PACKAGE_CHANGED`，包含一个字符串数组，包含所有已更改的组件。 |
| `EXTRA_DONT_KILL_APP` | 1+ | 属于 `ACTION_PACKAGE_REMOVED` 和 `ACTION_PACKAGE_CHANGED` 操作，覆盖重启目标应用程序的默认行为。 |
| `EXTRA_DATA_REMOVED` | 3+ | 属于 `ACTION_PACKAGE_REMOVED` 操作，用于指示移除应为完全卸载，同时删除数据和代码，而不是保留数据的部分卸载（后者作为更新的一部分执行）。 |
| `EXTRA_HTML_TEXT` | 16+ | 可作为 `ACTION_SEND` 操作的一部分与 `EXTRA_TEXT` 附加信息一起使用，指示该附加文本是 HTML 格式的文本。 |
| `EXTRA_MIME_TYPES` | 19+ | 用于设置可接受的 MIME 类型（例如 `audio/mpeg` 或 `audio/*`）。 |
| `EXTRA_NOT_UNKNOWN_SOURCE` | 14+ | 属于 `ACTION_INSTALL_PACKAGE` 操作，指示要安装的应用程序是从发送 Intent 的应用程序安装的，而不是来自未知来源。 |
| `EXTRA_PACKAGE_NAME` | 24+ | 包含一个应用程序名称。 |
| `EXTRA_PHONE_NUMBER` | 1+ | 属于 `ACTION_NEW_OUTGOING_CALL` 和 `ACTION_CALL` 操作，包含呼叫的电话号码。 |
| `EXTRA_QUIET_MODE` | 24+ | 指示安静模式（其中配置文件中的所有应用程序都被终止）是已打开还是已关闭。 |
| `EXTRA_REFERRER` | 17+ | 当 Intent 启动 Activity 时使用，用于标识发起启动的来源。值以 URI 形式提供。 |
| `EXTRA_REPLACING` | 3+ | 作为 `ACTION_PACKAGE_REMOVED` 操作的一部分使用，指示包已被替换。 |
| `EXTRA_REFERRER_NAME` | 22+ | `EXTRA_REFERRER` 附加信息的替代项；但是，值以字符串形式提供，而不是 URI。 |
| `EXTRA_SHUTDOWN_USERSPACE_ONLY` | 19+ | 作为 `ACTION_SHUTDOWN` 操作的一部分使用，标识应执行部分关机。此部分关机仅重启用户空间，而不是执行完整的操作系统重启。 |
| `EXTRA_SUBJECT` | 1+ | 一个高级附加信息，包含正在发送的数据的主题。 |
| `EXTRA_TEXT` | 1+ | 属于 `ACTION_SEND` 操作。一个高级附加信息，包含要以 `CharSequence`（或 `String`，因为 `String` 实现了 `CharSequence` 接口）形式接收的数据。 |
| `EXTRA_TIME` | 30+ | 一个高级附加信息，其值包含自纪元以来的毫秒时间（例如，1608553466）。 |
| `EXTRA_TIMEZONE` | 30+ | 属于 `ACTION_TIMEZONE_CHANG`



