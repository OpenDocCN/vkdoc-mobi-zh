# 7. 数字

**摘要：** `NSNumber` 是 Objective-C 中用于处理数字的类。`NSNumber` 提供了一种将浮点数和整数值转换为面向对象数字对象的方法。虽然你不能在表达式中使用 `NSNumber` 对象，但当需要复杂格式化时，`NSNumber` 对象会非常有用。

## NSNumber

`NSNumber` 是 Objective-C 中用于处理数字的类。`NSNumber` 提供了一种将浮点数和整数值转换为面向对象数字对象的方法。虽然你不能在表达式中使用 `NSNumber` 对象，但当需要复杂格式化时，`NSNumber` 对象会非常有用。

`NSNumber` 对象可以通过多种不同的构造器创建，但最常见的创建 `NSNumber` 对象的方式是使用 `@` 符号后跟一个数字。

```
NSNumber *num1 = @1;
NSNumber *num2 = @2.25;
```

有时你可能希望使用与特定方式存储的数字相匹配的特殊构造器。

```
NSNumber *num3 = [NSNumber numberWithInteger:3];
NSNumber *num4 = [NSNumber numberWithFloat:4.44];
```

### 转换为原始数据类型

`NSNumber` 对象不能用于表达式中，但 `NSNumber` 有一些内置函数，可以以原始数据类型的形式返回该对象。你必须在表达式中使用这些函数之前，先使用它们来转换数字。

```
CGFloat result = [num1 floatValue] + [num2 floatValue];
```

上面使用的函数是 `floatValue`，但还有更多如 `intValue` 和 `doubleValue` 等函数，它们与 C 语言编程中的 `int` 和 `double` 等原始数据类型相匹配。`stringValue` 是另一个函数，它会将数字格式化为字符串返回，这在生成报告时非常有用。

### 格式化数字

当你需要为报表和演示文稿中的显示格式化数字时，`NSNumber` 变得非常有用。与 `NSNumberFormatter` 类一起使用时，你可以将数字输出为本地化货币格式、科学记数法格式，甚至可以输出为拼写格式。

为此，你必须先创建一个新的数字格式化器，然后设置你想要使用的格式化样式。

```
NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
formatter.numberStyle = NSNumberFormatterCurrencyStyle;
```

然后，你可以发送 `stringFromNumber:` 消息来获取格式化后的数字。以下是在 `NSLog` 的上下文中向控制台写入消息的示例：

```
NSLog(@"Formatted num2 = %@", [formatter stringFromNumber:num2]);
```

由于我在美国设置，这将在我的计算机上输出 `$2.25`。你的输出将根据你的 Mac 或 iOS 设备上设置的区域设置而有所不同。

### 将字符串转换为数字

你也可以将字符串转换为数字。如果你有一个以字符串形式表示的数字，你可以使用数字格式化器将该字符串转换为 `NSNumber` 对象。

只需将数字格式化器的样式更改为存储该数字时所用的样式。然后使用 `NSNumberFormatter numberFromString:` 消息创建一个新的 `NSNumber` 对象。以下是将字符串 “two point two five” 转换为数字 `2.25` 的方法：

```
formatter.numberStyle = NSNumberFormatterSpellOutStyle;
NSNumber *num5 = [formatter numberFromString:@"two point two five"];
```

