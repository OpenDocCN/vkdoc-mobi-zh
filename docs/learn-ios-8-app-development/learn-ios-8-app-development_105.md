# 排版后内容

如果您的应用需要了解任何运动测量的变化率，它就需要时间信息。例如，要测量角旋转的变化，您需要用当前速率减去先前速率，然后除以两个样本之间的时间差。

但是，您在哪里可以找到这些测量的采集时间？在之前的章节中，我写道："`CMAccelerometerData`唯一的属性是`acceleration`"，并对`CMGyroData`和`CMMagnetometerData`做出了类似的表述。这并不完全准确。

`CMAccelerometerData`、`CMGyroData`、`CMMagnetometerData`和`CMDeviceMotion`类都是`CMLogItem`的子类。`CMLogItem`类定义了一个`timestamp`属性，上述所有类都继承了该属性。

`timestamp`属性记录了进行测量的确切时间，使您的应用能够精确比较样本并计算其变化率、将其记录保存，或用于您可能想到的任何其他目的。

**提示** 如果您需要计算姿态变化（减去两个`CMAttitude`对象的值），`multiplyByInverseOfAttitude(_:)`函数将为您完成计算。

