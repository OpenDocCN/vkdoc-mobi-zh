# 文档提供者

文档提供者是*存储访问框架（SAF）*的一部分。它提供了以文档为中心的数据访问视图，并且还展示了文档目录的分层超级结构。

> **注意**
> 存储访问框架（SAF）自 API 级别 19 开始引入。截至 2022 年中期，超过 99% 的活跃 Android 设备都支持该框架。

文档提供者的主要思想是：你的应用程序提供对文档的访问（无论相应的数据存储在哪里），而不关心文档及其结构如何呈现给用户或其他应用程序。文档提供者的数据模型由一个到多个从根节点开始的树组成，子节点要么是文档，要么是构成子树的目录，这些子树中又包含其他目录和文档。因此，它类似于文件系统中的数据结构。

要开始使用文档提供者，你需要创建一个实现`android.provider.DocumentsProvider`的类，它本身就是`android.content.ContentProvider`的一个专门子类。作为最低要求，你必须实现以下方法：

## `override fun onCreate(): Boolean`

使用此方法初始化文档提供者。由于它在应用的主线程上运行，因此不得在此执行耗时操作。但你可以准备对该提供者的数据访问。如果提供者加载成功，则返回`true`，否则返回`false`。

## `override fun queryRoots(projection: Array<out String>?): Cursor`

此方法用于查询提供者数据结构的根节点。在许多情况下，数据将适合放入一个树中，因此你只需要提供一个根节点，但你可以根据需要设置任意多个根节点。`projection`参数可能呈现一个要包含在结果集中的列列表。这些列名与`DocumentsContract.Root`中的`COLUMN_`常量相同。它可以是`null`，意思是返回所有列。该方法必须返回最多包含以下字段的游标（此处显示的是`DocumentsContract.Root`中的常量名）：

- **`COLUMN_AVAILABLE_BYTES`**：（长整型）该根节点下的可用字节数。可选，可以为`null`表示“未知”。
- **`COLUMN_CAPACITY_BYTES`**：（长整型）该根节点下树的总容量，以字节为单位。可以理解为文件系统的容量。可选，可以为`null`表示“未知”。
- **`COLUMN_DOCUMENT_ID`**：对应于该根节点的目录的 ID（字符串）。必填。
- **`COLUMN_FLAGS`**：适用于根节点的标志（整型）。是（`DocumentsContract.Root`中的常量）的组合：
  - `FLAG_LOCAL_ONLY`（仅限于设备本地，无需网络访问）
  - `FLAG_SUPPORTS_CREATE`（根节点下至少有一个文档支持创建内容）
  - `FLAG_SUPPORTS_RECENTS`（可查询根节点以显示最近更改的文档）
  - `FLAG_SUPPORTS_SEARCH`（该树允许搜索文档）
- **`COLUMN_ICON`**：（整型）根节点的图标资源 ID。必填。
- **`COLUMN_MIME_TYPES`**：（字符串）支持的 MIME 类型。如果有多个，请使用换行符“\n”作为分隔符。可选，可以为`null`表示支持所有 MIME 类型。
- **`COLUMN_ROOT_ID`**：（字符串）根节点的唯一 ID。必填。
- **`COLUMN_SUMMARY`**：（字符串）此根节点的摘要信息，可能会显示给用户。可选，可以为`null`表示“未知”。
- **`COLUMN_TITLE`**：（字符串）根节点的标题，可能会显示给用户。必填。

如果这组根节点发生了变化，你必须使用`DocumentsContract.buildRootsUri`调用`ContentResolver.notifyChange`来通知系统。

## `override fun queryChildDocuments(parentDocumentId: String?, projection: Array<out String>?, sortOrder: String?): Cursor`

返回指定目录中包含的直接子文档和子目录。针对 API 级别 26 或更高版本的应用，应改为实现`fun queryChildDocuments(parentDocumentId: String?, projection: Array<out String>?, queryArgs: Bundle?): Cursor`，并在此方法中使用：

```kotlin
override fun queryChildDocuments(
    parentDocumentId: String?,
    projection: Array<out String>?,
    sortOrder: String?): Cursor {
    val bndl = Bundle()
    bndl.putString(
        ContentResolver.QUERY_ARG_SQL_SORT_ORDER,
        sortOrder)
    return queryChildDocuments(
        parentDocumentId, projection, bndl)
}
```

