# 文档格式

本文档中有两个新的表格元素。第一个表格的 `id` 属性为 `listTable`，用户将看到它。这个表格有一个包含列标题的标题行，但 `tbody` 元素是空的。我将在此处使用模板为购物清单中的每个项目插入一行。

第二个表格元素包含模板。模板本身由 `tbody` 元素定义，但 WinJS 模板的一个特殊之处在于它们需要位于格式良好的 HTML 中。例如，你不能直接将 `tbody` 元素定义为 `body` 的子元素，因为 `tbody` 元素不允许成为 `body` 元素的子元素。这意味着在使用模板时，HTML 文档中最终会包含一些多余的元素。

## 使用模板

模板通过 `data-win-control` 属性值 `WinJS.Binding.Template` 来标识。这会告诉 WinJS 处理该元素，查找声明性绑定，并为 DOM 中代表该模板元素的 `HTMLElement` 对象添加一些特殊属性。

如前所述，我喜欢将项目拆分，以便每个功能区域都有对应的 JavaScript 文件。为此，我创建了一个名为 `ui.js` 的新 JavaScript 文件，如列表 2-13 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 2 章 ■ 数据与绑定**

***列表 2-13.** * 在 `ui.js` 文件中使用模板

```
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("UI.List", {

displayListItems: function () {

WinJS.Utilities.empty(itemBody);

var list = ViewModel.UserData.getItems();

for (var i = 0; i < list.length; i++) {

itemTemplate.winControl.render(list.getAt(i), itemBody);

}

WinJS.Utilities.children(itemBody).listen("click", function (e) {

ViewModel.State.selectedItemIndex = this.rowIndex - 1; WinJS.Utilities.children(itemBody).removeClass("selected"); WinJS.Utilities.addClass(this, "selected");

});

},

setupListEvents: function () {

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

var itemsList = ViewModel.UserData.getItems();

eventTypes.forEach(function (type) {

itemsList.addEventListener(type, UI.List.displayListItems);

});

},

});

})();
```

我定义了一个 `UI` 命名空间，其中包含一个具有 `displayListItems` 和 `setupListEvents` 函数的 `List` 对象。在 `displayListItems` 函数中，我定位了代表模板和生成元素目标的 `HTMLElement` 对象。我使用 `document.getElementById` 方法通过它们的 `id` 属性值来定位这些元素。

`WinJS.Utilities.empty` 方法移除一个元素的子元素，我这样做是为了避免每次调用该函数时都只是向表格添加新行。

若要遍历 `WinJS.Binding.List` 对象中的项目，请对每个项目调用模板对象上的 `winControl.render` 方法。`winControl` 由 `WinJS.UI.processAll` 方法创建，它返回为不同类型的用户界面控件创建的 Windows 8 特定属性。

`render` 方法被添加到那些 `data-win-control` 属性为 `WinJS.Binding.Template` 的元素上。`render` 方法的参数是要处理的数据项以及新生成内容将要添加到的目标元素。因此，通过对我的购物 `List` 对象中的每个项目调用 `render` 方法，我能够创建表格行来填充我的 Windows Store 应用布局。

创建好表格行后，我使用 `WinJS.Utilities.children` 方法为新创建的 `tr` 元素设置一个 `click` 事件的事件监听器。

最后，我设置了 `ViewModel.State.selectedItemIndex` 属性（稍后将定义），以便在接收到 `click` 事件时，使用 `tr` 元素可用的 `rowIndex` 属性来指示哪一行被选中，并确保 `selected` 类仅应用于用户选中的行。

## 响应列表更改

我在 `UI` 命名空间中定义的另一个函数是 `setupListEvents`。此函数监听我在上一节中描述的每个列表事件，并在收到这些事件时调用 `displayListItems` 函数。

这样，每当列表内容发生变化时，我就可以使用模板重新渲染表格元素的内容。这是一种处理列表更改的粗糙方法，但对于一个简单的示例来说已经足够了。传递给处理程序函数的事件对象包含有关哪个列表元素已更改的详细信息，这是在实现更优雅的方法时必不可少的信息。

> **提示** 我将事件处理程序设置在一个单独的函数中，以便可以重复调用 `displayListItems`。如果我将事件处理程序设置在 `displayListItems` 函数内部，那么每次列表内容更改时都会创建一组新的处理程序。

## 跟踪选中的项

在 `displayListItems` 函数中，我更新了 `ViewModel.State.selectedItemIndex` 属性的值，以跟踪表格元素中的哪个项目已被选中。现在是时候定义这个属性了。列表 2-14 显示了对 `viewmodel.js` 文件的补充。

