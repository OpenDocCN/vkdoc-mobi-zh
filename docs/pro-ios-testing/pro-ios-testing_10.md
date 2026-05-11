# 11. 使用命令行工具

> *要让持续集成正常工作的最重要实践是频繁地提交到主干或主线。您应该每天至少提交几次代码。*
>
> —David Farley

## 简介

虽然使用 Xcode 的 UI 界面来配置和运行测试非常方便，但当您想在远程服务器上运行时，这是不可能的。

幸运的是，安装的每个新 Xcode 都附带一个名为“Xcode 命令行工具”的工具，可以帮助您在没有 UI 界面的情况下运行测试。

本章通过为您提供将出色的测试套件集成到持续集成环境中的工具，完成了拼图的最后一块。

在本章中，您将学习

1.  CI/CD 的含义以及测试如何融入其中
2.  Xcode 提供了哪些命令行工具
3.  如何安装和设置 `xcodebuild` 应用程序
4.  如何从命令行运行测试
5.  如何从命令行运行测试计划
6.  令人兴奋且有用的 `xcodebuild` 功能

## 究竟什么是 CI/CD？

“CI”和“CD”经常一起出现——“CI/CD”。然而，这是两个不同的过程：

**CI（持续集成）** – 在持续集成过程中，我们获取来自所有开发人员/第三方/服务器的代码变更，并将它们**合并在一起**，形成一个新的可工作构建版本。此过程的目标是确保这个构建版本稳定，且在此过程中没有出现任何问题。

**CD（持续部署）** – 在“持续部署”中，我们将构建版本上传到在线服务，并确保我们有一个可下载的应用可用。实际上，CD 是**CI 过程的延伸**，不能独立存在。

### 测试如何融入其中？

基于前面的数据，您可以理解测试是这个过程中的核心部分。实际上，没有任何测试参与的持续集成，就像盛装打扮、请了保姆，然后去餐馆只为喝一杯水——这根本不值得。

有许多工具可以帮助您在 CI 环境中自动集成测试，但它们都依赖 Xcode 命令行工具来运行这些测试。

## 命令行工具

命令行工具是将测试脚本化并融入 CI/CD 环境的绝佳方式。它们允许您从终端命令行构建、归档和测试您的项目，并通过不同的参数和参数定制您的运行。

命令行工具不仅适用于 CI/CD——它们还可以帮助您在开发过程中自动化任务。例如，您可以将测试不同测试计划、提交代码、然后归档等重复操作实现在一个脚本文件中，而不是手动重复执行。

### 认识 `xcodebuild`

“`xcodebuild`”是命令行工具包中的主要工具，用于构建、归档、分析测试以及您可以通过方案执行的任何操作。

`xcodebuild` 的真正威力在于能够灵活运行各种操作和配置，因此，它是用于测试的主要工具。

### 安装与设置 `xcodebuild`

虽然您可以单独下载命令行工具，但它们随每个新 Xcode 一同提供。

但如果您仍然想下载它们，您有两个选择：

*   从开发者网站下载。
*   使用 `xcode-select` 从命令行安装。

“`xcode-select`”是一个随 macOS 捆绑的命令。如果您想用它来安装命令行工具，请打开终端并输入

```
$ xcode-select –install
```

然后按回车键。

许多开发人员的机器上安装了多个版本的 Xcode，使用 `xcodebuild` 的首要步骤之一是确保它与正确的 Xcode 版本一起工作。

要找出哪个是“活动”的 Xcode 版本，我们可以再次使用 `xcode-select`：

```
$ xcode-select -p
/Applications/Xcode.app/Contents/Developer
```

要更改活动的 Xcode 版本，请定位开发者路径并使用 `-switch` 命令：

```
$ xcode-select -switch /Applications/Xcode12.app/Contents/Developer
```

要卸载命令行工具，只需使用以下命令删除 `/Library/Developer/CommandLineTools`：

```
$ sudo rm -r /Library/Developer/CommandLineTools
```



### 使用 xcodebuild 运行测试

使用 `xcodebuild` 运行测试相当简单，基础运行只需要很少的参数。首先，你需要确保 `Terminal` 中的当前目录是你的项目目录。

一个基础的 `xcodebuild` 命令看起来像这样：

```
$ xcodebuild \
-workspace MyWeatherApp.xcworkspace \
-scheme MyWeatherApp \
-destination 'platform=iOS Simulator,name=iPhone 11' \
test
```

让我们分解一下命令参数：

