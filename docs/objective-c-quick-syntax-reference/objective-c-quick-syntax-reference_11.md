# 6. 字符串

**摘要**  
`NSString` 是 Objective-C 中用于处理字符串的类。`NSString` 管理构成字符串的字符列表。`NSString` 对象是不可变的，这意味着一旦你创建了一个 `NSString` 对象，就无法更改它。

## NSString

`NSString` 是 Objective-C 中用于处理字符串的类。`NSString` 管理构成字符串的字符列表。`NSString` 对象是不可变的，这意味着一旦你创建了一个 `NSString` 对象，就无法更改它。

`NSString` 对象可以使用多种不同的构造函数进行创建，但最常见的创建方式是使用 `@` 符号后跟引号。实际上，在第 1 章的 Hello World 示例中你已经见过这种方式。

`NSLog(@"Hello, World!");`

那个参数就是一个 `NSString` 对象，尽管不太容易看出来，因为这里无需显式声明 `NSString`。更常见的是这样创建 `NSString` 对象：

`NSString *firstName = @"Matthew";`

`NSString *lastName = @"Campbell";`

这是另一个 `NSString` 构造函数 `stringWithFormat:`，当需要使用其他变量和对象来组成新字符串时，它经常被使用：

`NSString *n = [NSString stringWithFormat:@"%@ %@", firstName, lastName];`

而构造函数 `stringWithContentsOfFile:encoding:error:` 则用于根据文件内容创建一个新的 `NSString` 对象。

`NSString *fileName = @"/Users/Shared/report.txt";`

```
NSString *fileContents = [NSString stringWithContentsOfFile:fileName
                                  encoding:NSStringEncodingConversionAllowLossy
                                  error:nil];
```

## NSMutableString

有时，你希望在程序执行过程中能够向字符串添加或删除字符。例如，你可能需要维护用户在程序中所作更改的日志，并且不希望每次更改时都创建新的字符串。这种情况就需要使用 `NSMutableString`。

你可以使用相同的构造函数来创建 `NSMutableString` 对象，但使用 `@""` 将对象直接赋值为字符串的快捷方式除外。要创建一个简单的 `NSMutableString`，请使用 `stringWithString:` 构造函数。

`NSMutableString *alpha = [NSMutableString stringWithString:@"A"];`



### 插入字符串

你可以在可变字符串构成字符列表的任意位置插入字符串。只需确保指定的插入点在字符列表的范围内即可。如果你的字符串只有 10 个字符长，就不要尝试在位置 20 插入字符串。你可以通过向字符串发送 `length` 消息来获取字符串的长度。

要插入字符串，你需要同时指定要插入的字符串和起始位置。以下是将 `B` 插入到可变字符串 `alpha` 中的方法：

```
[alpha insertString:@"B"
            atIndex:[alpha length]];
```

这里你发送了 `insertString:atIndex:` 消息。第一个参数是 `@"B"`，即你想要添加到可变字符串 `A` 中的字符串。`atIndex:` 参数是 `alpha` 字符串的长度，因为你想要将 `B` 追加到 `A` 字符串的末尾，以生成 `@"AB"`。

如果你只是想追加一个字符串，还有一种更简单的方法。你可以发送 `appendString:` 消息，该方法只需要字符串参数。由于假定该字符串会被追加到可变字符串的末尾，因此不需要指定插入点。

```
[alpha appendString:@"C"];
```

### 删除字符串

就像你可以向可变字符串添加字符串一样，你也可以移除可变字符串的部分内容。在删除字符串时，你需要同时指定起点和长度。有一个名为 `NSRange` 的复合类型可以帮助你完成此操作。`NSRange` 有两个关联变量：`location` 和 `length`。在向可变字符串发送 `deleteCharactersInRange:` 消息之前，你需要先创建一个这种复合类型的实例。

```
NSRange range;
range.location = 1;
range.length = 1;
[alpha deleteCharactersInRange:range];
```

这段代码将从你在前面步骤中创建的 `ABC` 字符串中删除 `B`。

**注意：** 当你使用 `NSRange` 时，应牢记字符串是以字母列表形式存储的，其索引从 0 开始。

### 查找与替换

任何使用过文字处理软件的人都知道查找与替换功能有多么方便。你只需向程序提供要替换的文本以及替换后的文本即可。`NSMutableString` 也具备此功能。

要对可变字符串执行查找与替换操作，你需要定义一个范围，并提供你要查找的字符串以及用于替换它的字符串。你还可以指定搜索选项。

```
range.location = 0;
range.length = 2;
[alpha replaceOccurrencesOfString:@"AC"
                       withString:@"ABCDEFGHI"
                          options:NSLiteralSearch
                            range:range];
```

这里你首先做的是重用 `NSRange` 类型的 `range` 变量，以指定要检查的字符串部分。你将从开头开始，搜索整个字符串的长度。

接下来，你定义要替换的字符串 `@"AC"`，以及用作替换的字符串 `@"ABCDEFGHI"`。

在选项中，你设置了 `NSLiteralSearch` 选项。这意味着该方法将要求你的字符串完全匹配。你也可以指定 `NSCaseInsensitiveSearch` 来忽略大小写，以及 `NSRegularExpressionSearch`，它允许你使用正则表达式。

**注意：** 正则表达式是一种用于在字符串中搜索模式的工具。它们被广泛应用于许多编程语言中。对正则表达式的完整解释超出了本书的范围，但如果你经常处理字符串，它值得深入研究。

最后一个参数是你在发送消息之前设置好的 `range` 变量。

