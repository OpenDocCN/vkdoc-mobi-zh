# 第 2 章

## 数据与绑定

在本章中，我将向你展示如何定义和使用构成 Windows 8 应用程序核心的数据。为此，我将松散地遵循*视图模型*模式，该模式允许我将数据与用于向用户呈现数据的 HTML 清晰地分离开来。这使得应用程序更易于编写、测试和维护。

你可能已经从模型-视图-控制器（MVC）和模型-视图-视图控制器（MVVC）等设计模式中熟悉了模型和视图模型。

我不打算在本书中深入探讨这些模式的细节。关于 MVC 和 MVVC 有很多优秀的信息可供参考，从维基百科开始就有一些非常平衡且富有洞察力的描述。

我发现使用视图模型的好处巨大，对于所有但最简单的 Windows 8 项目而言，都非常值得考虑，我建议你认真考虑遵循同样的路线。我并非模式狂热者，我坚信应该采纳那些能解决实际问题的模式和技术的部分，并使其适应特定项目。因此，你会发现我对视图模型的使用方式持有相当宽松的看法。

本章主要关注 Windows 应用商店应用程序后台的底层机制。我会从简单开始，向你展示将 JavaScript 代码添加到 Windows 8 项目的约定，以及如何使用 Windows 8 的 JavaScript 特性来减少全局命名空间污染。接着，我会定义一个简单的视图模型，并演示将数据从视图模型带入应用程序显示的不同技术。本章旨在为 Windows 应用商店应用程序打下坚实基础，并让你掌握核心的 Windows 8 JavaScript 功能。表 2-1 提供了本章摘要。

***表 2-1.** 本章摘要*

| 问题 | 解决方案 | 清单 |
| --- | --- | --- |
| 创建一个视图模型。 | 使用 `WinJS.Namespace.define` 方法创建一个包含应用程序数据的全局对象。 | |
| 显示视图模型中的值。 | 使用 `data-win-bind` 属性创建声明式绑定，并调用 `WinJS.Binding.processAll` 方法进行处理。 | 2, 3 |

（*续*）

