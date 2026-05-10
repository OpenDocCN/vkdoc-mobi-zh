# 注意

`-cStringUsingEncoding:` 等方法返回的 C 字符串指针的生命周期与字符串对象的生命周期紧密相关。当字符串对象被销毁时，该临时字符串缓冲区也会被释放。在字符串对象引用超出作用域后，请勿继续使用该指针，否则垃圾回收器可能会过早地回收该对象——从而销毁其临时的 C 字符串缓冲区。

`-cStringUsingEncoding:`、`-UTF8String`和`-fileSystemRepresentation`这些消息非常便于将`NSString`对象传递给任何需要传统 C 字符串的方法或函数。列表 8-5 演示了如何生成临时 C 字符串并将其传递给传统的 C 函数。当字符串对象是文件或路径名时，应使用`-fileSystemRepresentation`方法。如果字符串仅包含 ASCII 字符，则`-UTF8String`和`-cStringUsingEncoding:NSASCIIStringEncoding`是等效的。

## 列表 8-5. 将`NSString`对象作为 C 字符串传递

```
NSString *filePath = @"/tmp/somefile.dat";
NSString *fileMode = @"w+";
...
FILE *file = fopen([filePath fileSystemRepresentation], [fileMode UTF8String]);
```

编码后字符串的长度通常与`NSString`对象中的 Unicode 字符数量不同——通常更长。可以使用`-lengthOfBytesUsingEncoding:(NSStringEncoding)enc`或`-maximumLengthOfBytesUsingEncoding:(NSStringEncoding)enc`方法来确定容纳编码字符串所需的 8 位字符数。后者速度更快，但通常会高估所需的字节数。这两种方法都不将终止空字符计入其计数中。列表 8-6 展示了一个示例，演示如何将具有特定编码的 C 字符串提取到动态分配的缓冲区中。

## 列表 8-6. 从`NSString`中提取 C 字符串

```
NSString *unicodeString = …
int len = [unicodeString lengthOfBytesUsingEncoding:NSWindowsCP1250StringEncoding];
NSMutableData *buffer = [NSMutableData dataWithLength:len+1];
[unicodeString getCString:(char*)[buffer bytes]
                maxLength:len+1
                 encoding:NSWindowsCP1250StringEncoding];
// [buffer bytes] 现在包含了 unicodeString 的以 null 结尾的 C 字符串版本
// 使用 Windows 中欧和东欧字符集进行编码。
```

如果字符串中的 Unicode 字符无法通过请求的编码来表示，则上述消息将返回`NO`或`NULL`。你可以使用`-canBeConvertedToEncoding:(NSStringEncoding)enc`来预先检查转换，或者考虑使用`-dataUsingEncoding:(NSStringEncoding)enc allowLossyConversion:(BOOL)flag`。为`allowLossyConversion`传递`YES`将始终返回一个编码后的字符串，但会省略或简化任何无法编码的字符。另请注意，`NSNonLossyASCIIStringEncoding`以及`NSUTF…StringEncoding`系列的编码可以表示整个 Unicode 字符范围，并且将始终返回一个成功编码的字符串。

最后两个方法，`-getCharacters:(unichar*)buffer range:(NSRange)range`和`-getCharacters:(unichar*)buffer`，是`+stringWithCharacters:length:`的补充方法。两者都将 Unicode 值提取到`unichar`数组中。这些方法不执行任何编码。提取的`unichar`字符数量始终与源范围相同。

### 格式化字符串

从其他值创建字符串的方法多得令人眼花缭乱，但迄今为止最通用的方法是`+[NSString stringWithFormat:(NSString*)format, …]`。其在 Java 中的对应方法是`java.lang.String.format(String,Object…)`。它具有相同的用途和使用方式，但格式化字符串中的格式说明符略有不同。

