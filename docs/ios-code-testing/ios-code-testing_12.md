# 这就是装饰器模式的精髓。

`PersonalBankAccount` 类装饰了 `BankAccount` 类，在 `BankAccount` 提供的功能基础上增加了一些额外的功能。你可以在代码中的任何位置使用 `PersonalBankAcccount` 实例来代替 `BankAccount` 实例。你也可以通过继承 `BankAccount` 并修改子类中实现的方法来实现此模式。延续用于创建 `PersonalBankAccount` 类的方法，`BusinessBankAccount` 类将与 `PersonalBankAccount` 类非常相似，唯一的区别在于 `deposit()` 和 `withdraw()` 方法的实现：

```
func withdraw(_ amount:Float, _ person:AccountOwner?) -> Void {
    if withinDailyWithdrawalLimit(amount) {
        bankAccount.withdraw(amount, person)
        logWidthdrawalForAudit(amount, person)
    }
}
func deposit(_ amount:Float, _ person:AccountOwner?) -> Void {
    if withinDailyDepositlLimit(amount) {
        bankAccount.deposit(amount, person)
        logDepositForAudit(amount, person)
    }
}
```

## 使用协议解耦类

在上一节中，我们研究了一些可用于向遗留类添加代码的技术，以便能够为这些新代码编写测试，同时对现有遗留代码的影响最小。

随着时间的推移，当你决定开始为遗留代码编写测试时，你可能会尝试一次为一个类编写单元测试。然而，遗留代码库中的类通常是紧密耦合的，并且无法孤立地实例化单个类。

这种紧密耦合表现为一棵依赖类树，为了实例化你真正感兴趣的类，需要实例化所有这些依赖类。

当你将遗留类引入测试环境时，管理类之间的依赖关系将成为你将面临的最大挑战之一。在本节中，我们将研究如何使用协议来打破遗留代码库中常见的类之间的紧密耦合。

暂时考虑一下 `BankAccount` 类的实例变量，部分内容如下所示：

```
class BankAccount: NSObject {
    var accountName:String
    var accountNumber:String
    var sortingCode:String
    var accountType:AccountType
    var transactions:[Transaction]
    var owners:[AccountOwner]
    ...
    ...
}
```

`transactions` 和 `owners` 变量在该类与 `Transaction` 和 `AccountOwner` 类之间建立了强耦合关系。在不引入其他两个类的情况下，无法在测试中实例化 `BankAccount`。

你可以通过如下使用协议的方式来打破这些类之间的紧密耦合：

1.  创建两个新协议：`TransactionProtocol` 和 `AccountOwnerProtocol`。
2.  在 `TransactionProtocol` 协议中声明 `Transaction` 类所有的公开方法和公开实例变量。
3.  在 `AccountOwnerProtocol` 协议中声明 `AccountOwner` 类所有的公开方法和公开实例变量。
4.  修改 `Transaction` 类的声明，使其实现 `TransactionProtocol` 协议。
5.  修改 `AccountOwner` 类的声明，使其实现 `AccountOwnerProtocol` 协议。
6.  修改 `BankAccount` 类的声明，使其使用协议代替具体的类：

```
class BankAccount: NSObject {
    ...
    ...
    var transactions:[TransactionProtocol]
    var owners:[AccountOwnerProtocol]
    ...
    ...
}
```

通过为实例变量使用协议而不是具体的类名，你打破了 `BankAccount` 类先前存在的紧密耦合。现在可以轻松地将 `BankAccount` 类纳入测试，而无需引入 `Transaction` 和 `AccountOwner` 类。你可以使用存根对象来代替 `Transaction` 和 `AccountOwner` 类。

## 使用依赖注入创建更可测试的代码

依赖注入（DI）背后的思想是使类和方法之间的依赖关系更加明确。考虑以下添加到 `BankAccount` 类中的看似无害的方法，该方法用于提供某个特定时间点的账户余额。

```
func accountBalanceDescription() -> String {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "MMMM dd yyyy"
    return "The balance in your account as of \(dateFormatter.string(from:
Date())) is \(accountBalance)"
}
```

