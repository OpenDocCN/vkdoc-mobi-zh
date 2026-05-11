# 内容提供者属性

`icon`  
用于提供者图标的资源 ID。默认使用父组件的图标。

`initOrder`  
在此处可为同一应用的内容提供者指定实例化顺序。数值越大，实例化越早。请谨慎使用此属性，因为启动顺序依赖可能表明应用设计存在缺陷。

`label`  
用于标签的字符串资源 ID。默认使用应用的 `label`。

`multiprocess`  
若设为 `true`，运行在多个进程中的应用可能会同时运行多个内容提供者实例。否则，最多只会存在一个内容提供者实例。默认值为 `false`。

`name`  
`ContentProvider` 实现的完全限定类名。

`permission`  
用于同时设置 `readPermission` 和 `writePermission` 的便捷方式（详见下文）。若后两者之一被指定，则优先采用该指定值。

`process`  
若希望内容提供者在不同于应用自身的进程中运行，请指定进程名称。若以“:”开头，则该进程为应用私有；若以小写字母开头，将使用全局进程（需具备相应权限）。默认在应用进程中运行。

`readPermission`  
客户端为读取内容提供者数据所必须具备的权限。未获得该权限的客户端仍可能通过 `grantUriPermissions` 属性访问内容。

`syncable`  
内容提供者的数据是否应与服务器同步。

`writePermission`  
客户端为写入内容提供者数据所必须具备的权限。未获得该权限的客户端仍可能通过 `grantUriPermissions` 属性访问内容。

---

若使用 `grantUriPermissions` 临时授予通过隐式 `Intent` 调用的其他应用组件 URI 权限，则必须精心构造此类 `Intent`：(1) 添加标志 `Intent.FLAG_GRANT_READ_URI_PERMISSION`；(2) 在 `Intent` 的 `data` 字段中添加允许访问的 URI。示例如下：

```
intent.action = "com.example.app.VIEW" // 设置 INTENT 动作
intent.flags = Intent.FLAG_ACTIVITY_NEW_TASK
intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION) // 授予临时读取权限
intent.data = Uri.parse("content:///") // 请使用你自己的 URI！
startActivity(intent)
```

在被调用组件的 Intent 过滤器中，必须指定一个 `<data>` 元素，且该元素必须包含适当的 URI *和* MIME 类型。虽然我们未明确声明，但必须指定 MIME 类型的原因是：Android 系统在解析 `Intent` 时，会通过内容提供者的 `getType(Uri)` 方法自动添加 MIME 类型。例如：

被调用组件随后获得按指定方式访问该 URI 的权限。完成操作后，应调用 `revokeUriPermission(String, Uri, Int)` 撤销已获得的临时权限：

```
revokePermission(getPackageName(), uri, Intent.FLAG_GRANT_READ_URI_PERMISSION or Intent.FLAG_GRANT_WRITE_URI_PERMISSION)
```

在 `<provider>` 元素内，可添加若干子元素：

*   **meta-data**

根据指定方式，必须指定“resource”或“value”。使用“resource”时，像 `@string/someString` 这样的资源 ID 会将资源 ID 本身赋给元条目；而使用“value”和 `@string/someString` 则会将资源的*内容*赋给元条目。

*   **grant-uri-permission**

授予特定 URI 权限（可使用零到多个此子元素）。仅当父元素的 `grantUriPermissions` 属性设为 `false` 时，此子元素才允许访问特定 URI。请使用以下属性之一：“path”用于完整 URI，“pathPrefix”用于以特定值开头的 URI，“pathPattern”支持通配符——`X*` 表示匹配零到多个任意字符 `X`，`.*` 表示匹配零到多个任意字符。

*   **path-permission**

若要定义内容提供者可提供的部分数据，可使用此元素指定路径和所需权限。“path”属性指定完整路径，“pathPrefix”属性用于匹配路径的起始部分，“pathPattern”是完整路径，但支持通配符：`*` 匹配零到多个特定字符，`.*` 匹配零到多个任意字符。`permission` 属性指定读写权限，而 `readPermission` 和 `writePermission` 属性则区分读权限和写权限。若后两者之一被指定，则优先于 `permission` 属性。

---

## 设计内容 URI

URI 描述了内容请求者感兴趣的数据域。从 SQL 的角度看，这相当于表名。然而 URI 的功能不止于此。URI 的官方语法是：

```
scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
```

