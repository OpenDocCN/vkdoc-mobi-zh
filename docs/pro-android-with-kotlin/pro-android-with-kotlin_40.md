# 上下文菜单

上下文菜单在长按已注册的视图后显示。要进行此注册，请在 Activity 内部调用 `registerForContextMenu()` 并将该视图作为参数传入：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
...
registerForContextMenu(myViewId)
}
```

如果你希望多个视图都能显示上下文菜单，可以多次执行此操作。

注册上下文菜单后，你可以在 Activity 中重写 `onCreateContextMenu(...)` 来定义它，并重写 `onContextItemSelected` 来监听菜单选择事件。示例如下：

```kotlin
override
fun onCreateContextMenu(menu: ContextMenu, v: View,
menuInfo: ContextMenuInfo?) {
super.onCreateContextMenu(menu, v, menuInfo)
val inflater = menuInflater
inflater.inflate(R.menu.context_menu, menu)
}
override
fun onContextItemSelected(item: MenuItem): Boolean {
when (item.itemId) {
ctxmenu_item1 -> {
Toast.makeText(this,"CTX Item 1",
Toast.LENGTH_LONG).show()
}
ctxmenu_item2 -> {
Toast.makeText(this,"CTX Item 2",
Toast.LENGTH_LONG).show()
}
else -> return
super.onContextItemSelected(item)
}
return true
}
```

XML 定义放在一个标准的菜单 XML 文件中，例如 `res/context_menu.xml`：

也可以通过在 Activity 内部调用 `openContextMenu( someView )` 以编程方式打开上下文菜单。

