# SKActions 与 SKTextures：你的第一个动画精灵

你的项目中已经包含了用于启动画面的图像，现在需要将其添加到屏幕上。在刚刚添加的 `self.backgroundColor` 代码行之后，立即添加以下代码：

```objective-c
/* 在此处设置你的场景 */

self.backgroundColor = [SKColor blackColor];
```

```objective-c
NSString *fileName = @"";
if (self.frame.size.width == 480) {
    fileName = @"SewerSplash_480";
    // iPhone Retina (3.5 英寸)
} else {
    fileName = @"SewerSplash_568";
    // iPhone Retina (4 英寸)
}

SKSpriteNode *splash = [SKSpriteNode spriteNodeWithImageNamed:fileName];
splash.name = @"splashNode";
splash.position = CGPointMake(CGRectGetMidX(self.frame), CGRectGetMidY(self.frame));
[self addChild:splash];
```

首先创建了一个空的 `NSString` 用于保存文件名。然后判断用户使用的是 3.5 英寸屏幕还是 4 英寸屏幕。这里检查的是视图的高度而非宽度，因为在应用程序的这个阶段，设备尚未完成旋转——几乎旋转了，但尚未完成。

确定要使用的文件名后，通过便捷方法 `spriteNodeWithImageNamed` 创建了一个 `SKSpriteNode` 对象。

接着，设置 `name` 属性为节点赋予一个字符串名称，以便后续在其他方法中按需引用该节点。

### 锚点

默认情况下，精灵的帧以其位置为中心，且该位置就是图形/帧的中心点。通过修改精灵节点的 `anchorPoint` 属性，可以将其位置移动到帧上的其他点。图 2-3 说明了这一点。

**图 2-3.** 对比两种锚点设置

在图 2-3 的示例中，可以看到当精灵旋转时，移动锚点会带来显著差异，因为它成为了旋转的中心点。然而，在处理碰撞和接触时，锚点同样会产生影响，这一点将在后续章节中看到。请注意，锚点使用的值是单位坐标系中的数值，因此无论精灵尺寸如何，左下角都是 `(0.0, 0.0)`，右上角都是 `(1.0, 1.0)`。

### 回到启动画面

在场景中设置新精灵的位置时，需要牢记精灵的锚点默认是 `(0.5, 0.5)`，即图像的中心。因此，你也希望图像在场景中居中显示。`position` 属性需要 `CGPoint` 值，你可以使用两个便捷方法（`CGRectGetMidX` 和 `CGRectGetMidY`）来确定 X 轴和 Y 轴的中心点。眼尖的读者可能已经注意到，你在 `CGPointMake` 方法中似乎颠倒了顺序。确实如此，这是因为设备尚未正式旋转为横屏模式，而你已经确定要实现横屏效果。因此，在这段代码中，你仍然需要检查 `SKView` 的高度，以确定屏幕尺寸和精灵的位置（而精灵是以横屏方向绘制的）。

现在，精灵已经创建并配置完毕，是时候将其放入场景并进行绘制了——将它作为子节点添加到 `SKScene` 中（如前面代码所示）。

如果你现在构建并运行程序，应该能看到漂亮的启动画面。

### 场景切换

接下来，你将实现这样一个功能：当用户触摸屏幕时，不会生成旋转的宇宙飞船，而是将他们带到主游戏画面（下一个场景）。

首先，删除不再需要的代码。删除 `SKBMyScene.m` 文件中 `touchesBeganWithEvent` 方法内 for 循环的所有内容，使其变成这样：



