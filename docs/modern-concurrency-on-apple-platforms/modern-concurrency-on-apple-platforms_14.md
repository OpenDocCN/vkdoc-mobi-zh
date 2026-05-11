# 主执行者与最终执行者

在第 [6] 章中，我们学习了执行者。执行者是同步自身内部状态的引用类型，因此从不同线程修改它们是安全的。这是通过保证一次只有一个任务或线程可以修改执行者的状态来实现的。执行者是在并发上下文中使用的可发送类型。执行者是你实例化的类型，并且在大多数情况下，可以像普通类一样对待。但是，当我们*需要*某些总是保证在同一个线程上运行，但可能分布在甚至不同文件中时，会发生什么呢？

在学习全局执行者之前，我想谈谈我们的老朋友：主线程。我现在想讨论这个，是因为它与本章的主题相关。



## 主线程

如果你为苹果平台编程已有一段时间，一定对主线程（The Main Thread）有所了解。如果这是你第一次听说它，别担心，我们将在本节中重温这个概念。主线程的唯一职责是运行你的 UI 代码。无论是要更新标签、为视图添加颜色，还是切换开关，你都需要在主线程上执行这些操作。我们不允许在主线程之外更新 UI，否则轻则引发警告，重则导致运行时崩溃。当我们运行异步任务时——无论是使用新的并发系统还是 GCD 等旧工具——若想将返回数据显示在 UI 中，就必须在主线程上完成。

由于主线程负责更新 UI，在其上执行任何高开销操作都会导致应用卡顿数秒，或使 UI 出现明显的卡顿和帧率下降。如果应用长时间无响应，系统将自行介入并终止它。正因如此，包括 `URLSession` 中所有网络调用在内的许多 API，默认都会在独立线程中运行——无论你使用的是这些方法的 `async/await` 版本，还是传统的基于闭包的版本。你*可以*强制让 `URLSession` 和其他异步 API 在主线程上运行，但绝对不应该这么做，而且我怀疑你永远找不到一个合理的借口。

简而言之，任何与 UIKit（以及扩展的 SwiftUI）相关的操作都必须在主线程上完成，并且需要避免用高开销任务阻塞主线程。传统上，我们会通过调用类似 `DispatchQueue.main.async` 的方式将数据交给主线程处理。这个方法接受一个在主线程上执行的闭包，因此非常适合在此处理 UI 相关操作。`DispatchQueue` 是 GCD 中的一个对象，其静态属性 `main` 指向始终在主线程上运行的“主队列”。这是一种快速且无痛地返回主线程的方式，既安全又便捷，但若大量使用，也容易形成令人头疼的“末日金字塔”。iOS 中以“UI”为前缀的任何对象（如 `UIButton`、`UISwitch`），或 macOS 中的任何 AppKit 对象，都应仅从主线程进行修改。

**提示**

从 Xcode 14 及其附带的所有操作系统版本开始，`UIImage` 和 `UIColor` 是“所有 UI 前缀对象都应从主线程修改”这一规则的特例。自该 Xcode 版本起，`UIImage` 和 `UIColor` 遵循 `Sendable` 协议，因此可以安全地在不同并发域之间传递。

在现代并发系统中，系统会决定是否在不同的线程中启动你的任务。请记住，我们之前学到：任务在遇到 `await` 关键字时会暂停，而系统可能会决定在另一个线程中执行该调用。如果发生暂停，你既不知道代码将在何处执行，甚至不知道它会使用哪个线程来交付结果。那么，在使用新并发系统时，我们如何将结果交付到主线程，以便在完成高开销任务后更新 UI 呢？问得好！

### 主执行器

我终于可以向大家解释一个贯穿本书、却一直难以找到合适时机说明的概念了。没错，我说的就是主执行器（Main Actor），在代码中表现为 `@MainActor`。