可以看到，“user”“password”和“port”部分是可选的，在 Android 环境中通常不会指定它们。但这并非禁止，在某些情况下也是有意义的。“host”部分被最广泛地解释为提供某种内容的主体，这正是此处对其的解读，其中“某种内容”即数据。为使这一概念更清晰，在 Android 中“host”部分通常被称为*authority*。例如，在*通讯录*系统应用中，authority 会是 `com.android.contacts`（不要使用字符串，而应使用类常量字段——详见下文的“Contract”部分）。“scheme”按惯例通常为 `content`。因此通用通讯录 URI 以如下形式开始：

```
content://com.android.contacts
```

URI 的“path”部分指定数据域，或从 SQL 角度理解为“表”。例如通讯录中的用户个人资料数据通过以下 URI 访问：

```
content://com.android.contacts/profile
```

在此示例中路径只有一个元素，但可以更复杂，如 `pathpart1/pathpart2/pathpart3`。URI 还可以包含指定筛选条件的“query”部分。查看 `android.content.ContentProvider` 类中的查询方法，我们已有在 API 层面指定筛选条件的可能性，但允许在 URI 中包含查询参数也是完全可以接受的（尽管非必需）。若需在查询参数中放入多个元素，可遵循常规惯例使用“&”作为分隔符，例如 `name=John&age=37`。“fragment”部分指定辅助资源，在内容提供者中不常用。但如果对你有帮助，也可以使用。由于 URI 是一种非常通用的构造，且猜测用于访问某些应用所提供内容的正确 URI 几乎不可能，因此内容提供者应用通常会提供一个契约类来帮助构建正确的 URI——详见下文“构建内容接口契约”部分。

---

## 构建内容接口契约

客户端访问数据所使用的 URI 代表了对内容的接口。因此，提供一个客户端可查阅以确定使用哪些 URI 的集中式位置是个好主意。Android 文档建议为此目的使用内容契约类。此类契约类的大致轮廓如下：


```kotlin
class MyContentContract {
    companion object {
        // 授权信息和基础 URI
        @JvmField
        val AUTHORITY = "com.xyz.whatitis"
        @JvmField
        val CONTENT_URI = Uri.parse("content://" +
                AUTHORITY)
        // 基于 ID 查询的选择条件
        @JvmField
        val SELECTION_ID_BASED = BaseColumns._ID +
                " = ? "
    }
    // 针对不同数据表（或项目类型）
    // 建议用内部类来分别定义。
    // 以下仅为一个示例
    class Items {
        companion object {
            // 项目名称
            @JvmField
            val NAME = "item_name"
            // 项目的 Content URI
            @JvmField
            val CONTENT_URI = Uri.withAppendedPath(
                MyContentContract.CONTENT_URI, "items")
            // 项目目录的 MIME 类型
            @JvmField
            val CONTENT_TYPE =
                ContentResolver.CURSOR_DIR_BASE_TYPE +
                        "/vnd." + MyContentContract.AUTHORITY +
                        ".items"
            // 单个项目的 MIME 类型
            @JvmField
            val CONTENT_ITEM_TYPE =
                ContentResolver.CURSOR_ITEM_BASE_TYPE +
                        "/vnd." + MyContentContract.AUTHORITY +
                        ".items"
            // 在此可以添加数据库列名、投影规范
            // 或排序规范等
            // ...
        }
    }
}
```

当然，界面设计的一部分必须为类、字段名和字段值使用有意义的名字。

**注意**

契约类中描述的界面并非*必须*对应实际的数据库表——将界面与实际实现从概念上解耦，并在此提供以任意方式衍生的表连接或其他项目类型，不仅完全可行，而且是有益的。

关于这个结构有几点说明：

- 如果你能确保客户端仅使用 Kotlin 作为平台，那么代码可以写得更简短，无需任何样板代码：

```kotlin
object MyContentContract2 {
    val AUTHORITY = "com.xyz.whatitis"
    val CONTENT_URI = Uri.parse("content://"
            + AUTHORITY)
    val SELECTION_ID_BASED =
        BaseColumns._ID + " = ? "
    object Items {
        val NAME = "item_name"
        val CONTENT_URI =
            Uri.withAppendedPath(
                MyContentContract.CONTENT_URI, "items")
        val CONTENT_TYPE =
            ContentResolver.CURSOR_DIR_BASE_TYPE +
                    "/vnd." + MyContentContract.AUTHORITY +
                    ".items"
        val CONTENT_ITEM_TYPE =
            ContentResolver.CURSOR_ITEM_BASE_TYPE +
                    "/vnd." + MyContentContract.AUTHORITY +
                    ".items"
    }
}
```