## `override fun queryChildDocuments(parentDocumentId: String?, projection: Array<out String>?, queryArgs: Bundle?): Cursor`

返回指定目录中包含的直接子文档和子目录。bundle 参数包含查询参数，键为：

- `ContentResolver.QUERY_ARG_SQL_SELECTION`
- `ContentResolver.QUERY_ARG_SQL_SELECTION_ARGS`
- `ContentResolver.QUERY_ARG_SQL_SORT_ORDER` 或 `ContentResolver.QUERY_ARG_SORT_COLUMNS`（后者是一个字符串数组）

`parentDocumentId` 是我们想要列出的目录的 ID，在 `projection` 中你可以指定应返回的列——使用 `DocumentsContract.Document` 中的 `COLUMN_` 常量列表。或者传入 `null` 以返回所有列。生成的 `Cursor` 最多返回以下字段（键是 `DocumentsContract.Document` 中的常量）：

- **`COLUMN_DISPLAY_NAME`**：（字符串）文档的显示名称，用作显示给用户的主要标题。必填
- **`COLUMN_DOCUMENT_ID`**：（字符串）文档的唯一 ID。必填
- **`COLUMN_FLAGS`**：文档的标志。是（`DocumentsContract.Document` 中的常量名）的组合：
  - `FLAG_SUPPORTS_WRITE`（支持写入）
  - `FLAG_SUPPORTS_DELETE`（支持删除）
  - `FLAG_SUPPORTS_THUMBNAIL`（支持缩略图表示）
  - `FLAG_DIR_PREFERS_GRID`（对于目录，是否应以网格形式显示）
  - `FLAG_DIR_PREFERS_LAST_MODIFIED`（对于目录，首选按“最后修改时间”排序）
  - `FLAG_VIRTUAL_DOCUMENT`（没有 MIME 类型的虚拟文档）
  - `FLAG_SUPPORTS_COPY`（支持复制）
  - `FLAG_SUPPORTS_MOVE`（支持在树内移动）
  - `FLAG_SUPPORTS_REMOVE`（支持从层次结构中移除（不是删除！））
- **`COLUMN_ICON`**：（整型）文档的特定图标资源 ID。可以为 `null` 以使用系统默认图标
- **`COLUMN_LAST_MODIFIED`**：（长整型）文档最后修改的时间戳，以毫秒为单位，从 UTC 时间 1970 年 1 月 1 日 00:00:00.0 开始计算。必填，但如果未定义，可以为 `null`。



- **`COLUMN_MIME_TYPE`**：（字符串）文档的 MIME 类型。必填项

- **`COLUMN_SIZE`**：（长整型）文档的大小（以字节为单位），如果未知则为 `null`。必填项

- **`COLUMN_SUMMARY`**：（字符串）文档的摘要，可能会显示给用户。可选项，可为 `null`

对于网络相关操作，你可以先返回部分数据，并在 `Cursor` 上设置 `DocumentsContract.EXTRA_LOADING`，以表示仍在获取更多数据。然后，当网络数据可用时，你可以发送变更通知来触发重新查询并返回完整内容。为支持变更通知，你必须使用相关 URI（可能来自 `DocumentsContract.buildChildDocumentsUri()`）触发 `Cursor.setNotificationUri()`。然后，你可以使用该 URI 调用 `ContentResolver.notifyChange()` 来发送变更通知。

- **`fun openDocument(documentId: String?, mode: String?, signal: CancellationSignal?): ParcelFileDescriptor`**

打开并返回请求的文档。它应返回一个可靠的 `ParcelFileDescriptor`，以便检测远程调用方何时完成文档的读取或写入。如果在下载内容时阻塞，应定期检查 `CancellationSignal.isCanceled()` 以放弃已废弃的打开请求。参数如下：`documentId` 是要返回的文档的 ID。`mode` 指定“打开”模式，例如“r”、“w”或“rw”。应始终支持模式“r”。如果传递的模式不被支持，提供程序应抛出 `UnsupportedOperationException`。如果模式仅是“r”或“w”，你可以返回管道或套接字对，但像“rw”这样的复杂模式意味着磁盘上支持定位的普通文件。如果请求应被取消，调用者可能会使用 `signal`。可为 `null`。