UIKit 和 SwiftUI 都是庞大的框架。仔细想想，它们由成百上千个类组成，并且很可能分散在框架内的不同文件中。它们都有一个共同要求：任何 UI 更新都应在主线程上完成。将某个声明标记为 `@MainActor`，意味着该特定功能将在主执行器上运行。`@MainActor` 本质上被视为一种属性。自 iOS 15、macOS 12、watchOS 8 和 tvOS 16 起，这些框架中的大多数类都已标记为 `@MainActor`。对于 SwiftUI，创建 `ObservableObjects` 需要手动标记为 `@MainActor`，因为该协议并未自带此标记。将 UIKit、SwiftUI 和 AppKit 中的所有对象都标记为 `@MainActor`，是一种确保它们全部在主线程上运行的优雅解决方案。

#### 使用 `@MainActor`

与 `@Sendable` 属性类似，你也可以在多种场景下使用 `@MainActor`“属性”。首先，你可以将其作为类型声明（如结构体或类）的一部分。代码清单 8-1 声明了两种新类型，其中一种被标记为 `@MainActor`。

```
struct MusicAlbum {
let name: String
let artist: String
let releaseYear: Int
}
@MainActor class MusicLibrary {
var name: String
var albums: [MusicAlbum] = []
init(name: String) {
self.name = name
}
}
代码清单 8-1
为类添加 @MainActor
```

因此，我们对 `MusicLibrary` 实例执行的每一操作也都需要在主执行器上运行。`MusicLibrary` 中的每个属性和方法也都会在主执行器上运行。假设你有一个函数 `createLibraryAndAddAlbum`，该函数是在 `MusicLibrary` 对象本身之外创建的。你可能会尝试在这个新函数中操作你的音乐库。在代码清单 8-2 中，我们尝试在未运行于 `@MainActor` 上下文中使用该类型。

```
func createLibraryAndAddAlbum() {
let library = MusicLibrary(name: "Andy's Library")
let album = MusicAlbum(name: "Imaginareum", artist: "Nightwish", releaseYear: 2011)
library.shows += [album]
}
代码清单 8-2
在非 Main Actor 上下文中操作 MusicLibrary
```

如果尝试编译，你会遇到与尝试在并发域之间发送非 Sendable 类型时非常相似的错误。即：

```
Call to main actor-isolated initializer 'init(name:)' in a synchronous nonisolated context
...
Property 'albums' isolated to global actor 'MainActor' can not be mutated from this context
```

通过为 `createLibraryAndAddAlbum` 添加 `@MainActor`，该函数也将在主执行器上运行。这样，`MusicLibrary` 和 `createLibraryAndAddAlbum` 函数将在同一执行器（同一线程）上运行，无论它们是否位于同一文件，甚至跨越不同框架。这就是主执行器被称为*全局执行器*的原因。

代码清单 8-3 将能够无错编译。

```
@MainActor
func createLibraryAndAddAlbum() {
let library = MusicLibrary(name: "Andy's Library")
let album = MusicAlbum(name: "Imaginareum", artist: "Nightwish", releaseYear: 2011)
library.albums += [album]
}
代码清单 8-3
我们可以将 @MainActor 添加到分散的声明中，即使它们位于不同文件或框架中
```

如果你位于另一个执行器中，可以调用标记为 `@MainActor` 的方法，但这需要异步完成（使用 `await` 或 `async let`），因为 `@MainActor` 会同步所有使用它的声明之间的内部状态。

这正是许多人在使用主执行器时常感困惑的地方。

假设你在 `MusicLibrary` 对象中加入了代码清单 8-4 中的方法。

```
@MainActor
func populateFromWebService() async {
albums = []
albums = try await WebService.shared.albumsFromService()
}
代码清单 8-4
一个用于获取专辑的异步方法
```

假设 `populateFromWebService` 方法是一个会暂停的长时间运行操作。执行器将在何处执行？

这个长时间运行的操作——即 `populateFromWebService` 函数本身——仍会在另一个线程上运行。你无法预知其运行位置，只知道主执行器上的方法会暂停，线程在其他工作完成后才会返回继续执行。



