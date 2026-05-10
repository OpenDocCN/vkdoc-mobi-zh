# 实现

在实现过程中，你可以将 `exclamationCount` 和 `setExclamationCount` 方法替换为一行代码，并将 `originalString` 和 `setOriginalString` 替换为第二行代码：

```
@implementation AwesomeString

@synthesize exclamationCount;
@synthesize originalString;

. . .
```

`synthesize` 快捷指令会促使编译器生成访问实例变量所需的代码。`@implementation` 中的代码是根据 `@interface` 中的 `@property` 定义生成的。你可能会厌倦输入所有这些 `@` 符号，但这些 `@synthesize` 语句帮你省去了编写 19 行代码的工作。

对于更大的类，代码行数的节省效果会更加显著。

---

