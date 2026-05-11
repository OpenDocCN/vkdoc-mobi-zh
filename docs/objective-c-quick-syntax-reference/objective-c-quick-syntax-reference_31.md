# 26. 错误处理

摘要

当程序遇到意外错误时，它们可能会行为异常或完全停止工作。理想情况下，程序员应该在程序投入使用之前发现并修复所有 bug，但在某些情况下，程序员无法完全掌控整个局面。例如，当程序需要的资源（如文件或网站）不存在时，就可能会发生错误。

## 错误处理定义

当程序遇到意外错误时，它们可能会行为异常或完全停止工作。理想情况下，程序员应该在程序投入使用之前发现并修复所有 bug，但在某些情况下，程序员无法完全掌控整个局面。例如，当程序需要的资源（如文件或网站）不存在时，就可能会发生错误。

处理这类情况的最佳实践是向程序添加错误处理。这意味着当错误发生时，程序可以恢复或优雅地关闭。`NSError` 是程序员用来处理错误的 Foundation 类。



## NSError

`NSError`经常在涉及文件操作时使用。许多 Foundation 类使用`NSError`对象来帮助处理错误。其模式是：声明一个`NSError`对象，在通过引用传递之前将其设置为`nil`。以下是`NSString`方法从文件内容创建字符串时的工作方式：

```
NSError *error = nil;
NSString *file = @"/Users/Shared/array.txt";
NSString *content = [NSString stringWithContentsOfFile:file
                               encoding:NSStringEncodingConversionAllowLossy
                                            error:&error];
```

在`error`参数前面看到的`&`符号称为 AddressOf 运算符。这意味着你传递的是对象的内存地址，而不是副本，因此当方法中的代码需要修改`error`对象时，你将能够看到结果。

要检查`error`对象，你可以在消息之后立即加入如下代码：

```
if(!error)
    NSLog(@"content = %@", content);
else
    NSLog(@"error = %@", error);
```

如果没有错误，则对内容进行处理；否则，处理错误。如果此代码成功，则日志会打印出以下内容：

```
content = <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<array>
    <string>A</string>
    <string>B</string>
    <string>C</string>
    <string>D</string>
</array>
</plist>
```

这个文件是我 Mac 上的一个文件，但实际内容无关紧要。如果你将文件名更改为 Mac 上不存在的文件，则会收到错误消息，如下所示：

```
error = Error Domain=NSCocoaErrorDomain Code=260 "The file "arrayf.txt" couldn’t be opened because there is no such file." UserInfo=0x10010a8a0 {NSFilePath=/Users/Shared/arrayf.txt, NSUnderlyingError=0x10010a650 "The operation couldn’t be completed. No such file or directory"}
```

此时，你应该使用应用程序用户界面提示用户寻求帮助。

## Try/Catch 语句

Try/catch 语句是另一种尝试捕获错误的方式。其思想是：你可以识别容易出错的代码区域，然后将这些区域包裹在一个称为 try 块的代码块中。你还可以识别一个称为 catch 块的代码块，该代码块会在 try 块中的代码失败时执行。

你还可以设置一个称为 finally 块的代码块，无论 try 块是否失败，该代码块都会执行。让我们通过读取一个仅包含四个元素的数组文件，然后尝试读取超出边界的第五个元素来尝试这一点。

```
NSArray *array = [NSArray arrayWithContentsOfFile:file];
@try {
    NSString *fifthItem = [array objectAtIndex:4];
    NSLog(@"fifthItem = %@", fifthItem);
}
@catch (NSException *exception) {
    NSLog(@"exception = %@", exception);
}
@finally {
    NSLog(@"Moving on...");
}
```

如果执行此代码，try 块会失败，控制权将转到 catch 块，并且日志会打印出一条消息：

```
exception = *** -[__NSArrayM objectAtIndex:]: index 4 beyond bounds [0 .. 3]
Moving on...
```

文本“Moving on...”会出现，因为它是 finally 块的一部分，并且无论如何都会执行。

