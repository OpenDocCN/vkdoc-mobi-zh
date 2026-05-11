# 第 1 章：事件处理与用户输入

当`InputHandler`检测到交互（点击、触摸或移动）时，会调用第一个函数。它会计算增量（即控制器中心与触摸坐标之间的 x 和 y 距离）。

接下来，使用几个简单的三角函数公式计算半径和方位角的值。最后，如果位置自上次以来发生了变化，则会通知用户。从游戏代码中使用摇杆甚至更加简单（参见清单 5-30）。

**清单 5-30.** *将摇杆添加到游戏中*

```
function init() {
    canvas = initFullScreenCanvas("mainCanvas");
    ctx = canvas.getContext("2d");
    joystick = new Joystick(canvas);
    var r = joystick.getControllerRadius();
    joystick.setPosition(r + 50, canvas.height - r - 50);
    animateCanvas();
}

function animateCanvas() {
    var speed = 20;
    var joystickRadius = joystick.getRadius();
    if (joystickRadius > 0) {
        var joystickAzimuth = joystick.getAzimuth();
        var xSpeed = joystickRadius * Math.cos(joystickAzimuth) * speed;
        var ySpeed = joystickRadius * Math.sin(joystickAzimuth) * speed;
        controlledObject.x += xSpeed;
        controlledObject.y += ySpeed;
    }
    // ... the rest of animation code goes here
}
```

虚拟摇杆是一个非常棒的工具，尤其适用于移动设备。然而，在桌面浏览器上存在可用性问题。主要问题是您必须按住鼠标按钮才能移动它。如果您的游戏同时面向移动和桌面平台，请确保考虑可用性，不仅针对触摸屏，也要考虑传统的鼠标和键盘。摇杆演示的最终版本位于名为`07.joystick.html`的文件中，该文件随本章的源代码一起提供。

## 总结

在本章中，我们深入探讨了浏览器事件处理和用户控制。我们了解了触摸界面与常规桌面浏览器输入模型的不同之处。我们发现处理浏览器事件可能是一项繁琐的任务。更好的方法是创建一个合适的中间 API 层，该层隐藏事件细节并公开简单的接口。

我们学习了如何构建在游戏内部使用的自定义事件处理程序。对于像将事件对象发送给直接订阅者这样的简单任务，使用浏览器重量级的事件模型通常没有意义。

我们通过拖放和像素完美选择模型开发了与游戏交互的高级方式。最后，我们创建了模拟摇杆的自定义组件——摇杆自电子游戏诞生以来一直用于与视频游戏交互的硬件。

完成本章后，我们已准备好迎接下一个挑战：渲染虚拟世界。

