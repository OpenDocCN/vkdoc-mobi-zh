# 生命周期事件

在本书的最后一章中，我将向你展示如何通过响应关键的 Windows 事件来控制 Windows 8 应用的生命周期。我将展示如何替换 Visual Studio 添加到项目中的代码，如何正确处理应用被挂起和恢复，以及如何实现将你的应用融入 Windows 8 提供的更广泛用户体验的合约。在此过程中，我将演示地理位置功能的使用，并展示如何设置和管理一个循环的后台任务。表 5-1 提供了本章的总结。

***表 5-1.*** 章节总结

| 问题 | 解决方案 | 列表 |
|------|----------|------|
| 确保你的应用接收挂起和恢复事件。 | 订阅来自`Windows.UI.WebUI.WebUIApplication`对象的事件。 | |
| 创建一个循环的后台任务。 | 使用`WinJS.Promise`对象作为其他异步活动的包装器。 | 2–4 |
| 在应用被挂起前请求更多时间。 | 调用传递给挂起处理程序函数的事件上的`suspendingOperation.getDeferral`方法。 | 5 |
| 实现一个合约。 | 在清单中声明合约，并响应激活事件中的类型信息。 | 6–8 |

## 处理应用生命周期

在第 1 章中，我向你展示了 Visual Studio 放置在`default.js`文件中的骨架代码，以便为我的示例项目提供快速启动。这段代码处理 Windows 8 应用的*生命周期事件*，确保我能适当响应操作系统发送给我的信号。Windows 8 应用的生命周期有三个关键阶段。

第一阶段，*激活*，发生在 Windows 希望你的应用执行某些任务时。最常见的任务是 Windows 希望启动你的应用并显示给用户，但也有其他任务，你将在本章后面看到我展示如何实现*合约*时看到一个例子。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 5 章 ■ 生命周期事件

用户不会关闭 Windows 8 应用；他们只是切换到另一个应用，让 Windows 来处理一切。这就是 Windows 8 UI 上没有关闭按钮或菜单栏的原因。不再需要的 Windows 8 应用会进入第二阶段，被*挂起*。在挂起状态下，不会执行任何应用代码，也不会与用户进行交互。

如果用户切换回一个被挂起的应用，则会发生第三阶段：应用被*恢复*。执行继续后，应用将显示给用户。

挂起的应用并不总是被恢复。例如，如果设备内存不足，Windows 可能会简单地*终止*一个挂起的应用。

## 修正 Visual Studio 事件代码



Visual Studio 添加到新项目中的代码足够让一个基础应用启动并运行，但它并未支持全部的生命周期事件。它能够很好地处理激活和挂起操作，却阻止了应用在恢复时收到通知。幸运的是，API 中还有其他地方可以让我注册以接收生命周期事件，因此本章我的首要任务是更新 `default.js`，以便在我的应用进入全部三个生命周期阶段时能够正确收到通知。清单 5-1 展示了这些更改。

**清单 5-1.** 注册生命周期事件通知

```javascript
(function () {
    "use strict";

    Windows.UI.WebUI.WebUIApplication.addEventListener("activated", performInitialSetup);
    Windows.UI.WebUI.WebUIApplication.addEventListener("resuming", performResume);
    Windows.UI.WebUI.WebUIApplication.addEventListener("suspending", performSuspend);

    function performInitialSetup(e) {
        WinJS.UI.processAll().then(function () {
            UI.List.displayListItems();
            UI.List.setupListEvents();
            UI.AppBar.setupButtons();
            UI.Flyouts.setupAddItemFlyout();
            ViewModel.State.bind("selectedItemIndex", function (newValue) {
                WinJS.Utilities.empty(itemDetailTarget)
                var url = newValue == -1 ? "/html/noSelection.html" :
                    "/pages/itemDetail/itemDetail.html"
                WinJS.UI.Pages.render(url, itemDetailTarget);
            });
            WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget);

            function setOrientationClass() {
                if (Windows.UI.ViewManagement.ApplicationView.value ==
                    Windows.UI.ViewManagement.ApplicationViewState.fullScreenPortrait) {
                    WinJS.Utilities.addClass(contentGrid, "flex");
                } else {
                    WinJS.Utilities.removeClass(contentGrid, "flex");
                }
            };
            window.addEventListener("resize", setOrientationClass);
            setOrientationClass();
            unsnapButton.addEventListener("click", function (e) {
                Windows.UI.ViewManagement.ApplicationView.tryUnsnap();
            });
            Tiles.sendTileUpdate();
            Tiles.sendBadgeUpdate();
        });
    }

    function performResume(e) {
        WinJS.Utilities.query("#topRightContainer h1")[0].innerText = "Resumed";
    }

    function performSuspend(e) {
        WinJS.Utilities.query("#leftContainer h1")[0].innerText = "Suspended";
    }
})();
```

