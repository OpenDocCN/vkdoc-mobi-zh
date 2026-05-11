# 11. Foundation 框架

## 摘要

在之前的几章中，你学习了 Objective-C 语言的基础知识，并掌握了如何使用它编写简单程序。在本章中，我将为你提供一份关于 Objective-C 中最常用类的快速参考。这些类包含在 Foundation 框架中，为编写完整的应用程序提供了大量基础支持。

本章第一部分介绍了 Foundation 框架提供的一些基础类。这些类（如 `NSString` 和 `NSValue`）在整个系统中被广泛使用，是构建其他一切功能的基础。

本章第二部分则提供了关于最常用集合类的快速参考。这些类用于存储对象集，并通过其他类和算法以更复杂的方式进行操作。

## 基础类

以下基础类在整个系统中被广泛使用，是构建其他一切功能的基础。

### NSObject

`NSObject` 类是 Cocoa 中大多数其他类的超类。`NSObject` 提供了在整个系统中使用的重要服务，例如内存分配、值观察和方法派发。在本节中，你将看到此类中重要方法的列表，以及如何与之交互。



* `+ (void)load`：在类首次加载时执行。该方法适用于需在应用生命周期内仅执行一次的任务。
* `+ (void)initialize`：在类被首次使用前调用。也就是说，该方法会在第一个非初始化/非加载方法执行前运行，通常在首次创建对象时触发。这与 `load` 方法不同，后者在类首次加载到内存时即被调用。
* `- (id)init`：默认初始化方法，通常用于 `alloc`/`init` 序列组合中。
* `+ (id)new`：可替代 `alloc`/`init` 组合使用。
* `+ (id)allocWithZone:(NSZone *)zone`：用于在由参数 `NSZone` 定义的内存区域中分配 `NSObject`，该参数决定了内存分配策略。
* `+ (id)alloc`：使用默认 `NSZone` 返回一个新的 `NSObject`。返回的对象未被初始化，因此 `alloc` 常与目标类的 `init` 方法组合使用。`alloc` 返回的对象归调用者所有，不再使用时需释放该对象。
* `- (void)dealloc`：释放对象占用的内存及其他资源。重写此方法时，应始终调用父类的版本，以避免基类出现资源泄漏。
* `- (void)finalize`：仅在启用垃圾回收时使用。其目标是在系统回收对象前释放对象使用的所有资源。使用 ARC 时，此方法已废弃。
* `- (id)copy`：返回一个与原对象同类的副本。与 `alloc` 和 `new` 类似，返回的对象归调用者所有，调用者需负责在不使用时释放对象，以避免内存泄漏。
* `- (id)mutableCopy`：返回目标对象的可变副本。此方法由需要为不可变对象创建可变副本的类实现。
* `+ (id)copyWithZone:(NSZone *)zone`：返回一个包含目标对象副本的新对象，使用 `zone` 参数作为 `allocWithZone:` 的参数。
* `+ (id)mutableCopyWithZone:(NSZone *)zone`：返回目标对象的一个可后续修改的副本。使用 `zone` 参数决定对象分配方式，类似于 `allocWithZone:`。
* `+ (Class)superclass`：返回一个标识目标对象父类的类对象（若目标对象派生自某个基类）。
* `+ (Class)class`：返回一个标识目标对象所属类的类对象。返回的对象之后可作为类消息的目标。例如，若 `obj` 是一个对象，可使用 `[[obj class] alloc]` 创建一个同一类的未初始化对象。
* `+ (BOOL)instancesRespondToSelector:(SEL)aSelector`：若目标类或其某个父类实现了给定的选择器，则返回 YES。
* `+ (BOOL)conformsToProtocol:(Protocol *)protocol`：若目标类或其某个父类实现了作为参数传入的协议，则返回 YES。
* `- (IMP)methodForSelector:(SEL)aSelector`：返回给定方法选择器的实现。`IMP` 值通常是一个函数指针，该函数在响应给定选择器时被调用。
* `+ (IMP)instanceMethodForSelector:(SEL)aSelector`：返回与给定选择器对应的实例方法的实现（若存在该实现）。之后可调用 `IMP` 值来为特定对象执行该选择器。
* `- (void)doesNotRecognizeSelector:(SEL)aSelector`：此方法可在子类中重写。用于判断当前类是否不识别某个选择器。类可根据自身希望对该选择器做出的响应返回不同的值。
* `- (id)forwardingTargetForSelector:(SEL)aSelector`：通过重写此方法，可为当前类无法直接处理的任何选择器定义一个目标对象。
* `- (void)forwardInvocation:(NSInvocation *)anInvocation`：将 `NSInvocation` 参数所代表的方法调用转发至其适当的目标。此方法通常在被要求将一个或多个消息转发给其他对象的类中重写。
* `- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector`：返回给定选择器的方法签名。此方法用于需要在运行时确定某个方法的参数或返回类型时。
* `+ (NSMethodSignature *)instanceMethodSignatureForSelector:(SEL)aSelector`：返回由给定选择器标识的实例方法的实现地址。
* `+ (NSString *)description`：返回用于描述对象的字符串。通常在需要此类服务的类中重写。不应依赖此方法返回的值进行对象识别；它最常用于调试。
* `+ (BOOL)isSubclassOfClass:(Class)aClass`：若目标类派生自作为参数传入的类，或与其相同，则返回 true。当需要使用此方法时，请先尝试在类层次结构中定义一个多态方法。

### NSString

`NSString` 存储不可变的字符序列。Objective-C 应用程序中的大多数文本数据都存储为 `NSString` 对象。`NSString` 类提供了丰富的接口；实际上，许多框架选择通过使用协议，用额外方法丰富 `NSString` 接口。以下是 `NSString` 类中最常见方法的列表：



