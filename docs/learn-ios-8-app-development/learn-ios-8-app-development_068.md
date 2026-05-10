# 总结

概括来说，MVC 是好的。

所有这些计算机科学的学习是否让你想休息一下，听听喜欢的音乐？那么，下一章就是为你准备的。

## 练习

虽然你的`ColorModel`应用非常接近理想化的 MVC 通信模型，但它仍然依赖控制器来观察变化并将更新事件转发给视图对象。基于你目前所做的工作，让颜色视图直接观察数据模型的变化会有多困难？

其实并不难，这就是本章的练习。修改`ViewController`和`ColorView`，使`ColorView`成为`Color`对象中“颜色”变化的直接观察者。

这是在具有复杂数据模型和大量自定义视图对象的较大型应用中常见的模式。其优势在于每个视图对象承担起观察特定于该视图的数据模型变化的职责，从而减轻控制器对象的负担。

你可以在`Learn iOS Development Projects`![image](img/arrow.jpg)`Ch 8`![image](img/arrow.jpg)`ColorModel`![image](img/arrow.jpg)`E1`文件夹中找到我对这个练习的解决方案。

¹ 对于这条规则有一个例外，我将在本章末尾进行描述。
² 派生属性的名称实际上是函数名的一部分。当你观察一个属性键（例如`anyProp`）时，KVO 会检查你是否实现了名为`keyPathsForValuesAffectingAnyProp()`的类函数。如果你实现了，它会调用该函数来发现其依赖键。

---

