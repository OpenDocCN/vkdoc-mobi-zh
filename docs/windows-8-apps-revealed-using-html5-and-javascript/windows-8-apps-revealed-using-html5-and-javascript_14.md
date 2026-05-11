# 第三章：应用控件

大多数情况下，你在 Windows 8 应用中使用的用户界面控件与创建常规 HTML Web 应用时使用的控件相同。如果你想从用户那里收集数据，可以使用`select`、`input`和`textarea`等元素。如果你想允许用户启动操作，可以使用`button`元素，依此类推。这些元素在你应用中的外观由项目中的 CSS 和 Internet Explorer 10 的功能共同控制。

除了这些基本控件，WinJS API 还定义了一些特定于 Windows 8 的控件。在本章中，我将向你展示如何使用这些最重要的控件，重点介绍那些对实现 Windows 8 应用体验（无论对用户还是开发者）至关重要的控件。

我首先向你展示`AppBar`和`Flyout`控件。`AppBar`是所有 Windows 8 应用的核心功能，是用户执行无法直接在主应用布局中使用数据和控件进行的交互的机制。`Flyout`是一种为用户提供一致弹出窗口的方式，并且与`AppBar`紧密关联。

并非所有 WinJS 控件都是为了用户利益。在本章中，我将向你展示如何使用`HTMLControl`将 HTML 片段导入你的应用程序。`HTMLControl`仅适用于静态 HTML 内容，因此我还将展示如何使用*Pages*功能加载 HTML 及其关联的 JavaScript 和 CSS。这些技术允许有效划分应用内容和功能，从而带来更轻松的开发体验以及更简单的测试和维护。为了完整性，我将在本章最后展示如何将外部内容导入 Windows 8 应用，如果你已投资于现有内容基础设施并希望将其整合到应用中，这会很有用。表 3-1 提供了本章摘要。

**表 3-1.** 章节摘要

| 问题 | 解决方案 | 清单 |
| :--- | :--- | :--- |
| 指示一个`AppBar`。 | 定义一个`div`元素，其`data-win-control`属性为`WinJS.UI.AppBar`。 | 1, 3, 4 |
| 导入一段静态内容。 | 使用`HTMLControl`功能。 | 2 |
| 指示一个`Flyout`。 | 定义一个`div`元素，其`data-win-control`属性为`WinJS.UI.Flyout`。 | 5–7 |
| 将`Flyout`与`AppBar`按钮关联。 | 配置`button`元素，使其`type`属性为`flyout`，且`flyout`属性设置为`flyout`的`div`元素的`id`。 | 8 |
| 管理一个`Flyout`。 | 使用 DOM API 管理`Flyout`中的控件，并使用`Flyout`的`div`元素的`winControl`属性成员管理`Flyout`功能。 | 9, 10 |
| 将内容加载到 Windows 8 应用中。 | 创建 HTML 片段，并使用`WinJS.UI.Pages.define`方法注册一个在内容加载时执行的回调函数。使用`WinJS.UI.Pages.render`方法加载内容。 | 11–15 |
| 将 HTML、JavaScript 和 CSS 一起加载到 Windows 8 应用中。 | 创建一个 HTML 文档，并使用`script`和`link`元素引用 JavaScript 和 CSS 文件。使用`WinJS.UI.Pages.define`和`WinJS.UI.Pages.render`方法注册回调函数并加载文档。 | 16–19 |
| 显示 Windows 8 应用外部的内容。 | 使用`iframe`，并确保在应用的清单中声明了 Internet (客户端) 权限。 | 20–22 |

## 添加 AppBar

*AppBar*会在用户向上滑动手指或用鼠标右键单击时出现在屏幕底部。Windows 8 应用 UI 的重点似乎是主布局上的控件越少越好，并依赖`AppBar`作为除立即可用功能之外的任何交互机制。

我本可以直接在`default.html`中定义`AppBar`的 HTML，但正如本章开头所提到的，WinJS 支持动态加载和插入 HTML 片段。为了演示此功能，我在解决方案资源管理器中创建了一个`html`文件夹，并使用“添加新项”对话框中的“HTML 页面”项创建了一个`appbar.html`文件。

清单 3-1 展示了`appbar.html`的内容，其中包含我的`AppBar`定义。

**提示** 确保在创建`html`文件夹时没有运行调试器。如果正在运行，Visual Studio 将创建一个名为`NewFolder`的文件夹，并且在调试器停止之前不允许你更改名称。



