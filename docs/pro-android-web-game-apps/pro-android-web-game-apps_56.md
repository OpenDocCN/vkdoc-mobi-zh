# 排版后的内容

仅处理所需的部分。`rect` 也可以为 `null`，表示“重绘所有内容”。

**注意：** 可选使用 `DirtyRectangleManager` 意味着需要在所有地方都使用它，或者完全不使用。例如，无法仅在 `IsometricTileLayer` 中使用它，而在 `ObjectLayer` 中忽略。

#### 与图层的集成

我们已经创建了 `DirtyRectangleManager`，它已准备好追踪屏幕中已更新的部分。现在我们的任务是更新 `Game` 类和两个图层：`IsometricTileLayer` 和 `ObjectLayer`，使它们遵循 `DirtyRectangleManager` 的提示，仅重新渲染屏幕中已更改的部分。

首先更新 `Game` 类，然后将 `DirtyRectangleManager` 传递给两个图层，如清单 7-33 所示。

**清单 7-33.** *更新 Game 类以使用 DirtyRectangleManager*

```
function Game(canvas, map) {
    /* 未更改部分 */
    this._dirtyRectangleManager = new DirtyRectangleManager();
    this._imageManager = new ImageManager();
    /* 未更改部分 */
}

_p._initLayers = function() {
    var im = this._imageManager;
    this._tileLayer = new IsometricTileLayer(this._map, im.get("terrain"),
                    128, 68, 124, 62, 0, 3);
    this._tileLayer.setDirtyRectManager(this._dirtyRectangleManager);
    this._ball1 = new StaticImage(im.get("ball"), 370, 30);
    this._ball2 = new StaticImage(im.get("ball"), 370, 30);
    this._objectLayer = new ObjectLayer([this._ball1, this._ball2], 200, 4000,
                    4000);
    this._objectLayer.setDirtyRectManager(this._dirtyRectangleManager);
    /* 未更改部分 */
};

/** 在游戏世界中移动视口 */
_p.move = function(deltaX, deltaY) {
    this._dirtyRectangleManager.markAllDirty();
    this._tileLayer.move(-deltaX, -deltaY);
    this._objectLayer.move(-deltaX, -deltaY);
};

/** 处理尺寸调整 */
_p.resize = function() {
    this._dirtyRectangleManager.setViewport(this._canvas.width,
                    this._canvas.height);
    this._tileLayer.setSize(this._canvas.width, this._canvas.height);
    this._objectLayer.setSize(this._canvas.width, this._canvas.height);
};
```

这段代码相当直观。`_dirtyRectangleManager` 的更新方式与图层几乎相同。一旦玩家移动地图，整个帧都会被标记为脏区域。当玩家旋转屏幕或改变画布尺寸时，`_dirtyRectangleManager` 会获知新的视口尺寸。最后，两个图层都接收到同一个 `_dirtyRectangleManager` 实例，用于将屏幕各部分标记为脏区域。

接下来，更新最关键的部分——`Game` 类内的渲染代码，如清单 7-34 所示。

**清单 7-34.** *更新 Game 类的渲染代码*

```
_p._renderFrame = function() {
    this._ctx.save();
    if (!this._dirtyRectangleManager.isAllClean()) {
        var rect = this._dirtyRectangleManager.getDirtyRect();
        this._ctx.beginPath();
        this._ctx.rect(rect.x, rect.y, rect.width, rect.height);
        this._ctx.clip();
        this._tiledLayer.draw(this._ctx, rect);
        this._objectLayer.draw(this._ctx, rect);
    }
    this._dirtyRectangleManager.clear();
    this._ctx.restore();
};
```

这里有几个需要注意的重要事项。首先，我们首次见到对 `Context2D` API 的调用序列：

```
this._ctx.beginPath();
this._ctx.rect(rect.x, rect.y, rect.width, rect.height);
this._ctx.clip();
```

顾名思义，这三个调用设置了上下文上的裁剪区域。区域外的所有内容都将被忽略。裁剪区域是一个会改变上下文状态的参数，因此我们需要用 `ctx.save()` 和 `ctx.restore()` 调用来包裹函数体。

最后，该函数会检查矩形是否完全清洁，如果是，则忽略重绘（这是该策略最大的优势）。否则，每个图层都会接收到脏矩形作为 `draw` 方法的第二个参数。

**注意：** 既然图层已经拥有 `DirtyRectangleManager` 的实例，为什么还要将脏矩形传递给它？问得好。原因在于渲染策略并非图层的职责。图层不应了解游戏代码*具体如何*决定渲染场景的细节。例如，一种不同的


