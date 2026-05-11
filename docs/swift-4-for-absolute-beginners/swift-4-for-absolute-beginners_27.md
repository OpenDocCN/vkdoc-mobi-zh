# 使用字典类

Swift 的 `Dictionary` 类也是一种有用的集合类型。它允许存储对象，就像 `Array` 类一样，但 `Dictionary` 的不同之处在于它允许将一个键与条目关联起来。例如，你可以创建一个字典来存储某人的属性列表，例如 `firstName`、`lastName` 等。与数组使用索引访问属性不同，字典可以使用像 `"firstName"` 这样的 `String` 来访问。然而，所有键必须是唯一的——也就是说，`"firstName"` 不能存在多次。根据你的程序，找到唯一的键名通常不是问题。

下面是一个如何创建字典的例子：

```
var person: [String: String] = ["firstName": "John", "lastName": "Doe"]
```

这将创建一个名为 `person` 的简单字典。声明的下一部分告诉字典键和值将是什么类型的对象。在这个例子中，键是 `String`，值也是 `String`。然后你向字典中添加两个键。第一个键是 `firstName`，该键对应的值是 `John`。第二个键是 `lastName`，对应的值是 `Doe`。你可以使用与数组类似的语法来访问字典中的值。

```
print(person["firstName"])
```

这段代码将打印名称 `Optional("John")`，因为这是键 `firstName` 对应的值。`Optional` 出现在前面的示例中，因为字典中键对应的值是一个可选值。你可以使用相同风格的代码来更改字典中的值。假设在这个例子中，John 现在更喜欢别人叫他 Joe。你可以通过一行简单的代码来更改字典中的值。

```
person["firstName"] = "Joe"
```

你可以使用相同的表示法向字典添加一个新键。

```
person["gender"] = "Male"
```

如果你决定要从字典中删除一个键，比如刚才添加的 `gender` 键，可以通过将该键的值设置为 `nil` 来实现。

```
person["gender"] = nil
```

现在字典将只包含 `firstName` 和 `lastName`。记住，字典是无序的。你不能依赖其顺序，但有时你可能需要遍历字典。这可以通过与数组类似的方式完成。主要区别在于，在数组中，你分配一个变量，而在字典中，你需要同时分配键和值。请参见代码清单 8-7。

```
1    var person: [String: String] = ["firstName": "John", "lastName": "Doe"]
2    for (myKey, myValue) in person {
3         print(myKey + ": " + myValue)
4    }
代码清单 8-7.
遍历字典
```

这个例子将打印以下内容：

```
firstName: John
lastName: Doe
```

字典是一种组织无需排序数据的绝佳方式。它也是基于特定键查找数据的好方法。在 Swift 中，它们非常灵活，应该用于组织和优化你的代码。



