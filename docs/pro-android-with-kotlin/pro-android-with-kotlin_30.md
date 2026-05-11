# VoicemailContract

此合约允许访问与语音邮件提供商相关的信息。它主要由两个由内部类描述的表组成：

*   **VoicemailContract.Status** – 语音邮件源应用使用此合约向系统报告其状态。
*   **VoicemailContract.Voicemails** – 包含实际的语音邮件。

你可以列出这些表的内容——例如，对于 `Voicemails` 表，编写：

```
val uri = VoicemailContract.Voicemails.CONTENT_URI.
buildUpon().
appendQueryParameter(
VoicemailContract.PARAM_KEY_SOURCE_PACKAGE,
packageName)
.build()
showTable(uri)
fun showTable(tbl:Uri) {
Log.e("LOG", "####################################")
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

添加 `VoicemailContract.PARAM_KEY_SOURCE_PACKAGE` URI 参数非常重要；否则，你将遇到安全异常。

