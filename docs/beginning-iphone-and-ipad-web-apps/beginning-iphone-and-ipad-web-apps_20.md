# 使用触摸和手势事件

用户有`500ms`的时间来链接两个手势，这在大多数情况下足够长。超过这个限制后，录制将自动停止，并且如果有监听器，它们将被删除。

```
Recognizer.prototype.endStroke = function() {
    this.element.removeEventListener("touchmove", this, false);
    this.element.removeEventListener("touchend", this, false);
}
```

一旦序列结束，将调用`finalizeStrokes()`方法来确定签名。

```
Recognizer.prototype.finalizeStrokes = function() {
    var signature = [];
    for (var m = 0; m < this.strokes.length; m++) {
        var bound = this.strokes[m].bound;
        var points = this.strokes[m].points;
        bound.fit();
        var w = (bound.extent.x - bound.coords.x + 1) / 3;
        var h = (bound.extent.y - bound.coords.y + 1) / 3;
        for (var n = 0; n < points.length - 1; n++) {
            var px = points[n].x - bound.coords.x;
            var py = points[n].y - bound.coords.y;
            var mx = px / w << 0; // 位移位运算符允许非常快速的 Math.floor()
            var my = py / h << 0;
            var value = my * 3 + mx + 1;
            if (value != signature[signature.length - 1]) {
                signature.push(value);
            }
        }
    }
    this.strokes = [];
    this.interpreter(signature.join(''), this.strokeSource);
    this.strokeSource = null;
}
```

每记录一个笔画，边界框会按需调整，并计算网格的尺寸。然后，调整点以标准化其坐标，并估计它们在网格上的位置。如果到达了新的网格块，则将其添加到签名中。最后，清空笔画数组，并使用最终签名作为参数启动解释器。

### 使用`Recognizer`对象

从这里开始，使用`Recognizer`对象非常简单，因为您只需要实例化该对象并为签名定义一个解释器。我们将使用一个列表来接收手指笔画，并相应地修改列表元素的文本样式。在此示例中，我们再次基于我们的 Web 应用程序模板构建。以下是需要修改`index.html`的内容：

```html
<body onload="init()">
    <div class="view" style="background: white">
        ...
        <div class="list-wrapper">
            <h2>Apress</h2>
            <ul>
                <li>Books</li>
                <li>Reviews</li>
                <li>Authors</li>
                <li>Contact</li>
            </ul>
        </div>
    </div>
</body>
```

然后，创建一个名为`strokes.js`的新 JavaScript 文件，其中包含`Recognizer`的代码以及以下内容：

```javascript
function interpreter(signature, source) {
    switch (signature) {
        case "456": // O--->
            sendEvent("SwipeRight", source);
            break;
        case "654": // <---O
            sendEvent("SwipeLeft", source);
            break;
        default:
            console.log("Unknown stroke received " + signature);
    }
}

function sendEvent(type, target) {
    var vendor = "book";
    var event = document.createEvent("Event");
    event.initEvent(vendor + type, true, false);
    target.dispatchEvent(event);
}
```

正如预期的那样，解释器接收签名以及笔触的来源。然后解释签名，并将事件发送到起源，将`initEvent()`的第二个参数设置为`true`以允许事件冒泡。接着，您只需要实例化该对象并准备监听器，其处理程序将修改列表元素的样式。

```javascript
function init() {
    var ul = document.getElementsByTagName("ul")[0];
    var rs = new Recognizer(ul, interpreter);

    /* 从右向左滑动事件时更改文本颜色 */
    ul.addEventListener("bookSwipeLeft", function(event) {
        var style = event.target.style;
        style.color = style.color ? "" : "red";
    }, false);

    /* 从左向右滑动事件时更改文本粗细 */
    ul.addEventListener("bookSwipeRight", function(event) {
        var style = event.target.style;
        style.fontWeight = style.fontWeight ? "" : "bold";
    }, false);
}
```

发送的新事件包括从右向左滑动和从左向右滑动。每个笔触将文本从黑色切换为红色，或从正常粗细切换为粗体，如图 13-6 所示。由于事件启用了冒泡，因此不必为每个列表元素添加独立的处理程序。事件将到达列表项并继续传播。



## 第 13 章：使用触摸和手势事件

向上传递到附加监听器的父元素。这使你可以集中处理事件。

**图 13-6.** 滑动操作改变文本样式

### 提高精确度

如你所见，实现新的手势事件相当简单。尽管如此，精确度很大程度上取决于用户绘制的笔画，特别是在处理对角线时可能会下降。对角线通常会跨越多列和多行，从而为单次笔画增加多种可能的路径。一种有趣的解决方式是在解释阶段使用正则表达式，以限制确定笔画所需进行的连续比较次数。这也会加速你的应用程序，因为正则表达式引擎是原生执行的，而不像自定义测试那样，随着笔画变得复杂而越来越复杂。

另一种方法是使用方向而非网格，尝试确定笔画的方向而不是定位点。这种方法被许多应用程序采用，例如 Firefox 的 FireGestures 扩展，并且已被证明非常精确和高效。

## 总结

在开发应用程序时，应充分考虑触摸在用户与设备之间建立的独特关系。正确使用和处理触摸及手势不仅可以极大地提升用户体验：只要稍加想象，你还能带来新功能，让你的应用程序在网页中脱颖而出。