- `- (id)init`; 是一个简单的初始化器，返回一个空的 `NSString` 对象。
- `- (id)initWithCharactersNoCopy:(unichar *)characters length:(NSUInteger)length freeWhenDone:(BOOL)freeBuffer`; 使用指向字符数组的指针初始化字符串，长度由参数 `length` 定义。参数 `freeBuffer` 决定在执行拷贝后是否应释放缓冲区。
- `- (id)initWithCharacters:(const unichar *)characters length:(NSUInteger)length`; 使用作为参数传入的字符来初始化目标 `NSString` 对象。
- `- (id)initWithUTF8String:(const char *)nullTerminatedCString`; 使用作为参数传入的以空字符结尾的 C 字符串来初始化字符串。
- `- (id)initWithString:(NSString *)aString`; 使用另一个同类型的对象来初始化 `NSString`。
- `- (id)initWithFormat:(NSString *)format, ...`; 使用格式化字符串来初始化字符串。格式化字符串遵循与 `NSLog` 参数相同的约定，并且是 C 标准库中 `printf` 函数所用格式的扩展。请参考 `NSLog` 的文档了解具体格式。
- `- (id)initWithFormat:(NSString *)format arguments:(va_list)argList`; 与上面的初始化器类似，但参数以 `va_list` 形式传递。
- `- (id)initWithFormat:(NSString *)format locale:(id)locale, ...`; 使用格式化字符串初始化 `NSString`。但格式参数将根据作为参数传入的 `locale` 对象进行解释。
- `- (id)initWithFormat:(NSString *)format locale:(id)locale arguments:(va_list)argList`; 与上面类似，但格式参数以 `va_list` 形式传递，而不是直接作为一组值。
- `- (id)initWithData:(NSData *)data encoding:(NSStringEncoding)encoding`; 使用包含编码后字符串的 `NSData` 对象来初始化 `NSString` 对象。编码由第二个参数决定。
- `- (id)initWithBytes:(const void *)bytes length:(NSUInteger)len encoding:(NSStringEncoding)encoding`; 使用作为指针传入的字节数组来初始化 `NSString`。第二个参数确定数组 `bytes` 的长度。
- `- (id)initWithBytesNoCopy:(void *)bytes length:(NSUInteger)len encoding:(NSStringEncoding)encoding freeWhenDone:(BOOL)freeBuffer`; 使用字节数组初始化 `NSString`，长度由第二个参数决定。encoding 参数用于指定数据的编码。最后一个参数可用于确定初始化结束时是否释放缓冲区。
- `+ (id)string`; 是一个类方法，返回一个使用 `init` 方法初始化的 `NSString` 对象。可用于快速返回一个新字符串。遵循 Objective-C 的内存分配规则，调用者不拥有此方法返回的字符串。
- `+ (id)stringWithString:(NSString *)string`; 返回一个 `NSString` 对象，该对象使用一个已有的字符串初始化。
- `+ (id)stringWithCharacters:(const unichar *)characters length:(NSUInteger)length`; 返回一个新的 `NSString` 对象，该对象使用 `characters` 所指向数组的内容进行初始化。数组的长度由第二个参数决定。
- `+ (id)stringWithUTF8String:(const char *)nullTerminatedCString`; 返回一个新的 `NSString` 对象，该对象使用以空字符结尾的 C 字符串初始化。
- `+ (id)stringWithFormat:(NSString *)format, ...`; 返回一个新字符串，其格式类似于 `NSLog` 使用的格式。请查阅 `NSLog` 文档以了解格式语法定义的详细信息。
- `+ (id)localizedStringWithFormat:(NSString *)format, ...`; 使用格式化字符串返回一个新的字符串对象，与上一个方法类似。格式参数将使用用户当前的默认语言环境进行本地化。
- `- (id)initWithCString:(const char *)nullTerminatedCString encoding:(NSStringEncoding)encoding`; 使用以空字符结尾的 C 字符数组初始化字符串，该数组将根据作为第二个参数传入的编码进行解释。
- `+ (id)stringWithCString:(const char *)cString encoding:(NSStringEncoding)enc`; 返回一个新字符串，并使用一个已有的以空字符结尾的字符数组对其进行初始化，该数组将根据作为第二个参数传入的编码进行解释。
- `- (id)initWithContentsOfURL:(NSURL *)url encoding:(NSStringEncoding)enc error:(NSError **)error`; 使用给定 URL 的内容初始化字符串。例如，这可用于加载文件或网页的内容。它使用作为第二个参数提供的编码。如果发生失败，此函数可能返回一个错误对象。
- `- (id)initWithContentsOfFile:(NSString *)path encoding:(NSStringEncoding)enc error:(NSError **)error`; 使用文件的内容初始化字符串，文件的路径由第一个参数决定，并使用给定的编码。如果出错，此函数也可能返回一个错误对象。
- `+ (id)stringWithContentsOfURL:(NSURL *)url encoding:(NSStringEncoding)enc error:(NSError **)error`; 返回一个新字符串，该字符串使用第一个参数中传入的 URL 的内容，并使用给定的编码。如果失败，可能返回一个错误对象。
- `+ (id)stringWithContentsOfFile:(NSString *)path encoding:(NSStringEncoding)enc error:(NSError **)error`; 返回一个新字符串，该字符串使用文件的内容初始化，文件的路径在第一个参数中给出。文件使用给定的编码进行解释。如果发生失败，可能返回一个错误对象。
- `- (id)initWithContentsOfURL:(NSURL *)url usedEncoding:(NSStringEncoding *)enc error:(NSError **)error`; 使用给定 URL 对象的内容初始化新字符串。URL 的内容使用第二个参数进行编码。如果失败，可能返回一个错误对象。
- `- (id)initWithContentsOfFile:(NSString *)path usedEncoding:(NSStringEncoding *)enc error:(NSError **)error`; 使用由 `path` 参数确定的文件内容初始化字符串对象。所使用的编码在第二个参数中给出。最后，如果失败，可能返回一个错误对象。
- `+ (id)stringWithContentsOfURL:(NSURL *)url usedEncoding:(NSStringEncoding *)enc error:(NSError **)error`; 返回一个新字符串，该字符串使用给定 URL 的内容初始化，该 URL 可以是本地文件或远程资源。内容使用第二个参数进行编码，如果失败，可能返回一个错误对象。
- `+ (id)stringWithContentsOfFile:(NSString *)path usedEncoding:(NSStringEncoding *)enc error:(NSError **)error`; 返回一个新对象。该字符串使用存储在给定路径中的文件内容进行初始化，并使用作为第二个参数传入的编码。如果失败，可能返回一个错误对象。
- `- (NSString *)stringByAppendingString:(NSString *)aString`; 返回一个新的字符串对象。目标字符串与参数 `aString` 连接，并作为新对象返回。
- `- (NSString *)stringByAppendingFormat:(NSString *)format, ...`; 返回一个新字符串。当前目标字符串的内容与作为第一个参数传入的格式化字符串连接在一起。格式字符串可能附带额外的参数，这些参数将传递给该方法。
- `- (__strong const char *)cStringUsingEncoding:(NSStringEncoding)encoding`; 返回一个以空字符结尾的字符数组（传统上称为 C 字符串），使用作为第一个参数提供的编码。调用者不拥有此字符串，如果你打算稍后使用它，应复制该字符串。
- `- (BOOL)getCString:(char *)buffer maxLength:(NSUInteger)maxBufferCount encoding:(NSStringEncoding)encoding`; 将当前的字符串内容复制到一个字符数组中。数组的大小通过 `maxBufferCount` 参数传递。所使用的编码作为第三个参数传递。
- `- (BOOL)getBytes:(void *)buffer maxLength:(NSUInteger)maxBufferCount usedLength:(NSUInteger *)usedBufferCount encoding:(NSStringEncoding)encoding options:(NSStringEncodingConversionOptions)options range:(NSRange)range remainingRange:(NSRangePointer)leftover`; 也将当前字符串的内容复制到给定的字符数组中，但它提供了更多选项。除了最大长度和编码之外，你还可以传递一个整数，该整数将更新为已使用的字符数；一个 options 参数，提供解释编码的可能方式；一个要复制的给定字符范围；以及最终一个指向范围的指针，该范围将更新为剩余的字符范围。
- `- (NSString *)stringByFoldingWithOptions:(NSStringCompareOptions)options locale:(NSLocale *)locale`; 返回一个新的字符串对象，并应用了折叠选项。同时应用搜索选项以及第三个参数中的语言环境。
- `- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement options:(NSStringCompareOptions)options range:(NSRange)searchRange`; 返回一个基于当前字符串的新字符串对象。新字符串中所有出现的给定 `target` 都被替换为 `replacement` 字符串。你可以为替换传递额外的选项，其类型为 `NSStringCompareOptions`。最后，你可以使用最后一个参数确定替换的范围。
- `- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement`; 是上一个方法的简化版本。通过将所有出现的 `target` 替换为 `replacement` 字符串来返回一个新字符串。
- `- (NSString *)stringByReplacingCharactersInRange:(NSRange)range withString:(NSString *)replacement`; 返回一个基于当前字符串值的新字符串，其中第一个参数确定的范围被第二个参数中的替换字符串替换。
- `- (__strong const char *)UTF8String`; 返回一个以空字符结尾的字符数组（传统的 C 字符串），表示当前字符串的内容。此方法假设你正在使用 UTF8 编码。返回的字符数组不为调用者所拥有，因此如果你稍后想要使用该字符串，应复制一份。
- `+ (NSStringEncoding)defaultCStringEncoding`; 返回 `NSString` 对象的默认编码。此方法可用作检索当前编码的便捷方式。
- `+ (const NSStringEncoding *)availableStringEncodings`; 返回当前使用的 `NSString` 编码列表。编码序列以空字符结尾。
- `+ (NSString *)localizedNameOfStringEncoding:(NSStringEncoding)encoding`; 返回作为参数传入的编码的本地化名称。
- `- (NSUInteger)length`; 返回字符串中的字符数。
- `- (unichar)characterAtIndex:(NSUInteger)index`; 返回存储在由 index 给定位置处的单个字符。如果索引无效，则会生成一个 `NSRangeException`。
- `- (void)getCharacters:(unichar *)buffer range:(NSRange)aRange`; 将由 `range` 确定的字符序列复制到给定的缓冲区中。该方法假设缓冲区有足够的空间来容纳指定范围内的所有字符。
- `- (NSString *)substringFromIndex:(NSUInteger)from`; 返回一个新字符串，该字符串使用当前字符串的内容初始化，从第一个参数确定的位置开始。
- `- (NSString *)substringToIndex:(NSUInteger)to`; 返回一个新字符串，该字符串使用当前字符串的内容初始化，从第一个字符开始，到第二个参数中给定的索引处结束。
- `- (NSString *)substringWithRange:(NSRange)range`; 返回一个使用当前字符串内容初始化的新字符串。所使用的子字符串由 `range` 参数确定。
- `- (NSComparisonResult)compare:(NSString *)string`; 返回比较当前字符串和作为参数传入的字符串的结果。
- `- (NSComparisonResult)compare:(NSString *)string options:(NSStringCompareOptions)mask`; 返回比较当前字符串和作为参数传入的字符串的结果。你还可以提供选项来确定应如何比较字符串。
- `- (NSComparisonResult)compare:(NSString *)string options:(NSStringCompareOptions)mask range:(NSRange)compareRange`; 返回比较当前字符串的某个范围与作为参数传入的字符串的结果，使用给定的比较选项。
- `- (NSComparisonResult)compare:(NSString *)string options:(NSStringCompareOptions)mask range:(NSRange)compareRange locale:(id)locale`; 将目标字符串的给定范围与作为第一个参数传入的字符串进行比较。此函数使用一组比较选项，并使用给定的语言环境进行比较。
- `- (NSComparisonResult)caseInsensitiveCompare:(NSString *)string`; 使用不区分大小写的比较来比较当前字符串和作为参数传入的字符串。
- `- (NSComparisonResult)localizedCompare:(NSString *)string`; 使用当前语言环境比较目标字符串和作为参数传入的字符串。
- `- (NSComparisonResult)localizedCaseInsensitiveCompare:(NSString *)string`; 比较目标字符串和作为第一个参数传入的字符串。比较使用当前语言环境选项执行。
- `- (NSComparisonResult)localizedStandardCompare:(NSString *)string`; 比较目标字符串和作为第一个参数传入的字符串。比较方法使用了当前语言环境定义。
- `- (BOOL)isEqualToString:(NSString *)aString`; 当目标字符串和参数字符串具有相同内容时返回 YES。
- `- (BOOL)hasPrefix:(NSString *)aString`; 如果目标字符串以作为参数传入的字符串开头，则返回 YES。
- `- (BOOL)hasSuffix:(NSString *)aString`; 如果目标字符串以作为参数传入的字符串结尾，则返回 YES。
- `- (NSRange)rangeOfString:(NSString *)aString`; 如果 `aString` 是目标 `NSString` 的子字符串，则此方法将其位置作为 `NSRange` 返回。
- `- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask`; 如果 `aString` 是目标 `NSString` 的子字符串，则将其位置作为 `NSRange` 返回。比较将根据作为参数 `mask` 传入的选项执行。
- `- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask range:(NSRange)searchRange`; 如果 `aString` 是目标 `NSString` 的子字符串并且包含在范围 `searchRange` 内，则将其位置作为 `NSRange` 返回。比较将根据作为参数 `mask` 传入的选项执行。
- `- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask range:(NSRange)searchRange locale:(NSLocale *)locale`; 如果 `aString` 是目标 `NSString` 的子字符串并且包含在范围 `searchRange` 内，则将其位置作为 `NSRange` 返回。比较将使用参数 `locale` 进行本地化。
- `- (double)doubleValue`; 尝试将目标字符串的内容转换为 `double`，同时跳过开头的空白字符。
- `- (float)floatValue`; 尝试将目标字符串的内容转换为 `float`，同时跳过开头的空白字符。
- `- (int)intValue`; 尝试将目标字符串的内容转换为 `int`，同时跳过开头的空白字符。
- `- (NSInteger)integerValue`; 尝试将目标字符串的内容转换为 `NSInteger`，同时跳过开头的空白字符。
- `- (long long)longLongValue`; 尝试将目标字符串的内容转换为 `long long`，同时跳过开头的空白字符。
- `- (BOOL)boolValue`; 尝试将目标字符串的内容转换为 `BOOL`，同时跳过开头的空白字符。
- `- (NSArray *)componentsSeparatedByString:(NSString *)separator`; 返回一个数组，其中每个元素都是目标字符串的一个组成部分。这些组成部分由作为参数传入的分隔符确定。
- `- (NSArray *)componentsSeparatedByCharactersInSet:(NSCharacterSet *)separator`; 返回一个数组，其中每个元素都是目标字符串的一个组成部分。这些组成部分通过检查作为参数传入的分隔符集合中的每个成员来确定。
- `- (NSString *)commonPrefixWithString:(NSString *)aString options:(NSStringCompareOptions)mask`; 返回一个新字符串，该字符串对应于目标字符串和参数 `aString` 共享的最大公共前缀。
- `- (NSString *)uppercaseString`; 返回一个新字符串，其内容为当前字符串的大写形式。
- `- (NSString *)lowercaseString`; 返回一个新字符串，其内容为目标字符串的小写形式。
- `- (NSString *)capitalizedString`; 返回一个新字符串，其内容为目标字符串的首字母大写形式。
- `- (NSString *)uppercaseStringWithLocale:(NSLocale *)locale`; 返回一个新字符串，其内容为目标字符串的大写形式，并使用当前语言环境。
- `- (NSString *)lowercaseStringWithLocale:(NSLocale *)locale`; 返回一个新字符串，其内容为目标字符串的小写形式，并使用当前语言环境。
- `- (NSString *)capitalizedStringWithLocale:(NSLocale *)locale`; 返回一个新字符串，其内容为目标字符串的首字母大写形式，并使用当前语言环境。
- `- (NSString *)stringByTrimmingCharactersInSet:(NSCharacterSet *)set`; 返回一个基于目标字符串内容的新字符串。结果 `NSString` 中会移除作为参数传入的集合中包含的字符。
- `- (NSString *)stringByPaddingToLength:(NSUInteger)newLength withString:(NSString *)padString startingAtIndex:(NSUInteger)padIndex`; 返回一个新字符串，该字符串通过使用一个填充字符串（作为第二个参数传入）填充目标字符串的内容来创建。填充后字符串的最终长度由参数 `newLength` 给出。你还可以确定进行填充的起始索引。
- `- (void)getLineStart:(NSUInteger *)startPtr end:(NSUInteger *)lineEndPtr contentsEnd:(NSUInteger *)contentsEndPtr forRange:(NSRange)range`; 返回关于包含在作为最后一个参数给定的范围中的行的起始和结束信息。
- `- (NSRange)lineRangeForRange:(NSRange)range`; 返回一个范围，该范围确定包含在作为参数传递给此方法的原始范围中的行的起始和结束位置。
- `- (void)getParagraphStart:(NSUInteger *)startPtr end:(NSUInteger *)parEndPtr contentsEnd:(NSUInteger *)contentsEndPtr forRange:(NSRange)range`; 此方法用于根据由 `range` 参数指定的范围计算段落的起始位置。关于段落起始和结束位置的信息通过作为前三个参数传入的指针进行保存。
- `- (NSRange)paragraphRangeForRange:(NSRange)range`; 计算输入范围内包含一个或多个段落的字符范围。
- `- (void)enumerateSubstringsInRange:(NSRange)range options:(NSStringEnumerationOptions)opts usingBlock:(void (^)(NSString *substring, NSRange substringRange, NSRange enclosingRange, BOOL *stop))block`; 使用作为参数传入的块来枚举目标字符串的所有子字符串。该块应用于指定范围，并使用由参数 `options` 确定的枚举选项。
- `- (void)enumerateLinesUsingBlock:(void (^)(NSString *line, BOOL *stop))block`; 使用作为参数传入的块来枚举当前字符串中的每一行，其中行由换行符分隔。
- `- (NSString *)description`; 返回字符串的简短描述。这通常仅用于调试目的。
- `- (NSUInteger)hash`; 返回一个可用作哈希值的整数值，例如，当将字符串插入哈希表时。
- `- (BOOL)writeToURL:(NSURL *)url atomically:(BOOL)useAuxiliaryFile encoding:(NSStringEncoding)enc error:(NSError **)error`; 写入作为第一个参数传入的 URL 的内容。在最终复制到目标之前，可能会使用一个辅助文件来存储数据。使用给定的编码，并在失败时返回一个错误对象。
- `- (BOOL)writeToFile:(NSString *)path atomically:(BOOL)useAuxiliaryFile encoding:(NSStringEncoding)enc error:(NSError **)error`; 将当前字符串的内容写入文件，文件位置由 `path` 参数确定。第二个参数确定在写入过程中是否应使用辅助文件。使用的编码由第三个参数确定。如果失败，将返回一个错误对象。