然而，如果我们希望 Java 客户端也能使用这个界面，就必须使用这些 `companion object` 和 `@JvmObject` 声明及修饰符。

- 使用 `companion object` 和 `@JvmObject` 注解可以像 Java 的静态字段一样，通过 `TheClass.THE_FIELD` 进行访问。

- 你可以考虑为客户端提供等效的 Java 构造，这样如果客户端只使用 Java，就无需学习 Kotlin 语法。

- `Uri.parse()` 和 `Uri.withAppendedPath()` 方法调用只是 `Uri` 类使用的两个例子。该类还包含其他一些有助于构建正确 URI 的方法。

- 你也可以在契约类内部提供辅助方法。如果这样做，请确保该界面类不依赖其他类，并为 `fun` 函数声明添加 `@JvmStatic` 修饰符，使其可从 Java 调用。

然后，你可以将此契约类（或者多个类，如果你想同时用 Kotlin 和 Java 记录界面）公开提供给任何可能使用你内容提供器应用的客户端。

## 基于 `AbstractCursor` 等的游标类

`ContentProvider` 中所有的 `query*()` 方法都返回一个 `android.database.Cursor` 对象。从包名可以看出这是一个以数据库为中心的类，这实际上是 Android 的一个小设计缺陷，因为内容接口本应对访问方法保持不可知。

除此之外，`Cursor` 接口为希望遍历结果集的客户端提供了一个随机访问接口。你可以使用基础实现 `android.database.AbstractCursor` 作为游标类——它已经实现了该接口的多个方法。具体做法是编写 `class MyCursor : AbstractCursor { ... }` 或 `val myCursor = object : AbstractCursor { ... }`，然后实现所有抽象方法并重写部分其他方法，使类具备实际功能：

- **`override fun getCount(): Int`**

  可用的数据集数量。

- **`override fun getColumnNames(): Array<String>`**

  列名的有序数组。

- **`override fun getInt(column: Int): Int`**

  获取一个整型值（列索引从 0 开始）。

- **`override fun getLong(column: Int): Long`**

  获取一个长整型值（列索引从 0 开始）。

- **`override fun getShort(column: Int): Short`**

  获取一个短整型值（列索引从 0 开始）。

- **`override fun getFloat(column: Int): Float`**

  获取一个浮点值（列索引从 0 开始）。

- **`override fun getDouble(column: Int): Double`**

  获取一个双精度浮点值（列索引从 0 开始）。

- **`override fun getString(column: Int): String`**

  获取一个字符串值（列索引从 0 开始）。

- **`override fun isNull(column: Int): Boolean`**

  指示该值是否为 `null`（列索引从 0 开始）。

- **`override fun getType(column: Int): Int`**

  你不必重写此方法，但如果未重写，它将始终返回 `Cursor.FIELD_TYPE_STRING`，并假定 `getString()` 始终返回有意义的内容。若需要更精细的控制，可让它返回 `FIELD_TYPE_NULL`、`FIELD_TYPE_INTEGER`、`FIELD_TYPE_FLOAT`、`FIELD_TYPE_STRING` 或 `FIELD_TYPE_BLOB` 中的一个。列索引从 0 开始。

- **`override fun getBlob(column: Int): ByteArray`**

  如果你要支持 BLOB（二进制大对象），请重写此方法。否则将抛出 `UnsupportedOperationException`。

- **`override fun onMove(oldPosition: Int, newPosition: Int): Boolean`**

  尽管没有标记为 `abstract`，你*必须*重写此方法。你的实现必须将游标移动到结果集中对应的位置。可能的值范围从 `-1`（第一个位置之前，无效位置）到 `count`（最后一个位置之后，无效位置）。如果移动成功，请返回 `true`。如果不重写，则不会发生任何操作，并且该函数始终返回 `true`。

`AbstractCursor` 还提供了一个方法 `fillWindow(position: Int, window: CursorWindow?): Unit`，你可以用它基于查询结果集填充 `android.database.CursorWindow` 对象。请参阅 `CursorWindow` 的在线 API 文档，以进一步了解此方法。

除了 `AbstractCursor`，`Cursor` 接口还有多个其他（抽象）实现可供使用。我们在表 6-2 中简要总结了它们。

