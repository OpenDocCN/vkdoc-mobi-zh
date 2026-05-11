# `ctx.fillRect(0, 0, canvas.width, canvas.height);`

`spriteSheet.drawFrame(ctx, currentFrame, 10, 40);`

`requestAnimationFrame(arguments.callee);`

}

`animate(0);`

如果屏幕上有多个精灵，每个精灵都有自己的动画参数（如每帧时长、帧数等），该如何处理？像示例中那样存储数据很快就会变得混乱不堪。看来是时候再创建一个类了。

#### 实现 `Animator`

动画并非仅限于切换帧。事实上，任何对元素形状、位置、颜色或其他视觉属性进行的平滑渐进式改变，都属于动画范畴。我们刚才看到的只是最简单的情况：每一帧在屏幕上停留固定的时间，然后由下一帧替换，如此往复，直至动画结束。动画播放完毕后，下一轮循环会从第一帧重新开始。

如果将同样的方法应用于改变对象的坐标，就会得到另一种类型的动画——匀速运动。现实生活中有很多这样的例子：在空旷公路上行驶的汽车就是其中之一。然而，更多运动实例都伴随着加速或减速（如苹果从树上掉落）、方向变化（如摆动的钟摆），甚至遵循着更复杂的规律。

这些更复杂的动画场景在休闲游戏和街机游戏中经常出现。例如，当角色在绳索上摆动时，其速度并非恒定；向下摆动时会加速，向上摆回时会减速。还有许多涉及加速和减速的运动实例，因此在本节介绍的`Animator`类中实现这些机制是很有价值的。

`Animator`有两个主要用途。第一种是播放匀速动画，就像我们在上一节中做的那样。在这种情况下，`Animator`仅仅作为计算动画循环进度的代码的封装器。第二种用途是处理带加速和减速的动画更新。

我们将先查看使用`Animator`的代码，然后讨论其实现。清单 4-27 展示了一个使用`Animator`进行简单帧切换的示例。我们创建了`Animator`实例并设置了动画参数——目前仅设置了序列的总时长。之后，`Animator`会返回一个从动画开始时刻起算的进度比例值。

**清单 4-27.** *Animator 的基本使用方法*

```
// 动画总帧数
var numFrames = 5;

// 每帧时长
var TIME_PER_FRAME = 50;

var animator = new Animator(numFrames*TIME_PER_FRAME);

// 启动动画序列
animator.start();

function animate(t) {
   var deltaTime = 获取从上一帧经过的时间();

   var fraction = animator.update(deltaTime);

   // 根据进度比例和总帧数计算当前帧
   var currentFrame = Math.floor(fraction*numFrames);

   // 绘制当前帧
   drawFrame(currentFrame);

   // 循环执行
   requestAnimationFrame(arguments.callee);
}

animate(0);
```

`drawFrame()` 和 `获取从上一帧经过的时间()` 都是占位函数，实现起来相当容易。`Animator` 类的核心思想是将计算动画当前进度比例的代码与更新屏幕的代码分离开来。如果需要动画化对象的位置而不是帧，代码的改动量会非常小，如清单 4-28 所示。

**清单 4-28.** *更新对象位置。对象在 500 毫秒内从(0, 0)移动到(300, 300)。*

```
// 动画总时长 - 半秒
var animator = new Animator(500);

// 启动序列
animator.start();

function animate(t) {
   var deltaTime = 获取从上一帧经过的时间();

   var fraction = animator.update(deltaTime);

   var pos = fraction*500;

   drawToken(pos);

   // 循环执行
   requestAnimationFrame(arguments.callee);
}

animate(0);
```



