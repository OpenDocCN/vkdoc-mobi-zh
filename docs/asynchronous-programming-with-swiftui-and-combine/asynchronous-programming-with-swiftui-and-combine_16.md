# 11. 实现自定义 Combine 运算符

在前面的章节中，您学习了如何使用 Combine 访问网络、处理错误，并以对应用程序用户有意义的方式暴露可能发生的错误。

毫不奇怪，我们最终得到的代码看起来比最初的要复杂一些。毕竟，正确处理错误所占用的代码行数比完全不处理错误（或直接忽略错误）要多。

在本章中，我们将通过利用 Combine 最强大的工具之一：运算符，来改善这种情况。您已经在之前的章节中使用过运算符，在本章中，我们将更深入地了解它们是什么、如何工作，以及——最重要的是——将代码重构为自定义 Combine 运算符如何使其更易于推理和更易于复用。

## 什么是 Combine 运算符？

Combine 定义了三个主要概念来实现响应式编程的思想：

1. 发布者（`Publisher`）
2. 订阅者（`Subscriber`）
3. 运算符（`Operator`）

**发布者**随时间传递值，**订阅者**在接收这些值时对其进行操作。**运算符**位于发布者和订阅者之间，可用于操纵值流。

需要运算符的原因有几个：

* 发布者并不总是以订阅者所需的格式生成事件。例如，发布者可能发出 HTTP 网络请求的结果，但我们的订阅者需要的是自定义数据结构。在这种情况下，我们可以使用`map`或`decode`等运算符将发布者的输出转换为订阅者期望的数据结构。

* 发布者可能产生比订阅者感兴趣的事件更多的事件。例如，输入搜索词时，我们可能对每一次按键都不感兴趣，而只对最终的搜索词感兴趣。在这种情况下，我们可以使用`debounce`或`throttle`等运算符来减少订阅者必须处理的事件数量。

运算符帮助我们获取发布者产生的输出，并将其转换为订阅者可以消费的内容。在之前的章节中，我们已经使用了许多内置运算符，例如：

* `map`（及其异常友好版本`tryMap`）用于转换元素
* `debounce`用于仅在两个事件之间存在暂停时发布元素
* `removeDuplicates`用于删除重复事件
* `flatMap`用于将元素转换为新的发布者



## 实现自定义操作符

通常，在创建 Combine 管道时，我们会从一个发布者（publisher）开始，然后连接一系列 Combine 的内置操作符来处理该发布者发出的事件。在任何一个 Combine 管道的末端，都有一个接收事件的订阅者（subscriber）。正如你在第 10 章所见，管道很快就会变得相当复杂。

从技术上讲，操作符只是创建其他发布者和订阅者的函数，这些发布者和订阅者处理它们从上游发布者接收到的*事件*。

这意味着我们可以通过扩展 `Publisher` 来创建我们自己的自定义操作符，扩展中是一个函数，该函数返回一个发布者（或订阅者），这个发布者（或订阅者）对我们使用它的发布者所接收到的事件进行操作。

让我们通过实现一个简单的操作符来看看这在实际中意味着什么，该操作符允许我们使用 Swift 的 `dump()` 函数来检查流经 Combine 管道的事件。这个函数会将变量的内容打印到控制台，并以嵌套树的形式显示变量的结构——类似于 Xcode 中的调试检查器。

你可能知道 Combine 的 `print()` 操作符，它的工作方式非常相似。然而，它没有提供那么多细节，更重要的是，它不以嵌套结构显示结果。

要添加一个操作符，我们首先需要为 `Publisher` 类型添加一个扩展。由于我们不想操作此操作符接收到的事件，我们可以使用上游发布者的类型作为结果类型，并返回 `AnyPublisher<Self.Output, Self.Failure>` 作为结果类型：

```
extension Publisher {
    func dump() -> AnyPublisher<Self.Output, Self.Failure> {
    }
}
```

在函数内部，我们可以使用 `handleEvents` 操作符来检查此管道处理的任何事件。`handleEvents` 有一系列可选的闭包，当发布者接收到新的订阅、新的输出值、取消事件、完成时，或者当订阅者请求更多元素时，这些闭包会被调用。由于我们只对新的 `Output` 值感兴趣，我们可以忽略大部分闭包，只实现 `receiveOutput` 闭包。

每当我们收到一个值时，我们将使用 Swift 的 `dump()` 函数将值的内容打印到控制台：

```
extension Publisher {
    func dump() -> AnyPublisher<Self.Output, Self.Failure> {
        handleEvents(receiveOutput:  { value in
            Swift.dump(value)
        })
        .eraseToAnyPublisher()
    }
}
```

我们可以像使用 Combine 的任何内置操作符一样使用这个操作符。在下面的例子中，我们将新的操作符附加到一个发出当前日期的简单发布者上：

```
Just(Date())
    .dump()
    // 打印：
    // ▿ 2022-03-02 09:38:49 +0000
    //   - timeIntervalSinceReferenceDate: 667906729.659255
```

## 实现带延迟的重试操作符

既然我们已经对如何实现一个简单的操作符有了基本的了解，让我们看看是否可以重构上一节中的代码。以下是相关部分：

```
return dataTaskPublisher
    .tryCatch { error -> AnyPublisher<Data, Error> in
        if case APIError.serverError = error {
            return Just(Void())
                .delay(for: 3, scheduler: DispatchQueue.global())
                .flatMap { _ in
                    return dataTaskPublisher
                }
                .print("before retry")
                .retry(10)
                .eraseToAnyPublisher()
        }
        throw error
    }
    .map(\.data)
```