**表 6-2** 更多游标实现

| `android.database` 中的名称 | 描述 |
|---|---|
| `AbstractWindowedCursor` | 继承自 `AbstractCursor`，拥有一个包含数据的 `CursorWindow` 对象。子类负责在 `onMove(Int, Int)` 操作期间填充游标窗口，并在必要时分配新的游标窗口。与 `AbstractCursor` 相比更易实现，但需要在 `onMove()` 中添加大量功能。 |
| `CrossProcessCursor` | 一种允许从远程进程使用的游标实现。它只是 `android.database.Cursor` 接口的扩展，包含三个额外的方法：`fillWindow(Int, CursorWindow)`、`getWindow(): CursorWindow` 和 `onMove(Int, Int): Boolean`。它不提供自身实现；你需要重写 `Cursor` 中定义的所有方法。 |


`CrossProcessCursorWrapper` | 一种游标实现，允许从远程进程使用它。实现`CrossProcessCursor`并持有一个`Cursor`委托，该委托也可以是`CrossProcessCursor`。 |
`CursorWrapper` | 持有一个`Cursor`委托，所有方法调用都将转发给它。 |
`MatrixCursor` | `Cursor`的完整实现，以对象数组的形式在内存中存储数据。你必须使用`addRow(...)`来添加数据。内部类`MatrixCursor.RowBuilder`可用于构建行，供`MatrixCursor.addRow(Array<Object>)`使用。 |
`MergeCursor` | 使用它可以透明地合并或连接游标对象。 |
`sqlite.SQLiteCursor` | `Cursor`的一种实现，数据由 SQLite 数据库提供支持。使用其中一个构造函数将游标连接到 SQLite 数据库对象。 |

## 基于游标接口的游标类

实现游标的一种更底层的方法是不依赖`AbstractCursor`，而是自己实现所有接口方法。然后你可以像`class MyCursor : Cursor { ... }`那样使用子类化，或者像`val myCursor = object : Cursor { ... }`那样使用匿名对象。在在线文本伴侣的“游标接口”部分，我们列出并描述了所有接口方法。

## 在提供者代码内分派 URI

为了简化传入 URI 的分派，`android.content.UriMatcher`类非常方便。如果你有类似这样的查询相关 URI：

```
people            #列出目录中的所有人员
people/37         #查询 ID 为 37 的人员
people/37/phone   #获取 ID 为 37 的人员的电话信息
```

并且想使用一个简单的 switch 语句，你可以在你的类或对象内部编写：

```
val PEOPLE_DIR_AUTHORITY = "directory"
val PEOPLE = 1
val PEOPLE_ID = 2
val PEOPLE_PHONES = 3
val uriMatcher = UriMatcher(UriMatcher.NO_MATCH)
init {
uriMatcher.addURI(PEOPLE_DIR_AUTHORITY,
"people", PEOPLE)
uriMatcher.addURI(PEOPLE_DIR_AUTHORITY,
"people/#", PEOPLE_ID)
uriMatcher.addURI(PEOPLE_DIR_AUTHORITY,
"people/#/phone", PEOPLE_PHONES)
}
```

其中“#”代表任意数字，“*”匹配任意字符串。

在你的`ContentProvider`实现中，然后你可以使用以下结构来分派传入的字符串 URL：

```
when(uriMatcher.match(url)) {
PEOPLE ->
// 传入路径 = people，对其进行处理...
PEOPLE_ID ->
// 传入路径 = people/#，对其进行处理...
PEOPLE_PHONES ->
// 传入路径 = people/#/phone，...
else ->
// 做其他事情
}
```

## 提供内容文件

内容提供者不仅仅可以访问类似数据库的内容；它们还可以公开用于检索类似文件数据（如图像或声音文件）的方法。为此目的，提供了以下方法：