与 Java 类似，格式字符串参数是结果字符串的模板，其中嵌入了使用参数列表中的值替换的格式说明符（例如`%d`）。与 Java 不同的是，可变参数列表中的值要么是对象指针，要么是未包装在对象中的原始值。实际上，将它们包装在对象中会严重限制`-stringWithFormat:`的实用性。避免将原始值转换为对象使得`-stringWithFormat:`使用起来更加方便——也更高效。以至于你会发现，在很多本应使用`java.lang.StringBuffer`或其他技术的地方，你会转而使用它。

格式说明符遵循以下通用语法：

```
%[argument_index$][flags][width][.precision][length]conversion
```

唯一显著的区别是可选的长度修饰符，Java 不需要它。常用的转换格式说明符及其 Java 等效项列于表 8-7 中。

**表 8-7.** 格式转换说明符

| Java 类型 | Objective-C 类型 | 替换为 |
| --- | --- | --- |
| `%s`/`%S` (对象) | `%@` | 对象的`-description` (`toString()`) |
| `%p` | 任意指针 | 十六进制内存地址 |
| `%s` | `char*` | 以 Null 结尾的 ASCII C 字符串 |
| `%S` | `unichar*` | 以 Null 结尾的 Unicode C 字符串 |
| `%c` | `char` | 8 位 ASCII 字符 |
| `%c` | `char` `%C` `unichar` | 16 位 Unicode |
| `%d` | `%d`/`%D`/`%i` `int` | 有符号十进制值 |
| `%u`/`%U` | `unsigned int` | 无符号十进制值 |
| `%o` | `%o`/`%O` `unsigned int` | 八进制值 |
| `%x`/`%X` | `%x`/`%X` `unsigned int` | 十六进制值 |
| `%e`/`%E` | `%e`/`%E` `float`, `double` | 科学记数法表示的值 |
| `%f` | `%f`/`%F` `float`, `double` | 十进制值 |
| `%g`/`%G` | `%g`/`%G` `float`, `double` | 十进制值或科学记数法表示的值 |
| `%a`/`%A` | `%a`/`%A` `float`, `double` | 十六进制值 |
| `%%` | `%%` | 字面量`%`字符 |

最显著的区别是 Objective-C 使用`%@`转换说明符来表示对象描述；这取代了 Java 的`%s`说明符。对所有 Objective-C 字符串和对象参数使用`%@`。所有其他格式说明符都期望一个原始标量或指针。标志、宽度和精度修饰符的行为与它们在 Java 中的行为大致相同。

C 语言的可变参数列表不提供关于各个参数的类型或大小信息。其机制在第 6 章的“可变参数”部分有解释。每个参数的类型和大小的推断依据是格式字符串中的长度和转换说明符。这带来了两个重要限制。

第一个限制是关于可选的参数索引修饰符：你不能“跳过”参数。以语句`[NSString stringWithFormat:@"%d-%3$d",1,2,3]`为例。格式字符串不提供关于第二个可变参数的类型或大小的信息。缺少此信息，格式化器便没有足够的信息来提取第三个可变参数，因此格式化操作会失败。

第二个限制是，长度修饰符和转换说明符的组合必须与参数的类型一致。格式化器盲目地假设每个参数都与说明符隐含的类型一致，并据此进行解释。这是导致乱码字符最常见的一个原因，有时还可能导致灾难性的运行时错误（例如，试图将一个整数解释为指向对象的指针）。表 8-8 列出了长度修饰符及每个修饰符所隐含的参数类型。如果省略了长度修饰符，则假定参数类型与表 8-7 中列出的类型兼容。

**表 8-8.** 转换长度修饰符

| 修饰符 | 适用的转换 | 参数类型 |
| --- | --- | --- |
| `hh` | `d`, `i`, `o`, `u`, `x`, `X` | `char`, `unsigned char` |
| `h` | `d`, `i`, `o`, `u`, `x`, `X` | `short int`, `unsigned short int` |
| `l` | `d`, `i`, `o`, `u`, `x`, `X` | `long int`, `unsigned long int` |
| `ll`, `q` | `d`, `i`, `o`, `u`, `x`, `X` | `long long int`, `unsigned long long int` |
| `L` | `a`, `A`, `e`, `E`, `f`, `F`, `g`, `G` | `long double` |



