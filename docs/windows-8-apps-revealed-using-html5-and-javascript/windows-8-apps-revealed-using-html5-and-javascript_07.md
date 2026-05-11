# 以 `win-type` 开头的类名

以 `win-type` 开头的类名定义在 `ui-dark.css`（或者，如果你切换了主题，则是 `ui-light.css`）中，用于设置 Windows 8 应用中文本的大小。有一系列与文本大小渐变（从最大到最小）相关的样式：`win-type-xx-large`、`win-type-x-large`、`win-type-large`、`win-type-medium`、`win-type-small` 和 `win-type-x-small`。还有另外两种 `win-type` 样式：当文本无法放入其父级元素时，`win-type-ellipsis` 会使用省略号（`...`）；而 `win-type-interactive` 则使元素看起来像链接一样。在 `default.html` 中，我使用了 `win-type-xx-large` 样式来在布局中创建占位标题。

## 探索 `default.css` 文件

清单 1-4 展示了 `/css/default.css` 文件的内容。按照惯例，`css` 文件夹用于放置定义你的 CSS 样式的文件，而 `default.css` 文件由 Visual Studio 自动创建，它会同时在 `default.html` 文件中添加一个对应的 `link` 元素。

Windows 8 项目依赖于带有一些特定于供应商前缀的标准 CSS。微软过去曾因其引入自己的 CSS 属性而臭名昭著，但你在本书中会遇到的那些属性之所以存在，要么是因为相关的 W3C 标准尚未完成，要么是因为存在某些特定于 Windows 8 功能且需要传达给 Windows 应用商店应用的属性。你可以在清单中看到这两种情况。Visual Studio 创建的文件非常简单，我添加的内容以粗体显示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章 ■ 入门指南**

***清单 1-4.*** `default.css` 文件的内容

```
body {
    background-color: #4A8C43;
}

#contentGrid {
    display: -ms-grid;
    -ms-grid-rows: 1fr 1fr;
    -ms-grid-columns: 60% 60%;
    height: 100%;
    overflow: scroll;
}

#contentGrid div.gridLeft {
    margin-left: 1em;
    margin-right: 1em;
}

#contentGrid div.gridRight {
    margin-right: 1em;
}

#leftContainer {
    -ms-grid-column: 1;
    -ms-grid-row: 1;
    -ms-grid-row-span: 2;
}

#topRightContainer {
    -ms-grid-column: 2;
    -ms-grid-row: 1;
}

#bottomRightContainer {
    -ms-grid-column: 2;
    -ms-grid-row: 2;
}

@media screen and (-ms-view-state: fullscreen-landscape) {
}

@media screen and (-ms-view-state: filled) {
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章 ■ 入门指南**

```
@media screen and (-ms-view-state: snapped) {
}