*   **workspace** – 如果你在使用工作空间而非项目（例如使用 CocoaPods），请在此处传入你的工作空间名称。
*   **scheme** – 你所选的方案名称。
*   **test** – 运行方案的 "test" 动作。
*   **destination** – 指定用于测试的平台。目标可以是模拟器或物理设备。让我们深入了解这一点。

#### 目标参数

`destination` 参数的语法基于键值对。第一个键是 **platform**，它描述了是设备还是模拟器以及具体的平台类型。

以下是你可以使用的平台列表：

*   **OS X**，你的 Mac
*   **iOS**，已连接的 iOS 设备
*   **iOS Simulator**
*   **watchOS**
*   **watchOS Simulator**
*   **tvOS**
*   **tvOS Simulator**

第二个键值对与设备类型相关。

如果是物理设备，你可以使用 **name** 来指定实际设备名称，或者使用 **id** 来指定设备的 UUID。

如果是模拟器，**name** 键描述模拟器的名称（例如 "iPhone 11"），另一个键值对是 **os**，用于指定操作系统版本（例如 "11.0"）。

让我们看一些例子：

要在运行 iOS 12.0 的 iPhone 11 模拟器上运行测试：
```
-destination "platform=iOS Simulator, name=iPhone 11, OS=12.0"
```

要在物理设备上运行测试：
```
-destination "platform=iOS, name=Avi's iPhone"
```

要列出所有可用的目标设备，请在终端输入：
```
$ instruments -s devices
```

#### 从命令行运行测试计划

你可以使用 `xcodebuild` 直接从命令行运行测试计划。

要查看一个方案下可用的测试计划列表，使用 **showTestPlans** 参数：
```
$ xcodebuild -scheme 'My Weather App' -showTestPlans
Test plans associated with the scheme "My Weather App":
Localization Test Plan
Memory
```

使用特定方案运行测试将执行默认的测试计划。要运行一个特定的测试计划，使用 **testPlan** 参数：
```
$ xcodebuild \
-workspace MyWeatherApp.xcworkspace \
-scheme MyWeatherApp \
-destination 'platform=iOS Simulator,name=iPhone 11' \
-testPlan 'Memory' test
```

### 更多重要的 xcodebuild 参数

`xcodebuild` 还有更多功能。

要列出所有方案、构建配置和目标，使用 **list** 参数：
```
$ xcodebuild -list
Information about project "My Weather App":
Targets:
My Weather App
My Weather AppTests
My Weather AppUITests
Build Configurations:
Debug
Release
If no build configuration is specified and -scheme is not passed then "Release" is used.
Schemes:
My Weather App
```

如果你已经为测试构建了应用，并希望在不重新构建的情况下再次运行测试，可以使用 **run-without-building** 来节省时间：
```
$ xcodebuild \
-workspace MyWeatherApp.xcworkspace \
-scheme MyWeatherApp \
-destination 'platform=iOS Simulator,name=iPhone 11' \
run-without-building Test
```

另一方面，如果你只想构建而不测试，可以使用 **build-for-testing** 参数：
```
$ xcodebuild \
-workspace MyWeatherApp.xcworkspace \
-scheme MyWeatherApp \
-destination 'platform=iOS Simulator,name=iPhone 11' \
build-for-testing Test
```

如果你想确保 Xcode 在运行测试前清理项目，可以在命令中添加 **clean**：
```
$ xcodebuild \
clean \
-workspace MyWeatherApp.xcworkspace \
-scheme MyWeatherApp \
-destination 'platform=iOS Simulator,name=iPhone 11' \
test
```

要查看所有可用的 SDK，尝试 **showsdks** 参数：
```
$ xcodebuild -showsdks
iOS SDKs:
iOS 13.2                      -sdk iphoneos13.2
iOS Simulator SDKs:
Simulator - iOS 13.2          -sdk iphonesimulator13.2
macOS SDKs:
DriverKit 19.0                -sdk driverkit.macosx19.0
macOS 10.15                   -sdk macosx10.15
tvOS SDKs:
tvOS 13.2                     -sdk appletvos13.2
tvOS Simulator SDKs:
Simulator - tvOS 13.2         -sdk appletvsimulator13.2
watchOS SDKs:
watchOS 6.1                   -sdk watchos6.1
watchOS Simulator SDKs:
Simulator - watchOS 6.1        -sdk watchsimulator6.1
```

## 总结

我们已经了解到，仅仅编写出色的测试是不够的；确保持续运行它们也同样重要。作为 iOS 开发者，我们需要专注于编写出色的软件和解决复杂问题。让自动化服务器来负责为我们运行测试。



