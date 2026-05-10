# 第三章：使用对话框

**59**

务必使用片段管理器上的 getter 方法来检索已管理片段的实例，然后适当地转换该引用，并直接在该片段上调用方法。你甚至可以在另一个片段内部执行此操作。你通过接口和 Activity 将片段相互隔离的程度，或者通过片段间通信建立依赖关系的程度，取决于你的应用程序有多复杂以及你想要实现多少复用。

## 使用 Toast

`Toast` 就像一个迷你警报对话框，它包含一条消息，显示一定时间后会自动消失。它没有任何按钮。因此可以说它是一种短暂的警报消息。它被称为 `Toast`，是因为它像吐司面包从烤面包机中弹出一样弹出。

清单 3-6 展示了一个如何使用 `Toast` 显示消息的示例。

**清单 3-6.** 使用 `Toast` 进行调试

```java
//创建一个函数，将消息包装成 toast
//显示 toast

public void reportToast(String message)
{
    String s = MainActivity.LOGTAG + ":" + message;
    Toast.makeText(activity, s, Toast.LENGTH_SHORT).show();
}
```

清单 3-6 中的 `makeText()` 方法不仅可以接受 Activity，还可以接受任何上下文对象，例如传递给广播接收器或服务的上下文对象。这扩展了 `Toast` 在 Activity 之外的使用。

## 参考资料

- [www.androidbook.com/androidfragments/projects](http://www.androidbook.com/androidfragments/projects)  
  本章的测试项目。ZIP 文件名为 `AndroidFragments_ch03_Dialogs.zip`。下载内容包括 `PickerDialogFragmentDemo` 中的日期和时间选择器对话框示例。

- [`developer.android.com/guide/topics/ui/dialogs.html`](http://developer.android.com/guide/topics/ui/dialogs.html)  
  Android SDK 文档，提供了关于使用 Android 对话框的出色介绍。你将在这里找到关于如何使用托管对话框的解释以及各种可用对话框的示例。

**60**

**第三章：使用对话框**

- [`developer.android.com/reference/android/content/DialogInterface.html`](http://developer.android.com/reference/android/content/DialogInterface.html)  
  为对话框定义的许多常量。

- [`developer.android.com/reference/android/app/AlertDialog.Builder.html`](http://developer.android.com/reference/android/app/AlertDialog.Builder.html)  
  `AlertDialog.Builder` 类的 API 文档。

- [`developer.android.com/reference/android/app/ProgressDialog.html`](http://developer.android.com/reference/android/app/ProgressDialog.html)  
  `ProgressDialog` 的 API 文档。

- [`developer.android.com/guide/topics/ui/controls/pickers.html`](http://developer.android.com/guide/topics/ui/controls/pickers.html)  
  关于使用日期选择器和时间选择器对话框的 Android 教程。

## 总结

本章讨论了异步对话框以及如何使用对话框片段，包括以下主题：

- 什么是对话框以及为什么要使用它
- Android 中对话框的异步特性
- 在屏幕上显示对话框的三个步骤
- 创建片段
- 对话框片段创建视图层次结构的两种方法
- 片段事务如何参与显示对话框片段，以及如何获取一个对话框片段
- 当用户在查看对话框片段时按下返回键会发生什么
- 返回栈以及管理对话框片段
- 当对话框片段上的按钮被点击时会发生什么，以及如何处理
- 一种从对话框片段向调用它的 Activity 进行通信的简洁方式
- 一个对话框片段如何调用另一个对话框片段并仍然能够返回上一个对话框片段
- `Toast` 类以及如何将其用作简单的警报弹窗

---

