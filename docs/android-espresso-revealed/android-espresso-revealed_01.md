# 引言

## 本书读者对象

本书是一本指导手册，面向对使用 Espresso for Android（Espresso）进行 Android 测试自动化感兴趣的**质量保证工程师**和**测试自动化工程师**，指导他们如何编写 Android 用户界面测试。对于参与编写 UI 测试或集成测试的 Android 开发者而言，本书也极具价值。

本书主要面向具备中高级 Android 测试自动化知识的软件或测试工程师编写；不过，拥有基础开发和测试自动化经验的工程师也将从中受益。

## 本书涵盖内容

目前存在大量优质的官方 Android 测试文档，包括包含源代码的 GitHub 项目，但有时很难找到所需的信息片段，尤其是涉及 Android Espresso 用户每天都会遇到的纯自动化 UI 端到端测试相关内容。

我试图涵盖使用 Espresso 测试框架编写功能性 UI 自动化测试的所有主要主题，包括运行自动化测试的不同方式、以易于维护的方式构建测试项目，以及使用能够以更少工作量实现自动化测试的工具。

## 源代码与示例项目

为展示本书中的所有代码示例，我们复刻并修改了 Google 示例的 Android 架构 TODO 应用程序项目（`https://github.com/googlesamples/android-architecture`），以便能够使用 Espresso for Android 和 UI Automator 测试框架展示大部分 Android UI 测试自动化示例。

该示例 TODO 应用程序项目包含两个分支，其中一个分支使用 Android 测试支持库依赖项，另一个分支涵盖 AndroidX 测试库的使用。读者可以自由选择自己偏好的分支。

源代码也可通过位于 `www.apress.com/9781484243145` 的“下载源代码”链接获取。

## 章节概述

### 第 1 章：Espresso for Android 入门

本章介绍 Espresso 的基础知识。它定义了用户界面测试的目标和方法，并提供了在 Android Studio IDE 项目中设置测试的示例。本章还解释了如何识别 Android 应用程序 UI 元素、执行操作和断言，以及如何对其应用匹配器。在本章结束时，你将能够编写简单的测试，并在 Android Studio IDE 中于设备或模拟器上执行这些测试。本章还包含如何通过 Gradle 或 shell 命令运行测试的示例。

### 第 2 章：按需定制 Espresso

通过更高级的示例，你将学习如何实现自定义的 `ViewAction`，包括点击和滑动操作；以及自定义的 `ViewMatcher`，例如匹配像 `RecyclerView` 这样的复杂视图。你将学习如何使用自定义操作和匹配器，实现自定义的 `FailureHandler`，并能在测试失败时截取和保存屏幕截图。

