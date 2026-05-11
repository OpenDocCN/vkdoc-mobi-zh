# 问题 58：在 Swift 枚举中，原始值（raw values）和关联值（associated values）有什么区别？

原始值用于枚举中每个 case 都由一个编译时设定的值表示的情况。它们类似于常量，例如：

```
enum E: Int {
case A  // 如果不指定，基于 IntegerLiteralConvertible 的枚举从 0 开始
case B
}
```

在这个例子中，`A` 的值将是 `0`，而 `B` 的值将是 `1`。

关联值更像是变量，与枚举的某个 case 相关联。

```
enum E {
case A(Int)
case B
case C(String)
}
```