- **`override fun queryDocument(documentId: String?, projection: Array<out String>?): Cursor`**

返回单个请求文档的元数据。参数如下：`documentId` 是要返回的文档的 ID，`projection` 是放入游标的列列表。使用 `DocumentsContract.Document` 中的常量；有关列表，请参见前面 `queryChildDocuments()` 方法描述。如果此处使用 `null`，则返回所有列。

在文件 `AndroidManifest.xml` 中，注册文档提供程序的方式几乎与注册其他提供程序相同：

在前面的查询中，我们看到 `Cursor` 对象返回标志，指示支持最近文档和在树内搜索。为实现这一点，你必须在你的 `DocumentsProvider` 实现中实现一个或两个额外的方法：

- **`override fun queryRecentDocuments(rootId: String, projection: Array<String>): Cursor`**

旨在返回指定根目录下最近修改过的文档。返回的文档应按照 `COLUMN_LAST_MODIFIED` 降序排序，并且最多显示 64 条记录。最近文档不支持变更通知。

- **`querySearchDocuments(rootId: String, query: String, projection: Array<String>): Cursor`**

旨在返回指定根目录下与给定查询匹配的文档。返回的文档应按照相关性降序排序。对于慢速查询，你可以返回部分数据，并在游标上设置 `EXTRA_LOADING`，以表示正在获取更多数据。然后，当数据可用时，你可以发送变更通知来触发重新查询并返回完整内容。为支持变更通知，你必须使用相关 URI（可能来自 `buildSearchDocumentsUri(String, String, String)`）调用 `setNotificationUri(ContentResolver, Uri)`。然后，你可以使用该 URI 调用方法 `notifyChange(Uri, android.database.ContentObserver, boolean)` 来发送变更通知。

一旦你的文档提供程序配置并运行，客户端组件就可以使用 `ACTION_OPEN_DOCUMENT` 或 `ACTION_CREATE_DOCUMENT` Intent 来打开或创建文档。Android 系统选择器将负责向用户展示适当的文档——你无需为文档提供程序提供自己的 GUI。

此类客户端访问的示例如下：

```
// 一个整数，可用于在调用的 Intent 返回时标识该调用
val READ_REQUEST_CODE = 42
// 此示例中使用的 ACTION_OPEN_DOCUMENT 是
// 用于选择文档（例如通过系统文件浏览器选择文件）
// 的 Intent
val intent = Intent(Intent.ACTION_OPEN_DOCUMENT)
// 过滤仅显示可以“打开”的结果，例如
// 文件（与信息项列表相对）
intent.addCategory(Intent.CATEGORY_OPENABLE)
// 你可以使用过滤器，例如仅显示图像。
// 要改为搜索所有文档，可以在此处使用 "*/*"
intent.type = "image/*"
// 实际的 Intent 调用 - 系统将提供 GUI
startActivityForResult(intent, READ_REQUEST_CODE)
```

当从系统选择器中选择一个项目后，要捕获 Intent 的返回，你可以编写类似以下内容：

```
override fun onActivityResult(requestCode:Int,
resultCode:Int,
resultData:Intent) {
// ACTION_OPEN_DOCUMENT Intent 是使用
// 请求码 READ_REQUEST_CODE 发送的。如果此处看到的
// 请求码不匹配，则它是某个其他 Intent 的响应，
// 下面的代码不应运行。
if (requestCode == READ_REQUEST_CODE
&& resultCode == Activity.RESULT_OK) {
// 选中的文档显示在
// intent.getData() 中
val uri = resultData.data
Log.i("LOG", "Uri: " + uri.toString())
showImage(uri) // 用它做点什么
}
}
```

除了像示例中那样打开文件，你还可以对 Intent 返回中收到的 URI 执行其他操作。例如，你也可以发出查询来获取元数据，如前面的查询方法所示。由于 `DocumentsProvider` 继承自 `ContentProvider`，你可以使用其中描述的方法——请参阅前面的“提供内容文件”部分，来为文档的字节打开一个流。

7. 权限



