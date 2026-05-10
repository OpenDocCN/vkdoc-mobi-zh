# `for` 循环

Swift 的 `for` 和 `switch` 语句功能丰富。让我们先看看 `for` 循环。

### `for` 循环简介

Swift 的 `for` 循环有三种形式：传统形式、范围形式和集合形式。传统版本是所有类 C 语言中常见的 `init` / `while` / `increment` 组合形式。示例如下：

```
for condition = true; condition; condition = false {
}
```

第一个语句执行一次以初始化循环。然后，只要条件表达式为 `true`，循环就会重复。每次循环结束时，执行增量语句，并重新评估条件表达式。这些都很直观。

另外两种形式通常被称为 `for`-`in` 语句，它们会遍历数值范围或集合中的元素。范围形式如下所示：

```
for i in 0..<100 {
    // 执行 100 次，i 从 0 到 99
}

for i in 0...100 {
    // 执行 101 次，i 从 0 到 100
}
```

`for`-`in` 范围循环遍历由范围描述的一系列数字。*闭区间* `0...100` 包含从 0 到 100 的所有值（总共 101 个数字）。*半开区间* `0..<100` 包含从 0 到 99 的数字。请注意，范围中的任一值可以是任何数值表达式，例如 `0..<inventory.count`。

**警告**  范围的起始值必须小于或等于结束值。

第二种 `for`-`in` 循环格式遍历集合的内容。这可用于遍历数组、字典或字符串中的值。对于数组和字符串（在这种情况下，字符串被视为字符数组），格式很简单，如下所示：

```
let notes = [ "do", "re", "mi", "fa", "sol", "la", "ti" ]
for note in notes {
    // 循环执行 7 次：第一次 note 是 "do"，第二次是 "re"，依此类推
}
for character in "When you know the notes to sing, you can sing most anything" {
    // 循环执行 59 次，字符串中的每个字符一次
}
```

当与字典一起使用时，循环变量是一个包含每个条目的 `(key, value)` 对的元组。您可以使用元组循环变量，或者将其解构为键和值变量。两种方式都在以下示例中展示：

```
let doReMi = [ "doe": "a deer, a female deer",
               "ray": "a drop of golden sun",
               "me":  "a name, I call myself",
               "far": "a long long way to run",
               "sew": "a needle pulling thread",
               "la":  "a note to follow so",
               "tea": "I drink with jam and bread" ]
for note in doReMi {
    // 元组循环变量
    println("\(note.0): \(note.1)")
}

for (noteName, meaning) in doReMi {
    // 解构后的变量
    println("\(noteName): \(meaning)")
}
```

请注意，永远不要为循环变量放置 `let`、`var` 或类型。它总是一个常量（`let`），其类型由范围或集合的元素类型决定。还要记住，数组和字符串总是按顺序处理的，但字典是无序集合，元素的顺序是不确定的。

