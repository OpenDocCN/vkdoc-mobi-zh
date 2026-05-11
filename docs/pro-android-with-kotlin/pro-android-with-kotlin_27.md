# 探究 Android 设置表

要探究这些表，请查阅 `android.provider.Settings` 的在线 API 文档。它展示并描述了所有可能的设置项。要列出完整的设置，你可以使用与之前用于 `ContactsContract` 合约类相同的函数：

```
showTable(Settings.Global.CONTENT_URI)
showTable(Settings.System.CONTENT_URI)
showTable(Settings.Secure.CONTENT_URI)
...
fun showTable(tbl:Uri) {
Log.e("LOG", "##################################")
Log.e("LOG", tbl.toString())
val cursor = contentResolver.query(
tbl, null, null, null, null)
cursor.moveToFirst()
while (!cursor.isAfterLast) {
Log.e("LOG", "New entry:")
for(name in cursor.columnNames) {
val v = cursor.getString(
cursor.getColumnIndex(name))
Log.e("LOG","  > " + name + " = " + v)
}
cursor.moveToNext()
}
}
```

你的应用读取设置不需要特殊权限。但是，只有 `Global` 和 `System` 表才支持写入，并且你还需要一个特殊构造来获取权限：

```
if(!Settings.System.canWrite(this)) {
val intent = Intent(
Settings.ACTION_MANAGE_WRITE_SETTINGS)
intent.data = Uri.parse(
"package:" + getPackageName())
startActivity(intent)
}
```

通常你通过调用以下代码获取权限：

```
ActivityCompat.requestPermissions(this,
arrayOf(Manifest.permission.WRITE\_SETTINGS), 42)
```

然而，对于设置权限，当前 Android 版本会立即拒绝此请求。因此你不能使用该方法，而需要像之前展示的那样调用 `Intent`。

要访问特定条目，你同样可以使用合约类中的常量和方法：

```
val uri = Settings.System.getUriFor(
Settings.System.HAPTIC_FEEDBACK_ENABLED)
Log.e("LOG", uri.toString())
val feedbackEnabled = Settings.System.getInt(
contentResolver,
Settings.System.HAPTIC_FEEDBACK_ENABLED)
Log.e("LOG", Integer.toString(feedbackEnabled))
Settings.System.putInt(contentResolver,
Settings.System.HAPTIC_FEEDBACK_ENABLED, 0)
```

### 注意事项

虽然可以为特定设置获取单独的 URI，但你不应该使用 `ContentResolver.update()`、`ContentResolver.insert()` 或 `ContentResolver.delete()` 方法来修改值。请改用合约类提供的方法。

