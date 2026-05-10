# 动画化拨号盘

舞台已经搭好，所有演员都已就位。你定义了一个将拨号盘中心固定到特定位置的行为，以及第二个将顶部中心点“拉向”第二个锚点的行为。当你移动第二个锚点时，动作就开始了，如图 16-7 所示。

找到 `-rotateDialView:` 方法，并删除第一条语句，即创建仿射变换并将其应用于视图的那条语句。将该代码替换为以下内容（新代码以粗体显示）：

```
- (void)rotateDialView:(double)rotation
{
    CGPoint center = dialView.center;
    CGFloat radius = dialView.frame.size.height/2 + kSpringAnchorDistance;
    CGPoint springPoint = CGPointMake(center.x+sin(rotation)*radius,
                                      center.y-cos(rotation)*radius);
    springBehavior.anchorPoint = springPoint;
}
```

这里没有采用传统方式告诉图形系统你想要什么样的变化（比如将视图旋转一定角度），而是描述了对物理环境的改变，并让动态动画器模拟其结果。在此应用中，你移动了附着在视图顶部中心点的锚点。移动锚点会在新锚点和视图中的附着点之间产生吸引力。由于视图中心被第一个行为固定，视图顶部点要靠近新锚点的唯一方式就是旋转视图——而这正是实际发生的情况。

运行应用并查看效果。拨号盘的行为更像一个“真正的”拨号盘。有加速、减速，甚至振荡。这些效果都归功于动态动画器中的物理引擎。

尝试修改 `kSpringAnchorDistance`、`kSpringDamping` 和 `kSpringFrequency` 的值，观察它们如何影响拨号盘。作为额外练习，添加第三个行为来为拨号盘增加一些“阻力”。创建一个 `UIDynamicItemBehavior` 对象，将其与拨号视图关联，并将其 `angularResistance` 属性设置为非 `0` 的值；建议从 `2.0` 开始尝试。不要忘记将完成后的行为添加到动态动画器中。

现在你拥有一个炫酷的倾斜仪，它运行流畅且观察起来很有趣。既然你已经知道向应用添加运动数据以及使用视图动力学模拟运动是多么简单，让我们来看看其他一些运动数据的来源。

## 获取其他类型的运动数据

截至撰写本文时，你的应用还可以使用其他三种运动数据。你可以收集和使用这些其他类型的数据，以替代或补充加速度计数据。以下是 iOS 提供的运动数据类型：

- **陀螺仪**：测量设备绕其三个轴的旋转速率
- **磁力计**：测量周围磁场的方位
- **设备运动**：结合来自加速度计、磁力计和陀螺仪的信息，生成关于设备运动和空间位置的有用值

使用其他类型的运动数据与使用加速度计数据的方法完全相同，但有一点例外。并非所有 iOS 设备都配备陀螺仪或磁力计。你必须决定你的应用是必须拥有这些功能，还是可以在没有它们的情况下运行。这个决定将决定你如何配置应用的项目和编写代码。让我们从陀螺仪开始。



### 陀螺仪数据

若你关注设备旋转的瞬时速率——逻辑上等同于加速计数据，但针对的是角力——则需收集陀螺仪数据。收集陀螺仪数据的方式与加速计数据几乎完全相同。首先设置运动管理对象的 `gyroUpdateInterval` 属性，然后向其发送 `-startGyroUpdates` 或 `-startGyroUpdatesToQueue:withHandler:` 消息。

`gyroData` 属性返回一个 `CMGyroData` 对象，该对象包含一个 `rotationRate` 属性值。此属性是一个结构体（与 `CMAcceleration` 类似），包含三个值：`x`、`y` 和 `z`。每个值代表绕该轴的旋转速率，单位为弧度/秒。

你必须考虑用户设备可能没有陀螺仪的情况。有两种处理方式：

- 如果你的应用必须依赖陀螺仪硬件才能运行，请在应用的属性列表中将 `gyroscope` 值添加到 `Required Device Capabilities` （必需设备功能）集合中。
- 如果你的应用无论有无陀螺仪均可运行，请检测运动管理对象的 `gyroAvailable` 属性。

第一种方法将陀螺仪硬件设为应用运行的必要条件。若添加到应用属性列表中，iOS 将不允许该应用安装在缺少陀螺仪的设备上。App Store 可能会对缺少陀螺仪的用户隐藏该应用，或提醒他们该应用可能无法在其设备上运行。

如果你的应用可以使用陀螺仪数据，但没有也能正常运行，请通过读取运动管理对象的 `gyroAvailable` 属性来检测陀螺仪是否存在。若值为 `YES`，则可放心启动并使用陀螺仪数据。若值为 `NO`，则需另行安排。

### 磁力计数据

通过磁力计数据可以获取设备周围磁场的强度和方向。现在，这些内容听起来可能像老生常谈：

