# Markdown 排版后的内容

**ip** 脚本元素的顺序在 Windows Store 应用和 Web 应用中一样重要。我 `default.js` 文件中的代码依赖我的视图模型，因此必须确保 `viewmodel.js` 的脚本元素位于 `default.js` 之前。

声明式绑定包含两部分。第一部分是 `HTMLElement` 对象定义的属性名称，该对象代表文档对象模型（DOM）中的元素。我使用了 `innerText`，它将设置 `span` 元素的文本内容。

属性名称后跟冒号（`:`），然后是应分配给该属性的数据项名称。我指定了 `UserData.homeZipCode`。

**T**

**ip** 如果你指定任何也是 JavaScript 保留关键字的属性名称，声明式绑定会静默失败。这意味着，例如，你应该避免在绑定中使用 `class` 属性，而改用 `className` 属性。

仅仅向 HTML 元素添加 `data-win-binding` 属性是不够的。我还必须告诉 WinJS API 搜索文档，以便找到并处理绑定属性。清单 2-3 展示了 `default.js` 文件。我删除了 Visual Studio 创建的一些注释，并定义了一些稍后将构建的占位函数。

***清单 2-3.*** 处理 WinJS 绑定

```
(function () {

"use strict";

var app = WinJS.Application;

app.onactivated = function (eventObject) {

if (eventObject.detail.kind ===

Windows.ApplicationModel.Activation.ActivationKind.launch) {

if (eventObject.detail.previousExecutionState !==

Windows.ApplicationModel.Activation

.ApplicationExecutionState.terminated) {

performInitialSetup(eventObject);

} else {

performRestore(eventObject);

}

WinJS.UI.processAll();

}

};
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/index-44_1.jpg)

第二章 ■ 数据和绑定

```
app.oncheckpoint = function (eventObject) {

performSuspend(eventObject);

};

app.start();

function performInitialSetup(e) {

WinJS.Binding.processAll(document.body, ViewModel);

}

function performRestore(e) {

// TODO

}

function performSuspend(e) {

// TODO

}

})();
```

重要的变化是对 `WinJS.Binding.processAll` 方法的调用。参数是处理应开始的元素以及声明式绑定的数据值来源。通过指定 `document.body` 元素，我告诉 WinJS 处理整个文档。第二个参数告诉 WinJS 使用 `ViewModel` 对象作为数据源。

声明式数据绑定是相对于数据源的，这就是为什么我的示例中的绑定引用 `UserData.homeZipCode` 而不是 `ViewModel.UserData.homeZipCode`：

```
<span data-win-bind="innerText: UserData.homeZipCode"></span>
```

这些更改的结果是 `span` 元素的内容被设置为 `homeZipCode` 属性的值，如图 2-1 所示。

***图 2-1.*** 使用声明式绑定显示视图模型值

### 创建动态绑定

我在上一个示例中使用的绑定是*静态*的，意味着视图模型属性的值与包含绑定声明的 `span` 元素的值之间没有持续的关系。静态绑定就像视图模型值的快照。一旦快照被拍摄，标记中的值就固定了，即使属性的值发生更改。

*动态绑定*（属性更改*确实*会导致元素更新）对于大多数应用更为有用。在 WinJS 中，当声明式绑定所依赖的数据属性是*可观察的*时，它们会自动变为动态。要创建可观察属性，我必须在视图模型中使用 `WinJS.Binding.as` 方法，如清单 2-4 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第二章 ■ 数据和绑定

***清单 2-4.*** 在视图模型中创建可观察项

```
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" />
(function () {

"use strict";

WinJS.Namespace.define("ViewModel", {
```



`UserData: WinJS.Binding.as({`

```
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

})
```

`ViewModel.UserData.homeZipCode = "NY 10118";` `ViewModel.UserData.addStore("Whole Foods");` `ViewModel.UserData.addStore("Kroger");`

`ViewModel.UserData.addStore("Costco");`

`ViewModel.UserData.addStore("Walmart");`

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 2 章 ■ 数据与绑定

`ViewModel.UserData.addItem("Apples", 4, "Whole Foods");` `ViewModel.UserData.addItem("Hotdogs", 12, "Costco");` `ViewModel.UserData.addItem("Soda", "4 pack", "Costco");`

`})();`

这里的变化虽然微妙但却很重要。`WinJS.Binding.as` 接收一个对象作为参数，并返回一个其*简单属性*可观察的对象。简单属性是指值为原始类型的属性，比如数字或字符串。其值为对象、数组或函数的属性不属于简单属性，不会通过 `as` 方法变为可观察。

`WinJS.Binding.as` 方法会用 getter 和 setter 替换数据属性，当属性值变化时会触发通知。指向对象、数组或函数的属性会保持原样，不会被 `as` 方法改变。（本章后面我会解释如何处理数组。）

你需要对*对象*调用 `as` 方法，而不是对属性或简单值调用。如果直接在属性上调用 `WinJS.Binding.as`，你只会得到该属性的值，而任何使用该属性的绑定都不会自动更新：

```
// 这种方式不会更新

var myObject = {

myObservableValue: WinJS.Binding.as("MyInitialValue")

};

// 这种方式会更新

var myOtherObject = WinJS.Binding.as({

myObservableValue: "MyInitialValue"

});
```

当你尝试用第一种方式创建可观察值时，不会报错。`WinJS` 只会默默地忽略该请求，从而得到一个静态绑定。采用第二种方式，你就能创建出值变化时自动更新的绑定。

## 将命名空间与可观察项结合使用

有时，`WinJS` API 会给人这样一种印象：不同开发团队原本可以更细致地协调他们的工作。一个例子就是，当尝试在使用 `WinJS.Namespace.define` 方法全局导出的对象上创建可观察数据项时，就会出现这种情况。

`WinJS.Binding.as` 方法在处理简单属性时，会向对象中添加若干私有成员，并遵循通用约定，在这些成员名称前加上下划线前缀。但正如我所解释的，`WinJS.Namespace.define` 方法不会将这些成员全局导出。为绕过这一冲突，我调整了创建 `ViewModel.UserData` 对象的方式，如清单 2-5 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 2 章 ■ 数据与绑定

***清单 2-5.*** 调整全局对象的结构以导出私有成员

```
. . .

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

//

*. . . UserData 对象的成员写在这里 . . .*

})

});