在主 actor 本身上执行的，是 `albums` 方法的*赋值*操作。仅仅是赋值。`populateFromWebService` 会在其他地方运行，但其返回数据赋值给 `self.albums` 的操作则会运行在主 actor 上。许多人都误以为 `populateFromWebService` 本身会在主 actor 上执行。也就是说，如果这个方法正在从某个慢速服务下载 JSON 数据并解析它，或者执行任何耗时超过几毫秒的操作，它就是在主线程上运行的。这种理解是不正确的，幸运的是，全局 actor 系统比这要智能得多。如果你在 `@MainActor` 上运行某些内容，要知道它的所有语句都会在主 actor 上运行，但如果它遇到了一个引起挂起的调用，那么触发挂起的操作的执行将在其他地方完成，而数据则会在主线程上返回（并进行赋值）。可以说，`await` 的“左侧”会在主线程上执行，而 `await` 关键字的“右侧”则会在其他地方进行。

你不必将整个类或结构体的声明都标记为主 actor。如果你只需要某些属性和方法在主 actor 上运行，你可以仅将它们标记为 `@MainActor`。代码清单 8-5 修改了 `MusicLibrary` 对象，添加了一个新变量和一个新方法，移除了整个声明中的 `@MainActor` 标记，并将其添加到了新添加的符号上。

```
class MusicLibrary {
    var name: String
    var albums: [MusicAlbum] = []
    @MainActor var albumCover: UIImage?
    init(name: String) {
        self.name = name
    }
    @MainActor func updateAlbumCover() {
        // ... 从互联网下载新封面
        // 并更新 albumCover 属性。
    }
}
代码清单 8-5
仅将部分属性和方法标记为 @MainActor
```

现在，这个类可以在任何地方使用，但任何我们对 `albumCover` 想要执行的操作，都必须在运行于主 actor 的上下文中完成。

如果你将整个类隔离到主 actor 上运行（通过将其标记为 `@MainActor class MyClass`...），那么该类将始终在主 actor 上运行，从其他地方调用它则需要等待。如果你有一些方法需要对主 actor 是 `nonisolated`（非隔离）的（如同常规 actor 定义，全局 actor 可以拥有非隔离的方法），你可以将其标记为 `nonisolated`，并从任何其他并发上下文中使用它。

代码清单 8-6 将 `MusicLibrary` 类标记为在主 actor 上运行，并为其添加了一个 `nonisolated` 方法。

```
@MainActor
class MusicLibrary {
    var name: String
    var albums: [MusicAlbum] = []
    @MainActor var albumCover: UIImage?
    init(name: String) {
        self.name = name
    }
    nonisolated func libraryPurpose() -> String {
        "这个库存放着我最喜欢的歌曲"
    }
}
代码清单 8-6
向运行在主 actor 上的类添加 nonisolated 方法
```

请注意，`nonisolated` 方法必须是*真正*隔离的。你不能返回该类中的某个属性派生的属性。我们无法返回 `name`、`albums`，甚至是专辑数量（`albums.count`），因为这些属性都是隔离到主 actor 上的，我们无法通过非隔离的上下文返回隔离的属性。同样，也不能对存储属性使用 `nonisolated` 关键字。

就像 `@Sendable` 一样，我们也可以将闭包和函数标记为 `@MainActor`，以便让它们在主线程上运行。我们已经看到函数如何被标记为 `@MainActor`。对于闭包，你可以使用类似于 `@Sendable` 的语法，但将 `@Sendable` 替换为 `@MainActor`。总的来说，任何可以使用 `@Sendable` 的地方，也都可以使用 `@MainActor`。这包括变量闭包声明、作为函数参数的闭包声明等等。

代码清单 8-7 展示了如何将闭包标记为 `@MainActor`。

```
Task { @MainActor in
}
代码清单 8-7
将闭包标记为在 @MainActor 上运行
```

在这个特定示例中，我们声明了一个将在主 actor 上运行的 Task。我向你展示这个是为了让你看到，你真的可以在任何地方使用 `@MainActor`。除非是为了更新 UI，否则在主线程上运行闭包可能不是一个好主意。

你也可以通过使用静态的 `run` 方法在 `@MainActor` 上运行任何代码。代码清单 8-8 展示了如何做到这一点。

```
Task {
    await MainActor.run {
        // 更新你的 UI。
    }
}
代码清单 8-8
在主 actor 上运行代码
```

