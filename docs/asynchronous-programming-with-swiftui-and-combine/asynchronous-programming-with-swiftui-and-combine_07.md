# 4. 状态管理

SwiftUI 与你以前可能使用过的许多其他 UI 工具包不同：你不能直接操纵 UI 视图。你在屏幕上看到的任何变化都是由应用程序数据模型的状态及其转换决定的。你可能听说过“在 SwiftUI 中，视图是应用程序状态的函数”。

这意味着什么？为什么它如此重要？

命令式 UI 工具包有一个非常大的缺陷：它们允许开发者独立更新模型和视图。这常常导致状态不一致和部分更新。你可能曾经使用过表现出这种行为应用程序。例如，你在一个详情屏幕中进行了更改，但主屏幕上的更改却没有反映出来。^(³⁶)

SwiftUI 的设计者选择了一种不同的方法：SwiftUI 中的视图要么是静态的，要么显示由模型元素驱动的信息。你无法直接操纵 SwiftUI 视图。决定 UI 状态的数据被称为*数据源（Source of Truth）*，每个 UI 元素只能有一个数据源。

这导致了确定性的状态和一致的用户界面。

SwiftUI 提供了多种在你的应用程序中管理状态的方法。在本章中，我们将学习不同的技术，讨论它们的优点以及何时使用它们。

为了更好地理解这个复杂的主题，我们将研究一些现实场景，并讨论你可以在每种场景中使用状态管理模式。

## 在 SwiftUI 中管理状态

SwiftUI 视图是结构体，这意味着它们是值类型。选择值类型来定义应用程序 UI 的一个主要原因是，在复制值类型时，你可以确保应用程序的其他部分不会在背后更改数据。^(³⁷) 正如我们稍后将看到的，视图的 `body` 是一个计算属性，这使得开发者无法意外地直接修改视图。

然而，更新 UI 对于几乎任何应用程序都是一个关键特性，那么我们如何构建能够动态更新其 UI 的应用程序呢？

SwiftUI 提供了两个互补的工具，确保你可以自由地管理和更新数据模型，同时保证 UI 始终与数据模型保持同步。

这两种机制都建立在*属性包装器（property wrappers）*之上。

第一个工具帮助你将数据模型定义为能够发布其状态的形式。

其次，SwiftUI 在一个只有它自己能够控制的内存区域中为你管理 UI 的状态。

因此，SwiftUI 允许你以一种易于构建和可复用的方式定义数据模型。通过使 UI 更新*单向化*，SwiftUI 让你摆脱了必须始终保持视图和模型同步更新的负担。

让我们首先来看看将数据模型连接到应用程序用户界面的不同方式。

SwiftUI 提供了几种存储和传递数据的方法。哪种方法最合适取决于你的应用程序的哪个部分拥有数据、你使用的是值类型还是对象，以及视图需要对数据具有读/写访问权限还是只读访问权限。



## 绑定值类型

如果您想要在视图中显示的数据是枚举、结构体或简单类型，您可以使用 `@State` 或 `@Binding` 来包装该变量，或者直接绑定到该变量。

如果您只是想显示变量的值，您可以直接将变量连接到视图。例如：

```
let name = "Peter"
...
Text("Hello, \(name), how are you?")
```

这也适用于复杂类型，比如地址：

```
struct Address {
let street: String
let postCode: String
let city: String
let country: String
}
let appleHQ = Address(street: "One Infinite Loop",
postCode: "CA 95014",
city: "Cupertino",
country: "United States")
...
Text("Apple HQ: \(appleHQ.street)")
```

这种方法在许多情况下效果很好，例如在列表视图行中显示复杂对象的属性。但是，结构体在被分配给 `let` 常量后是不可变的，因此您在初始化后无法更改其属性。这意味着您无法从 SwiftUI 视图内部操作结构体的属性。此外，普通的结构体和属性不提供发布更改的机制，这意味着 SwiftUI 无法追踪对简单变量或结构体的任何更改。

如果您想让 SwiftUI 管理 UI 的更新以反映底层数据的变化，您需要使用它的状态管理属性包装器。

我们要看的第一个是 `@State`，它也是最容易使用的。将 `@State` 应用于属性，可以让 SwiftUI 框架管理该属性的状态，并通过重绘依赖视图来响应更新。反之，用户在 UI 中做出的任何更改（例如，通过拖动滑块或在 `TextField` 中输入文本）都将应用于该属性。