我使用了通过 `Windows.UI.WebUI.WebUIApplication` 类提供的事件，这些事件与生命周期事件完美对应。我的示例 Windows 8 应用当前并未执行任何受应用挂起和恢复影响的任务，但我想向你展示如何测试这些事件。为此，我在 `performResume` 和 `performSuspend` 函数中添加了语句，这些语句会更改 HTML 文档中 `h1` 元素的值，以指示何时接收到挂起和恢复事件。

Visual Studio 包含在调试器中模拟挂起和恢复事件的支持，但我建议你学习如何在操作系统中直接触发这些事件，这样你就不需要使用 Visual Studio 进行测试了。

问题在于 Visual Studio 必须禁用真实的操作系统事件，以便 Windows 8 应用进程始终处于其控制之下。这意味着 Visual Studio 可能会创建一种与用户使用你的应用时所看到的略微不同的体验。

不幸的是，当你不使用 Visual Studio 工作时，你将无法访问 JavaScript 控制台窗口等有用的工具，当然也包括调试器本身。你必须找到另一种机制来观察应用生命周期的变化，这就是我通过更改布局中 `h1` 元素显示的文本来处理恢复和挂起事件的原因，如下所示：

```javascript
...
WinJS.Utilities.query("#leftContainer h1")[0].innerText = "Suspended";
...
```

通过使用布局来记录接收到的生命周期事件，我能够确保我的应用收到了我所期望的事件。现在，我已经修复了 `default.js` 文件中的事件处理代码，并通过更改应用布局增加了记录事件接收的支持，接下来我将向你展示生成这些事件的不同方法：通过 Visual Studio 以及直接使用 Windows 8。

## 使用 Visual Studio 模拟生命周期事件

要使用 Visual Studio 模拟生命周期事件，请从 **调试** 菜单中选择 **开始调试** 来启动应用。Visual Studio 会在工具栏上显示一个弹出菜单，如图 5-1 所示，其中包含挂起和恢复应用的选项；这些选项会向应用发送挂起和恢复事件。还有一个选项会发送挂起事件然后终止应用（这实际上会终止应用，而不仅仅是模拟效果）。

**图 5-1.** 使用 Visual Studio 模拟生命周期事件

> **提示：** 对于某些版本的 Visual Studio，只有在 **视图** > **工具栏** 菜单中选中 **调试位置** 选项后，工具栏菜单才会显示。

要测试 Visual Studio 对生命周期事件的支持，请启动应用，然后从菜单中选择 **挂起** 项。应用将收到挂起事件，并且不再显示。稍等片刻，然后从 Visual Studio 菜单中选择 **恢复**；应用将重新出现，你会看到屏幕顶部的 `h1` 元素已更新，反映了已收到挂起和恢复事件的事实，如图 5-2 所示。

**图 5-2.** 使用应用布局中的元素记录生命周期事件

## 触发生命周期事件

在接下来的部分中，我将向你展示如何在不使用 Visual Studio 的情况下触发生命周期事件。使用真实事件测试你的应用行为非常重要，而不要仅仅依赖 Visual Studio 支持的模拟生命周期。

### 激活应用

要触发激活事件，请从 Visual Studio 的 **调试** 菜单中选择 **开始执行（不调试）** 来启动应用程序。你也可以从 **开始** 屏幕启动应用，无论是在模拟器中还是在本地机器上。关键是不使用调试器来启动应用。

### 挂起应用

挂起应用最简单的方法是按下 `Win+D` 切换到桌面。打开 **任务管理器**，右键单击你的 Windows 8 应用对应的项，然后从弹出菜单中选择 **转到详细信息**。任务管理器将切换到 **详细信息** 选项卡。（你可能需要先单击 **更多详细信息** 按钮，然后才能选择 **详细信息** 选项卡。）在 **详细信息** 选项卡上，你会看到一个或多个 `WWAHost.exe` 进程，每个进程对应一个 Windows 8 应用。你可以通过以下方式判断哪个是示例应用：几秒钟后，**状态** 列中显示的值将从 `Running` 变为 `Suspended`，这告诉你 Windows 已挂起该应用，如图 5-3 所示。

