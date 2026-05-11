# 实现这一优化

##### 实现

脏矩形算法的实现并不困难，但需要仔细注意：你必须记住将脏区域的尺寸传递给引擎的每一层，并让每一层更新脏矩形集合。

通常的做法是引入一个特殊的**“脏区域阈值”**，当脏区域过大时触发该阈值。一旦达到阈值，我们只需将整个屏幕标记为脏区域，并且在下一帧之前不执行任何脏区域检查。这个小技巧可以为你节省一些 CPU 时间。例如，如果已有 75%的区域是脏的，那么绘制大块屏幕区域与从头重绘整个帧在 CPU 周期上并没有太大差异。此时，我们可以停止脏矩形检查，并像之前章节那样渲染帧。

该优化的核心类称为`DirtyRectangleManager`。它的工作是跟踪脏区域，将它们合并为一个矩形，并跟踪阈值。该类在屏幕空间中运行。实际上，脏矩形是一种纯粹的渲染优化，与游戏世界等更高级别的实体无关。`DirtyRectangleManager`跟踪的区域始终与视口相同。

**注意：** 同样的方法不仅适用于游戏，也适用于任何 GUI 应用程序。例如，Swing——用于构建用户界面的 Java 工具包——就具有相同的功能。它只渲染自上次以来发生变化的界面部分——一个按下的按钮、一个移动的控件等等。

接下来，我们来编写新类的代码。代码相当简短，让我们先查看清单 7-31，然后进行解释。

**清单 7-31.** `DirtyRectangleManager`

```
/*
 * 构造函数接受一个参数，即阈值。
 * 该值是介于 0 和 1 之间的数字。
 * 0 表示——每次出现脏矩形时，重绘整个屏幕；
 * 1 表示——从不使用阈值，只有当整个帧真正全部变脏时才重绘
 */

function DirtyRectangleManager(allDirtyThreshold) {
    // 保存阈值，默认值为 0.5
    this._allDirtyThreshold = allDirtyThreshold == undefined ? 0.5 : allDirtyThreshold;

    // 视口参数
    this._viewport = new Rect(0, 0, 100, 100);

    // 当前覆盖所有较小脏区域的脏矩形
    this._dirtyRect = null;

    // 当达到阈值时为 true
    this._allDirty = true;

    // 开始时将整个屏幕标记为脏区域。
    // 第一次需要重绘全部内容！
    this.markAllDirty();
}

_p = DirtyRectangleManager.prototype;

/**
 * 设置视口的大小。
 */
_p.setViewport = function(width, height) {
    if (this._viewport.width == width && this._viewport.height == height) {
        return;
    }
    this._viewport.width = width;
    this._viewport.height = height;
    this.markAllDirty();
};

/**
 * 将给定区域标记为脏区域。这是图层 API 使用的主要函数，
 * 用于通知层的部分内容在更新过程中发生了变化，
 * 并且需要在下一帧重绘
 */
_p.markDirty = function(rect) {
    if (!(rect.width || rect.height) || this._allDirty) {
        return;
    }

    // 我们只关心与视口相交的矩形
    // 如果矩形远离世界的可见部分，则没有跟踪的意义
    rect = this._viewport.intersection(rect);
    if (!rect) {
        return;
    }

    // 如果已经有一些区域被标记为脏区域，则找到同时覆盖
    // 当前脏矩形和新矩形的矩形
    if (this._dirtyRect) {
        this._dirtyRect = this._dirtyRect.convexHull(rect);
    } else {
        // 如果是第一个脏矩形，则将其保存为脏区域
        this._dirtyRect = this._viewport.intersection(rect);
    }

    // 检查阈值。如果达到，则将整个屏幕标记为脏区域
    if (this._dirtyRect.width * this._dirtyRect.height > 
```



this._allDirtyThreshold * this._viewport.width * this._viewport.height)

{
    this.markAllDirty();
}

};