[www.it-ebooks.info](http://www.it-ebooks.info/)

***表 2-1.***（*续*）

| 问题 | 解决方案 | 清单 |
| --- | --- | --- |
| 创建动态绑定。 | 使用 `WinJS.Binding.as` 方法创建一个可观察的数据属性。为该属性分配新值以触发 HTML 中的更新。 | 4, 6–8 |
| 在全局可用的命名空间对象中创建可观察的数据属性。 | 确保具有可观察属性的对象不是由 `WinJS.Namespace.define` 方法直接导出的。 | |
| 创建可观察的数组。 | 使用 `WinJS.Binding.List` 对象。 | 9–11 |
| 从可观察的数组生成 HTML 元素。 | 使用 WinJS 模板功能。 | 12–17 |

## 创建 JavaScript 文件

第一步是创建一个新的 JavaScript 文件。与 Web 应用程序不同，没有必要减少 Windows 应用商店应用程序中 JavaScript 文件的数量，因为当应用程序启动时，它们已经驻留在用户的计算机上。这意味着我不需要合并文件，也不需要考虑最小化代码以减小脚本文件的大小。

相反，我可以为每个主要的应用程序功能创建独立的文件。

在解决方案资源管理器窗口中右键点击`js`文件夹，从菜单中选择添加 ➤ 新建项来添加一个新文件。从列表中选择 JavaScript 文件，将名称设置为`viewmodel.js`，然后点击添加按钮创建该文件。Visual Studio 将在项目中添加一个空的新文件并打开它以供编辑。添加清单 2-1 中所示的语句。

***清单 2-1.** 视图模型类的初始内容*

```javascript
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" />
(function () {

    "use strict";

    WinJS.Namespace.define("ViewModel.UserData", {

        // 私有成员

        _shoppingItems: [],

        _preferredStores: [],

        // 公有成员

        homeZipCode: null,

        getStores: function () {

            return this._preferredStores;

        },

        addStore: function (newStore) {

            this._preferredStores.push(newStore);

        },

        getItems: function () {

            return this._shoppingItems;

        },

        addItem: function (newName, newQuantity, newStore) {

            this._shoppingItems.push({

                item: newName,

                quantity: newQuantity,

                store: newStore

            });

        }

    });

    ViewModel.UserData.homeZipCode = "NY 10118";
    ViewModel.UserData.addStore("Whole Foods");
    ViewModel.UserData.addStore("Kroger");

    ViewModel.UserData.addStore("Costco");

    ViewModel.UserData.addStore("Walmart");

    ViewModel.UserData.addItem("Apples", 4, "Whole Foods");
    ViewModel.UserData.addItem("Hotdogs", 12, "Costco");
    ViewModel.UserData.addItem("Soda", "4 pack", "Costco");

})();
```

我稍后会回到视图模型，但首先我需要解释代码的其他部分及其代表的约定。对于后续的 JavaScript 文件我不会重复这样做，但在你开始 Windows 8 开发时，提供一些有用的背景信息是很有必要的。

## 使用代码补全

Visual Studio 在编辑器中支持 JavaScript 代码补全，这使得编写代码更简单且不易出错。你必须使用引用元素将 JavaScript 文件引入代码补全的作用域，对于不在本地目录中的文件，如下所示：

```javascript
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" />
```

在引用元素前加上三个斜杠（`/`）字符会将引用引起 Visual Studio 的注意，并使 JavaScript 运行时将此语句视为注释。完成这些添加后，`viewmodel.js`文件就获得了对 WinJS API 的代码补全支持。如果你希望获得 WinJS 的代码补全，需要将这些引用元素添加到你的每个 JavaScript 文件中。

## 减少全局命名空间污染

JavaScript 中最严重的问题之一是变量名冲突。在函数外部定义的 JavaScript 变量是全局可用的，而由于有意义的变量名数量有限，不同代码区域尝试为不同目的使用相同变量名只是时间问题。全局变量被认为是*全局命名空间*的一部分，定义全局变量通常被称为*污染全局命名空间*。Windows 8 JavaScript 文件遵循三种不同的约定来减少命名空间污染，你都应该采用。

### 创建命名空间

WinJS API 通过`Namespace`对象的`define`方法帮助减少全局命名空间污染：

```javascript
WinJS.Namespace.define("ViewModel.UserData", {

//

. . .

*此处定义了 `ViewModel.UserData` 对象的成员*

. . .

});
```


`define`方法的第一个参数是你要分配给对象的全局名称。在本例中，我指定了`ViewModel.UserData`，这创建了一个名为`ViewModel`的全局变量，该变量具有`UserData`属性。`UserData`属性的值是我作为第二个参数传递给`define`方法的对象，这实际上导出了该对象的成员，使它们可以通过`ViewModel`全局访问。

`UserData`。稍后我将数据应用到标记时，你会看到它的工作方式。

使用`define`方法有两个原因，而不是自己处理。首先，`ViewModel`对象仅在尚不存在时才会被创建。

这意味着我可以通过多次调用`define`方法来轻松构建视图模型的能力。其理念是，我可以在单个全局命名空间对象下关联复杂的对象和函数，并减少变量名冲突的可能性。

**提示** 有一种更复杂的方法来处理这个问题，称为异步模块定义（AMD）。AMD 技术通过允许 JavaScript 文件的消费者选择访问 JavaScript 库的变量名称，从而有效消除了全局变量名冲突。Windows 8 不直接支持 AMD 模块，但你可以使用支持 AMD 的脚本加载器，例如`require.js`。

使用`define`方法的第二个原因是，它不会导出任何以下划线字符开头的属性，这是定义私有成员的常见 JavaScript 约定。这意味着当我导出`UserData`对象时，`_shoppingItems`和`_preferredStores`属性不会全局可用。

这是一个很好的技巧，它意味着全局对象的私有实现细节保持私有，但正如你将了解到的，它确实会与其他 WinJS 功能引起一些轻微的问题。

### 使用自执行函数

第二个约定是在 JavaScript 文件中使用自执行函数。自执行函数的基本形式如下：
```
(function() {
    // ... statements go here ...
})();
```
将函数包裹在圆括号中，然后立即添加另一对圆括号，会导致函数被定义并立即执行。

你在函数内部定义的任何变量都限定在函数本身的作用域内，并且不会污染全局命名空间。当函数执行完成后，JavaScript 运行时会自动清理任何未全局导出的变量。

### 使用严格模式

`use strict`语句对使用 JavaScript 的方式施加了一些约束。

其中一个约束是，在函数中隐式创建全局变量会变成错误。当你没有使用`var`关键字时，会隐式创建全局变量：
```
var color1 = "blue"; // 正确 - 作用域限定在函数内
color2 = "red"; // 错误 - 这是一个全局变量
```
如果你定义了一个隐式全局变量，JavaScript 运行时会生成一个错误。使用严格模式完全是可选的，但这是一个好的实践，它会禁用一些更危险和奇怪的 JavaScript 行为。你可以通过阅读 ECMAScript 语言规范附录 C（位于`www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf`）来获取严格模式强制执行变更的完整详细信息。

### 回到视图模型

现在我已经解释了 Windows 8 JavaScript 文件的上下文和约定，我可以转向视图模型的定义。示例 Windows 8 应用程序的视图模型将很简单，这部分由`ViewModel.UserData`对象表示，将包含用户特定的数据：用户的家庭邮政编码、购物清单以及他们购买杂货的商店。

我定义了两个数组，用于保存购物清单上项目的详细信息以及用户偏好的商店：`_shoppingItems`和`_preferredStores`。这些属性不作为`ViewModel.UserData`对象的一部分导出，因为它们以下划线开头。为了提供对数据的访问，我定义了一组函数，这些函数返回数组数据并接受要添加到数组中的新项目。`homeZipCode`属性是公开的，并构成全局可用的`ViewModel.UserData`对象的一部分。你可以直接读取和更改此属性的值。

**注意**  `UserData`对象的形状和结构有点奇怪，因为我想展示 WinJS API 的不同方面。在实际项目中，你会以更一致的方式处理数据项。

为了在应用程序中有一些数据可供使用，我在自执行函数中添加了一些语句，用于定义邮政编码、添加一些商店，并在购物清单上放置一些简单的项目：
```
ViewModel.UserData.homeZipCode = "NY 10118";
ViewModel.UserData.addStore("Whole Foods");
ViewModel.UserData.addStore("Kroger");
ViewModel.UserData.addStore("Costco");
ViewModel.UserData.addStore("Walmart");
ViewModel.UserData.addItem("Apples", 4, "Whole Foods");
ViewModel.UserData.addItem("Hotdogs", 12, "Costco");
ViewModel.UserData.addItem("Soda", "4 pack", "Costco");
```
## 使用数据绑定

数据绑定是一种机制，通过它你可以将视图模型中的数据包含在向用户显示的 HTML 中。WinJS API 通过`WinJS.Binding`命名空间支持绑定。数据绑定是在 Windows 应用商店应用中使用视图模型的关键。如果你想构建可扩展且稳健的 Windows 8 应用程序，我建议投入时间学习如何充分利用 WinJS 绑定功能。

**提示** WinJS 数据绑定不如一些更广泛使用的 Web 应用 JavaScript 库（如`Knockout.js`、`Backbone`和`Angular.js`）那样完整或灵活。你可以轻松地在 Windows 应用商店应用中使用这些库，但我建议花时间理解 WinJS 功能，因为它已集成到 WinJS API 中包含的一些 UI 控件中。

### 使用基本声明式绑定

使用绑定的最简单方式是声明式地，这意味着你直接在 HTML 标记中包含视图模型数据的详细信息。清单 2-2 展示了如何在`default.html`中绑定到`homeZipCode`值。

**清单 2-2.** 一个简单的声明式绑定

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>MetroGrocer</title>
    <!-- WinJS references -->
    <link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">
    <script src="//Microsoft.WinJS.1.0/js/base.js"></script>
    <script src="//Microsoft.WinJS.1.0/js/ui.js"></script>
    <!-- MetroGrocer references -->
    <link href="/css/default.css" rel="stylesheet">
    <script src="/js/viewmodel.js"></script>
    <script src="/js/default.js"></script>
</head>
<body>
    <div id="contentGrid">
        <div id="leftContainer" class="gridLeft">
            <h1 class="win-type-xx-large">左侧完整容器</h1>
            <div class="win-type-x-large">
                邮政编码是：
                <span data-win-bind="innerText: UserData.homeZipCode"></span>
            </div>
        </div>
        <div id="topRightContainer" class="gridRight">
            <h1 class="win-type-xx-large">右上容器</h1>
        </div>
        <div id="bottomRightContainer" class="gridRight">
            <h1 class="win-type-xx-large">右下容器</h1>
        </div>
    </div>
</body>
</html>
```

我添加了一个`script`元素，将`viewmodel.js`引入 HTML 文档。然而，最重要的添加是`span`元素及其`data-win-bind`属性。