以下代码片段展示了一个著名的“Hello World”示例的用户界面：它定义了一个 `name` 变量，用户可以通过在 `TextField` 视图中输入其姓名来更改它。结果，`Text` 视图将显示一条包含用户姓名的问候语。

```
@State var name = "Peter"
...
TextField("Enter your name", text: $title)
Text("Hello \(title), nice to meet you!")
```

通过在 `name` 变量前添加 `@State`，SwiftUI 会为该变量创建一个 `Binding`，可以通过使用变量的 `$` 前缀版本（即 `$name`）的*投影值*来访问该绑定。我们在 `TextField` 视图中使用这个 `Binding`，以允许用户操纵底层值。在下面一行中，我们直接访问 `name` 属性以在 `Text` 视图中显示问候语。

我们将在本章后面更深入地了解这一切是如何在幕后工作的。

如果您的视图需要访问在其他地方（例如，在父视图中）定义的数据，那么它不拥有该数据的所有权。在这种情况下，您可以使用 `@Binding` 将数据连接到视图。这使视图能够读取、写入和观察数据。我们在前面的示例中已经使用了绑定：一个 `TextField` 需要一个 `Binding` 作为其第二个参数：`TextField(_ title: StringProtocol, text: Binding<String>)`。在示例中，我们使用 `@State` 为 `name` 变量创建了一个绑定，并将其传递给了 `TextField` 视图。

一个绑定可以引用一个 `@State` 变量或一个 `@Observable` 对象（稍后会详细介绍）。

当从几个更小、更专业的视图组装一个视图时，绑定特别有用。它们是帮助创建可重用视图的重要工具。

下面的代码示例展示了父视图和子视图如何通过在父视图中使用 `@State` 和在子视图中使用 `@Binding` 来共享相同的状态：

```
struct ParentView: View {
@State var favouriteNumber: Int = 42
var body: some View {
VStack {
Text("Your favourite number is \(favouriteNumber)")
ChildView(number: $favouriteNumber)
}
}
}
struct ChildView: View {
@Binding var number: Int
var body: some View {
Stepper("\(number)", value: $number, in: 0...100)
}
}
```

`@State` 和 `@Binding` 都最适合管理本地 UI 状态。例如，您可以使用 `@State` 来管理一个决定模态面板是否显示的布尔属性。特别是 `@Binding`，还可以用于将视图连接到更复杂对象的单个属性，我们将在本章后面看到这一点。

但是，您应该避免对想要持久化到磁盘或通过网络发送的复杂对象使用 `@State` 或 `@Binding`。SwiftUI 有更强大的工具来管理此类对象。

由于标记为 `@State` 的属性通常用于处理本地 UI 状态，您应该将它们设置为 `private`，以确保它们不会被外部意外修改。

## 绑定对象

如果您要在 SwiftUI 视图中使用的数据存在于引用类型（即 `class`）中，您应该使用 `@StateObject`、`@ObservedObject` 或 `@EnvironmentObject` 之一来管理其状态。您还需要使该类遵循 `ObservableObject` 协议。

一个遵循 `ObservableObject` 的类充当发布者，并通知其订阅者关于其标记为 `@Published` 的属性的更改。

从消费者（即订阅 `ObservableObject` 发送的更新的视图）的角度来看，这三个属性包装器的行为完全相同：视图（及其子视图）可以订阅 `ObservableObject` 的单个属性（通过直接属性访问或使用绑定），并在该对象的任何已发布属性发生更改时接收更新。

`@StateObject`、`@ObservedObject` 和 `@EnvironmentObject` 之间唯一的不同之处在于它们如何管理数据。

在我们查看它们具体如何管理数据之前，我们需要了解 `ObservableObject` 是如何工作的。

## ObservableObject

要将一个简单的 Swift 类转换为可观察对象，您需要使其遵循 `ObservableObject` 协议。由于这是一个类协议，您只能在类上使用它，而不能在值类型（例如结构体或枚举）上使用。