*   `override fun getStreamTypes(uri:Uri, mimeTypeFilter:String) : Array<String>`  
    如果你的内容提供者提供文件，请重写此方法以允许客户端根据 URI 确定支持的 MIME 类型。`mimeTypeFilter`不应为`null`，你可以使用它来过滤输出。它支持通配符，因此如果客户端想要检索所有值，它将在此处写入“*/*”，你的提供者代码需要正确处理此情况。输出还必须包含那些可能是提供者执行适当类型转换后结果的所有类型。可能返回`null`以指示结果集为空。例如“image/png”或“audio/mpeg”。

*   `override fun openFile(uri:Uri, mode:String): ParcelFileDescriptor`  
    重写此方法以处理打开文件 blob 的请求。参数“mode”必须是以下之一（没有默认值）：
    *   “r” 表示只读访问
    *   “w” 表示只写访问（如果数据已存在则先擦除）
    *   “wa” 类似“w”，但可能追加数据
    *   “rw” 表示读取和追加写入
    *   “rwt” 类似“rw”，但会截断现有数据
    要了解如何处理返回的`ParcelFileDescriptor`，请参阅下文。

*   `override fun openFile(uri:Uri, mode:String, signal:CancellationSignal): ParcelFileDescriptor`  
    与`openFile(Uri, String)`相同，但另外客户端可以在读取文件进行中时发出取消信号——提供者可以保存`signal`对象，并通过定期调用`signal`对象上的`throwIfCancelled()`来捕获客户端的取消请求。

*   `override fun openAssetFile(uri:Uri, mode:String): AssetFileDescriptor`  
    这类似于`openFile(Uri, String)`，但可以由需要能够返回文件子集（通常是其 APK 中的资产）的提供者实现。要实现此目的，你可能想使用`android.content.res.AssetManager`类。你可以在上下文的`asset`字段中找到它，因此，例如在 Activity 中，你可以直接使用`asset`来寻址`AssetManager`。

*   `override fun openAssetFile(uri:Uri, mode:String, signal:CancellationSignal): AssetFileDescriptor`  
    与`openAssetFile(Uri, String)`相同，但允许从客户端取消。提供者可以保存`signal`对象，并通过定期调用`signal`对象上的`throwIfCancelled()`来捕获客户端的取消请求。

*   `override fun : openTypedAssetFile(uri:Uri, mimeTypeFilter:String, opts:Bundle): AssetFileDescriptor`  
    如果你希望客户端能够按 MIME 类型读取（不是写入！）资产数据，请实现此方法。默认实现将`mimeTypeFilter`与从`getType(Uri)`获得的任何内容进行比较，如果匹配，则简单地转发到`openAssetFile(...)`。

*   `override fun : openTypedAssetFile(uri:Uri, mimeTypeFilter:String, opts:Bundle, signal:CancellationSignal): AssetFileDescriptor`  
    与`openTypedAssetFile(Uri, String, Bundle)`相同，但允许从客户端取消。提供者可以保存`signal`对象，并通过定期调用`signal`对象上的`throwIfCancelled()`来捕获客户端的取消请求。

*   `override fun <T : Any?> openPipeHelper(uri: Uri?, mimeType: String?, opts: Bundle?, args: T, func: PipeDataWriter<T>?): ParcelFileDescriptor`  
    一个用于实现`openTypedAssetFile(Uri, String, Bundle)`的辅助函数。它创建一个数据管道和一个后台线程，允许你将生成的数据流回客户端。此函数返回一个新的`ParcelFileDescriptor`。工作完成后，调用者必须关闭它。

*   `override fun openFileHelper(uri:Uri, mode:String): ParcelFileDescriptor`  
    这是一个供子类使用的便捷方法。默认实现打开一个文件，其路径由使用提供的 URI 进行的`query()`结果给出。对于文件路径，从查询结果中提取“_data”成员，并且结果集计数必须为`1`。

那些返回`ParcelFileDescriptor`对象的方法可以像这样调用适当的构造函数：

```
val fd = ... // 获取 ParcelFileDescriptor
val inpStream = ParcelFileDescriptor.AutoCloseInputStream(fd)
val outpStream = ParcelFileDescriptor.AutoCloseOutputStream(fd)
```

来为文件构建输入和输出流。一旦流的工作完成，你必须在流上使用`close()`方法 —— “Auto”意味着当你关闭流时，`ParcelFileDescriptor`会自动为你关闭。

类似地，那些返回`AssetFileDescriptor`对象的方法可以像这样调用适当的构造函数：

```
val fd = ... // 获取 AssetFileDescriptor
val inpStream = AssetFileDescriptor.AutoCloseInputStream(fd)
val outpStream = AssetFileDescriptor.AutoCloseOutputStream(fd)
```

来为文件构建输入和输出流。同样，一旦流的工作完成，你必须在流上使用`close()`方法 —— 只有当你关闭流时，`AssetFileDescriptor`才会为你自动关闭。

## 通知监听器数据已更改



