# 签名

每个应用的 APK 文件在设备上运行之前都需要进行签名。对于 "debug" 构建类型，会自动为你选择合适的签名配置，所以在调试开发阶段，你无需关心签名问题。

然而，发布版 APK 需要正确的签名配置。如果你使用 Android Studio 的菜单 构建 ➤ 生成签名 APK，Android Studio 会帮助你创建和/或使用合适的密钥。但你也可以在模块的 `build.gradle` 文件中指定签名配置。为此，添加一个 `signingConfigs { ... }` 部分，如下所示

```
android {
...
defaultConfig {...}
signingConfigs {
release {
storeFile file("myrelease.keystore")
storePassword "passwd"
keyAlias "MyReleaseKey"
keyPassword "passwd"
}
}
buildTypes {
release {
...
signingConfig signingConfigs.release
}
}
}
```

并且从发布构建类型内部引用一个签名配置，如代码清单中 `signingConfig ...` 所示。为此你需要提供的密钥库是一个标准的 Java 密钥库——请参阅 Java 的在线文档，以了解如何构建一个。或者，你可以让 Android Studio 通过菜单中选择 构建 ➤ 生成签名 APK 时弹出的对话框来帮助你创建一个密钥库。