### NSMutableString

`NSMutableString`类是`NSString`的子类，允许修改其内容。因此，`NSString`可用的所有方法对`NSMutableString`同样有效，但你也可以直接修改字符串的内容，而无需为中间结果创建新的字符串。此类用于需要操作给定字符串中存储的值，或创建由两个或多个来源收集的数据计算出的字符串的情况。以下是其值得注意的方法列表：

- `- (void)replaceCharactersInRange:(NSRange)range withString:(NSString *)aString;`：将指定范围内的所有字符替换为作为第二个参数传入的字符串。
- `- (void)insertString:(NSString *)aString atIndex:(NSUInteger)loc;`：将第一个参数传入的字符串内容插入到目标字符串中，从参数`loc`指定的位置开始。
- `- (void)deleteCharactersInRange:(NSRange)range;`：用于删除由`range`参数中位置定义的子字符序列。
- `- (void)appendString:(NSString *)aString;`：使用`aString`参数中包含的字符扩充当前字符串。
- `- (void)appendFormat:(NSString *)format, ...;`：使用提供的格式说明符将字符串追加到当前字符串的末尾。格式与`NSLog`使用的格式类似。
- `- (void)setString:(NSString *)aString;`：使用`aString`参数重置字符串中存储的字符。
- `- (id)initWithCapacity:(NSUInteger)capacity;`：使用第一个参数中指示的容量初始化目标`NSMutableString`对象。
- `+ (id)stringWithCapacity:(NSUInteger)capacity;`：是一个类方法，创建并返回一个新的字符串，使用给定的容量进行初始化。
- `- (NSUInteger)replaceOccurrencesOfString:(NSString *)target withString:(NSString *)replacement options:(NSStringCompareOptions)options range:(NSRange)searchRange;`：通过将`target`参数的出现替换为第二个参数定义的`replacement`来更改可变字符串。

### NSValue

`NSValue`为作为其他数据类型包装器的对象提供了抽象接口。`NSValue`被用作其他重要类（如`NSNumber`和`NSData`）的基础。以下是其主要方法的列表：