**图 5-3.** 显示已挂起的 Windows 8 应用的 Windows 任务管理器

### 恢复应用

切换回应用将恢复它。你会看到布局顶部显示的 `h1` 元素表明恢复和挂起事件都已由 Windows 发送。

恢复后的应用状态与挂起时的状态完全相同。你的布局、数据、事件处理程序以及其他所有内容都将保持不变。例如，在处理恢复事件时，你无需调用 `processAll`。



你的应用可能已被挂起很长时间，尤其是当设备进入低电量状态（如睡眠模式）时。网络连接将被你之前通信的所有服务器关闭（这就是为什么你在收到挂起事件时应显式关闭它们的原因），并且在你的应用恢复运行时必须重新打开连接。你还需要刷新可能已过时的数据，这包括位置数据，因为设备可能在你应用被挂起期间移动过位置。

## 添加异步活动

现在我已确认应用能够接收并响应恢复和挂起事件，接下来可以添加需要周期性任务的功能。在本示例中，我将使用地理定位服务来报告当前设备位置。为此，我创建了一个名为 `location.js` 的新 JavaScript 文件，其内容如清单 5-2 所示。

**清单 5-2.** 追踪设备位置

```
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" />
[www.it-ebooks.info](http://www.it-ebooks.info/)

(function () {
    "use strict";

    var currentPromise;
    var tracking = false;

    function trackLocation() {
        currentPromise = new WinJS.Promise(function (complete) {
            var geo = new Windows.Devices.Geolocation.Geolocator();
            if (geo) {
                geo.getGeopositionAsync().then(function (position) {
                    WinJS.xhr({
                        url: "http://nominatim.openstreetmap.org"
                            + "/reverse?format=json&lat="
                            + position.coordinate.latitude
                            + "&lon=" + position.coordinate.longitude
                    }).then(function (data) {
                        var dataObject = JSON.parse(data.response);
                        if (dataObject.address.road) {
                            var date = new Date();
                            var time = date.getHours() + ":" + date.getMinutes()
                                + ":" + date.getSeconds();
                            document.getElementById("geo").innerText =
                                dataObject.address.road + " (" + time + ")";
                        }
                    });
                });
            }
            complete();
        });

        currentPromise.then(function () {
            if (tracking) {
                setTimeout(trackLocation, 5000);
            }
        });
    }

    WinJS.Namespace.define("Location", {
        startTracking: function () {
            tracking = true;
            trackLocation();
        },
        stopTracking: function () {
            tracking = false;
            return currentPromise;
        }
    });
})();
```

## 使用位置追踪

Windows 8 的地理定位服务可通过 `Windows.Devices.Geolocation.Geolocator` 对象使用。你可以订阅位置信息变化时的事件，但我想演示一个重复的后台任务，因此使用了 `getGeopositionAsync` 方法，该方法会生成当前位置的快照。这是一个异步操作，因此 `getGeopositionAsync` 方法返回一个 `WinJS.Promise` 对象，该对象在位置信息可用时被完成。

获取位置数据后，我使用 `WinJS.xhr` 对象进行 Ajax 调用，该对象是标准浏览器 `XmlHttpRequest` 对象的 `Promise` 封装。我的 Ajax 请求发送到反向地理编码服务，这样我就能将地理定位服务提供的纬度和经度信息转换为街道地址。地理编码服务返回 JSON 字符串，我将其解析为 JavaScript 对象，从中读取街道名称，以便连同显示上次位置更新的时间戳一起展示给用户。

**提示：** 我使用了 OpenStreetMap 的地理编码服务，因为它不需要唯一的账户令牌。这意味着你无需创建 Google Maps 或 Bing Maps 账户即可运行本示例。

我在 `default.html` 文件中添加了一些简单元素，以便向用户显示位置信息。你可以在清单 5-3 中看到这些添加内容。

**清单 5-3.** 向 `default.html` 添加元素以显示位置信息

```
...
<script src="/js/tiles.js"></script>
<script src="/js/location.js"></script>
<script src="/js/default.js"></script>
</head>
<body>
<div class="midtitle">
    <h2>你的当前位置是：<span id="geo"></span></h2>
</div>
<div id="contentGrid">
```



