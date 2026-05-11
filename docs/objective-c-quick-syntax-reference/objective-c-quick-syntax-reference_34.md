# 29. Web 服务

摘要

许多公司（如 Facebook 和 Twitter）通过网站向用户提供服务。通常，这些服务也对开发者开放，以便在他们的应用中使用；这些服务被称为 Web 服务。Web 服务是驻留在 Web 服务器上的功能和内容，你可以通过一组定义明确的规则（称为 API，应用程序编程接口）来使用它们。

## Web 服务定义

许多公司（如 Facebook 和 Twitter）通过网站向用户提供服务。通常，这些服务也对开发者开放，以便在他们的应用中使用；这些服务被称为 Web 服务。Web 服务是驻留在 Web 服务器上的功能和内容，你可以通过一组定义明确的规则（称为 API，应用程序编程接口）来使用它们。

使用 Web 服务的一般模式是：构建请求、发送请求、接收响应，然后解释响应。Objective-C 内置了对 Web 服务的支持。要发送请求和接收响应，你可以使用`NSURLConnection`类和`NSData`类。要解释（或解析）响应，你可以使用`NSJSONSerialization`类。

注意

JSON 代表 JavaScript 对象表示法（JavaScript Object Notation），用于数据存储和传输。实现为 REST（表述性状态转移）Web 服务的 Web 服务将提供 JSON 响应数据。`NSJSONSerialization`类使得在 Objective-C 中处理 JSON 更加容易。

### Bitly 示例

Bitly 是一个很好的 Web 服务示例，我喜欢用它来做演示，因为它非常简单且功能清晰。Bitly 会将长 URL（你在 Web 浏览器中输入的字符串）转换为更易于输入的短 URL。我将使用 bitly 的 Web 服务来展示`NSURLConnection`类。

注意

