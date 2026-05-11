# 事件处理与用户输入

### 常规 DOM 事件监听器

常规 DOM 事件（如 `touchstart` 或 `mousedown`）的 `listener`，会将浏览器事件转换为自定义的 `down` 事件并立即触发。同时还会保存交互起始点的坐标，后续需要这些坐标来检查用户是否超过了移动阈值。

## 第 5 章：事件处理与用户输入

### 清单 5-15. `_onDownDomEvent`：将“交互起始”事件转换为自定义格式

```javascript
/**
 * 监听“down”类型的 DOM 事件：mousedown 和 touchstart。
 * @param e DOM 事件
 */
_p._onDownDomEvent = function(e) {
    // 必须保存这些坐标以支持 moveThreshold——这
    // 可能是移动的起始点，不能简单地
    var coords = this._lastMoveCoordinates = this._getInputCoordinates(e);

    // 触发“down”事件——所有坐标以及
    // 原始 DOM 事件都会传递给监听器
    this.emit("down", {x: coords.x, y: coords.y, domEvent: e});

    // 通常我们希望阻止浏览器对原始 DOM 事件的进一步处理。
    this._stopEventIfRequired(e);
};
```

接下来是清单 5-16 所示的 `_onUpDomeEvent()` 函数。它稍微复杂一些。顾名思义，它处理移动事件：`touchmove` 和 `mousemove`。`_onUpDomeEvent` 的主要任务是跟踪位移增量和阈值。如果从 `down` 事件到当前点的距离超过阈值，则这不是微移动。一旦突破阈值，我们就不再需要检查它。`_moving` 标志会在移动结束后重置。

### 清单 5-16. `_onMoveDomEvent` 跟踪移动并检查阈值是否被突破

```javascript
/**
 * 监听“move”类型的 DOM 事件：mousemove 和 touchmove。该函数
 * 略微复杂。它会跟踪自上次“move”动作以来移动的距离，
 * 此外，如果仍处于 _moveThreshold 范围内，则忽略移动并吞掉事件。
 * @param e DOM 事件
 */
_p._onMoveDomEvent = function(e) {
    var coords = this._getInputCoordinates(e);

    // 计算位移增量
    var deltaX = coords.x - this._lastMoveCoordinates.x;
    var deltaY = coords.y - this._lastMoveCoordinates.y;

    // 检查阈值，如果从初始点击到当前位置的距离
    // 大于阈值，则将其判定为真实移动
    if (!this._moving && Math.sqrt(deltaX*deltaX + deltaY*deltaY) > this._moveThreshold) {
        this._moving = true;
    }

    // 如果当前交互正在“移动”中（已经越过了阈值）
    // 则触发事件，否则忽略该交互。
    if (this._moving) {
        this.emit("move", {x: coords.x, y: coords.y, deltaX: deltaX, deltaY: deltaY, domEvent: e});
        this._lastMoveCoordinates = coords;
    }

    this._stopEventIfRequired(e);
};
```

## 第 5 章：事件处理与用户输入（续）

下一个函数是 `_onUpEvent`，即监听 `touchend` 和 `mouseup` 事件的监听器，如清单 5-17 所示。它的工作非常简单：将事件转换为自定义格式并重置 `_moving` 标志。移动结束，下一次移动将再次跟踪阈值。

### 清单 5-17. `_onUpDomEvent`：处理交互结束并清除 `_moving` 标志的函数

```javascript
/**
 * 监听“up”类型的 DOM 事件：mouseup 和 touchend。Touchend
 * 不包含关联的坐标，因此该函数
 * 会在 TouchInputHandler 中被重写
 * @param e DOM 事件
 */
_p._onUpDomEvent = function(e) {
    // 工作方式与 _onDownDomEvent 完全相同
    var coords = this._getInputCoordinates(e);

    this.emit("up", {x: coords.x, y: coords.y, moved: this._moving, domEvent: e});
    this._stopEventIfRequired(e);

    // 交互结束。重置标志
    this._moving = false;
};
```

该类中最后一个工具函数 `_stopEventIfRequired()` 的代码非常直观（见清单 5-18）。正如我们讨论过的，它会阻止浏览器事件的传播并阻止默认操作。