- 使用 `magnetometerUpdateInterval` 属性设置磁力计更新的频率。
- 使用 `-startMagnetometerUpdates` 或 `-startMagnetometerUpdatesToQueue:withHandler:` 消息启动磁力计测量。
- `magnetometerData` 属性返回一个包含当前读数的 `CMMagnetometerData` 对象。
- `CMMagnetometerData` 对象的唯一属性是 `magneticField` 属性，这是一个包含三个值的结构体：`x`、`y` 和 `z`。每个值代表沿该轴的磁场方向和强度，单位为 μT（微特斯拉）。
- 要么将 `magnetometer` 值添加到应用的 `Required Device Capabilities` 属性中，要么检查 `magnetometerAvailable` 属性以确定设备是否具有磁力计。

与加速计和陀螺仪数据类似，`magnetometerData` 属性返回原始、未经滤波的磁场信息。这将是地球磁场、设备自身磁偏、任何环境磁场、磁干扰等的组合。

从这些数据中分离出磁北方向有一定难度。看起来像北方的方向可能来自微波炉。同样，加速计数据可能因设备倾斜、在移动的车辆中或两者同时发生而改变。你可以通过收集并关联多个仪器的数据来理清一些相互矛盾的指标。例如，通过检查加速计和陀螺仪的变化，可以区分倾斜和水平移动；倾斜会同时改变两者，但水平移动仅会记录在加速计上。

如果你开始感到不安，觉得当初应该在物理和数学课上更用心，那大可放心；iOS 已为你考虑周全。

### 设备运动与姿态

`CMMotionManager` 还通过其设备运动接口提供设备物理位置和运动的统一视图。设备运动的属性和方法结合了来自加速计、陀螺仪以及有时是磁力计的信息。它整合所有这些数据，并生成一个经过滤波、统一且校准的设备在空间中的运动与位置图像。

你使用设备运动的方式与使用前述三种仪器非常相似：

- 使用 `deviceMotionUpdateInterval` 属性设置设备运动更新的频率。
- 通过发送 `-startDeviceUpdates`、`-startDeviceMotionUpdatesToQueue:withHandler:`、`-startDeviceMotionUpdatesUsingReferenceFrame:` 或 `-startDeviceMotionUpdatesUsingReferenceFrame:toQueue:withHandler:` 消息启动设备运动更新。
- `deviceMotion` 属性返回一个包含当前运动与姿态信息的 `CMDeviceMotion` 对象。
- 使用 `deviceMotionAvailable` 属性确定设备运动数据是否可用。

设备运动与先前接口有两个主要区别。启动更新时，你可以选择提供一个 `CMAttitudeReferenceFrame` 常量，该常量为设备选择参考系。有四种选择：

- 设备方向任意
- 方向任意，但使用磁力计消除“偏航漂移”
- 方向校准至磁北
- 方向校准至真北（需要定位服务）

设备的自然参考位置可以想象为将 iPhone 或 iPad 平放在面前的桌子上，屏幕朝上，主屏幕按钮朝向自己。从主屏幕按钮到设备顶部的连线为 Y 轴。X 轴从左到右水平延伸。Z 轴垂直穿过设备，上下方向。

设备仍在桌面上旋转时，会改变其方向。参考系关注的就是这个方向。如果方向无关紧要，你可以使用任意一种参考系。如果需要知道相对于真北或磁北的方向，请使用其中一种校准参考系。

注意

并非所有姿态参考系在每款设备上都可用。请使用 `+availableAttitudeReferenceFrames` 方法确定设备支持哪些参考系。

第二个主要区别是 `CMDeviceMotion` 对象。与其他运动数据对象不同，该对象包含多个属性，如表 16-1 所示。

**表 16-1.** 关键 `CMDeviceMotion` 属性

| 属性 | 描述 |
| --- | --- |
| `attitude` | 一个 `CMAttitude` 对象，描述设备的实际姿态（空间位置），表示为一组三个属性值（`pitch`（俯仰角）、`roll`（翻滚角）和 `yaw`（偏航角））。其他属性以数学上等价的形式（旋转矩阵和四元数）描述相同的信息。 |
| `rotationRate` | 一个包含三个值（`x`、`y` 和 `z`）的结构体，描述绕这些轴的旋转速率。 |
| `userAcceleration` | 一个 `CMAcceleration` 结构体（`x`、`y` 和 `z`），描述设备的运动。 |
| `magneticField` | 一个 `CMCalibratedMagneticField` 结构体（`x`、`y`、`z` 和 `accuracy`（精度）），描述地球磁场的方向。 |

乍一看，所有这些信息似乎与来自加速计、陀螺仪和磁力计的数据相同——只是重新包装了一下。实际上并非如此。`CMDeviceMotion` 对象结合了来自多个仪器的信息，以更全面地解读设备的运行状态。具体来说：