SwiftUI 本身没有定义 `ObservableObject`，而是从 Combine 框架中导入它，这绝非巧合。SwiftUI 利用了 Combine 的发布者，而无需开发者深入了解 Combine 框架和函数式响应式编程。在不了解 Combine 或使用其任何高级功能的情况下，构建 SwiftUI 应用绝对是可行的。尽管如此，Combine 提供了许多有用的功能，如防抖、错误处理、重试以及其他有用的特性。一旦您度过了初始学习曲线，在您的应用程序中集成此类功能，使用 Combine 将比手动实现容易得多。

使一个类遵循 `ObservableObject` 并将其部分属性标记为 `@Published`，将会把这个类转变为一个 Combine 发布者，当其任何一个已发布属性发生变化时，它会发出事件。一旦您在视图上声明了一个属于 `ObservableObject` 的属性，并将其标记为 `@StateObject`、`@ObservedObject` 或 `@EnvironmentObject`，您就可以将其属性连接到视图或其子视图。

SwiftUI 将开始观察这些对象，并根据需要重新渲染视图，以使其与模型的状态保持同步。



## `@StateObject`

当使用`@StateObject`时，SwiftUI 会管理底层对象的生命周期，确保它只被创建一次，即使 SwiftUI 需要因模型更新而重新创建整个视图也是如此。

这对于那些可能因视图外部事件而发生变化的视图来说非常重要。考虑以下代码片段。它展示了一个屏幕，允许用户使用`Stepper`选择一个数字。此外，用户可以通过点击`ColorPicker`视图来改变屏幕的前景色。该屏幕的开发者决定将步进器和包含数据的对象移到一个名为`StateStepper`的独立视图中。

```
class Counter: ObservableObject {
    @Published var count = 0
}

struct StateStepper: View {
    @StateObject var stateCounter = Counter()
    
    var body: some View {
        Section(header: Text("@StateObject")) {
            Stepper("Counter: \(stateCounter.count)", value: $stateCounter.count)
        }
    }
}

struct ContentView: View {
    @State var color: Color = Color.accentColor
    
    var body: some View {
        VStack(alignment: .leading) {
            StateStepper()
            ColorPicker("Pick a color", selection: $color)
        }
        .foregroundColor(color)
    }
}
```

一旦颜色改变，SwiftUI 将重新渲染所有需要改变颜色的元素。这将导致`StateStepper`被重新创建。

由于`stateCounter`被标记为`@StateObject`，`Counter`对象只会在第一次被创建，并且 SwiftUI 将管理其生命周期。因此，当用户决定更改屏幕颜色时，`stateCounter.count`的值不会被重置为零。

如果开发者选择使用`@ObservedObject`，那么更改颜色将导致`stateCounter`被重新创建，并且其中的数据将丢失。

### 何时使用

在以下情况下使用`@StateObject`：

- 当你需要监听`ObservableObject`中的更改或更新时
- 并且你希望监听的实例是在视图本身中创建的

也就是说，当要使用该对象的视图是该数据的所有者时。

## `@ObservedObject`

与`@StateObject`类似，`@ObservedObject`包装了一个`ObservableObject`，使其在视图中可用，以便视图（及其子视图）可以订阅该对象的已发布属性。

与`@StateObject`不同，由`@ObservedObject`包装的对象会在每次视图被重新创建时被重新创建。让我们沿用之前的代码片段，但这次使用`@ObservedObject`而不是`@StateObject`：

```
class Counter: ObservableObject {
    @Published var count = 0
}

struct ObservedStepper: View {
    @ObservedObject var counter = Counter()
    
    var body: some View {
        Section(header: Text("@ObservedObject")) {
            Stepper("Counter: \(counter.count)", value: $counter.count)
        }
    }
}

struct ContentView: View {
    @State var color: Color = Color.accentColor
    
    var body: some View {
        VStack(alignment: .leading) {
            ObservedStepper()
            ColorPicker("Pick a color", selection: $color)
        }
        .foregroundColor(color)
    }
}
```

运行这段代码时，每当用户选择一种颜色，`counter`都会被重新创建，导致`count`被重置为零。对用户来说，界面就像患了失忆症一样。

在 SwiftUI 的第一个版本中，`@ObservedObject`是在视图内创建和观察对象的唯一方式。现在你仍然可以这样做（至少编译器不会抛出警告，而且可能仍有不少 SwiftUI 应用在使用这种方法），但使用`@ObservedObject`来*创建*模型对象是一种应该避免的反模式。