. . .
```

`define` 方法不会移除它所导出对象内部属性的私有成员，因此我可以通过将 `UserData` 对象指定为 `ViewModel` 对象的一个属性，来导出它的私有成员。

## 更新可观察数据项

可观察数据项的更新只有一个方向：从视图模型流向绑定。要使数据更新流向另一个方向（从元素流向视图模型），你必须使用常规的 JavaScript DOM API 技术。清单 2-6 展示了在 `default.html` 的标记中添加输入元素和按钮元素，用于更新 `homeZipCode` 属性。

***清单 2-6.*** 捕获将用于更新视图模型的数据

```
<!DOCTYPE html>

<html>

<head>
```



```html
<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS 引用 -->
<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">
<script src="//Microsoft.WinJS.1.0/js/base.js"></script>
<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer 引用 -->
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

        [www.it-ebooks.info](http://www.it-ebooks.info/)

        ## 第 2 章 ■ 数据与绑定

        <div class="win-type-x-large">
            <label for="newZip">输入新邮政编码：</label>
            <input id="newZip" data-win-bind="value: UserData.homeZipCode"/>
            <button id="newZipButton">更新邮政编码</button>
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

从 input 元素获取值并更新视图模型的值无需任何 Windows 8 专属技术。清单 2-7 展示了 `default.js` 文件中的改动，这些改动响应 button 元素的 click 事件，并通过 input 元素 DOM 对象的 `value` 属性更新视图模型。

**清单 2-7.** 响应变更事件更新视图模型

```
. . .
function performInitialSetup(e) {
    WinJS.Binding.processAll(document.body, ViewModel);
    WinJS.Utilities.query('#newZipButton').listen("click", function (e) {
        ViewModel.UserData.homeZipCode =
            WinJS.Utilities.query('#newZip')[0].value;
    });
}
. . .
```

我在编写这一补充功能时利用了 `WinJS.Utilities` 命名空间，该命名空间包含 jQuery 等实用程序库中的部分功能。其 API 与 jQuery 大体相同，但查询元素时不是使用 `$` 快捷函数，而是通过 `WinJS.Utilities.query` 方法完成。虽然并未提供 jQuery 的全部功能，但你可以使用 `WinJS.Utilities` 命名空间来定位元素、应用 CSS 样式和类，以及设置事件处理程序。

在此清单中，我使用 `query` 方法在 HTML 文档中搜索 `newZipButton` 元素，并对返回结果调用 `listen` 方法为 `click` 事件设置处理程序。当按钮被点击时，我定位 input 元素，从该对象中读取 `value` 属性，并将其赋值给视图模型中的 `homeZipCode` 属性。

`query` 方法返回的是一个元素数组。由于没有与 jQuery 的 `val` 方法等价的函数，因此我将返回结果视为数组，获取代表与选择器匹配的第一个元素的 `HTMLElement` 对象，然后读取其 `value` 属性。实现相同结果的另一种方法是将元素的 `id` 属性值作为全局变量进行定位。在常规 Web 应用开发中通常要避免使用此功能，因为它在不同浏览器中的实现并不一致；但在开发 Windows 应用商店应用时，可以安全地依赖 Internet Explorer 10 实现的功能，因为你的应用始终会使用该浏览器引擎来运行。你可以看到我在清单 2-8 中如何使用此功能来简化 click 事件处理程序的代码设置。当然，我无法访问 `WinJS.Utilities` 命名空间中的 `listen` 方法，因此我使用了标准 DOM API 中的 `addEventListener` 方法。

**清单 2-8.** 将元素的 `id` 属性视为全局变量来定位元素

```
. . .
function performInitialSetup(e) {
    WinJS.Binding.processAll(document.body, ViewModel);
    newZipButton.addEventListener("click", function (e) {
        ViewModel.UserData.homeZipCode = newZip.value;
```
```