让我们首先为 `Publisher` 上的 `retry` 操作符构建一个重载扩展：

```
extension Publisher {
    func retry(_ retries: Int, withDelay delay: Int)
        -> Publishers.TryCatch<Self, Just<Void>.Delay<DispatchQueue>.FlatMap<Self, AnyPublisher<Self.Output, Self.Failure>>>
        where T == Self.Output, E == Self.Failure
    {
    }
}
```

这定义了两个输入参数，`retries` 和 `withDelay`，我们可以用它们来指定应该重试上游发布者的次数以及每次重试之间应间隔的时间（以秒为单位）。

由于我们将在新的操作符内部使用 `tryCatch` 操作符，我们需要使用它的发布者类型 `Publishers.TryCatch` 作为返回类型。

有了这个，我们现在可以通过粘贴现有的实现来实现操作符的主体：

```
extension Publisher {
    func retry(_ retries: Int, withDelay delay: Int)
        -> Publishers.TryCatch<Self, Just<Void>.Delay<DispatchQueue>.FlatMap<Self, AnyPublisher<Self.Output, Self.Failure>>>
        where T == Self.Output, E == Self.Failure
    {
        return self.tryCatch { error -> AnyPublisher<Self.Output, Self.Failure> in
            return Just(Void())
                .delay(for: .init(integerLiteral: delay),
                       scheduler: DispatchQueue.global())
                .flatMap { _ in
                    return self
                }
                .retry(retries)
                .eraseToAnyPublisher()
        }
    }
}
```

你可能已经注意到我们移除了错误检查。这是因为 `APIError` 是一个特定于我们应用程序的错误类型。由于我们希望使其成为一个可以在其他应用程序中也可以使用的实现，让我们看看如何使其更灵活。

## 条件性重试

为了使这段代码在其他上下文中可重用，让我们添加一个尾随闭包的参数，调用者可以使用它来控制操作符是否应该重试。

```
func retry(_ retries: Int, withDelay delay: Int, condition: ((E) -> Bool)? = nil) -> Publishers.TryCatch<Self, Just<Void>.Delay<DispatchQueue>.FlatMap<Self, AnyPublisher<Self.Output, Self.Failure>>> where T == Self.Output, E == Self.Failure {
    return self.tryCatch { error -> AnyPublisher<Self.Output, Self.Failure> in
        if condition?(error) == true {
            return Just(Void())
                .delay(for: .init(integerLiteral: delay),
                       scheduler: DispatchQueue.global())
                .flatMap { _ in
                    return self
                }
                .retry(retries)
                .eraseToAnyPublisher()
        }
        else {
            throw error
        }
    }
}
```

如果调用者没有提供该闭包，操作符将使用参数 `retries` 和 `delay` 进行重试。

有了这个，我们可以简化原始调用：

```
// ...
return dataTaskPublisher
    .retry(10, withDelay: 3) { error in
        if case APIError.serverError = error {
            return true
        }
        return false
    }
    .map(\.data)
// ...
```

## 实现指数退避的重试操作符

现在，让我们更进一步，实现一个带有指数退避的 `retry` 操作符版本。

> *指数退避通常作为计算机系统（如 Web 服务*^(⁷⁹)*）中速率限制*^(⁷⁸)*机制的一部分，用于帮助实施对资源的公平分配并防止网络拥塞*^(⁸⁰)*。（Wikipedia*^(⁸¹)*）

为了增加两次请求之间的延迟，我们引入一个局部变量来保存当前间隔，并在每次请求后将其加倍。为了实现这一点，我们需要将启动原始管道的内部管道包装在一个能够递增退避变量的管道中：

```
func retry(_ retries: Int,
           withBackoff initialBackoff: Int,
           condition: ((E) -> Bool)? = nil)
    -> Publishers.TryCatch<Self, Just<Void>.Delay<DispatchQueue>.FlatMap<Self, AnyPublisher<Self.Output, Self.Failure>>>
    where T == Self.Output, E == Self.Failure
{
    return self.tryCatch { error -> AnyPublisher<Self.Output, Self.Failure> in
        if condition?(error) ?? true {
            var backOff = initialBackoff
            return Just(Void())
                .flatMap { _ -> AnyPublisher<Self.Output, Self.Failure> in
                    let result = Just(Void())
                        .delay(for: .init(integerLiteral: backOff),
                               scheduler: DispatchQueue.global())
                        .flatMap { _ in
                            return self
                        }
                    backOff = backOff * 2
                    return result.eraseToAnyPublisher()
                }
                .retry(retries - 1)
                .eraseToAnyPublisher()
        }
        else {
            throw error
        }
    }
}
```

要仅对某些类型的错误使用指数退避，我们可以像之前一样实现闭包来检查错误。以下是一个代码片段，展示了如何对任何 `APIError.serverError` 使用初始间隔为 3 秒的递增退避：

```
return dataTaskPublisher
    .retry(2, withBackoff: 3) { error in
        if case APIError.serverError(_, _, _) = error {
            return true
        }
        else {
            return false
        }
    }
// ...
```

要忽略错误类型而使用指数退避，代码会更加简洁：

```
return dataTaskPublisher
    .retry(2, withIncrementalBackoff: 3)
// ...
```

## 总结

Combine 是一个非常强大的框架，它允许我们为应用程序组合出非常高效的数据和事件处理管道。

在本章中，你学会了如何将现有的 Combine 管道重构为可重用的 Combine 操作符，从而使你的代码更具可读性。

脚注 1 2 3 4



