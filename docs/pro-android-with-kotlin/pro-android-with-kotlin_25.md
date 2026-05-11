# 消费内容

为了消费内容，内容提供者的客户端使用一个 `android.content.ContentResolver` 对象。任何 `Context` 对象（包括 Activity、Service 等）都提供一个 `getContentResolver()` 方法，在 Kotlin 中简写为 `contentResolver`。

## 使用 Content Resolver

访问类似数据库的内容时，你可以使用以下 `ContentResolver` 方法：

- **`insert(url: Uri, values: ContentValues): Int`**：插入一条记录。
- **`delete(url: Uri, where: String, selectionArgs: Array<String>): Int`**：删除记录。
- **`update(uri: Uri, values: ContentValues, where: String, selectionArgs: Array<String>): Int`**：更新记录。
- **`query(uri: Uri, projection: Array<String>, queryArgs: Bundle, cancellationSignal: CancellationSignal): Cursor`**：查询。
- **`query(uri: Uri, projection: Array<String>, selection: String, selectionArgs: Array<String>, sortOrder: String, cancellationSignal: CancellationSignal): Cursor`**：查询。
- **`query(uri: Uri, projection: Array<String>, selection: String, selectionArgs: Array<String>, sortOrder: String): Cursor`**：查询。

这些方法的签名和含义与对应的 `ContentProvider` 方法密切相关——请参阅前面的“提供内容”部分。也可以查阅在线 API 参考。

要访问文件内容，你可以使用以下方法之一：

- **`openAssetFileDescriptor(uri: Uri, mode: String, cancellationSignal: CancellationSignal): AssetFileDescriptor`**：打开内部（资产）文件。
- **`openAssetFileDescriptor(uri: Uri, mode: String): AssetFileDescriptor`**：打开内部（资产）文件，无取消信号。
- **`openTypedAssetFileDescriptor(uri: Uri, mimeType: String, opts: Bundle, cancellationSignal: CancellationSignal): AssetFileDescriptor`**：打开类型化的内部（资产）文件。
- **`openTypedAssetFileDescriptor(uri: Uri, mimeType: String, opts: Bundle): AssetFileDescriptor`**：打开类型化的内部（资产）文件，无取消信号。
- **`openFileDescriptor(uri: Uri, mode: String, cancellationSignal: CancellationSignal): ParcelFileDescriptor`**：打开文件。
- **`openFileDescriptor(uri: Uri, mode: String): ParcelFileDescriptor`**：打开文件，无取消信号。
- **`openInputStream(uri: Uri): InputStream`**：打开输入流。
- **`openOutputStream(uri: Uri, mode: String): OutputStream`**：打开输出流。
- **`openOutputStream(uri: Uri): OutputStream`**：打开输出流，模式为 “w”。

`open*Descriptor()` 方法同样与“提供内容”部分中对应的 `ContentProvider` 方法密切相关。另外两个方法 `openInputStream()` 和 `openOutputStream()` 是用于更方便地访问文件（流）数据的便捷方法。

要注册内容观察者以便在内容发生变化时异步收到通知——请参阅前面的“通知监听器数据变更”部分——可以使用以下方法：

- `registerContentObserver(uri: Uri, notifyForDescendants: Boolean, observer: ContentObserver)`
- `unregisterContentObserver(observer: ContentObserver)`

要使用通过实现 `call()` 方法进行扩展的内容提供者，你可以使用内容解析器对应的 `call(uri: Uri, method: String, arg: String, extras: Bundle): Bundle` 方法。