这个方法存在两个问题：

1.  每次调用此方法时都会创建一个新的 `DateFormatter` 实例。这是非常低效的。
2.  方法签名本身没有任何内容表明该方法会创建一个具有特定日期格式的日期格式化器。

该方法显然依赖于一个 `DateFormatter` 实例，并且这种依赖是隐藏的，因为你需要查看方法的实现才能意识到它的存在。在这种情况下，为了使其更加明确，你可以简单地将日期格式化器作为参数注入到方法中：

```
func accountBalanceDescription(_ dateFormatter:DateFormatter) -> String {
    return "The balance in your account as of \(dateFormatter.string(from:
Date())) is \(accountBalance)"
}
```

如果隐藏的依赖是资源或类的多个方法之间共享的数据源，那么问题会严重得多。例如，考虑以下一对方法：

```
func deposit(_ amount:Float, _ person:AccountOwner) -> Void {
    let transactionDate = NSDate()
    if let newTransaction = Transaction(txDescription: "Cash Deposit",
                                        date: transactionDate,
                                        isIncoming: true,
                                        amount: "\(amount)") {
        self.transactions.append(newTransaction)
        UserDefaults.standard.set(transactionDate,
                                  forKey: "lastDepositDate")
    }
}
func lastDepositDate() -> NSDate? {
    return UserDefaults.standard.object(forKey: "lastDepositDate")
        as? NSDate
}
```

这些方法的问题在于它们对共享的 `UserDefaults` 实例存在隐藏依赖。这种代码非常难以测试。同样，你可以通过将依赖关系暴露在作为方法输入的参数列表中来打破这些方法与 `UserDefaults` 类之间的紧密耦合：

```
func deposit(_ amount:Float, _ person:AccountOwner?,
             _ defaults:UserDefaults) -> Void {
    let transactionDate = NSDate()
    if let newTransaction = Transaction(txDescription: "Cash Deposit",
                                        date: transactionDate,
                                        isIncoming: true,
                                        amount: "\(amount)") {
        self.transactions.append(newTransaction)
        defaults.set(transactionDate, forKey: "lastDepositDate")
    }
}
func lastDepositDate(_ defaults:UserDefaults) -> NSDate? {
    return defaults.object(forKey: "lastDepositDate") as? NSDate
}
```

然而，这个方案给我们带来了一个新问题——这两个方法旨在访问同一个 `UserDefaults` 实例。通过将 `UserDefaults` 实例暴露在参数列表中，你现在允许用户可能将不同的 `UserDefaults` 实例注入到这些方法中。

在这种情况下，更好的解决方案是将 `UserDefaults` 实例设置为同时包含这两个方法的类的实例变量，并且不在方法签名中暴露 `UserDefaults` 对象：

```
class BankAccount: NSObject {
    var defaults:UserDefaults?
    ...
    ...
    ...
    func deposit(_ amount:Float, _ person:AccountOwner?) -> Void {
        let transactionDate = NSDate()
        if let newTransaction = Transaction(txDescription: "Cash Deposit",
                                            date: transactionDate,
                                            isIncoming: true,
                                            amount: "\(amount)") {
            self.transactions.append(newTransaction)
            self.defaults?.set(transactionDate, forKey: "lastDepositDate")
        }
    }
    func lastDepositDate() -> NSDate? {
        return self.defaults?.object(forKey: "lastDepositDate") as? NSDate
    }
    ...
    ...
    ...
}
```

明确依赖关系会导致对象之间的耦合更松散，并使隔离测试方法变得更容易。



## 总结

在本章中，你学习了重构遗留代码或将经过良好测试的代码添加到遗留代码库的不同方法。你学会了运用**单一职责原则**将大型类拆分为更小的类，并将新代码封装到方法或新类中。

你还学习了使用协议作为解耦类的方式，应用装饰器模式，以及通过依赖注入来创建更易于测试的代码。

## 备注

1. 《重构遗留代码》，Michael C. Feathers 著，Prentice Hall Professional Technical Reference 出版。ISBN 0-13-117705-2。

