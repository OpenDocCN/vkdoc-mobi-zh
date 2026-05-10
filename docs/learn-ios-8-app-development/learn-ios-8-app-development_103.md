# 陀螺仪硬件要求与磁力计数据

第一种方法使陀螺仪硬件成为应用运行的必要条件。如果将此要求添加到应用的属性列表中，iOS 将不允许在不具备陀螺仪的设备上安装该应用。App Store 可能会向没有陀螺仪的用户隐藏该应用，或提醒他们该应用可能无法在其设备上运行。

添加此`key`的方式与您在第 14 章中添加`gamekit`能力要求的方式完全相同。请找到第 14 章中的“将 GameKit 添加到您的应用”部分，按照编辑`Required Device Capabilities`集合的说明操作，将`gyroscope`替换为`gamekit`。

如果您的应用可以使用陀螺仪数据但并非必需，请通过读取运动管理器对象的`gyroAvailable`属性来检测陀螺仪的存在。如果其值为`true`，请放心启动并使用陀螺仪数据；如果为`false`，则需另做安排。

### 磁力计数据

通过磁力计数据可以获取设备周围磁场的强度和方向。到了这一步，这听起来可能像是老生常谈。

1. 使用`magnetometerUpdateInterval`属性设置磁力计更新的频率。
2. 调用`startMagnetometerUpdates()`或`startMagnetometerUpdatesToQueue(_:,withHandler:)`函数启动磁力计测量。
3. `magnetometerData`属性返回一个包含当前读数的`CMMagnetometerData`对象。
4. `CMMagnetometerData`对象的唯一属性是`magneticField`属性，该属性包含三个值：`x`、`y`和`z`。每个值代表沿该轴方向的磁场强度和方向，单位为μT（微特斯拉）。
5. 将`magnetometer`值添加到应用的`Required Device Capabilities`属性中，或检查`magnetometerAvailable`属性以确定设备是否具备磁力计。

与加速度计和陀螺仪数据类似，`magnetometerDate`属性返回原始、未经滤波的磁场信息。这将是地球磁场、设备自身的磁偏置、任何环境磁场、磁干扰等的组合。

从这些数据中解析出磁北方向有点棘手。看起来像北方的信号可能来自微波炉。类似地，加速度计数据可能会因为设备倾斜或处于移动的汽车中（或两者兼有）而发生变化。您可以通过收集和关联来自多个仪器的数据来解开这些相互矛盾的指标。例如，通过检查加速度计和陀螺仪的变化，您可以区分倾斜和水平移动：倾斜会同时改变两者，但水平移动仅会在加速度计上记录变化。

如果您开始感到自己应该在物理和数学课上更专心听课的懊恼，请放心：iOS 已经为您考虑周全了。

### 设备运动与姿态

`CMMotionManager`还通过其设备运动接口提供了设备物理位置和运动的统一视图。设备运动属性和函数结合了来自加速度计、陀螺仪，有时还包括磁力计的信息。它整合所有这些数据，并生成一个经过滤波、统一和校准的设备在空间中的运动与位置图景。

使用设备运动的方式与使用前述三种仪器非常相似。

1. 使用`deviceMotionUpdateInterval`属性设置设备运动更新的频率。
2. 调用以下函数之一启动设备运动更新：`startDeviceUpdates()`、`startDeviceMotionUpdatesToQueue(_:,withHandler:)`、`startDeviceMotionUpdatesUsingReferenceFrame(_:)`或`startDeviceMotionUpdatesUsingReferenceFrame(_:,toQueue:,withHandler:)`。
3. `deviceMotion`属性返回一个包含当前运动和姿态信息的`CMDeviceMotion`对象。
4. 使用`deviceMotionAvailable`属性确定设备运动数据是否可用。

设备运动与之前接口有两个主要区别。启动更新时，您可以提供一个`CMAttitudeReferenceFrame`常量，为设备选择一个*参考系*。有四个选项：

- 设备方向任意
- 方向任意，但使用磁力计消除“航向漂移”
- 方向校准至磁北
- 方向校准至真北（需要定位服务）

设备的参考位置可以想象为：将您的 iPhone 或 iPad 平放在面前的桌子上，屏幕朝上，Home 键朝向您。从 Home 键到设备顶部的线是 y 轴。x 轴从左侧水平延伸到右侧。z 轴垂直穿过设备。

设备在桌上保持平放时旋转，会改变其*方向*。参考系关注的就是这个方向。如果方向不重要，您可以使用任意参考系。如果您需要了解相对于真北或磁北的方向，请使用校准后的参考系。

**注意** 并非所有姿态参考系在每个设备上都可用。使用`availableAttitudeReferenceFrames()`函数确定设备支持哪些参考系。

第二个主要区别是`CMDeviceMotion`对象。与其他运动数据对象不同，该对象有多个属性，如表 16-1 所示。

**表 16-1.** 关键 `CMDeviceMotion` 属性

| 属性 | 描述 |
| --- | --- |
| `attitude` | 一个`CMAttitude`对象，描述设备的实际姿态（空间位置），表示为三个属性值（`pitch`、`roll`和`yaw`）的三元组。其他属性以数学上等效的形式（旋转矩阵和四元数）描述相同信息。 |
| `rotationRate` | 一个包含三个值（`x`、`y`和`z`）的结构，描述绕这些轴的旋转速率。 |
| `userAcceleration` | 一个`CMAcceleration`结构（`x`、`y`和`z`），描述设备的运动。 |
| `magneticField` | 一个`CMCalibratedMagneticField`结构（`x`、`y`、`z`和`accuracy`），描述地球磁场的方向。 |

乍一看，所有这些信息似乎与来自加速度计、陀螺仪和磁力计的数据相同——只是重新打包。但事实并非如此。`CMDeviceMotion`对象结合了来自多个仪器的信息，以推断出设备正在做什么的更全面图景。具体来说：

- `attitude`属性结合了来自陀螺仪的角度变化测量、来自加速度计的重力方向确定，有时还结合了磁力计来校准方向（绕 z 轴旋转）并防止漂移。
- `userAcceleration`属性关联加速度计和陀螺仪数据，排除重力和姿态变化的影响，以提供准确的加速度测量。
- `magneticField`属性针对设备偏置进行调整，并尝试补偿磁干扰。

总体而言，设备运动接口信息更丰富、更智能。如果说有缺点，那就是它需要更多的处理能力，这会消耗应用性能并缩短电池续航。如果您的应用只需要了解运动或旋转的大致情况，那么加速度计或陀螺仪的原始数据就足够了。但如果您确实需要了解设备的位置、方向或朝向，那么设备运动接口已经为您计算好了。

**注意** 设备可能需要以圆形图案倾斜，以帮助校准磁力计。如果您将`CMMotionManager`的`showsDeviceMovementDisplay`属性设置为`true`，iOS 会自动显示一个提示用户执行此操作的界面。