`await` 关键字是必需的。因为我们可能并非处于也在主 actor 上运行的上下文中，所以这些调用需要异步进行。这种在主 actor 上运行代码的方法很有用。如果你不得不继续编写基于闭包的代码，并且还无法完全迁移到 `async/await`，你可以使用这种方法快速将信息传递回主线程，而无需使用 GCD 及其 `DispatchQueue.main.async` 方法。

## 理解全局 Actor

要理解什么是全局 actor，最简单的方法就是理解主 actor。

正如我们之前所说，主 actor 是一个全局 actor。全局 actor 可以应用于各种声明，即使它们位于框架中的不同文件里。之前我们提到过，UIKit（除了 SwiftUI 之外创建 iOS 应用的主要框架）及其 macOS 对应框架 AppKit，它们的类型分散在不同的文件中。必须有一种方法可以确保这些 UI 类以与新的 Swift 并发系统良好协作的方式在主线程上运行。

一个可以跨不同声明应用的 actor（即使它们位于不同文件中）就是*全局 actor*。正如你本章所见，主 actor 是在 Apple 平台上开发现代并发应用的一个非常重要的组成部分，因为它为我们提供了一种在主线程上执行工作以更新 UI 的方式。主 actor 将同步所有对其的访问，以确保所有 UI 更新都能正确完成，且没有竞态条件。

`@MainActor` 是 Apple 提供给我们的一种全局 actor。它很出色，能完成它的工作，但我们可以创建自己的全局 actor 吗？答案是响亮的：可以！



### 创建全局 Actor

您可以创建自己的全局 Actor，使其与程序的其他部分隔离，就像 `@MainActor` 将其状态隔离到主线程以进行 UI 更新一样。为此，声明一个将用作属性的结构体，并为其赋予一个名为 `shared` 的静态属性。同时使用 `@globalActor` 属性标记此结构体。

列表 8-9 声明了一个名为 `@MediaActor` 的全局 Actor。

```
@globalActor
struct MediaActor {
    actor ActorType { }
    static let shared: ActorType = ActorType()
}
列表 8-9
声明我们自己的全局 Actor
```

现在，我们标记为 `@MediaActor` 的所有内容都将在同一个 Actor 上运行，自动同步全局 Actor 的状态，并确保不会发生数据竞争。在此示例中，我们将其命名为 `MediaActor`，但您可以根据应用程序上下文为您的 Actor 命名任何合适的名称，例如 `@DatabaseActor`。

在列表 8-10 的示例中，我们将创建一个全局的 `Videogame` 数组。然后，我们将从多个不同的位置对其进行读写，以展示我们的 `@MediaActor` 的实际运行情况。

```
struct Videogame {
    let id = UUID()
    let name: String
    let releaseYear: Int
    let developer: String
}
@MediaActor var videogames: [Videogame] = []
列表 8-10
创建一个新对象以及一个在我们的 MediaActor 上运行的全局变量
```

注意

在此示例中，`videogames` 是一个全局变量。通常，我不赞同这样使用全局变量，但我认为这是一个学习全局 Actor 的极好示例。

现在，假设您有一个视图控制器，可以在其中对此数组执行操作。我们可以添加一个随机电子游戏、移除一个随机电子游戏以及检索一个随机电子游戏的信息。列表 8-11 展示了这样一个视图控制器的精简版本。

```
@MainActor
class ViewController: UIViewController {
    @MediaActor
    func addRandomVideogames() {
        let zeldaOot = Videogame(name: "The Legend of Zelda: Ocarina of Time", releaseYear: 1998, developer: "Nintendo")
        let xillia = Videogame(name: "Tales of Xillia", releaseYear: 2013, developer: "Bandai Namco")
        let legendOfHeroes = Videogame(name: "The Legend of Heroes: A Tear of Vermilion", releaseYear: 2004, developer: "Nihon Falcom")
        videogames += [zeldaOot, xillia, legendOfHeroes]
    }
    @MediaActor
    func removeRandomvideogame() {
        if let randomElement = videogames.randomElement() {
            videogames.removeAll { $0.id == randomElement.id }
        }
    }
    @MediaActor
    func getRandomGame() -> Videogame? {
        return videogames.randomElement()
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        Task {
            await addRandomVideogames()
            await removeRandomvideogame()
            if let randomGame = await getRandomGame() {
                print("Random game: \(randomGame.name)")
            }
        }
    }
}
列表 8-11
添加操作以处理 videogames
```

