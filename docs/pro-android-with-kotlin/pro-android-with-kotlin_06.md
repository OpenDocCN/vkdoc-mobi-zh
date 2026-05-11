# 关于代码的一些说明

虽然我力求对本书中展示的所有代码遵循*整洁代码*原则，但为了简洁明了，我使用了两种反模式，**你不应该**在你的生产代码中遵循：

-   我没有使用本地化的字符串资源。因此，当你在 XML 资源中看到类似：

```
android:text = "Some message"
```

的内容时，你实际应该做的是创建一个字符串资源，并让属性引用它，如下所示：

```
android:text = "@string/message"
```

-   对于日志记录语句，我总是使用`"LOG"`作为标签，例如：

```
Log.e("LOG", "The message")
```

在你的代码中，你应该基于类名创建一个标签：

```
companion object {
val TAG="The class name"
...
}
```

然后使用它：

```
Log.e(TAG, "The message")
```

