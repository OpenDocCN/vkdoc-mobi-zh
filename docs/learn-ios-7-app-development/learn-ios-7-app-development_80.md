# 将对象转换为属性列表对象

属性列表的一个重大限制在于，它们只能包含属性列表对象（`NSNumber`、`NSString`、`NSDictionary` 等）。任何你想要存储在用户默认（或任何属性列表）中的数据，都必须转换为这些对象之一。以下是存储其他类型数值的三种最常见技术：

- 将数值转换为字符串
- 将数值转换为属性列表字典
- 将对象序列化为 `NSData` 对象

第一种技术足够简单，尤其是因为 Cocoa Touch 中有许多函数可以为你完成此操作。例如，假设你需要在用户默认中存储一个 `CGRect` 值。`CGRect` 不是属性列表对象——它甚至不是一个对象。你可以将其四个浮点字段作为单独的值存储，如下所示：

```
CGRect saveRect = self.someView.frame;
[userDefaults setFloat:saveRect.origin.x forKey:@"HPFrame.x"];
[userDefaults setFloat:saveRect.origin.y forKey:@"HPFrame.y"];
[userDefaults setFloat:saveRect.size.height forKey:@"HPFrame.height"];
[userDefaults setFloat:saveRect.size.width forKey:@"HPFrame.width"];
```

并且你还需要反向操作来恢复矩形。这看起来工作量很大。幸运的是，有两个函数——`NSStringFromCGRect` 和 `CGRectFromString`——可以将矩形转换为字符串对象并再转换回来。现在，保存矩形的代码可以这样写：

```
[userDefaults setObject:NSStringFromCGRect(self.someView.frame)
                 forKey:@"HPFrame"];
```

所以，如果你能找到能让你在数值与属性列表对象之间相互转换的函数，就使用它们。

第二种技术就是你将用于地图位置的方法。你将编写一对方法。第一个方法会将 `MKPointAnnotation` 对象的主要属性作为由 `NSString` 和 `NSNumber` 对象组成的字典返回。第二个方法将获取该字典并重新设置这些属性。

首先，在你的项目中添加一个新分类。在项目导航器中选择一个文件，例如 `HPViewController.m`，然后选择“新建 ➤ 文件...”命令（文件菜单或右键/按住 Control 键单击）。从 Cocoa Touch 组中，选择“Objective‑C 分类”模板。将分类命名为 `HPPreservation`，并将其设为 `MKPointAnnotation` 的分类。

**注意**

分类会向现有类添加额外的方法。在第 14 章中，你使用了分类向自己的类添加方法，但它们同样可以轻松地向你未编写的类添加新方法。我将在第 20 章中解释分类的来龙去脉。

在 `MKPointAnnotation+HPPreservation.h` 的 `@interface` 中，添加两个方法声明：

```
- (NSDictionary*)preserveState;
- (void)restoreState:(NSDictionary*)state;
```

在 `MKPointAnnotation+HPPreservation.m` 中，在 `#import` 语句之后立即定义三个字典键的常量：

```
#define kInfoLocationLatitude   @"lat"
#define kInfoLocationLongitude  @"long"
#define kInfoLocationTitle      @"title"
```

在 `@implementation` 部分，编写这两个方法：

```
- (NSDictionary*)preserveState
{
    CLLocationCoordinate2D coord = self.coordinate;
    return @{ kInfoLocationLatitude:    @(coord.latitude),
              kInfoLocationLongitude:   @(coord.longitude),
              kInfoLocationTitle:       self.title };
}

- (void)restoreState:(NSDictionary*)state
{
    CLLocationCoordinate2D coord;
    coord.latitude = [state[kInfoLocationLatitude] doubleValue];
    coord.longitude = [state[kInfoLocationLongitude] doubleValue];
    self.coordinate = coord;
    self.title = state[kInfoLocationTitle];
}
```

第一个方法返回一个新的字典（`NSDictionary`）对象，其中包含三个值：标注的纬度、经度和标题。字典中的值是 `NSNumber` 和 `NSString` 对象，完全适合存储在属性列表中。而这正是你接下来要做的。

第二个方法反向操作，使用字典中的值设置标注的坐标和标题。现在，让我们使用这些方法来保存和恢复地图位置。