- `- (void)getValue:(void *)value;`：将`NSValue`中存储的内部值复制到给定的存储区域。
- `- (const char *)objCType;`：返回一个以空字符结尾的字符数组（C 字符串），该数组编码了`NSValue`中存储的数据类型。此类型描述被`NSValue`中的其他方法使用。
- `- (id)initWithBytes:(const void *)value objCType:(const char *)type;`：使用`value`参数中传入的数据初始化`NSValue`。此方法还将数据类型设置为参数`type`。注意，不应手动生成`type`；而应使用`@encode`关键字查找与现有类型对应的字符串。
- `+ (NSValue *)valueWithBytes:(const void *)value objCType:(const char *)type;`：返回一个新的`NSValue`对象，其中包含通过`value`参数地址传入的数据。此方法还将数据类型设置为参数`type`。注意，不应手动生成`type`；而应使用`@encode`关键字查找与现有类型对应的字符串。
- `+ (NSValue *)value:(const void *)value withObjCType:(const char *)type;`：类似于上述方法。
- `+ (NSValue *)valueWithNonretainedObject:(id)anObject;`：创建一个新的`NSValue`，使用`anObject`作为其初始值。使用此方法的优点是它不会保留传入的对象，因此当与其他容器一起使用时可能很有用。
- `- (id)nonretainedObjectValue;`：将包含的数据作为`id`返回（内部被视为指针）。
- `+ (NSValue *)valueWithPointer:(const void *)pointer;`：创建一个新的`NSValue`对象，使用作为参数传入的指针作为内容。如果你想在容器中存储指针，此方法很有用。
- `- (void *)pointerValue;`：将目标`NSValue`对象中包含的数据作为指针返回。
- `- (BOOL)isEqualToValue:(NSValue *)value;`：如果目标对象与作为参数传入的`value`相等，则返回`YES`。比较使用存储值的适当类型进行。

### NSNumber

`NSNumber`是用于操作原生数字的基本类。`NSNumber`的最大用例是在 Objective-C 集合中存储原生数字数据类型。此类充当数字子类的外壳，因此为所有子类提供了通用接口，这样你只需使用一个数字类。