@media screen and (-ms-view-state: fullscreen-portrait) {
}
```

`@media` 规则的工作方式与常规媒体查询类似，但其所响应的属性是 Windows 8 特有的，并代表了不同的方向和使用场景（我将在第 4 章中解释和演示）。

**提示** Visual Studio 会缩进 CSS 样式以创建视觉层次。出于某些原因，这让我抓狂，因此我已在本节的所有清单中禁用了此功能。你可以通过选择 **工具 ➤ 选项**，导航到 **文本编辑器 ➤ CSS ➤ 格式设置**，然后取消选中“层次结构缩进”选项来更改 Visual Studio 显示 CSS 的方式。

通过我的补充，我为应用定义了一个背景色，这遵循了 Windows 8 中明显的柔和色彩趋势。我做的其他补充是将 CSS3 网格布局应用于我在 `default.html` 中定义的 `div` 元素。你可以在 Windows 8 应用中使用任何新的 CSS3 布局模型（实际上，任何 CSS 布局都可以），但网格布局的规范尚未最终确定，因此我必须在布局属性前加上 `-ms` 前缀。

**CSS3 网格布局快速介绍**

你可能从未使用过网格布局，因为它并未在主流 Web 浏览器中一致地实现。幸运的是，在开发 Windows 8 Web 应用时，你只需要担心 Internet Explorer 10，它用于向用户显示 JavaScript Windows 应用商店应用。在此边栏中，我将向你快速介绍 CSS3 网格布局的基本特性。要开始使用网格布局，你必须设置 `display` 属性，并为将包含网格的元素指定行数和列数，如下所示：

```
#contentGrid {
    display: -ms-grid;
    -ms-grid-rows: 1fr 1fr;
    -ms-grid-columns: 60% 60%;
}
```

`display` 属性必须设置为 `-ms-grid`。`-ms-grid-rows` 和 `-ms-grid-columns` 属性指定网格的尺寸。这些尺寸可以指定为分数单位（表示为 `fr`）、可用空间的百分比或固定尺寸。我指定了两个等高的行和两列，列的宽度设置为父元素宽度的 60%。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章 ■ 入门指南**

在 Windows 应用商店应用中（或者至少在目前开发的那些应用中很常见），通常会提供一个比屏幕更宽的内容区域，并允许用户从左向右滚动以访问应用的不同区域。将累计宽度设置为 120% 即可实现此行为，你将在本章稍后运行示例 Web 应用时看到这一点。

对于单个项目，你需要指定它们应显示在哪一行和哪一列，如下所示：

```
#leftContainer {
    -ms-grid-column: 1;
    -ms-grid-row: 1;
    -ms-grid-row-span: 2;
}
```

`-ms-grid-column` 和 `-ms-grid-row` 属性在网格中定位元素。这两个属性都是基于 1 的，这意味着将元素定位在第 1 列和第 1 行将把它放置在网格的左上角位置。默认情况下，元素占据一个网格方块，但你可以使用 `-ms-grid-row-span` 和 `-ms-grid-column-span` 属性来更改这一点。在示例中，我让 `leftContainer` 元素跨越了两行。另一个值得关注的属性是 `-ms-grid-column-align`，我在示例中没有使用它。该属性指定元素在网格方块内的对齐方式，可以设置为 `start`、`end`、`center` 或 `stretch`。如果你使用的是从左到右的语言（如英语），那么 `start` 和 `end` 值会分别将元素左对齐和右对齐。`center` 值将元素居中，而 `stretch` 值则会调整元素大小，使其完全填满其分配的空间。

你可以使用网格属性创建一些非常复杂的布局。有关详细信息，请参阅 [www.w3.org/TR/css3-grid](http://www.w3.org/TR/css3-grid) 上的完整规范，但请注意，这还不是一个已批准的标准。

## 探索 `default.js` 文件

Visual Studio 创建的最后一个重要文件是 `default.js`，它在 `default.html` 中使用标准的 `script` 元素引用。你可以在清单 1-5 中看到此文件的内容。除了格式化代码使其适合页面外，我没有对此文件做任何更改。

***清单 1-5.*** `default.js` 文件的内容

```
// For an introduction to the Blank template, see the following documentation:
//
// http://go.microsoft.com/fwlink/?LinkId=232509
(function () {
    "use strict";
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章 ■ 入门指南**

```
    WinJS.Binding.optimizeBindingReferences = true;

    var app = WinJS.Application;
    var activation = Windows.ApplicationModel.Activation;
    app.onactivated = function (args) {
        if (args.detail.kind === activation.ActivationKind.launch) {
            if (args.detail.previousExecutionState !==
                activation.ApplicationExecutionState.terminated) {
                // TODO: This application has been newly launched. Initialize
                // your application here.
            } else {
                // TODO: This application has been reactivated from suspension.
                // Restore application state here.
            }
            args.setPromise(WinJS.UI.processAll());
        }
    };

    app.oncheckpoint = function (args) {
        // TODO: This application is about to be suspended. Save any state
        // that needs to persist across suspensions here. You might use the
        // WinJS.Application.sessionState object, which is automatically
        // saved and restored across suspension. If you need to complete an
        // asynchronous operation before your application is suspended, call
        // args.setPromise().
    };

    app.start();
})();
```