/**
 * 清除脏区域。通常在重绘后执行，此时所有脏区域都已更新为新内容并恢复为干净状态。
 */
_p.clear = function() {
    this._dirtyRect = null;
    this._allDirty = false;
};

/**
 * 将整个视口标记为脏区域。
 */
_p.markAllDirty = function() {
    this._allDirty = true;
    this._dirtyRect = this._viewport.copy();
};

/**
 * 如果当前帧中没有注册任何脏矩形则返回真（表示完全不需要重绘）
 */
_p.isAllClean = function() {
    return !(this._dirtyRect);
};

/**
 * 如果整个视口都是脏区域则返回真（需要重绘所有内容）
 */
_p.isAllDirty = function() {
    return this._allDirty;
};

/**
 * 返回覆盖所有已注册脏区域的当前脏矩形
 */
_p.getDirtyRect = function() {
    return this._dirtyRect;
};

该类有一个名为 `_dirtyRect` 的变量，用于跟踪当前标记为脏的区域。它可以处于两种状态之一——要么是“全部脏”（`all dirty`），要么是“正常”（`normal`）。如果 `_allDirty` 为 `true`，则所有其他脏矩形都会被忽略，`getDirtyRect()` 会返回整个视口。在正常模式下，每个新的脏矩形首先会被视口“裁剪”。我们对视口之外的部分不感兴趣（如果区域不可见，无论如何我们都不会重绘它，无论其是否脏）。然后，新矩形会与当前脏区域进行“合并”。`convexHull()` 方法会找到同时覆盖新脏区域和现有脏区域的最小矩形。

如你所见，`markAllDirty()` 是一个公共方法，因此客户端 API 可以直接调用它。这在诸如移动等场景中非常有用——当屏幕的整个区域都需要更新，且客户端 API 能立即知道视口已变脏时。

`DirtyRectangle` 是一种高效但具有侵入性的策略。一旦你使用了它，游戏的各个部分，哪怕是最小的部分，都必须了解它的存在。例如，当一个精灵的动画切换到下一帧时，精灵需要一种方法将区域标记为脏。或者，管理精灵的实体（例如一个图层）也可以执行此操作。关键在于，每一次移动、动画或地图块的变化——任何导致屏幕更新的操作——都应该伴随着对 `DirtyRectangleManager` 的调用。这就是为什么我们会在游戏层级的最顶层——`GameObject`——添加对新类的支持。清单 7-32 总结了这一修改。

**清单 7-32.** *使 GameObject 了解 DirtyRectangleManager*

```
function GameObject() {
    EventEmitter.call(this);
    this._id = GameObject._maxId++;
    this._bounds = new Rect(0, 0, 100, 100);
    this._dirtyRectManager = null;
}
```

```
_p.draw = function(ctx, rect) {
    // 待实现
};

_p.getDirtyRectManager = function() {
    return this._dirtyRectManager;
};

_p.setDirtyRectManager = function(dirtyRectManager) {
    this._dirtyRectManager = dirtyRectManager;
};

_p.markDirty = function(x, y, width, height) {
    if (this._dirtyRectManager) {
        var rect = arguments.length == 1 ? x : new Rect(x, y, width, height);
        this._dirtyRectManager.markDirty(rect);
    }
};
```

为了使得 `DirtyRectangleManager` 的使用成为可选的，我们假设实现类不会直接调用 `this._dirtyRectManager`，而只会通过调用 `markDirty()` 来使用它。是否采用这种优化由程序员决定。实际上，有些游戏会从这种方法中大大受益，而有些则不会。例如，在一个屏幕上挤满了移动僵尸的僵尸末日游戏中，你无需费心处理脏矩形——屏幕大部分时间都是脏的。另一方面，在一个世界大部分静态、最终播放动画的农场类游戏中，这种方法则会带来巨大收益。

`draw()` 函数现在多了一个参数 `rect`，它作为一个提示——表示当前正在重绘的矩形。各实现会利用这个提示来进行重绘。