所以如果你看到这样的代码：`@ObservedObject var foo = Bar()`，你应该重构代码并使用`@StateObject`。

### 何时使用

在以下情况下使用`@ObservedObject`：

- 当你需要监听`ObservedObject`中的更改和更新时
- 并且你想要在视图中观察的对象*不是*由该视图创建的，而是由视图外部创建的（例如，在父视图或应用结构中）

## `@EnvironmentObject`

理论上，`@StateObject`和`@ObservedObject`应该能为你提供足够的灵活性来构建任何需要访问共享状态的应用。在大多数情况下，你可以在应用的某个地方创建一个对象——例如，在应用对象本身中，或在某个顶级视图中，如主导航视图。然后，你可以通过将该对象注入到需要访问它的任何子视图的构造函数中，将其传递到视图层次结构中。

然而，并非所有视图都需要访问所有数据。例如，你的应用可能有一个表示已登录用户的共享状态。你可能想在主屏幕上显示用户的头像，也可能想在个人资料屏幕上显示他们的名字和姓氏，但要进入个人资料屏幕，用户可能需要先通过几个设置界面。而这些界面可能根本不需要访问用户对象。将用户对象拖过整个导航层次结构不仅没有必要，而且还会在不必要的地方在中间屏幕和用户对象之间创建紧密耦合。

对于这种情况，你可以使用`@EnvironmentObject`。顾名思义，它从环境中获取一个`ObservableObject`，并将其提供给视图。要注入一个对象到环境中，可以在任意视图上调用`.environmentObject(myObject)`。这将使`myObject`对所有子视图可用。要从环境中检索对象，可以在视图上声明一个属性，并将其标记为`@EnvironmentObject`。

```
class UserProfile: ObservableObject {
    @Published var name: String
}

struct EnvironmentObjectSampleScreen: View {
    @StateObject var profile = UserProfile(name: "Peter")
    @State var isSettingsShown = false
    
    var body: some View {
        VStack(alignment: .leading) {
            // ...
        }
        .sheet(isPresented: $isSettingsShown) {
            NavigationView {
                SettingsScreen()
            }
            .environmentObject(profile) // 非常重要，必须放在这里，而不是 NavigationView 内部！参见 https://developer.apple.com/forums/thread/653367
        }
    }
}

struct SettingsScreen: View {
    var body: some View {
        VStack(alignment: .leading) {
            NavigationLink(destination: UserProfileScreen()) {
                Text("User Profile")
            }
        }
    }
}

struct UserProfileScreen: View {
    @EnvironmentObject var profile: UserProfile
    
    var body: some View {
        VStack(alignment: .leading) {
            Form {
                Section(header: Text("User profile")) {
                    TextField("Name", text: $profile.name)
                }
            }
        }
    }
}
```

虽然可能很想为所有状态对象使用`@EnvironmentObject`，但值得注意的是，这种方法有一个严重的缺点：编译器无法检查你在尝试使用`@EnvironmentObject`获取环境中的`ObservableObject`之前是否已经注入了它。当试图从环境中检索一个不存在的对象时，你的应用将因运行时错误而崩溃。

SwiftUI 环境是一个非常强大的机制，我们将在后续章节中深入探讨更多细节。

### 何时使用

在以下情况下使用`@EnvironmentObject`：

- 当你需要监听`ObservedObject`中的更改和更新时
- 并且你不得不将一个`ObservedObject`通过几个不需要该对象的视图传递，然后才能到达需要访问该对象的视图



## 总结

在本章中，你学习了 SwiftUI 如何处理状态，并确保用户界面始终与应用程序的状态保持同步。

我们简要介绍了 SwiftUI 与 Combine 框架的关系，并了解到 `ObservableObject` 实际上是一个 Combine 发布者，它会将所有对其已发布属性的更新，传达给订阅了它的 SwiftUI 视图。

你学习了三种不同的属性包装器——`@StateObject`、`@ObservedObject` 和 `@EnvironmentObject`——它们如何管理所包装的可观察对象的生命周期，以及何时应该知道使用哪一种来将你的视图连接到应用程序的状态。

恭喜，你已经掌握了 SwiftUI 应用程序的关键构建模块之一！

脚注 1 2

