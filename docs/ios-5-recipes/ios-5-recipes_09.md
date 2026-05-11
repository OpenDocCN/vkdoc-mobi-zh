# UILongPressGestureRecognizer

`UILongPressGestureRecognizer`用于识别“长按”手势，即用户将一根或多根手指保持在相同位置超过正常轻触时长的操作。

与之前介绍的几种手势识别器类似，你可以通过`numberOfTapsRequired`和`numberOfTouchesRequired`属性指定所需的点击次数以及使用的手指/触摸数量。

这个`UIGestureRecognizer`的子类拥有额外属性来配置“长按”的性质。`minimumPressDuration`指定触摸必须保持多长时间，识别器的`state`才会转为`UIGestureRecognizerStateBegan`。`allowableMovement`属性接受一个浮点数，指定用户在长按手势失败前可以有多大的“活动余地”。该值应设置得足够大，使正常人能轻松保持触摸，但又不能大到允许可能跨越多个元素的显著移动。

`UILongPressGestureRecognizer`会在手势开始和结束时触发其动作，这可能导致不期望的行为。通常，你只需要响应`UIGestureRecognizerStateBegan`状态，以便用户能轻松获知手势已被识别。