- `- (id)initWithChar:` `(char)value`; 使用一个字符（`char`）值来初始化 `NSNumber` 对象。
- `- (id)initWithUnsignedChar:` `(unsigned char)value`; 使用一个无符号字符（`unsigned char`）值来初始化 `NSNumber` 对象。
- `- (id)initWithShort:` `(short)value`; 使用一个短整型（`short`）值来初始化 `NSNumber` 对象。
- `- (id)initWithUnsignedShort:` `(unsigned short)value`; 使用一个无符号短整型（`unsigned short`）值来初始化 `NSNumber` 对象。
- `- (id)initWithInt:` `(int)value`; 使用一个整型（`int`）值来初始化 `NSNumber` 对象。
- `- (id)initWithUnsignedInt:` `(unsigned int)value`; 使用一个无符号整型（`unsigned int`）值来初始化 `NSNumber` 对象。
- `- (id)initWithLong:` `(long)value`; 使用一个长整型（`long`）值来初始化 `NSNumber` 对象。
- `- (id)initWithUnsignedLong:` `(unsigned long)value`; 使用一个无符号长整型（`unsigned long`）值来初始化 `NSNumber` 对象。
- `- (id)initWithLongLong:` `(long long)value`; 使用一个长长整型（`long long`）值来初始化 `NSNumber` 对象。
- `- (id)initWithUnsignedLongLong:` `(unsigned long long)value`; 使用一个无符号长长整型（`unsigned long long`）值来初始化 `NSNumber` 对象。
- `- (id)initWithFloat:` `(float)value`; 使用一个浮点型（`float`）值来初始化目标 `NSNumber` 对象。
- `- (id)initWithDouble:` `(double)value`; 使用一个双精度浮点型（`double`）值来初始化目标 `NSNumber` 对象。
- `- (id)initWithBool:` `(BOOL)value`; 使用一个布尔型（`BOOL`）值来初始化目标 `NSNumber` 对象。
- `- (id)initWithInteger:` `(NSInteger)value`; 使用一个 `NSInteger` 值来初始化目标 `NSNumber` 对象。
- `- (id)initWithUnsignedInteger:` `(NSUInteger)value`; 使用一个 `NSUInteger` 值来初始化目标 `NSNumber` 对象。
- `+ (NSNumber *)numberWithChar:` `(char)value`; 返回一个新的 `NSNumber` 对象，并使用 `char` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedChar:` `(unsigned char)value`; 返回一个新的 `NSNumber` 对象，并使用 `unsigned char` 类型的值进行初始化。
- `+ (NSNumber *)numberWithShort:` `(short)value`; 返回一个新的 `NSNumber` 对象，并使用 `short` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedShort:` `(unsigned short)value`; 返回一个新的 `NSNumber` 对象，并使用 `unsigned short` 类型的值进行初始化。
- `+ (NSNumber *)numberWithInt:` `(int)value`; 返回一个新的 `NSNumber` 对象，并使用 `int` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedInt:` `(unsigned int)value`; 返回一个新的 `NSNumber` 对象，并使用 `unsigned int` 类型的值进行初始化。
- `+ (NSNumber *)numberWithLong:` `(long)value`; 返回一个新的 `NSNumber` 对象，并使用 `long` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedLong:` `(unsigned long)value`; 返回一个新的 `NSNumber` 对象，并使用 `unsigned long` 类型的值进行初始化。
- `+ (NSNumber *)numberWithLongLong:` `(long long)value`; 返回一个新的 `NSNumber` 对象，并使用 `long long` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedLongLong:` `(unsigned long long)value`; 返回一个新的 `NSNumber` 对象，并使用 `unsigned long long` 类型的值进行初始化。
- `+ (NSNumber *)numberWithFloat:` `(float)value`; 返回一个新的 `NSNumber` 对象，并使用 `float` 类型的值进行初始化。
- `+ (NSNumber *)numberWithDouble:` `(double)value`; 返回一个新的 `NSNumber` 对象，并使用 `double` 类型的值进行初始化。
- `+ (NSNumber *)numberWithBool:` `(BOOL)value`; 返回一个新的 `NSNumber` 对象，并使用 `BOOL` 类型的值进行初始化。
- `+ (NSNumber *)numberWithInteger:` `(NSInteger)value`; 返回一个新的 `NSNumber` 对象，并使用 `NSInteger` 类型的值进行初始化。
- `+ (NSNumber *)numberWithUnsignedInteger:` `(NSUInteger)value`; 返回一个新的 `NSNumber` 对象，并使用 `NSUInteger` 类型的值进行初始化。
- `- (char)charValue`; 返回存储在目标对象中的 `char` 值。
- `- (unsigned char)unsignedCharValue`; 返回存储在目标对象中的无符号 `char` 值。
- `- (short)shortValue`; 返回存储在目标对象中的 `short` 值。
- `- (unsigned short)unsignedShortValue`; 返回存储在目标对象中的 `unsigned short` 值。
- `- (int)intValue`; 返回存储在目标对象中的 `int` 值。
- `- (unsigned int)unsignedIntValue`; 返回存储在目标对象中的 `unsigned int` 值。
- `- (long)longValue`; 返回存储在目标对象中的 `long` 值。
- `- (unsigned long)unsignedLongValue`; 返回存储在目标对象中的 `unsigned long` 值。
- `- (long long)longLongValue`; 返回存储在目标对象中的 `long long` 值。
- `- (unsigned long long)unsignedLongLongValue`; 返回存储在目标对象中的 `unsigned long long` 值。
- `- (float)floatValue`; 返回存储在目标对象中的 `float` 值。
- `- (double)doubleValue`; 返回存储在目标对象中的 `double` 值。
- `- (BOOL)boolValue`; 返回存储在目标对象中的 `bool` 值。
- `- (NSInteger)integerValue`; 返回存储在目标对象中的整数值。
- `- (NSUInteger)unsignedIntegerValue`; 返回存储在目标对象中的 `unsigned int` 值。
- `- (NSString *)stringValue`; 返回存储在目标对象中的 `NSString` 值。
- `- (NSComparisonResult)compare:` `(NSNumber *)otherNumber`; 此方法返回一个 `NSComparisonResult` 结果，用于确定目标对象与给定参数的比较情况。
- `- (BOOL)isEqualToNumber:` `(NSNumber *)number`; 如果目标对象是一个数字，且其值与传入参数相等，则返回 `true`。
- `- (NSString *)descriptionWithLocale:` `(id)locale`; 返回一个描述该对象的字符串。该字符串会根据给定的区域设置进行编码。

## `NSData`

`NSData` 是一个通用类，为任意类型的数据提供了一个封装容器。例如，这些数据可以通过 C 风格的字节数组进行存储。`NSData` 负责维护其包含的数据的存储。其最重要的功能是定义了一个基于面向对象（OO）的接口，可用于，例如，将数据传递给其他对象，或将数据存储在标准容器中。以下是 `NSData` 所实现的方法列表：



- `- (NSUInteger)length`: 返回`NSData`对象的长度。
- `- (const void *)bytes`: 以 C 风格字节数组的形式返回`NSData`对象中存储的内容。
- `- (NSString *)description`: 返回`NSData`对象的简短描述，通常用于调试目的。
- `- (void)getBytes:(void *)buffer length:(NSUInteger)length`: 将`NSData`对象内部存储的字节复制到作为第一个参数传入的缓冲区中。要复制的最大字节数由`length`参数指定。
- `- (void)getBytes:(void *)buffer range:(NSRange)range`: 将`NSData`对象中指定范围内的字节复制到作为第一个参数传入的缓冲区中。
- `- (BOOL)isEqualToData:(NSData *)other`: 如果目标`NSData`对象与给定的`NSData`对象相等，则返回`YES`。
- `- (NSData *)subdataWithRange:(NSRange)range`: 返回一个新的`NSData`对象，其中包含目标`NSData`中由`range`参数指定的一部分数据。
- `- (BOOL)writeToFile:(NSString *)path atomically:(BOOL)useAuxiliaryFile`: 将`NSData`对象的内容写入由`path`参数指定的文件。如果`useAuxiliaryFile`为 true，则会使用一个临时文件来存储数据传输的中间阶段。
- `- (BOOL)writeToURL:(NSURL *)url atomically:(BOOL)atomically`: 将`NSData`对象的内容写入由给定 URL 指定的文件或网络资源。如果`useAuxiliaryFile`为 true，则会使用一个临时文件来存储数据传输的中间阶段。
- `- (BOOL)writeToFile:(NSString *)path options:(NSDataWritingOptions)writeOptionsMask error:(NSError **)errorPtr`: 将`NSData`对象的内容写入由给定路径指定的文件。如果`useAuxiliaryFile`为 true，则会使用一个临时文件来存储数据传输的中间阶段。会使用数据写入选项，并在失败时返回一个错误对象。
- `- (BOOL)writeToURL:(NSURL *)url options:(NSDataWritingOptions)writeOptionsMask error:(NSError **)errorPtr`: 将`NSData`对象的内容写入由给定 URL 指定的文件或网络资源。如果`useAuxiliaryFile`为 true，则会使用一个临时文件来存储数据传输的中间阶段。会使用数据写入选项，并在失败时返回一个错误对象。
- `- (NSRange)rangeOfData:(NSData *)dataToFind options:(NSDataSearchOptions)mask range:(NSRange)searchRange`: 在目标`NSData`对象中，使用`mask`参数指定的搜索选项，在`searchRange`确定的范围内查找给定的`dataToFind`。如果找到数据，则返回其范围。
- `+ (id)data`: 创建一个新的、空的`NSData`对象。
- `+ (id)dataWithBytes:(const void *)bytes length:(NSUInteger)length`: 创建一个新的`NSData`对象，并使用大小为`length`的 C 风格字节数组进行初始化。
- `+ (id)dataWithBytesNoCopy:(void *)bytes length:(NSUInteger)length`: 创建一个新的`NSData`对象，并使用大小为`length`的 C 风格字节数组进行初始化。此方法不会复制该数组。
- `+ (id)dataWithBytesNoCopy:(void *)bytes length:(NSUInteger)length freeWhenDone:(BOOL)b`: 创建一个新的`NSData`对象，并使用大小为`length`的 C 风格字节数组进行初始化。最后一个参数决定了当对象被释放时，该数组是否被释放。
- `+ (id)dataWithContentsOfFile:(NSString *)path options:(NSDataReadingOptions)readOptionsMask error:(NSError **)errorPtr`: 使用给定路径的文件内容创建一个新的`NSData`对象。使用读取选项，并在失败时返回一个错误对象。
- `+ (id)dataWithContentsOfURL:(NSURL *)url options:(NSDataReadingOptions)readOptionsMask error:(NSError **)errorPtr`: 使用给定 URL 的文件或网络资源内容创建一个新的`NSData`对象。使用读取选项，并在失败时返回一个错误对象。
- `+ (id)dataWithContentsOfFile:(NSString *)path`: 使用`path`指定的文件内容创建一个新的`NSData`对象。
- `+ (id)dataWithContentsOfURL:(NSURL *)url`: 使用`url`指定的文件或网络资源内容创建一个新的`NSData`对象。
- `- (id)initWithBytes:(const void *)bytes length:(NSUInteger)length`: 使用 C 风格字节数组初始化目标`NSData`对象，数组长度由第二个参数指定。
- `- (id)initWithBytesNoCopy:(void *)bytes length:(NSUInteger)length`: 使用 C 风格字节数组初始化目标`NSData`对象，数组长度由第二个参数指定。
- `- (id)initWithBytesNoCopy:(void *)bytes length:(NSUInteger)length freeWhenDone:(BOOL)b`: 使用 C 风格数据数组初始化目标`NSData`对象，数组长度由第二个参数指定。如果最后一个参数为`YES`，则当数据不再使用时，该数组会被释放。
- `- (id)initWithContentsOfFile:(NSString *)path options:(NSDataReadingOptions)readOptionsMask error:(NSError **)errorPtr`: 使用`path`指定的文件中存储的数据初始化目标`NSData`对象。使用读取选项，并在失败时返回一个错误对象。
- `- (id)initWithContentsOfURL:(NSURL *)url options:(NSDataReadingOptions)readOptionsMask error:(NSError **)errorPtr`: 使用`url`指定的文件或网络资源中存储的数据初始化目标`NSData`对象。使用读取选项，并在失败时返回一个错误对象。
- `- (id)initWithContentsOfFile:(NSString *)path`: 使用`path`指定的文件中存储的数据初始化目标`NSData`对象。
- `- (id)initWithContentsOfURL:(NSURL *)url`: 使用`url`指定的文件或网络资源中存储的数据初始化目标`NSData`对象。
- `- (id)initWithData:(NSData *)data`: 使用传入的`NSData`对象初始化目标`NSData`对象。
- `+ (id)dataWithData:(NSData *)data`: 返回一个新的`NSData`对象，并使用传入的`NSData`对象进行初始化。



### NSMutableData

`NSMutableData`是`NSData`的子类，可用于原地修改数据。因此，`NSMutableData`可在任何需要`NSData`的场景中使用。以下是可用的方法：

- `- (void *)mutableBytes`；返回指向目标对象中存储的数据的指针。只要您只使用之前分配的大小，就可以使用该指针修改数据。
- `- (void)setLength:(NSUInteger)length`；确定内部存储数据的内存缓冲区的长度。
- `- (void)appendBytes:(const void *)bytes length:(NSUInteger)length`；将第一个参数传入的数据追加到内部缓冲区。数据大小由`length`参数给出。
- `- (void)appendData:(NSData *)other`；将参数传入的`NSData`对象中存储的数据追加到内部缓冲区。
- `- (void)increaseLengthBy:(NSUInteger)extraLength`；更改内部数据表示，使其大小增加`extraLength`指定的数量。
- `- (void)replaceBytesInRange:(NSRange)range withBytes:(const void *)bytes`；更改内部存储的数据，将`range`参数确定的字节替换为第二个参数。
- `- (void)resetBytesInRange:(NSRange)range`；重置内部数据缓冲区的一部分，由参数`range`确定。给定范围内的数据重置为零值。
- `- (void)setData:(NSData *)data`；重置内部数据缓冲区，使其包含与参数传入的`NSData`对象相同的数据。
- `- (void)replaceBytesInRange:(NSRange)range withBytes:(const void *)replacementBytes length:(NSUInteger)replacementLength`；将目标对象中内部缓冲区的一部分替换为指针`replacementBytes`指向的字节序列。内部数据的具体部分由`range`参数确定。替换数组的大小由`replacementLength`参数给出。
- `+ (id)dataWithCapacity:(NSUInteger)aNumItems`；创建并返回一个类型为`NSMutableData`的新对象，容量由`aNumItems`指定。
- `+ (id)dataWithLength:(NSUInteger)length`；创建并返回一个类型为`NSMutableData`的新对象，长度由`length`参数指定。
- `- (id)initWithCapacity:(NSUInteger)capacity`；使用给定的容量初始化目标对象。
- `- (id)initWithLength:(NSUInteger)length`；使用`length`参数初始化目标对象。

## 集合类

集合类负责存储应用程序稍后可以检索的对象。Foundation 框架中有几种集合类。这些类根据数据的存储或检索方式进行分类。类也决定了集合是可变的还是不可变的。在下一节中，您将找到 Objective-C 中最常用于维护集合的类。

### NSArray

`NSArray`是 Foundation 框架中非常基础的类。它提供了顺序存储集合概念的面向对象实现。元素存储在相邻位置，可以使用基于索引的方法进行检索。以下是`NSArray`提供的实例方法和类方法列表：



- `- (NSUInteger)count`；返回 `NSArray` 对象中包含的元素数量。
- `- (id)objectAtIndex:(NSUInteger)index`；返回给定索引处存储的对象。
- `- (NSArray *)arrayByAddingObject:(id)anObject`；通过获取目标对象的当前元素并添加作为参数传入的对象，创建一个新数组。
- `- (NSArray *)arrayByAddingObjectsFromArray:(NSArray *)otherArray`；使用目标对象的内容并添加第二个参数数组中所有对象，创建一个新的 `NSArray`。
- `- (NSString *)componentsJoinedByString:(NSString *)separator`；返回一个字符串，该字符串通过连接目标数组的所有元素创建，分隔符由第一个参数决定。
- `- (BOOL)containsObject:(id)anObject`；如果 `anObject` 包含在目标 `NSArray` 中，则返回 true。
- `- (NSString *)description`；返回目标对象的简要描述。通常用于调试目的。
- `- (NSString *)descriptionWithLocale:(id)locale`；使用作为参数传入的区域设置，返回一个简短描述。
- `- (NSString *)descriptionWithLocale:(id)locale indent:(NSUInteger)level`；使用第一个参数中的区域设置返回一个简短描述，并根据第二个参数确定的级别数缩进该描述。
- `- (id)firstObjectCommonWithArray:(NSArray *)otherArray`；返回目标数组中第一个也出现在 `otherArray` 中的对象。
- `- (void)getObjects:(id __unsafe_unretained [])objects range:(NSRange)range`；将目标数组中存储的对象复制到 C 风格的对象数组中。你需要传入一个将要被复制的对象范围。
- `- (NSUInteger)indexOfObject:(id)anObject`；如果在目标数组中找到了 `anObject`，则返回其在数组中的索引。否则，此方法返回 `NSNotFound`。
- `- (NSUInteger)indexOfObject:(id)anObject inRange:(NSRange)range`；如果在第二个参数传入的范围内找到了 `anObject`，则返回其在目标数组中的索引。否则，此方法返回 `NSNotFound`。
- `- (NSUInteger)indexOfObjectIdenticalTo:(id)anObject`；如果在目标数组中通过内存地址比较找到了 `anObject`，则返回其在数组中的索引。否则，此方法返回 `NSNotFound`。
- `- (NSUInteger)indexOfObjectIdenticalTo:(id)anObject inRange:(NSRange)range`；返回传入对象的索引，地址比较方式与前一个方法类似。仅在传入的范围内搜索该对象。
- `- (BOOL)isEqualToArray:(NSArray *)otherArray`；仅当目标数组与 `otherArray` 包含相同的元素且顺序也相同时，才返回 YES。
- `- (id)lastObject`；返回目标数组中存储的最后一个对象。
- `- (NSEnumerator *)objectEnumerator`；返回一个 `NSEnumerator` 对象，可用于枚举目标对象。使用 `NSEnumerator` 对象，你可以通过 `nextObject` 方法依次访问目标数组的每个元素。
- `- (NSEnumerator *)reverseObjectEnumerator`；返回目标数组的一个 `NSEnumerator` 对象。但枚举过程是从数组的末尾向前进行。
- `- (NSData *)sortedArrayHint`；返回一个排序提示，可与其他排序方法一起使用。
- `- (NSArray *)sortedArrayUsingFunction:(NSInteger (*)(id, id, void *))comparator context:(void *)context`；返回一个新的已排序 `NSArray`，其中元素间的比较由传入的比较函数完成。上下文数据会传递给比较器。
- `- (NSArray *)sortedArrayUsingFunction:(NSInteger (*)(id, id, void *))comparator context:(void *)context hint:(NSData *)hint`；与前一个方法类似，但它还使用一个由 `sortedArrayHint` 方法返回的提示来加速排序。
- `- (NSArray *)sortedArrayUsingSelector:(SEL)comparator`；使用作为参数传入的选择器对目标 `NSArray` 进行排序。
- `- (NSArray *)subarrayWithRange:(NSRange)range`；返回一个新的 `NSArray` 对象，其中仅包含由参数 range 确定的那个范围内的对象。
- `- (BOOL)writeToFile:(NSString *)path atomically:(BOOL)useAuxiliaryFile`；将目标 `NSArray` 中存储的数据写入路径决定的文件。如果第二个参数为 YES，则使用临时文件来存储文件传输的中间阶段。
- `- (BOOL)writeToURL:(NSURL *)url atomically:(BOOL)atomically`；将目标 `NSArray` 中存储的数据写入由 `url` 决定的文件或网络资源。如果第二个参数为 YES，则使用临时文件来存储文件传输的中间阶段。
- `- (void)makeObjectsPerformSelector:(SEL)aSelector`；为目标 `NSArray` 中包含的每个对象调用作为第一个参数传入的选择器。
- `- (void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)argument`；为目标 `NSArray` 中包含的每个对象调用作为第一个参数传入的选择器。每次调用都将参数对象作为参数传递给该选择器。
- `- (NSArray *)objectsAtIndexes:(NSIndexSet *)indexes`；返回一个新的 `NSArray`，其中包含目标 `NSArray` 中在给定索引处的所有对象。
- `- (id)objectAtIndexedSubscript:(NSUInteger)idx`；返回目标 `NSArray` 中在给定索引处存储的单个对象。
- `- (void)enumerateObjectsUsingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block`；为目标 `NSArray` 的每个对象调用作为参数传入的 block。
- `- (void)enumerateObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block`；为目标 `NSArray` 的每个对象调用作为参数传入的 block，并使用由 `opts` 确定的选项。
- `- (void)enumerateObjectsAtIndexes:(NSIndexSet *)s options:(NSEnumerationOptions)opts usingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block`；为目标对象中包含的索引集合中的每个对象调用作为参数传入的 block，并使用由 `opts` 确定的选项。
- `- (NSUInteger)indexOfObjectPassingTest:(BOOL (^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回满足作为参数传入的谓词 block 的第一个对象。
- `- (NSUInteger)indexOfObjectWithOptions:(NSEnumerationOptions)opts passingTest:(BOOL(^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回满足作为参数传入的谓词 block 的对象。元素测试的顺序由 `opts` 参数决定。
- `- (NSUInteger)indexOfObjectAtIndexes:(NSIndexSet *)s options:(NSEnumerationOptions)opts passingTest:(BOOL (^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回满足作为参数传入的谓词 block 的对象的索引。元素测试的顺序由 `opts` 参数决定。
- `- (NSIndexSet *)indexesOfObjectsPassingTest:(BOOL (^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回一个索引集合，其中包含那些满足作为参数传入的谓词 block 的对象。
- `- (NSIndexSet *)indexesOfObjectsWithOptions:(NSEnumerationOptions)opts passingTest:(BOOL (^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回一个索引集合，其中包含那些满足作为参数传入的谓词 block 的对象。元素测试的顺序由 `opts` 参数决定。
- `- (NSIndexSet *)indexesOfObjectsAtIndexes:(NSIndexSet *)s options:(NSEnumerationOptions)opts passingTest:(BOOL (^)(id obj, NSUInteger idx, BOOL *stop))predicate`；返回一个索引集合，其中包含那些满足作为参数传入的谓词 block 的对象。仅使用第一个参数指示的对象。元素测试的顺序由 `opts` 参数决定。
- `- (NSArray *)sortedArrayUsingComparator:(NSComparator)cmptr`；使用作为参数给定的比较器，返回一个新的已排序数组。
- `- (NSArray *)sortedArrayWithOptions:(NSSortOptions)opts usingComparator:(NSComparator)cmptr`；使用作为参数给定的比较器，并采用提供的排序选项，返回一个新的已排序数组。
- `- (NSUInteger)indexOfObject:(id)obj inSortedRange:(NSRange)r options:(NSBinarySearchingOptions)opts usingComparator:(NSComparator)cmp`；返回给定对象在目标 `NSArray` 对象的范围 r 中的索引。搜索使用给定的选项执行，并使用作为最后一个参数提供的比较器。
- `+ (id)array`；返回一个新的空数组。
- `+ (id)arrayWithObject:(id)anObject`；返回一个包含单个对象的新数组。
- `+ (id)arrayWithObjects:(const id [])objects count:(NSUInteger)cnt`；返回一个包含由 C 风格数组决定的一组对象的新数组。数组中的元素数量由 `cnt` 给出。
- `+ (id)arrayWithObjects:(id)firstObj, ...`；返回一个新的 `NSArray`，其中包含由方法参数序列决定的一组对象。参数列表必须以 nil 结尾。
- `+ (id)arrayWithArray:(NSArray *)array`；返回一个使用给定数组中的对象初始化的新数组。
- `- (id)initWithObjects:(const id [])objects count:(NSUInteger)cnt`；使用作为第一个参数传入的 C 风格数组中的对象初始化一个 `NSArray` 对象。这些对象的数量由 `cnt` 决定。
- `- (id)initWithObjects:(id)firstObj, ...`；使用由方法参数序列决定的一组对象初始化一个 `NSArray`。参数列表必须以 nil 结尾。
- `- (id)initWithArray:(NSArray *)array`；使用一个已有的 `NSArray` 初始化目标 `NSArray` 对象。
- `- (id)initWithArray:(NSArray *)array copyItems:(BOOL)flag`；使用一个已有的 `NSArray` 对象初始化目标 `NSArray`。仅当 flag 为 YES 时，才会复制项目。
- `+ (id)arrayWithContentsOfFile:(NSString *)path`；使用由 `path` 参数决定的文件内容创建一个新的 `NSArray` 对象。
- `+ (id)arrayWithContentsOfURL:(NSURL *)url`；使用由 `url` 参数决定的文件或网络资源内容创建一个新的 `NSArray` 对象。
- `- (id)initWithContentsOfFile:(NSString *)path`；使用由 `path` 参数决定的文件内容初始化一个 `NSArray` 对象。
- `- (id)initWithContentsOfURL:(NSURL *)url`；使用由 `url` 参数决定的文件或网络资源内容初始化一个 `NSArray` 对象。



### NSDictionary

`NSDictionary` 是一种关联集合。也就是说，它提供了对象与其对应键之间的关联。`NSDictionary` 类支持对此类关联集合进行一些最常见的操作。以下是其方法列表：



*   `+ (id)dictionary`；返回一个新的空 `NSDictionary` 对象。
*   `+ (id)dictionaryWithObject:` `(id)object forKey:(id <NSCopying>)key`；返回一个新的 `NSDictionary` 对象，其中包含一个已设置到给定键的单一对象。
*   `+ (id)dictionaryWithObjects:` `(const id [])objects forKeys:(const id [])keys count:(NSUInteger)cnt`；返回一个新的 `NSDictionary` 对象，其中包含赋值给第二个参数中键的给定对象集。这些对象与键的数量由参数 `cnt` 决定。
*   `+ (id)dictionaryWithObjects:(const id [])objects forKeys:(const id <NSCopying> [])keys count:(NSUInteger)cnt`；返回一个新的 `NSDictionary`，其对象和键以 C 风格数组形式给出，对象与键的数量由参数 `cnt` 决定。键会被复制到容器中。
*   `+ (id)dictionaryWithObjectsAndKeys:` `(id)firstObject, ...`；返回一个新的 `NSDictionary` 对象，其对象和键由方法的参数决定。对象和键的列表必须以 nil 结尾。
*   `+ (id)dictionaryWithDictionary:` `(NSDictionary *)dict`；返回一个新的 `NSDictionary` 对象，其对象和键与参数 `dict` 相同。
*   `+ (id)dictionaryWithObjects:(` `NSArray *)objects forKeys:(NSArray *)keys`；返回一个新的 `NSDictionary` 对象，其对象和键由两个 `NSArray` 对象决定。两个数组必须具有相同数量的元素。
*   `- (id)initWithObjects:` `(const id [])objects forKeys:(const id [])keys count:(NSUInteger)cnt`；初始化一个 `NSDictionary` 对象，其中包含赋值给第二个参数中键的给定对象集。这些对象与键的数量由参数 `cnt` 决定。
*   `- (id)initWithObjects:(const id [])objects forKeys:(const id <NSCopying> [])keys count:(NSUInteger)cnt`；初始化一个 `NSDictionary` 对象，其中包含赋值给第二个参数中键的给定对象集。这些对象与键的数量由参数 `cnt` 决定。键会被复制，且其初始值不会发生任何更改。
*   `- (id)initWithObjectsAndKeys:` `(id)firstObject, ...`；使用参数列表中提供的一组对象及其对应键来初始化目标 `NSDictionary` 对象。该列表必须以 nil 结尾。
*   `- (id)initWithDictionary:` `(NSDictionary *)otherDictionary`；使用另一个 `NSDictionary` 实例的内容来初始化目标 `NSDictionary` 对象。
*   `- (id)initWithDictionary:(NSDictionary *)otherDictionary copyItems:(BOOL)flag`；使用另一个 `NSDictionary` 实例的内容来初始化目标 `NSDictionary` 对象。`otherDictionary` 中包含的值会被复制，而不会在内部保留。
*   `- (id)initWithObjects:` `(NSArray *)objects forKeys:(NSArray *)keys`；使用对象及其对应键来初始化目标 `NSDictionary` 对象。对象和键作为两个 `NSArray` 对象提供。
*   `+ (id)dictionaryWithContentsOfFile:` `(NSString *)path`；创建一个新的 `NSDictionary` 实例，并使用 `path` 确定的文件内容对其进行初始化。
*   `+ (id)dictionaryWithContentsOfURL:` `(NSURL *)url`；创建一个新的 `NSDictionary` 实例，并使用由 `url` 参数标识的文件或网络资源内容对其进行初始化。
*   `- (id)initWithContentsOfFile:` `(NSString *)path`；使用由 `path` 参数标识的文件内容来初始化目标 `NSDictionary` 对象。
*   `- (id)initWithContentsOfURL:(NSURL *)url`；使用由 `url` 参数标识的文件或网络资源内容来初始化目标 `NSDictionary` 对象。
*   `- (NSUInteger)count`；返回目标 `NSDictionary` 对象中存储的对象数量。
*   `- (id)objectForKey:(id)aKey`；返回与作为第一个参数提供的键相对应的对象。
*   `- (NSEnumerator *)keyEnumerator`；返回一个 `NSEnumerator` 对象，可用于遍历 `NSDictionary` 中的元素。
*   `- (NSArray *)allKeys`；返回一个新的 `NSArray`，其中包含目标 `NSDictionary` 实例中存储的所有键。
*   `- (NSArray *)allKeysForObject:` `(id)anObject`；返回一个新的 `NSArray`，其中包含目标 `NSDictionary` 中与 anObject 关联的所有键。
*   `- (NSArray *)allValues`；返回一个新的 `NSArray`，其中包含目标 `NSDictionary` 中存储的所有值。
*   `- (NSString *)description`；返回 `NSDictionary` 实例的简短描述。通常用于调试目的。
*   `- (NSString *)descriptionWithLocale:` `(id)locale`；返回 `NSDictionary` 实例的简短描述，并使用提供的语言环境。
*   `- (NSString *)descriptionWithLocale:(id)locale indent:(NSUInteger)level`；返回 `NSDictionary` 实例的简短描述，使用提供的语言环境，并按照最后一个参数指定的级别进行缩进。
*   `- (BOOL)isEqualToDictionary:` `(NSDictionary *)otherDictionary`；如果目标 `NSDictionary` 与提供的字典具有相同的对象和键，则返回 YES。
*   `- (NSEnumerator *)objectEnumerator`；返回一个 `NSEnumerator` 对象，可用于遍历 `NSDictionary` 中的元素。
*   `- (NSArray *)objectsForKeys:` `(NSArray *)keys notFoundMarker:(id)marker`；返回一个新的 `NSArray`，其中包含所有与第一个参数中存储的某个键相关联的对象。使用标记来指示特定键在目标 `NSDictionary` 中没有关联对象。
*   `- (BOOL)writeToFile:` `(NSString *)path atomically:(BOOL)useAuxiliaryFile`；将 `NSDictionary` 的内容写入由 `path` 参数指定的文件。如果第二个参数为 YES，则在存储完成之前，会使用一个临时文件来保存传输的内容。
*   `- (BOOL)writeToURL:` `(NSURL *)url atomically:(BOOL)atomically`；将 `NSDictionary` 的内容写入由 `url` 参数指定的文件或网络资源。如果第二个参数为 YES，则在存储完成之前，会使用一个临时文件来保存传输的内容。
*   `- (NSArray *)keysSortedByValueUsingSelector:` `(SEL)comparator`；返回一个新的 `NSArray`，其中包含 `NSDictionary` 中存储的键集，并根据作为参数传入的选择器按值排序。
*   `- (void)getObjects:` `(id __unsafe_unretained [])objects andKeys:(id __unsafe_unretained [])keys`；将 `NSDictionary` 中包含的对象和键集合复制到作为参数提供的 C 风格数组中。
*   `- (id)objectForKeyedSubscript:` `(id)key`；返回目标 `NSDictionary` 中与给定键对应的对象。
*   `- (void)enumerateKeysAndObjectsUsingBlock:` `(void (^)(id key, id obj, BOOL *stop))block`；为目标 `NSDictionary` 中存储的每个键调用 block 参数。
*   `- (void)enumerateKeysAndObjectsWithOptions:` `(NSEnumerationOptions)opts usingBlock:(void (^)(id key, id obj, BOOL *stop))block`；为目标 `NSDictionary` 中存储的每个键调用作为参数传入的 block，并使用给定的枚举选项。
*   `- (NSArray *)keysSortedByValueUsingComparator:` `(NSComparator)cmptr`；返回一个新的 `NSArray`，其中包含 `NSDictionary` 实例中存储的所有键，并根据作为参数传入的比较器对象进行排序。
*   `- (NSArray *)keysSortedByValueWithOptions:(NSSortOptions)opts usingComparator:(NSComparator)cmptr`；返回一个新的数组，其中包含目标 `NSDictionary` 中存储的所有键，根据给定的选项并使用比较器对象 `cmptr` 进行排序。
*   `- (NSSet *)keysOfEntriesPassingTest:` `(BOOL (^)(id key, id obj, BOOL *stop))predicate`；返回一个 `NSSet` 对象，其中包含目标 `NSDictionary` 中存储的所有通过 `predicate` 参数所决定测试的键。
*   `- (NSSet *)keysOfEntriesWithOptions:` `(NSEnumerationOptions)opts passingTest:(BOOL (^)(id key, id obj, BOOL *stop))predicate`；返回一个 `NSSet` 对象，其中包含目标 `NSDictionary` 中存储的所有通过 `predicate` 参数所决定测试的键。枚举将根据枚举选项 `opts` 执行。



### NSHashTable

`NSHashTable`是一个简单的集合类，其中元素存储在一个数组中。哈希表中的每个元素都与一个哈希键相关联。该键用于查找元素将被存储的确切位置。以下是其方法列表：

- `-(id)initWithOptions:(NSPointerFunctionsOptions)options capacity:(NSUInteger)initialCapacity`：使用提供的选项和由第二个参数定义的初始容量来初始化`NSHashTable`对象。
- `- (id)initWithPointerFunctions:(NSPointerFunctions *)functions capacity:(NSUInteger)initialCapacity`：使用一组指针函数初始化`NSHashTable`对象，并定义哈希表的初始容量。
- `+ (id)hashTableWithOptions:(NSPointerFunctionsOptions)options`：使用作为第一个参数传递的选项创建并初始化`NSHashTable`对象。
- `+ (id)hashTableWithWeakObjects`：返回一个新的哈希表，该哈希表对目标对象中包含的对象具有弱引用。此方法已弃用，应替换为以下方法。
- `+ (id)weakObjectsHashTable`：与前一方法类似，是推荐使用的方法。
- `- (NSPointerFunctions *)pointerFunctions`：返回存储在目标`NSHashTable`对象中的指针函数。
- `- (NSUInteger)count`：返回目标`NSHashTable`对象中包含的元素数量。
- `- (id)member:(id)object`：判断作为第一个参数传递的对象是否包含在目标`NSHashTable`中。如果答案为是，则返回该对象。否则，返回值为`nil`。
- `- (NSEnumerator *)objectEnumerator`：返回一个`NSEnumerator`对象，可用于遍历目标`NSHashTable`对象的元素。
- `- (void)addObject:(id)object`：向`NSHashTable`添加一个新对象。
- `- (void)removeObject:(id)object`：从目标`NSHashTable`中移除参数对象。
- `- (void)removeAllObjects`：通过移除内部存储的所有元素来清理目标`NSHashTable`对象。
- `- (NSArray *)allObjects`：返回一个包含`NSHashTable`中所有对象的`NSArray`。
- `- (id)anyObject`：返回目标`NSHashTable`中包含的一个对象。不保证返回的对象是随机选择的。
- `- (BOOL)containsObject:(id)anObject`：如果作为第一个参数传递的对象包含在目标`NSHashTable`中，则返回`YES`。
- `- (BOOL)intersectsHashTable:(NSHashTable *)other`：如果存在同时包含在目标`NSHashTable`对象和参数`other`中的元素，则返回`YES`。
- `- (BOOL)isEqualToHashTable:(NSHashTable *)other`：如果`other`等于目标`NSHashTable`对象，则返回`YES`。即两个哈希表必须包含相同的元素。
- `- (BOOL)isSubsetOfHashTable:(NSHashTable *)other`：如果作为参数传递的哈希表是目标`NSHashTable`的子集，则返回`YES`。即`other`中包含的每个元素也必须存在于目标对象中。
- `- (void)intersectHashTable:(NSHashTable *)other`：从目标`NSHashTable`中移除不属于作为参数传递的哈希表的任何元素。
- `- (void)unionHashTable:(NSHashTable *)other`：将作为参数传递的哈希表中尚未包含的任何元素纳入目标`NSHashTable`对象。
- `- (void)minusHashTable:(NSHashTable *)other`：从目标`NSHashTable`中移除包含在`other`哈希表中的任何元素。
- `- (NSSet *)setRepresentation`：创建一个包含目标`NSHashTable`对象中所有元素的新`NSSet`对象。

### NSSet

`NSSet`对象表示一个简单的对象集合。当主要目标是查询集合中的包含关系时，应使用`NSSet`。该类还提供了其他集合操作，例如交集、并集和子集提取等。以下是其方法：



*   `+ (id)set`；创建一个新的、空的 `NSSet` 对象。
*   `+ (id)setWithObject:(id)object`；创建一个新的 `NSSet` 对象，并通过添加作为参数传入的对象来初始化它。
*   `+ (id)setWithObjects:(const id [])objects count:(NSUInteger)cnt`；创建一个新的 `NSSet` 对象，并通过添加作为 C 风格数组传入的对象来初始化它。对象的数量由参数 `cnt` 决定。
*   `+ (id)setWithObjects:(id)firstObj, ...`；创建一个新的 `NSSet` 对象，并通过将对象作为参数列表添加来初始化它。该列表必须以 `nil` 结尾。
*   `+ (id)setWithSet:(NSSet *)set`；创建一个新的 `NSSet` 对象，并通过添加作为参数传入的集合中的所有元素来初始化它。
*   `+ (id)setWithArray:(NSArray *)array`；创建一个新的 `NSSet` 对象，并通过添加作为参数传入的 `NSArray` 中包含的所有元素来初始化它。
*   `- (id)initWithObjects:(const id [])objects count:(NSUInteger)cnt`；通过使用 C 风格数组对象中包含的元素来初始化一个 `NSSet` 对象。这些对象的数量由 `cnt` 给出。
*   `- (id)initWithObjects:(id)firstObj, ...`；通过添加作为参数给出的对象来初始化一个 `NSSet` 对象。对象列表必须以 `nil` 作为最后一个元素。
*   `- (id)initWithSet:(NSSet *)set`；通过添加现有 `NSSet` 对象中包含的所有元素来初始化一个 `NSSet` 对象。
*   `- (id)initWithSet:(NSSet *)set copyItems:(BOOL)flag`；通过添加现有 `NSSet` 中包含的所有元素来初始化一个 `NSSet` 对象。如果第二个参数是 `YES`，则复制元素，不保留原始值。
*   `- (id)initWithArray:(NSArray *)array`；通过添加作为参数传入的 `NSArray` 中包含的所有元素来初始化一个 `NSSet` 对象。
*   `- (NSUInteger)count`；返回目标 `NSSet` 对象中存储的元素数量。
*   `- (id)member:(id)object`；如果参数对象包含在目标 `NSSet` 对象中，则返回该参数对象。否则，此方法返回 `nil`。
*   `- (NSEnumerator *)objectEnumerator`；返回一个 `NSEnumerator` 对象，该对象可用于遍历目标 `NSSet` 对象中包含的对象。
*   `- (NSArray *)allObjects`；返回一个新的 `NSArray`，其中包含存储在目标 `NSSet` 对象中的所有对象。
*   `- (id)anyObject`；返回当前存储在 `NSSet` 中的任意一个对象。不保证会返回哪个元素。
*   `- (BOOL)containsObject:(id)anObject`；如果目标 `NSSet` 包含作为参数传入的对象，则返回 `YES`。
*   `- (NSString *)description`；返回目标 `NSSet` 对象的简短描述。
*   `- (NSString *)descriptionWithLocale:(id)locale`；使用指定的区域设置，为 `NSSet` 对象返回一个简短描述。
*   `- (BOOL)intersectsSet:(NSSet *)otherSet`；如果目标 `NSSet` 与给定的参数 `otherSet` 至少有一个共同元素，则返回 `YES`。
*   `- (BOOL)isEqualToSet:(NSSet *)otherSet`；如果目标 `NSSet` 的每个元素也包含在 `otherSet` 中，且两个集合的元素数量相同，则返回 `YES`。
*   `- (BOOL)isSubsetOfSet:(NSSet *)otherSet`；如果目标 `NSSet` 的每个元素也是 `otherSet` 的元素，则返回 `YES`。
*   `- (void)makeObjectsPerformSelector:(SEL)aSelector`；对目标 `NSSet` 对象的所有元素执行选择器 `aSelector`。
*   `- (void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)argument`；对目标 `NSSet` 对象的所有元素执行选择器 `aSelector`。第二个参数作为参数传递给该选择器。
*   `- (NSSet *)setByAddingObject:(id)anObject`；创建一个新的 `NSSet` 对象，并通过添加单个对象（即 `anObject` 参数）来初始化它。
*   `- (NSSet *)setByAddingObjectsFromSet:(NSSet *)other`；创建一个新的 `NSSet` 对象，并通过添加作为第一个参数传入的集合中的所有对象来初始化它。
*   `- (NSSet *)setByAddingObjectsFromArray:(NSArray *)other`；返回一个新的 `NSSet` 对象，并通过添加 `NSArray` `other` 中的所有成员来初始化它。
*   `- (void)enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop))block`；对目标 `NSSet` 的所有元素执行作为参数传入的 block。
*   `- (void)enumerateObjectsWithOptions:(NSEnumerationOptions)opts usingBlock:(void (^)(id obj, BOOL *stop))block`；使用第一个参数中传入的选项，对目标 `NSSet` 的所有元素执行该 block。
*   `- (NSSet *)objectsPassingTest:(BOOL (^)(id obj, BOOL *stop))predicate`；返回一个新的 `NSSet` 对象，其中包含目标集合中所有通过谓词参数所指定测试的元素。
*   `- (NSSet *)objectsWithOptions:(NSEnumerationOptions)opts passingTest:(BOOL (^)(id obj, BOOL *stop))predicate`；返回一个新的 `NSSet` 对象，其中包含目标 `NSSet` 中所有通过谓词参数所指定测试的对象。对象的枚举根据 `opts` 中的选项进行。

## 总结

在本章中，我提供了 Foundation 框架中最重要类的快速参考。我将阐述范围限制在最常用的接口上，以便你可以快速查阅这些类中包含的方法。

在本章的第一部分，我介绍了一些基本类的参考，例如 `NSObject`、`NSString` 和 `NSValue`。这些类是 Cocoa 提供服务的基石。本章的第二部分是一些最常用集合类的参考，例如 `NSArray`、`NSDictionary` 和 `NSSet`。

在接下来的几章中，我将开始介绍 Objective-C 开发者可用的工具和环境。你将首先了解 Objective-C 编译器及其最重要的配置。我将向你展示一些使用编译器的常见方法，以及如何调整其选项以达到不同的性能、日志记录或内存使用水平。