这看起来有很多内容需要解析，但实际上非常直接。

首先，`class ViewController` 声明被标记为 `@MainActor`。在所有随 Xcode 13 发布的 OS 版本中，我们不需要将 `@MainActor` 属性添加到类，但我决定添加它，以便您能看到一个声明可以包含多个在不同 Actor 上运行的符号。此类的大部分内容将在 `@MainActor` 上运行，但 `addRandomVideogames`、`removeRandomVideogame` 和 `getRandomVideogame` 都将在我们新的 `@MediaActor` 上运行。因为它们都具有我们自己的 `@MediaActor` 属性，所以它们可以与我们之前声明的全局 `videogames` 变量交互。并且它们都将以线程安全的方式与其交互，因为它们都隔离在 `@MediaActor` 上。这是一个优美的系统，我们可以标记代码的分布式部分以在同一个线程或 Actor 上运行，其内部的所有同步将防止我们引入数据竞争。

在 `viewDidLoad` 中，我们将同时运行所有电子游戏操作。我们将添加一个电子游戏，移除一个电子游戏，然后获取一个来打印其信息。将 `viewDidLoad` 标记为 `@MediaActor` 没有意义，即使我们尝试这样做，也会被阻止，因为已知此方法在大多数实现中会修改 UI。`viewDidLoad` 必须在主 Actor 上运行。

由于 `viewDidLoad` 在 `@MainActor` 上运行，而我们所有的电子游戏操作都在 `@MediaActor` 上运行，如果我们想从一个全局 Actor 访问另一个全局 Actor，我们需要*异步地*执行此操作。因此，我们使用 `Task {}` 创建一个异步上下文，并且我们需要对每个电子游戏操作使用 `await`，或者如果合理的话，使用 `async let` 并发调用它。获取电子游戏也是一个异步操作，因此我们可以在 `if let` 的右侧使用 `await` 来调用它。

现在，如果我们想有另一个操作这些电子游戏的函数 `showAvailableVideogames`，它可以放在任何地方。假设我们有一个虚构的 `Functions.swift` 文件，它完全独立于 `ViewController` 和 Actor 声明，我们可以就在那里添加新函数，添加 `@MediaActor` 属性，那么在 `@MediaActor` 上运行的任何内容都可以同步与其交互，而在其外部的内容则异步交互。列表 8-12 展示了这个新方法。

```
@MediaActor
func showAvailableGames() {
    for game in videogames {
        print("\(game.name)")
    }
}
列表 8-12
showAvailableGames 方法位于其自己的文件中，但仍隔离在 @MediaActor 中
```

## 总结

全局 Actor 允许我们标记分布在多个文件和其他逻辑或物理位置（例如不同框架）中的声明，并使它们在同一个线程上运行。我们说这些声明*隔离*于它们所运行的 Actor。全局 Actor 会同步其所有访问，对于开发者来说，只需像使用属性一样使用全局 Actor，即可确保它们在同一个 Actor 上运行并避免数据竞争。

Apple 在其所有具有 UI 的平台（包括 iOS、macOS、watchOS 和 tvOS）上都提供了 `@MainActor` 全局 Actor。每个 UI 元素，无论它来自 UIKit、AppKit 还是 SwiftUI，都隔离在主 Actor 上运行，如果我们想要更新或刷新 UI，我们需要在运行这些更新之前进入 `@MainActor`。这将确保所有对主线程的访问都由主 Actor 同步，它会刷新我们的 UI，并且我们不会看到与在主线程之外绘制 UI 相关的警告或错误。

我们可以为自己的功能创建自己的全局 Actor。例如，如果我们有一个与数据库交互的框架，我们可以为它的所有类添加我们自己的全局属性，以确保所有读写操作都发生在同一个线程中，从而避免数据竞争。

