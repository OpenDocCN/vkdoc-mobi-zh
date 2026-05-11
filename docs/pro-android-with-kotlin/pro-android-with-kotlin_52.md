# 从控制台运行构建

你不必使用 Android Studio 来构建应用程序。虽然使用 Android Studio 启动一个应用程序项目是一个好主意，但之后你可以使用终端来构建应用程序。这就是 Gradle 包装器脚本 `gradlew` 和 `gradlew.bat` 的用途——前者用于 Linux，后者用于 Windows。在接下来的段落中，我们将介绍一些针对 Linux 的命令行命令；如果你使用的是 Windows 开发机器，只需改用 BAT 脚本即可。

在前面几节中，我们已经看到每个构建的基本构建块由构建过程中执行的一个或多个任务组成。因此，我们首先想知道实际存在哪些任务。为此，要列出所有可用的任务，请输入

```
./gradlew tasks
```

这将为你提供一个详尽的列表以及每个任务的一些描述。接下来我们将了解其中一些任务。

要为构建类型 "debug" 或 "release" 构建应用的 APK 文件，请输入以下命令之一

```
./gradlew assembleDebug
./gradlew assembleRelease
```

这将在 `<PROJECT>/<MODULE>/build/outputs` 目录下创建一个 APK 文件。当然，你也可以指定你在 `build.gradle` 中定义的任何自定义构建类型。

要构建调试类型 APK 并将其安装到连接的设备或模拟器上，请输入

```
./gradlew installDebug
```

其中，对于参数中的 "Debug" 部分，你可以使用任何构建变体的驼峰命名名称进行替换。此命令会将应用安装到连接的设备上，但不会自动运行它——你必须手动运行！要安装*并*运行一个应用，请参见第 19 章。

如果你想知道你的应用模块有哪些依赖项，以查看依赖树，请输入

```
./gradlew dependencies :app:dependencies
```

或者将 "app" 替换为相关的模块名称。这会提供一个相当冗长的列表，因此你可能希望将其输出到一个文件中

```
./gradlew dependencies :app:dependencies > deps.txt
```

然后在编辑器中检查结果。