这一切都需要你完全理解这些事件的工作原理，以及用户可能如何使用他们的设备并对应用程序的界面做出反应。要深入掌握触摸和手势事件，最好的方法就是亲自体验你自己的应用程序和实验，这一点比以往任何时候都更加重要。

## 第 14 章：位置感知的 Web 应用程序

App Store 提供了大量应用程序，它们根据用户在特定时刻的位置来提供服务，例如 uLocate 推出的多位置服务聚合器 Where，以及为你提供探索城市新方式的 Foursquare。

通过将来自不断增长的空间相关数据库的信息与用户的近似位置相结合，你可以推荐享用自制辣味肉酱面的完美场所；通过营造出你真正了解用户的印象来自定义用户体验；提供下午的相关天气信息；或显示带有前往任何地址的导航地图。自首次发布以来，iPhone 就具备了在世界地图上定位用户的功能。尽管它最初并未配备辅助全球定位系统（A-GPS）芯片，但它通过混合算法来确定位置：融合了三角定位法（通过手机基站，这是手机中广泛使用的方法）的数据，并使用通过 Wi-Fi 定位系统（WPS）从网络收集的信息，从而在合理的时间内返回位置信息。

随着基站数量的增加，三角定位法变得更加高效，但其精度从未优于 100 米；从网络信息推断位置可能非常精确和快速，但仍然依赖于网络密度。

这种解决方案在大多数情况下都不错，但仍有改进空间。

> **注意：** 在所有设备上进行地理定位都会严重消耗电池电量，因此务必牢记这一点，并且仅在最终用户需要时才使用这些功能。例如，最好缓存数据，而不是每次加载页面都请求新位置。

此外，所采用的系统使得此功能仅对使用 `Core Location` API 的原生 iPhone 应用程序可用。iOS 3.0 带来了令人兴奋的可能性，即可以通过 Mobile Safari 中新增的 API，直接使用 JavaScript 访问地理数据。本章将指导你使用这个新提供的强大工具。

### 地理定位 API

此 API 的实现基于 W3C 规范。它允许你使用前面提到的来源来确定宿主设备的地理位置，并且非常易于上手。所有 API 方法都可通过 `navigator.geolocation` 对象访问，它们让你可以轻松地请求用户的位置，无论是单次请求还是重复请求以跟踪用户的移动（如果有的话）。

#### 隐私注意事项

在用户不知情的情况下授予对此类信息的访问权限，应始终进行一定程度的控制。为了尊重最终用户的隐私，iOS 在发送任何关于其位置的信息之前，总会请求用户许可，就像地图和相机应用程序那样（见图 14-1）。

**图 14-1.** 所有 iPhone 应用程序在发送位置数据前都会请求用户授权

#### 设置注意事项

在设备上测试 API 之前，请确保定位服务已激活。你可以在“设置”应用程序的“通用”子菜单中找到它。更清晰的做法是重置之前授予的地理定位请求授权，例如在一个全新的状态下工作。这可以通过导航到 `设置` > `通用` > `还原` > `还原定位服务` 来实现。

此外，在桌面浏览器上调试时，不要忘记检查 API 的实际可用性——这将节省你调试根本无法解释的代码的宝贵时间。这可以通过以下检查 `Geolocation` 对象的代码简单实现：

```javascript
if (window.navigator.geolocation) {
    /* API 可用 */
} else {
    /* 后备提示信息 */
}
```

同样，针对 Mobile Safari，并非所有用户都拥有最新版本的浏览器——尤其是在旧版本的 iPod touch 上，相关更新可能需要付费购买或根本无法获得。一般来说，这样做能让你的应用程序在更好的条件下为更多用户所用，而预先规划错误处理将有助于你提供更好的用户体验。

#### 获取当前位置

该 API 最显而易见且可能是最常用的用途是获取用户的当前位置。如前所述，你将使用 `Geolocation` 对象，并调用 `getCurrentPosition()` 方法。

```javascript
window.navigator.geolocation.getCurrentPosition(
    function(position) {
        /* 使用位置对象数据执行操作 */
    }
);
```

由于每个请求都需要一些时间，这可能会迅速冻结用户的设备，因此所有请求都是异步的。你需要传递一个回调函数作为参数，以便在数据可用时调用，如前所示。

#### 经度、纬度等

当请求成功时，会使用 `position` 参数调用回调函数，该参数包含有关用户位置的数据。此对象提供了八个属性，如表 14-1 所示。

**表 14-1.** `Position` 对象的属性

| 属性 | 描述 |
|----------|-------------|
| `position.timestamp` | 确定位置信息的时间（毫秒）。 |
| `position.coords.accuracy` | 返回的纬度/经度信息的精度（米；数值越低越好）。 |
| `position.coords.latitude` | 用户当前的纬度（十进制度数）。 |
| `position.coords.longitude` | 用户当前的经度（十进制度数）。 |
| `position.coords.altitudeAccuracy` | 返回的海拔信息的精度（米）。此值通常为 null。 |
| `position.coords.altitude` | 用户当前的海拔（米）。存在相同限制。 |
| `position.coords.heading` | 用户前进的方向（十进制度数）。此值通常为 null。 |
| `position.coords.speed` | 用户当前的速度。存在相同限制（米/秒）。 |



