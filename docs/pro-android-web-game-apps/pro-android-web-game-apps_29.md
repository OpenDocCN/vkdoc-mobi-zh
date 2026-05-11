# 处理后的文本

改动后的代码以**粗体**标出。仅需修改几行代码，我们就从帧动画切换到了位置动画！如果要添加加速和减速效果该怎么办？这一改动不应影响屏幕的重新渲染方式，唯一变化的是计算新进度比的算法。实际代码隐藏在动画器内部，因此客户端代码只需增加两行，如清单 4-29 所示。

**清单 4-29.** *设置加速与减速*

```javascript
var animator = new Animator(500);
animator.setAcceleration(0.5);
animator.setDeceleration(0.3);
```

其余代码保持不变！

**注意：** 整体解决方案的灵感来源于 Timing Framework (http://java.net/projects/timingframework)，该框架旨在简化 Java 桌面程序中用户界面元素的动画处理。您可以查阅该框架获取灵感。

除了加速和减速功能，`Animator` 还应支持设置循环次数、切换动画方向，以及提供动画事件通知功能，并具备暂停、停止、重置等简单控制。由于这部分代码与游戏开发特有算法无关，逐步编写该类意义不大。我们只需通览代码了解其工作原理即可。

完整代码可与本章其他源代码一同在 `js/Animator.js` 文件中找到。本章仅回顾其中最有趣的部分。起初我本想将全部代码放在此处，但当我最终完成了一组精巧的小功能、防错检查以及边缘情况处理方案后，发现仅打印清单就需要六页篇幅。因此，您最好直接获取源代码并亲身体验。您已经看到了 `Animator` 的几种基本用法。此外，本章源代码中还包含几个完整的示例。

`Animator` 拥有多个属性，每个属性都配有 getter 和 setter。在 Android 设备上调试运行不佳的代码可能会非常痛苦（请阅读附录 A 了解应对方法），常常需要在 `console.log` 输出上耗费数小时。因此，编写能检查参数限制的防错代码非常有意义。`Animator` 在将每个参数赋值给字段前都会进行检查（参见清单 4-30）。

**清单 4-30.** *设置 `Animator` 参数的示例——检查参数范围*

```javascript
_p.setDuration = function(duration) {
    // 如果动画正在运行，不允许更改参数
    this._throwIfStarted();
    if (duration < 1) {
        throw "Duration can't be < 1";
    }
    this._duration = duration;
};
```

虽然不太可能有人试图以显式设置的 0 时长启动 `Animator`，但如果时长值是通过某种方式生成或通过网络接收的，从技术上讲仍有可能出现这种状况。那么接下来会发生什么？代码处理此问题的最佳方式可能是抛出异常并通知程序员。允许不可接受的参数传递可能不会造成直接危害，但往往会导致难以追踪的不可预测错误。这种方法称为*快速失败*。

请注意函数的第一行。我们不希望 `Animator` 在运行时被重新配置，因为这同样可能引发错误。如果用户试图在动画运行时设置属性，他将会收到一个异常。

采用相同的方法，我们为其他属性也定义了 setter 和 getter。

- `duration`：单次动画循环的时长。
- `repeatCount`：循环次数；可以是整数，或设置为 `-1` 表示无限循环。
- `repeatBehavior`：可设置为 `Animator.RepeatBehavior.LOOP`（默认）或 `Animator.RepeatBehavior.REVERSE`。默认行为表示动画到达终点后，`Animator` 从 0 重新开始下一次循环并逐步运动到 1。



`is 'playing forward'` 每次都是如此。`REVERSE` 表示动画师在每次循环时切换方向。例如，偶数次循环从 0 到 1，而奇数次循环从 1 到 0。这种模式通常称为“正向播放，然后反向播放”。

-   **加速度（acceleration）**：加速时间所占的比例。
-   **减速度（deceleration）**：减速时间所占的比例。

`acceleration` 和 `deceleration` 这两个值值得更详细的解释。当我们使用所讨论的方法时，分数值会以恒定速率从 0 移动到 1。该速率由动画总时长决定，并且在循环期间不会改变。这种方法对于动画化精灵的帧效果不错，但在处理物理过程时存在局限性。

以这种方式动画化的汽车移动看起来不真实：刚才还静止不动的车辆，现在却以每小时 80 英里的速度移动！在现实世界中，它会先加速，然后匀速移动，最后减速。或者，先加速，持续加速，再多加一点速，如果是碰撞测试，最后撞到墙上。

为了模拟现实世界中的事件，我们需要处理加速度和减速度。`Animator` 中使用的值是动画时间的一部分——取值范围是 0 到 1。例如，如果我们有一个持续 1000 毫秒的动画，并将 `acceleration` 设置为 0.5，`deceleration` 设置为 0.3，我们会看到以下行为：汽车从速度 0（静止）开始；在前 500 毫秒内速度增加（`acceleration` 为 0.5 表示一半时间，即 500 毫秒）；然后以恒定速度从 500 毫秒运行到 700 毫秒；最后减速回到 0。

动画器的核心是一个名为 `update()` 的函数。我们会跟踪场景重绘之间经过的时间，并将该值传递给 `update` 函数。该函数返回一个分数，我们已经知道如何使用它了。让我们看看这个函数的代码。它是 `Animator` 最重要的部分，如清单 4-31 所示。

**第 4 章：动画与精灵**
**165**

**清单 4-31.** *更新函数：动画器的心脏*

```javascript
/**
 * 此函数应由外部定时器调用来更新动画器的状态。
 * @param deltaTime - 自上次更新以来经过的时间。0 是有效值。
 */
_p.update = function(deltaTime) {
    // 如果未启动，将返回 undefined
    if (!this._started) {
        return;
    }

    // 如果动画器暂停，我们传入 0 作为 deltaTime // 就像什么都没改变
    if (!this._running) {
        deltaTime = 0;
    }

    this._timeSinceLoopStart += deltaTime;

    // 如果超过了循环时间，我们必须处理下一步要做什么：
    // 调整动画方向、调用钩子函数等。
    if (this._timeSinceLoopStart >= this._duration) {
        // 以防万一，我们跳过了多过一个循环，
        // 确定我们错过了多少个循环
        var loopsSkipped = Math.floor(this._timeSinceLoopStart / this._duration);
        this._timeSinceLoopStart %= this._duration;

        // 截断为跳过的循环数。即使跳过了 5 个循环，
        // 但只剩下 3 个循环，我们也不想触发额外的监听器。
        if (this._repeatCount != Animator.INFINITE
            && loopsSkipped > this._repeatCount - this._loopsDone) {
            loopsSkipped = this._repeatCount - this._loopsDone;
        }

        // 为每个跳过的循环调用钩子函数
        for (var i = 1; i <= loopsSkipped; i++) {
            this._loopsDone++;
            this._reverseLoop = this._repeatBehavior ==
                Animator.RepeatBehavior.REVERSE && this._loopsDone % 2 == 1;
            this._onLoopEnd(this._loopsDone);
        }

        // 检查是否到达了动画的末尾
        if (this._repeatCount != Animator.INFINITE
            && this._loopsDone == this._repeatCount) {
            this._onAnimationEnd();
            this.stop();
            return;
        }
    }

    // 如果这是一个反向循环 // 也反转该分数
    var fraction = this._timeSinceLoopStart / this._duration;
    if (this._reverseLoop)
        fraction = 1 - fraction;

    // 交由预处理
    // （加速度/减速度、缓动函数等）
    fraction = this._timingEventPreprocessor(fraction);

    // 调用 update
};
```



`this._onUpdate(fraction, this._loopsDone);`

`return fraction;`

`};`

前两行代码用于检查动画是否正在运行。若动画器处于暂停状态，我们仅需通过将 `deltaTime` 设置为零来重新计算上次结果。对于暂停的动画器而言，时间已经停滞。这种做法看似浪费资源（因为可以将上次的值缓存后直接返回），但实际中你很少需要暂停单个动画器。如果整个游戏被暂停，通常你不会进行任何更新。因此，在常规情况下牺牲少量 CPU 循环以节省几次操作是可行的。

接下来， `Animator` 会检查自循环开始以来经过的时间量。若该数值超过循环持续时间，`Animator` 将进入下一次循环（或结束动画）。请注意我们特别关注了跳过多次循环的情况。这种情况并非如表面看上去那般不可能。请记住，当浏览器标签页处于非活动状态时，`requestAnimationFrame()` 不会执行其回调函数。同时，当隐藏浏览器窗口时，Android 原生浏览器会暂停定时器活动。完全有可能在数秒不活动后收到一次更新，并跳过几十个动画循环。如果游戏在非活动期间未被暂停，它必须尽力呈现一致的状态。

**注意：** 这个副作用非常有用。它让我们能够识别出距离上次更新已过去很长时间，并将其视为“自动暂停”。例如，若 `animate` 函数中的 `deltaTime` 大于 1000 毫秒，则表示用户切换到了不同标签页，或在 Android 设备上隐藏了浏览器窗口。对于玩家可能因不活动而失败的枪战游戏，启用暂停模式而非让玩家品尝失败苦果可能是个好主意。

当我们得知跳过的循环数量后，可以检查动画是否已完成、判断当前循环是正向还是反向执行，并执行其他维护操作。之后，我们就可以计算并返回 `fraction` 值了。

你是否注意到这四个函数：`_onLoopEnd()`、`_onAnimationEnd()`、`_timingEventPreprocessor()` 和 `_onUpdate()`？除 `_timingEventPreprocessor()` 外，其余三个函数均无实际操作。这些是插入的钩子函数，旨在让你无需侵入 `Animator` 代码即可扩展其功能。稍后我们将讨论如何扩展 `Animator`。

接下来是一个稍显复杂但适用于测试运动的示例。代码清单 4-32 绘制了一个子弹（带填充与描边的圆形），并为其从点 `(50, 50)` 到点 `(300, 50)` 的移动制作了动画。你可以在此处调整不同的动画参数，以确定最适合的配置。

**代码清单 4-32.** *使用动画器移动子弹，更多自定义属性*

```
animator = new Animator(1000);
animator.setRepeatCount(Animator.INFINITE);
animator.setRepeatBehavior(Animator.RepeatBehavior.REVERSE);
animator.setAcceleration(0.8);
animator.setDeceleration(0.15);
animator.start();
var lastUpdate = new Date();
var startX = 50;
var endX = 300;
function animate(t) {
    var now = t || new Date().getTime();
    var deltaTime = now - lastUpdate;
    lastUpdate = now;
    var fraction = animator.update(deltaTime);
    var x = (1 - fraction)*startX + fraction*endX;
    ctx.fillStyle = "white";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    drawBullet(x, 50, ctx);
    requestAnimationFrame(arguments.callee);
}
animate(0);
function drawBullet(x, y, ctx) {
    ctx.fillStyle = "darkred";
    ctx.strokeStyle = "black";
    ctx.lineWidth = 4;
    ctx.beginPath();
    ctx.arc(x, y, 15, 0, 2*Math.PI, false);
    ctx.fill();
    ctx.stroke();
}
```

看，这有多简单！花些时间多做尝试。试着改变大小以制造脉动效果，或者同时改变大小和颜色。尝试绘制并制作一个即将爆炸的卡通炸弹或发射导弹的飞船动画。发挥你的想象力吧。

**扩展动画器**



让我们回到之前提到的钩子函数。在 `Animator` 的默认实现中，这些函数是空的，但它们的主要用途是让你有机会通过自己的酷炫功能对其进行扩展。

让我们再次利用精灵对象，创建一个 `Animator` 的扩展版本，使其能够自动设置对象的某些属性；例如，将任意对象的高度从 30 像素更改为 150 像素，同时隐藏内部公式。能写出像下面这样的代码岂不是很棒？我们将使用钩子函数，将自定义逻辑插入到默认的 `Animator` 实现中，而无需更改该类的代码。

当你为其他开发者设计 API 时，考虑如何让你的逻辑能被定制通常是个好主意。每个开发者对于你的类应该执行何种“魔法”都有自己的想法。与其试图预测每一种可能的用例，不如像 `Animator` 那样添加一些扩展点，让其他开发者能够实现他们自己的想法，同时让你的类保持一致。清单 4-33 从客户的角度展示了相关代码。

**清单 4-33.** *`PropertyAnimator`：能在动画过程中设置类任意属性的类*

```js
animator = new PropertyAnimator(1000, [{
  "setter": setHeight,
  "start": 30,
  "end": 100
}, {
  "setter": setTransparency,
  "start": 50,
  "end": 255
}]);
animator.setEasingFunction(function(fraction) {
  return fraction*fraction*fraction;
});
animator.setRepeatCount(Animator.INFINITE);
animator.setRepeatBehavior(Animator.RepeatBehavior.REVERSE);
animator.setAcceleration(0.8);
animator.setDeceleration(0.15);
animator.start();
```

对于动画需要更改的每个属性，你需传入设置函数的名称、属性的起始值和结束值。剩下的工作就交给框架了（什么？难道两个类就能称为框架了吗？）。

让我们再加大难度，添加对自定义函数的支持，以便在将分数值传递给设置函数之前对其进行修改。对于这个特殊的对象，常规的动画规则根本不够用，我要求它按照二次方，不，是三次方的速率增长！

**注意：** 对分数值进行预处理的函数通常被称为*缓动函数*。即使使用最简单的缓动函数，你也能实现许多非常令人兴奋的动画效果。应用加速和减速的代码也是一种缓动函数。请参考 jQuery UI Effects ([`jqueryui.com/demos/effect/easing.html`](http://jqueryui.com/demos/effect/easing.html))，获取关于使用缓动函数的灵感。这些图是交互式的，点击即可查看不同的动画效果。

遗憾的是，本书的篇幅不足以涵盖我想介绍的所有内容。而且由于缓动函数是一个比较技术宅的话题，我将留给你自行探索。

让我们尝试扩展我们的 `Animator` 以满足这些需求。当然，我们不想去触碰基类并在其中添加越来越多的代码——那样最终会导致它变得庞大而缓慢。这就是为什么我们创建一个名为 `PropertyAnimator` 的新类。

像往常一样，先写构造函数。我们只需要两个参数和两个额外的属性来支持新功能（见清单 4-34）。

**清单 4-34.** *`PropertyAnimator` 的构造函数*

```js
function PropertyAnimator(duration, props) {
  Animator.call(this, duration);
  this._props = props;
  this._easingFunction = null;
}
```

接下来，我们需要缓动函数的设置器。你应该检查传递给设置器的参数是否有效。缓动函数的限制条件是：(1) 它应该是一个接受一个参数作为参数的函数，(2) 它返回一个介于 0 和 1 之间的数字。我们在设置器中唯一能测试的限制是，新值是一个函数，如清单 4-35 所示。

**清单 4-35.** *缓动函数的设置器*

```js
_p.setEasingFunction = function(easingFunction) {
  this._throwIfStarted();
```



```javascript
if (typeof easingFunction != "function") {
  throw "easingFunction must be a function";
}

this._easingFunction = easingFunction;
};
```

别忘了从父类调用 `_throwIfStarted()` 方法——我们必须确保在动画循环中不会破坏正在执行的操作。

以上是简单部分。现在我们需要做两件事：
- 在加速/减速的基础上应用新的缓动函数。
- 当计算出进度值后，将其传递给对象的 setter。

利用我们的钩子函数（见列表 4-36）可以轻松实现。用于预处理值的 `_timingEventPreprocessor()` 是应用自定义缓动的最佳位置。另一个名为 `_onUpdate()` 的函数则允许我们在完成更新前执行自定义操作。

**列表 4-36.** 为动画器添加新功能的自定义钩子函数

```javascript
// 在返回进度值前调用，重定义此函数可设置自定义缓动行为替代默认行为
_p._timingEventPreprocessor = function(fraction) {
  fraction = Animator.prototype._timingEventPreprocessor.call(this, fraction);
  if (this._easingFunction) {
    fraction = this._easingFunction.call(this, fraction);
    // 如果缓动函数返回值大于 1 或小于 0，则将其修剪到允许区间内
    if (fraction > 1) fraction = 1;
    if (fraction < 0) fraction = 0;
  }
  return fraction;
};

// 在进度值计算完成后、更新函数末尾调用。此钩子允许在返回进度值前执行额外操作。
// 在本例中，我们调用对象的 setter
_p._onUpdate = function(fraction) {
  this._props.forEach(function(item) {
    item.setter.call(this, (1 - fraction) * item.start + fraction * item.end);
  }, this);
};
```

这很容易实现。现在轮到你了。想一个炫酷的动画效果并尝试实现它。试试不同的缓动函数。如果使用 `Math.sin` 或 `Math.cos`，或者两者结合会怎样？你知道的，看起来可能很酷！本章提供的其他源代码中包含两个使用 `Animator` 的示例。第一个示例 `06.animator.html` 展示了两点之间的简单移动。第二个示例 `07.swinging_axe.html` 展示了如何结合精灵的位置、角度和大小变化来实现挥斧头的效果。

恭喜！现在你不仅掌握了动画的基础知识，还了解了一些高级特性。但动画还不是游戏，用户需要一种与游戏世界互动的方式。而这正是我们下一章要学习的内容！

## 总结

在本章中，我们学习了图像加载和动画的基础知识。我们从加载图像开始，了解了两种将图像数据传入浏览器的方式——从文件加载和从数据 URI 加载。我们学习了如何处理实际开发中可能出现的加载错误，比如连接中断或服务器上文件缺失。我们还学习了如何组织精灵表以及如何在画布上渲染图像。

下一步是掌握动画技术，从简单的屏幕帧切换，到更复杂的主题，例如优化动画一致性、处理加速与减速，以及自定义缓动函数。

我们简要概述了支持本章所述大部分技术的类——`Animator`（完整源代码随本章其他材料一同提供）。我们学习了如何使用 `Animator` 将更新动画状态（进度值）的代码与在屏幕上渲染新状态（即改变帧或元素位置）的代码解耦。

掌握动画处理技巧仍不足以制作完整的游戏——但我们已接近目标！游戏的核心在于玩家与游戏世界的交互。在下一章中，我们将学习如何响应用户输入，让玩家控制屏幕上的角色。

**事件处理与**