***列表 2-14.** * 定义 `selectedItemIndex` 属性

```
/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />
/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

var shoppingItemsList = new WinJS.Binding.List();

var preferredStoresList = new WinJS.Binding.List();

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

homeZipCode: null,

getStores: function () {

return preferredStoresList;

},

addStore: function (newStore) {

preferredStoresList.push(newStore);

},

getItems: function () {

return shoppingItemsList;

},

addItem: function (newName, newQuantity, newStore) {

shoppingItemsList.push({

item: newName,

quantity: newQuantity,

store: newStore

});

}

}),

State: WinJS.Binding.as({

selectedItemIndex: -1

})

});

//
// *. . . 为简洁起见，省略了测试数据 . . .*

})();
```

我使用 `State` 对象来区分应用当前状态所需的数据和用户创建的数据（由 `UserData` 对象表示）。

## 将模板应用于应用

在使用模板之前，必须确保已调用 `WinJS.UI.processAll` 方法。此方法处理 HTML 文档，查找具有 `data-win-control` 属性的元素并配置其功能。这包括查找和处理模板。

列表 2-15 显示了对 `default.js` 文件中定义的 `performInitialSetup` 函数所做的更改，其中我在调用 `WinJS.UI.processAll` 之后添加了对 `displayListItems` 和 `setupListEvents` 函数的调用（并且我从 `performInitialSetup` 函数中删除了前一个示例的代码）。

***列表 2-15.** * 确保在使用模板之前已处理元素

```
. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

});

}

. . .
```

`processAll` 方法在后台完成其工作，允许在调用 `processAll` 之后的 JavaScript 语句同时执行。使用后台任务是一个好主意，但由于我的 `displayListItems` 函数依赖于 `processAll` 创建的 `winControl` 属性，我需要确保在使用模板之前后台任务已经完成。

**理解 PROMISES**

Windows Store 应用依赖 JavaScript 的 `Promise` 模式来表示后台任务。`processAll` 方法的结果是一个 `WinJS.Promise` 对象，它是 JavaScript `Promise` 模式在 Windows 8 中的实现。



要使用`Promise`对象，我调用该对象的`then`方法，传入一个将在任务完成时执行的函数。在我的示例中，这个函数调用依赖于`processAll`方法已完成工作的`UI.List`命名空间。

`then`方法接受可选的第二和第三参数，它们定义在发生错误时执行的函数以及接收进度信息的函数，但本书中我假设所有后台任务都能正确完成。我建议你在自己的项目中花时间妥善处理任何错误。

这是 JavaScript Promise 最基本和常见的用法。查看`WinJS.Promise`对象的 API 定义，了解其他可用功能。

### 为模板元素设置样式

为了样式化模板生成的元素，我在`css`文件夹中添加了一个名为`list.css`的新文件。你可以在清单 2-16 中看到此文件的内容。

```
【list.css 内容】
#listTable {margin-top: 30px;border-collapse: collapse;}
#listTable th, #listTable td {padding-right: 20px;padding-left: 10px;}
#listTable th {font-size: 30pt; text-align: left;}
#listTable td {font-size: 26pt;padding-top: 5px;padding-bottom: 5px;}
#listTable tbody tr.selected {background-color: #85C54C;}
#listTable th.itemName, #listTable th.store {width: 40%;}
```

该文件包含标准 CSS，未使用任何特定于 Windows 应用商店应用开发的功能。

### 导入 JavaScript 和 CSS

应用模板的最后一步是将`/js/ui.js`和`/css/list.css`文件添加到`default.html`中，如清单 2-17 所示。

**清单 2-17.** 将`ui.js`文件添加到`default.html`

```html
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
<link href="/css/list.css" rel="stylesheet">
<script src="/js/viewmodel.js"></script>
<script src="/js/ui.js"></script>
<script src="/js/default.js"></script>
</head>
... 
</html>
```

### 测试模板

使用模板填充表格的结果如图 2-4 所示。请注意，即使模板在`default.html`文件中定义，你也看不到它本身。这是因为`WinJS.UI.processAll`方法将 CSS 的`display`属性设置为`none`，使得模板对用户不可见。

**图 2-4.** 使用模板和`List`对象生成表格行

## 总结

在本章中，我向你展示了如何在 Windows 8 应用中创建视图模型、如何使其全局可用，以及如何使数据项可观察。WinJS API 的当前状态使得这个过程有些繁琐，但回报是能够使用声明式绑定和模板，用应用数据填充你的 HTML。

---

