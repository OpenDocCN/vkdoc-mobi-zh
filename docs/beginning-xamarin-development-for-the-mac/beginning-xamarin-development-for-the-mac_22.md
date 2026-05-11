# 显示用户详情

在确保第一个视图准备就绪后，我们可以继续创建第二个视图，该视图用于显示用户详情。为此，我使用了 iOS 设计器，从工具箱中拖拽了一个新的视图控制器。接着，我将它的类设置为 `UserDetailsViewController`，并设计了它的视图，如图 7-1 右侧部分所示。我还更改了视图中选定控件的名称，如下所示：

*   第一个文本字段的名称是 `TextFieldName`。
*   第二个文本字段的名称是 `TextFieldEmail`。
*   `MKMapView` 控件的名称是 `MapViewAddress`。
*   第一个按钮命名为 `ButtonCancel`。
*   第二个按钮命名为 `ButtonUpdate`。

完成这些操作后，我开始实现逻辑。首先，在 `UserDetailsViewController` 类中，我声明了一个公共属性 `User` 和两个私有字段（列表 7-23）。该属性用于传递要显示的用户数据。第一个字段配置地图的区域，第二个字段存储对 `UsersRepository` 对象的引用。

```
public partial class UserDetailsViewController : UIViewController
{
public User User { get; set; }
private const double spanDelta = 0.05d;
private UsersRepository usersRepository;
// Further class definition
}
列表 7-23.
UserDetailsViewController 类的片段
```

然后，我实现了 `ViewDidLoad` 事件处理程序，如列表 7-24 所示。也就是说，我获取了 `UsersRepository` 类的实例，然后配置地图，使其可以滚动和缩放。

```
public override async void ViewDidLoad()
{
base.ViewDidLoad();
usersRepository = await UsersRepository.GetInstance();
MapViewAddress.ScrollEnabled = true;
MapViewAddress.ZoomEnabled = true;
}
列表 7-24.
获取 UsersRepository 实例与地图配置
```

这部分准备好之后，我就可以在视图中显示用户的姓名、电子邮件地址和位置了。我使用了 `ViewWillAppear` 视图事件处理程序（列表 7-25）。我通过添加两条语句来扩展此事件处理程序的基本实现，这两条语句调用了 `DisplayUserData` 和 `UpdateMap` 方法。如列表 7-25 所示，第一个方法用于更新文本字段的 `Text` 属性，以显示用户的姓名和电子邮件地址。第二个方法（稍后将讨论）获取用户的地理位置并将其显示在地图上。

```
public override void ViewWillAppear(bool animated)
{
base.ViewWillAppear(animated);
DisplayUserData();
UpdateMap(User.Address.Geo.ToCLLocationCoordinate2D());
}
private void DisplayUserData()
{
TextFieldName.Text = User.Name;
TextFieldEmail.Text = User.Email;
}
列表 7-25.
显示用户详情
```

下一步，我为两个按钮创建了 `TouchUpInside` 事件处理程序，然后根据列表 7-26 实现了它们。与第二个事件处理程序不同，与 `ButtonCancel` 关联的第一个事件处理程序用于在不更改用户数据的情况下导航回第一个视图，而第二个事件处理程序则首先根据应用用户所做的更改更新这些数据（参见列表 7-26 中的 `UpdateUserData` 方法）。

```
partial void ButtonCancel_TouchUpInside(UIButton sender)
{
DismissViewController(true, null);
}
partial void ButtonUpdate_TouchUpInside(UIButton sender)
{
UpdateUserData();
ButtonCancel_TouchUpInside(sender);
}
private void UpdateUserData()
{
User.Name = TextFieldName.Text;
User.Email = TextFieldEmail.Text;
usersRepository.Update(User);
}
列表 7-26.
按钮的 TouchUpInside 事件处理程序
```

最后，我使用列表 7-27 中的一组方法更新了地图视图。我之前在第 3 章中使用过类似的方法。因此，这里不再赘述。唯一的新内容是 `AddPin` 方法，它在地图上创建图钉标注。

```
private void UpdateMap(CLLocationCoordinate2D centerCoordinate)
{
AddPin(centerCoordinate);
SetMapRegion(centerCoordinate);
}
private void SetMapRegion(CLLocationCoordinate2D centerCoordinate)
{
var span = new MKCoordinateSpan(spanDelta, spanDelta);
var region = new MKCoordinateRegion(centerCoordinate, span);
MapViewAddress.SetRegion(region, false);
}
private void AddPin(CLLocationCoordinate2D centerCoordinate)
{
var pin = new PinMapAnnotation(centerCoordinate, User.Name);
MapViewAddress.AddAnnotation(pin);
}
列表 7-27.
在地图上显示用户的地理位置
```

要创建一个标注，首先需要实现一个派生自 `MKAnnotation` 的类，然后使用 `MKMapView` 类实例的 `AddAnnotation` 方法将类的实例添加到地图上（请回顾列表 7-27 中的 `AddPin` 方法）。列表 7-28 显示了 `PinMapAnnotation` 类的完整定义，我使用它来创建一个简单的图钉标注。`PinMapAnnotation` 有两个私有字段：`coordinate` 和 `title`。第一个字段表示图钉位置，另一个字段是指点击图钉时弹出的弹出窗口中显示的标题。还有三个与这些字段关联的方法。这些方法派生自基类，分别是：`Coordinate`、`SetCoordinate` 和 `Title`。前两个方法用于获取和设置坐标，最后一个方法用于获取标题值。`PinMapAnnotation` 的最后一个元素是类构造函数，它接受两个参数。它们的值存储在该类的适当私有成员中。

```
public class PinMapAnnotation : MKAnnotation
{
private CLLocationCoordinate2D coordinate;
private readonly string title;
public override CLLocationCoordinate2D Coordinate
{
get => coordinate;
}
public override void SetCoordinate(CLLocationCoordinate2D value)
{
coordinate = value;
}
public override string Title
{
get => title;
}
public PinMapAnnotation(CLLocationCoordinate2D coordinate, string title)
{
this.coordinate = coordinate;
this.title = title;
}
}
列表 7-28.
图钉地图标注的实现
```

现在，你可以在模拟器中编译并运行该应用。你会很快看到显示用户列表的第一个视图。你可以滑动每个元素来激活行操作。使用它们可以更新或删除用户。

## 总结

在本章中，我们学习了如何实现一个通过 HTTP 协议与 Web 服务交互的 Xamarin.iOS 应用。为此，我们使用了 `System.Net.Http` 程序集中的 `HttpClient` 类，并实现了一个用于从远程 RESTful 服务消费数据和向其发送数据的类。我们还创建了一个与远程资源同步的本地数据存储。随后，我们创建了单元测试来自动验证我们实现的正确性。最后，我们创建了显示从远程服务检索到的信息的应用。因为 `HttpClient` 类也可用于其他平台，包括通用 Windows 平台和 Android.iOS，所以我们也可以使用此处介绍的技术为这些平台开发 REST API 客户端。本章结束我们激动人心的 iOS 应用开发之旅。在下一章中，我们将学习如何为可穿戴的 Apple Watch 平台创建应用。