要跟着这个示例操作，你需要在 bit.ly 上创建一个免费账户，并获取你自己的 API 密钥和 API 用户名。如果你希望跟着这个示例操作，请前往[`https://bit.ly`](https://bit.ly/)获取你的账户。在示例中，当遇到`[YOUR API LOGIN]`或`[YOUR API KEY]`时，你需要替换为从 bitly 获取的登录名和密钥。



### 构造请求字符串

使用网络服务时，应参考发布该服务的公司提供的文档作为依据。该文档会提供可供使用的字符串和参数。你需要以 API 文档中的字符串（`http://api.bit.ly/shorten?version=2.0.1&longUrl=&login=&apiKey=&format=json`）为起点，结合 bitly 登录名、bitly 密钥和一个长 URL 作为参数，来构造请求字符串。

```
NSString *APILogin = @"[你的 API 登录名]";
```

```
NSString *APIKey = @"[你的 API 密钥]";
```

```
NSString *longURL = @"https://mobileappmastery.com";
```

```
NSString *requestString = [NSString stringWithFormat:@"http://api.bit.ly/shorten?version=2.0.1&longUrl=%@&login=%@&apiKey=%@&format=json", longURL, APILogin, APIKey];
```

### 创建会话和 URL

你需要两个对象：一个用于表示发送到服务器的请求 URL 的 `NSURL` 对象，以及一个用于执行网络工作的 `NSURLSession` 对象。

```
NSURL *requestURL = [NSURL URLWithString:requestString];
```

```
NSURLSession *session = [NSURLSession sharedSession];
```

### 发送和接收响应

你将使用 `NSURLSession` 对象的一个块（block）来请求网络服务缩短 URL。将处理响应所需的全部代码放入作为参数传递的块中。通过这种方法，你实际上是在同时完成两件事。

```
[[session dataTaskWithURL:requestURL
        completionHandler:^(NSData *data,
                                          NSURLResponse *response,
                                          NSError *error) {
        }] resume];
```

```
sleep(60);
```

你仍然需要填充用于处理响应的块，但这将启动操作。请注意，你需要在代码中放入一个 `sleep` 函数。`sleep` 函数会阻止主线程在 60 秒内执行新代码。你需要这样做，因为正在使用的方法将在后台线程上执行（这是使用网络服务时的最佳实践）。如果不阻止命令行应用完成网络服务，它将没有足够的时间为你获取结果。


好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


### 解析 JSON

在完成块（completion block）内部，你可以添加用于解释 Web 服务器响应的代码。由于你知道将要处理 JSON 数据，因此你需要使用 `NSJSONSerialization` 类。这里你需要一个 `NSError` 对象，以及由该块提供的 `NSData` 对象（它包含了来自 Web 服务的响应数据）。

```
[[session dataTaskWithURL:requestURL

        completionHandler:^(NSData *data,

                            NSURLResponse *response,

                            NSError *error) {

       NSError *e = nil;

       NSDictionary *bitlyJSON = [NSJSONSerialization JSONObjectWithData:data

                                                                                   options:0

                                                                                       error: &e];

        }] resume];

```

这会将所有 JSON 数据组织到一个 `NSDictionary` 集合中。这个字典内部可能包含其他字典、数组、数字和字符串。下一步是遍历所有这些返回的对象，以定位你需要的内容。同时，你还需要在这里测试错误。

```
[[session dataTaskWithURL:requestURL

        completionHandler:^(NSData *data,

                            NSURLResponse *response,

                            NSError *error) {

NSError *e = nil;

NSDictionary *bitlyJSON = [NSJSONSerialization JSONObjectWithData:data

                                                                                       options:0

                                                                                          error:&e];

if(!error){

    NSDictionary *results = [bitlyJSON objectForKey:@"results"];

    NSDictionary *resultsForLongURL = [results objectForKey:longURL];

    NSString *shortURL = [resultsForLongURL objectForKey:@"shortUrl"];

    NSLog(@"shortURL = %@", shortURL);

}

else

    NSLog(@"There was an error parsing the JSON");

        }] resume];

```

一旦设置完成，运行你的应用程序，你将能够从响应中检索到 `shortURL`，并在控制台日志中打印出以下内容：

```
shortURL = http://bit.ly/1fHrAsT
```

**注意**

像这样解析 Web 服务响应时，你需要通过查看 API 文档或查看返回的字符串，来调查重要数据所在的位置。

Matthew Campbell  
Objective-C Quick Syntax Reference  
10.1007/978-1-4302-6488-0  
© Apress 2013

**索引**

A  
- `Alloc` 和 `init` 方法  
- `AppendString`  
- 应用程序编程接口 (API)  
- 算术运算符  
  - 浮点数  
  - 运算符优先级  
  - 类型  
- 数组  
  - `count` 属性  
  - 可变字符串  
  - `NSArray` 类  
  - `NSArray enumerateObjectsUsingBlock` 方法  
  - numbers 数组  
  - `NSMutableArray`  
  - `NSNumberFormatter`  
  - `NSNumber` 对象  
  - 程序拼写出的字符串  
- 赋值运算符 (=)  

B  
- 后台进程  
  - `alloc` 和 `init` 函数  
  - `for` 语句  
  - `NSOperationQueue`  
- Bitly  
- 块 (Blocks)  
  - 赋值运算符 (=)  
  - `copy` 属性  
  - 声明  
  - 定义  
  - `generateReport` 方法  
  - `main` 函数  
  - `multiplyThese` 块  
  - `Project` 类  
  - `self` 关键字  
  - `squareThis` 函数  
- 布尔类型  
- 布尔变量  
- 构建并运行  
  - 构建 (building)  
  - 定义  
  - 包 (bundle)  
  - 按钮  
  - 编译代码  
  - 控制台日志的输出  
  - 构建选项  
- 包 (Bundle)  

C  
- 分类 (Categories)  
  - 分类接口  
  - 定义  
  - `@interface` 关键字  
  - `main` 函数  
  - `main.m` 文件  
  - 方法实现  
  - `Project` 类  
- `CGFloat` 数据类型  
- 类 (Classes)  
  - 类接口  
    - 基类  
    - 定义  
    - 基础框架  
    - 实现类扩展  
  - `@end` 和 `@implementation` 关键字  
  - 实例变量 / ivar  
  - 方法实现  
  - `@interface` 行和 `@end` 行  
  - `Project` 类  
  - 属性描述符  
  - `void` 返回类型  
- 类方法  
  - `alloc` 函数  
  - 控制台日志, 58  
  - 实例方法  
  - `printTimeStamp` 方法  
  - `project` 实现  
  - `project` 对象  
  - 示例程序  
- 命令行工具  
- 编译器 (另见 构建并运行)  
- 控制台日志  
- 构造函数  
  - `alloc` 函数  
  - `init` 函数  
  - `new` 关键字  
- 花括号 {}  

D  
- 自减运算符 (`--`)  
- 委托 (Delegation)  
  - 定义  
  - `hisTask: statusHasChangedToThis` 方法  
  - `project` 赋值  
  - 协议引用  
  - `TaskDelegate`  
  - `Task done` 属性, 发送消息  
  - `thisTask:statusHasChangedToThis` 方法  
- 取消注册观察者  
- 字典  
  - `NSDictionary` 枚举  
  - `NSDictionary` 类  
  - `objectForKey`  
  - `@` 符号  
  - `NSMutableDictionary`  
    - 添加/删除项目  
    - alloc 模式  
    - init 模式  
- Do 关键字  
- Do While 循环  
  - 数组计数器变量  
  - `do` 关键字  
  - `for` 循环  
  - 示例程序  

E  
- Else 关键字  
- 错误处理  
  - `NSError`  
  - try/catch 语句  

F, G  
- For-each 循环  
  - 数组 (numbers)  
  - 集合对象  
  - `NSDictionary` 输出  
  - 示例程序  
- For 循环  
  - 数组  
  - `count` 属性  
  - `NSNumberFormatter`  
  - `NSNumber` 对象  
  - 拼写出的字符串  
  - 花括号  
  - 自增 (i++)  
  - 用法  
- 格式说明符  

H  
- Hello World  
  - 自动释放池  
  - 构建并运行  
  - 代码注释  
  - 命令行工具项目  
  - `#import` 语句  
  - `NSLog` 函数  
  - `NSString` 对象  
  - 项目创建  
    - 代码编辑器和项目导航器  
    - Xcode 欢迎屏幕  
    - 项目导航器  
  - Xcode 下载步骤  

I  
- If 语句  
  - 布尔变量  
  - 定义  
  - `else` 关键字  
  - 嵌套 if else 语句  
- 实现类扩展  
  - `@end` 和 `@implementation` 关键字  
  - 实例变量 / ivar  
  - 方法实现  
- 自增运算符 (++)  
- 继承  
  - 类扩展  
  - 实例变量  
  - `member of` 运算符  
  - 私有实例变量  
  - `@private` 关键字  
  - 受保护可见性  
  - 公共实例变量可见性级别  
  - 方法覆盖  
  - 子类创建  
- 实例方法  
- 实例变量  
  - `member of` 运算符  
  - 私有实例变量  
  - 受保护可见性  
  - 公共实例变量可见性级别  
- 整数类型  
  - `NSIntegers`  
  - `NSUIntegers`  
- 集成开发环境 (IDE)  
- 接口  

J  
- JSON  
  - 错误  
  - `NSData` 对象  
  - `NSDictionary` 集合  

K  
- 键值编码  
  - 定义  
  - 获取属性值  
  - 设置属性值  
- 键值观察  
  - 定义  
  - 实现  
  - 添加观察者  
  - 取消注册观察者  
  - 测试值更改  
  - project 和 task 对象图  

L  
- LLVM 编译器  
- 逻辑运算符  

M  
- 方法覆盖  

N  
- 嵌套 If Else 语句  
- `NSArray` 类  
  - `NSArray enumerateObjectsUsingBlock` 方法  
  - numbers 数组  
- `NSCoding` 协议  
  - 解码器方法  
  - 编码器方法  
  - project 实现  
  - `Task` 类  
- `NSDictionary` 枚举  
  - 键, 对象列表  
  - `NSDictionary` 类  
  - `objectForKey` 输出  
  - 示例程序  
  - `@` 符号  
- `NSError`  
- `NSInteger` 变量  
- `NSLiteralSearch`  
- `NSLog`  
- `NSMutableArray`  
- `NSMutableDictionary`  
  - 添加/删除项目  
  - alloc 模式  
  - init 模式  
- `NSMutableString`  
  - `atIndex` 参数  
  - `deleteCharactersInRange`  
  - `find` 函数  
  - 插入  
  - `NSMutableString` replace 函数  
- `NSNumber`  
  - 将字符串转换为数字  
- `NSNumberFormatter`  
  - 原始数据类型  
- `NSNumberFormatter`  
- `NSObject` 类  
- `NSString` 构造函数  

O  
- 对象归档  
  - 控制台日志  
  - 定义  
  - `NSCoding` 协议  
    - 解码器方法  
    - 编码器方法  
    - project 实现  
    - `Task` 类  
  - 根对象  
- `ObjectForKey`  
- 面向对象编程  
- 对象  
  - 构造函数  
    - `alloc` 函数  
    - `init` 函数  
    - `new` 关键字  
  - 声明  
  - 定义  
  - 格式说明符  
  - 消息  
  - `NSFileManager` 对象  
  - `removeItemAtPath:error`  
  - `NSObject` 类  
- 运算符  
  - 算术运算符  
    - 浮点数  
    - 运算符优先级  
    - 类型  
  - 赋值运算符 (=)  
  - 自减运算符 (`--`)  
  - 定义  
  - 自增运算符 (++)  
  - 逻辑运算符  
  - 关系运算符  

P, Q  
- 原始数据类型  
- `PrintTimeStamp` 方法  
- 协议 (Protocols)  
  - 采纳  
  - 定义  
  - 可选方法  
  - 方法实现  

R  
- 关系运算符  
- `RemoveItemAtPath:error`  

S  
- 单例 (Singleton)  
  - `alloc` 和 `init` 函数  
  - `AppSingleton` 类  
    - 定义  
    - 实现  
    - 接口  
  - `@synchronized (self)` 块  
- `SquareThis` 函数  
- 字符串  
  - `NSMutableString`  
    - `atIndex` 参数  
    - `deleteCharactersInRange`  
    - `find` 函数  
    - 插入  
    - `NSMutableString` replace 函数  
  - `NSString` 类  
  - `NSString` 构造函数  
  - `NSString` 对象  
- Switch 语句  
  - `break` 关键字  
  - `case` 关键字  
  - `default` 情况  
  - 多 case 语句  
  - `NSInteger` 变量  
  - 示例程序  
  - `switch` 关键字  
  - 用法  

T  
- Try/catch 语句  

U  
- url  
  - `NSURL` 对象  

V  
- 变量  
  - 赋值运算符 (=)  
  - 布尔类型  
  - `CGFloat` 数据类型  
  - 花括号 {}  
  - 数据类型  
  - 声明  
  - 定义  
  - 整数类型  
    - `NSIntegers`  
    - `NSUIntegers`  

W  
- Web 服务  
  - API  
  - Bitly  
  - 定义  
  - JSON  
    - 错误  
    - `NSData` 对象  
    - `NSDictionary` 集合  
  - 请求字符串  
  - `sleep` 函数  
  - URL 创建  

X, Y, Z  
- Xcode  
  - 下载步骤  
  - Hello World 项目 (见 Hello World)  
  - 集成开发环境 (IDE)
