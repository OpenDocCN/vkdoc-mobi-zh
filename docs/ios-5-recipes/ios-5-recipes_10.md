# UIPinchGestureRecognizer

这个`UIGestureRecognizer`的子类用于识别捏合手势，即用户将两个触摸点相互靠近或远离。此类是连续型的，意味着只要捏合动作持续，它就保持活跃状态，但其值会不断变化。

你可以从每个捏合手势中访问两个属性：

1.  `scale`：由捏合产生的缩放因子
2.  `velocity`：给定捏合手势的速度

根据你的用途，你需要关注`state`的不同值。

