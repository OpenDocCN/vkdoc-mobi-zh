# 产品风味

*产品风味*允许区分不同的*功能集*或*设备要求*等特性，但你可以根据最适合自己的方式进行区分。

默认情况下，Android Studio 不会为新项目或模块准备不同的产品风味——如果需要，你必须在文件 `build.gradle` 的 `android { ... }` 元素内添加一个 `productFlavors { ... }` 部分。示例如下

```
buildTypes {...}
flavorDimensions "monetary"
productFlavors {
free {
dimension "monetary"
applicationIdSuffix ".free"
versionNameSuffix "-free"
}
paid {
dimension "monetary"
applicationIdSuffix ".paid"
versionNameSuffix "-paid"
}
}
```

其中可能的设置可以在 Android Gradle DSL 参考文档中查阅。这将生成如下形式的 APK 文件

```
app-free-debug.apk
app-paid-debug.apk
app-free-release.apk
app-paid-release.apk
```

维度甚至可以进行扩展。如果你向 `flavorDimensions` 行添加更多元素，例如 `flavorDimensions "monetary", "apilevel"`，就可以添加更多风味

```
flavorDimensions "monetary", "apilevel"
productFlavors {
free {
dimension "monetary" ... }
paid {
dimension "monetary" ... }
sinceapi21 {
dimension "apilevel"
versionNameSuffix "-api21" ... }
sinceapi24 {
dimension "apilevel"
versionNameSuffix "-api24" ... }
}
```

最终将生成以下 APK 文件集合：

```
app-free-api21-debug.apk
app-paid-api21-debug.apk
app-free-api21-release.apk
app-paid-api21-release.apk
app-free-api24-debug.apk
app-paid-api24-debug.apk
app-free-api24-release.apk
app-paid-api24-release.apk
```

要从可能的变体中过滤出某些变体，请在构建文件中添加一个 `variantFilter` 元素，并编写

```
variantFilter { variant ->
def names = variant.flavors*.name  // 这是一个数组
// 要过滤变体，在此处进行检查，然后
// 如果不需要某个变体，则执行 "setIgnore(true)"。
// 这只是一个示例：
if (names.contains("sinceapi24") &&
names.contains("free")) {
setIgnore(true)
}
}
```