### 清单 5-18. 阻止事件：通常用户除了游戏行为外，不希望浏览器默认行为同时发生

```javascript
_p._stopEventIfRequired = function(e) {
    if (this._stopDomEvents) {
        e.stopPropagation();
    }
};
```



`e.preventDefault();`

`}`

`};`

`InputHandlerBase`类定义了 API 的通用流程。当用户开始交互（具体方式不限，无论是点击鼠标还是触摸界面）时，会触发`down`事件。当用户按住控制器时，该类会忽略所有落在阈值半径内的移动操作，因为这些操作很可能不是有意的。如果用户达到阈值，该操作被判定为“移动”，`InputHandler`开始触发`move`事件。最后，当用户结束交互时，会触发`up`事件。

如你所见，我们使用的自定义事件不会暴露任何不必要的平台细节。此外，该类还支持一些额外的功能：`_moveThreshold`和用于追踪移动事件的增量（deltas）。

基于这个通用基类，具体处理器的实现并不困难。让我们从`MouseInputHandler`开始。它与默认行为的唯一区别在于，你需要在触发`move`事件之前跟踪按钮是否被按下（我们将忽略没有按下按钮的鼠标移动，因为在触摸界面中没有直接对应的操作）。

**创建`MouseInputHandler`**

从构造函数开始。`MouseInputHandler`应使用我们刚刚构建的自定义监听器来追踪浏览器事件。代码如清单 5-19 所示。

**清单 5-19.** *初始化 MouseInputHandler：自定义监听器追踪鼠标事件*

```javascript
/**
 * The implementation of the InputHandler for the desktop
 * browser based on the mouse events.
 */
function MouseInputHandler(element) {
    InputHandlerBase.call(this, element);
    this._attachDomListeners();
}

extend(MouseInputHandler, InputHandlerBase);
_p = MouseInputHandler.prototype;

/**
 * Attach the listeners to the mouseXXX DOM events
 */
_p._attachDomListeners = function() {
    var el = this._element;
    el.addEventListener("mousedown", this._onDownDomEvent.bind(this), false);
    el.addEventListener("mouseup", this._onUpDomEvent.bind(this), false);
    el.addEventListener("mousemove", this._onMoveDomEvent.bind(this));
};
```

现在可以使用`MouseInputHandler`了；它将每个鼠标事件转换为自定义形式。但它的运行并不完全正确。它会为每一次鼠标移动触发`move`事件，无论按钮是否被按下。我们已约定，只有当鼠标按钮按下时才触发`move`事件，因为在 Android 中，触摸移动最接近于鼠标界面中的拖拽操作。

首先，在构造函数中添加用于追踪按钮状态的标志（参见清单 5-20）。

**清单 5-20.** *添加用于追踪按钮状态的标志*

```javascript
function MouseInputHandler(element) {
    InputHandlerBase.call(this, element);
    // We need additional property to track if the
    // mouse is down.
    this._mouseDown = false;
    this._attachDomListeners();
}
```

现在重写所有的事件监听器，如清单 5-21 所示。`_onDownDomEvent()`将`_mouseDown`标志设置为`true`，`_onUpDomEvent()`将标志设置为`false`，而`_onMoveDomEvent()`在触发事件之前检查该标志。

**清单 5-21.** *追踪鼠标按钮状态，仅在按钮按下时触发 move 事件*

```javascript
/**
 * This method (and the next one) is overridden,
 * because we have to track the state of the mouse.
 * This could also be done in the separate listener.
 */
_p._onDownDomEvent = function(e) {
    this._mouseDown = true;
    InputHandlerBase.prototype._onDownDomEvent.call(this, e);
};

_p._onUpDomEvent = function(e) {
    this._mouseDown = false;
    InputHandlerBase.prototype._onUpDomEvent.call(this, e);
};

/**
 * We process the move event only if the mouse button is
 * pressed, otherwise the DOM event is ignored.
 */
_p._onMoveDomEvent = function(e) {
    if (this._mouseDown) {
        InputHandlerBase.prototype._onMoveDomEvent.call(this, e);
    }
};
```

最后一步是消除按钮追踪带来的小副作用。如果你在按下按钮时将光标移出画布，那么……



