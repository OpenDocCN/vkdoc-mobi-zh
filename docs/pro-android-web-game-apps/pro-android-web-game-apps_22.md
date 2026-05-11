# 排版后的文本

注册的回调函数，当操作完成时被调用。

让我们来看一下代码，如列表 4-1 所示。

## 第 4 章：动画与精灵

**127**

**列表 4-1.** **加载图像，最简单的示例**

```
var img = new Image(); // 创建新的 Image 对象。加载尚未开始。
img.onload = function() { // 当加载完成时调用此函数
    alert("Image is loaded"); // 此时你知道图像已就绪
};
img.src = "img/knight.png"; // 当你设置 src 属性时，浏览器开始下载文件。
```

图像加载是一个异步操作：一旦你设置了`src`属性，浏览器就开始加载字节数据，但脚本会继续运行。当加载完成时，浏览器会调用一个已注册的回调函数来通知你的脚本操作的结果。

你可以同时加载多个图像，但浏览器不保证任何特定的加载顺序。例如，一个最后开始加载的小图像可能最先加载完成。实际上，这意味着你始终需要检查每个已加入加载队列的图像的状态，而像“如果 `b.png` 已加载，那么 `a.png` 可能也加载完了”这样的假设是不可靠的。

**注意：** 另一个处理所有资源（不仅仅是图像）的重要规则：你绝不应依赖良好的网络状况，或假设你等待的资源 100%可用。当你创建游戏时，你通常是在一个运行如钟表般精确的本地环境中工作。在现实世界中，互联网连接可能在你最意想不到的时候中断。你应该为此做好准备，要么重试下载资源，要么通知用户问题并退出，要么在没有资源的情况下继续。在没有资源的情况下继续运行并不意味着你可以忽略事故。对于图像，如果不重要的图像加载失败，你可以使用占位图像来代替正确的图像（好吧，我们没能加载巫师帽子的纹理，但游戏的其他部分已加载完成，所以我们为什么要结束这个世界呢？）。

列表 4-2 展示了如何处理图像问题。请注意加粗的行。

**列表 4-2.** **使用错误回调**

```
var img = new Image();
img.onload = function() {
    alert("Image loaded");
};
img.onerror = function() { // 当出现问题时调用此回调
    alert("Failed to load the image");
};
img.src = "img/knight.png";
```

加粗的行显示了你应该在`Image`对象上注册的第二个回调函数——`onerror`。当图像无法加载时，此函数被调用，并给你运行后备机制的机会。

**注意：** 为什么我们使用 PNG 图像？PNG 已成为游戏开发的事实标准。它是一种*无损*格式，意味着即使在压缩后它也能保持像素完美（与 JPEG 不同）。它是开放的并且免费使用。此外，它支持透明度和半透明度。对于精灵来说，还需要什么呢？

#### 加载多个图像

通常你的游戏需要不止一个图像。你必须跟踪每个精灵的加载状态，对问题做出反应，并执行其他小的维护任务。创建一个负责此类任务的工具类是个好主意。我们想进一步简化图像加载！

我们需要管理多个图像的加载，当加载完成时触发一个单一的回调。由于图像加载是一个相对较慢的操作，我们需要某种进度指示。考虑到我们在创建一个用户友好的界面，我们想显示一个进度条。我们想要实现的最终任务是给每个图像一个别名：一个用于在项目中标识图像的字符串。使用别名比使用文件名要方便得多。文件名经常变化，而别名保持不变。

让我们从结果开始。列表 4-3 展示了我们如何使用`ImageManager`来预加载游戏所需的图像。



## 第 4 章：动画与精灵

**清单 4-3.** *ImageManager API：一种稍好的图像加载方式*

```
function init() {
    var canvas = initFullScreenCanvas("mainCanvas");
    var imageManager = new ImageManager();
    imageManager.load({
        "arch-left": "img/arch-left.png",
        "arch-right": "img/arch-right.png",
        "knight": "img/knight.png"
    }, onDone, onProgress);
}
```

`onProgress()` 函数：

```
function onProgress(loaded, total, key, path, success) {
    if (success) {
        // 进度条
        console.log("已加载 " + loaded + " / " + total);
    } else {
        // 错误处理
        console.log("错误：无法加载 " + path);
    }
}
```

`onDone()` 函数：

```
function onDone() {
    console.log("所有图片已加载完成");
}
```

首先，我们将三张图片（它们的别名和路径）添加到队列中。加载会立即开始。我们传入两个回调函数：`onLoaded` 和 `onProgress`。当所有图片都已加载就绪时，`onLoaded` 会被调用。每当得知某张图片的加载结果（成功或失败）时，`onProgress` 会被调用。我们可以利用这个回调来更新进度条，或向用户提示错误信息。该回调接受以下五个参数：

- `loaded`：目前已加载的图片数量；可用于移动进度条。
- `total`：队列中图片的总数。
- `key`：图片的别名。
- `path`：图片文件的实际路径；有助于调试加载问题。
- `success`：指示加载是否成功的标志。如果一切正常且图片可供渲染，则为 `true`；如果出现问题，则为 `false`。

现在让我们动手编写代码来实现这个管理器。

**注意：** 我假设你已经习惯了创建新类，并且知道你应该创建名为 `ImageManager.js` 的新文件，将其放入 `js` 文件夹，并在 HTML 页面中添加额外的 `<script>` 标签来加载它。

我们从构造函数开始。清单 4-4 展示了代码。

**清单 4-4.** *ImageManager 的构造函数*

```
function ImageManager(placeholderDataUri) {
    this._images = {};
}
```

`this._images` 是保存所有已加载图片的对象；`this._images` 的键是图片别名，值则是实际的图片对象。图片加载完成后，该对象会类似下面这样：

```
this._images = {
    "knight": [已加载的图片],
    "arch-left": [已加载的图片],
    "arch-right": [已加载的图片]
}
```

一旦图片加载完成，就可以非常方便地获取它们，如清单 4-5 所示。

**清单 4-5.** *`get` 函数通过别名返回已加载的图片*

```
_p.get = function(key) {
    return this._images[key];
};
```

`ImageManager` 类中最有趣的部分当然是 `load` 函数，它负责排队加载图片。清单 4-6 展示了这个函数的完整代码，我接下来会对其进行解释。

**清单 4-6.** *ImageManager 类的核心：发起图片加载的 `load` 函数*

```
_p.load = function(images, onDone, onProgress) {
    // 图片队列
    var queue = [];
    for (var im in images) {
        queue.push({
            key: im,
            path: images[im]
        });
    }
    if (queue.length == 0) {
        onProgress && onProgress(0, 0, null, null, true);
        onDone && onDone();
        return;
    }
    var itemCounter = {
        loaded: 0,
        total: queue.length
    };
    for (var i = 0; i < queue.length; i++) {
        this._loadItem(queue[i], itemCounter, onDone, onProgress);
    }
};
```

我们为用户想要加载的图片创建了一个队列。对于每张图片，我们存储其 `key`（别名）和 `path`（路径）。如果发现队列为空，我们会立即通知两个监听器，表示加载已完成（实际上，如果没有需要加载的内容，加载就会立即完成且没有任何错误）。

请注意下面这个表达式：

```
onProgress && onProgress(0, 0, null, null, true);
```

它的含义是“如果 `onProgress` 存在（不是 `null` 或 `undefined`），则调用它，否则什么都不做。” 这个表达式是一个简单的逻辑与（AND）运算。其巧妙之处在于：当表达式的第一部分为 `false`（`null` 和 `undefined` 会被转换为 `false`）时，结果保证为 `false`，因此 JavaScript 解释器就不会再去处理后续部分。


```markdown
# 排版后的内容

调用整个第二部分。换句话说，该表达式表示：“如果`onProgress`函数已定义，则调用它。” 这等价于：

```javascript
if (onProgress)
    onProgress(0, 0, null, null, true)
```

但被写成一行代码。您可以根据自己的喜好选择哪种写法。

接下来，我们创建`itemCounter`——用于跟踪此特定队列执行进度的对象：队列中的项目数量以及已加载的项目数量。请注意，`itemCounter`是一个局部变量，它“关联”于单个队列，而不是整个对象。如果您决定调用两次`load()`：

```javascript
imageManager.load({
    "arch-left": "img/arch-left.png",
    "arch-right": "img/arch-right.png"
}, onArchLoaded, onArchProgress);

imageManager.load({
    "knight": "img/knight.png"
}, onKnightLoaded, onKnightProgress);
```

那么您将拥有两个不同的队列、两个不同的计数器以及一组不同的回调函数。这些计数器不会冲突，同时调用也完全正常。如果我们使用单个共享对象来保存项目，则会出现各种错误。尽管此功能可能不常用，但让代码尽可能健壮是件好事。

在最后几行中，我们调用了`_loadItem()`——该函数为每个项目创建`Image`对象并设置`src`属性，以使浏览器加载图像。此函数的代码如清单 4-7 所示。

**第 4 章：动画与精灵**

**清单 4-7.** `_loadItem`函数，用于启动加载并为队列中的每个图像设置`onload`和`onerror`回调

```javascript
_p._loadItem = function(queueItem, itemCounter, onDone, onProgress) {
    var self = this;
    var img = new Image();
    img.onload = function() {
        self._images[queueItem.key] = img;
        self._onItemLoaded(queueItem, itemCounter, onDone, onProgress, true);
    };
    img.onerror = function() {
        self._onItemLoaded(queueItem, itemCounter, onDone, onProgress, false);
    };
    img.src = queueItem.path;
};
```

这行代码：

```javascript
var self = this;
```

是您在需要使用回调时使用的典型模式。在回调函数体内，`this`关键字引用的是状态发生变化的图像，而不是`ImageManager`对象。为了保存对`ImageManager`实例的引用，我们引入了一个名为`self`的额外变量。如果没有`self`变量，则无法从回调代码中引用`ImageManager`函数。解决此问题的另一种方法是使用`bind()`函数，我们将在本书后面探讨。

`_loadItem()`函数中的其余代码很容易理解。对于队列中的每个图像，它都会注册`onload`和`onerror`监听器。如果图像加载成功，则会使用相应的键将其添加到内部存储中。

`_onItemLoaded()`函数只是调用传递给原始`load()`函数的监听器。请看清单 4-8 中的代码。

**清单 4-8.** `_onItemLoaded`函数处理图像加载的结果，包括成功和失败两种情况

```javascript
_p._onItemLoaded = function(queueItem, itemCounter, onDone, onProgress, success) {
    itemCounter.loaded++;
    onProgress && onProgress(itemCounter.loaded, itemCounter.total,
                             queueItem.key, queueItem.path, success);
    if (itemCounter.loaded == itemCounter.total) {
        onDone && onDone();
    }
};
```

由于图像已加载完毕（无论成功与否），此函数会增加计数器，并使用适当的参数调用`onProgress`。如果计数器达到了图像总数，则`onItemLoaded()`还会调用`onDone()`。

就是这样。这个小型类（仅约 50 行）使图像加载变得更加容易！此外，它还具有一些非常好的特性。首先，您可以在并行队列中加载图像，而无需担心计数器冲突。其次，两个回调都是可选的。因此，如果您只需要知道图像何时加载完毕，而不关心进度，您的代码将变得更加简单，如下所示：

```javascript
imageManager.load({
    "arch-left": "img/arch-left.png",
    "arch-right": "img/arch-right.png"
}, function() {
    // 加载完成后的回调
});
```
```


```javascript
alert("两个架构部分均已加载");
```

`});`

借助 `ImageManager` 加载图像的完整示例，连同本章的源代码，一并收录在 `01.basic_image_manager.html` 文件中。

#### 使用数据 URL 作为图像

还记得我曾提到，为一个非关键图像加载失败时使用的占位图创建一个备用方案是个好主意。例如，在 3D 应用中，单个纹理丢失并不足以成为用错误终止游戏的充分理由。

现在我们来思考几个问题。如果我们连占位图都无法加载，该怎么办？有没有一种方法可以确保图像无论如何都能被加载？事实证明，确实存在这样的方法。毕竟，图像就是一组字节，那么，如果我们将这些字节直接存储在 JavaScript 代码中，并将其直接传输到图像对象中，会怎样呢？

有一种方法可以实现。`Image` 对象的 `src` 属性支持所谓的 **data URL（数据 URL）**。数据 URL 的样式如下：

```
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADIAAAAyCAYAAAAeP4ixAAAAm0lEQVRoQ+
3YMRWAMBQEwcRT/Cv4nuAhYYtQDQaAyXIFe2aedfk65+zLt1jbiwRiJxKwpBWwlrSClrQClrQKlrSKlt
UKWtIKWFarYEmraFmtoCWtgGW1Cpa0ipbVClrXf5x9z/LHvzMvEk7diRQsaRUtH3vQklbAslsFS1pFy2
oFLWkFLKtVsKRVtKxW0JJWwLJaBUtaRctqBa0X1+W43qGn25cAAAAASUVORK5CYII=
```

**图 4-4.** *无图占位符*

**注意：** 数据 URL 背后并没有什么魔法。其格式非常简单：`data:[<MIME 类型>][;charset=<编码>][;base64],<数据>`。在我们的例子中，MIME 类型是 `image/png`，且省略了字符集/编码部分。这里没有二进制图像编码，字符集/编码部分仅对文本信息有意义。然后，在 `base64` 之后是 Base64 编码的 PNG 数据。

`Base64` 是一种以文本字符串形式编码二进制数据的格式。`Base64` 用于在操作文本的系统中存储二进制数据。例如，无法将图像放入 XML 节点中，因为图像文件是二进制格式；而 XML 节点只能包含文本。由于 `Base64` 是一种文本格式，你可以将图像编码为 `Base64` 并存储在 XML 节点中。数据 URI 是另一个例子。URI 是文本，在其原始形式中不能附加任何二进制数据，但 `Base64` 编码解决了这个问题，并允许在有效的 URI 内存储图像数据。

本质上，数据 URL 是某些二进制数据的字符串表示形式，其前面带有元数据。例如，这个 URL 代表一个 50×50 像素的小图像，白色背景上有两个灰色矩形——正是我用作“无图”占位符的那个（见图 4-4）。

根据这个字符串构建图像的代码相当简单，如清单 4-9 所示。

**清单 4-9.** *从数据 URL 创建图像*

```javascript
var image = new Image();
image.src = "data:image/png;base64,iVBORw0KGgoAAAAN … 5CYII=";
```

这是一种在 JavaScript 中创建有效图像的可靠方法。这里不涉及网络操作，图像数据直接传输到 `Image` 对象中。尽管看起来图像是瞬间创建的，但对于数据 URL 并非如此。图像的加载方式与从网络下载的图像完全相同——在触发 `onload` 监听器之前，你无法使用它。另一方面，加载过程非常快，你可以放心地认为，该图像的创建速度会快于任何从服务器下载的其他图像。

使用数据 URL 作为图像数据源的完整示例，可以在本章的源代码文件 `02.data_url.html` 中找到。

让我们更新管理器，使其对无图占位符使用数据 URL（见清单 4-10）。

**清单 4-10.** *更新 ImageManager 以使用数据 URL 作为占位符*

```javascript
function ImageManager(placeholderDataUri) {

    this._imageQueue = [];

    this._images = {};

    if (placeholderDataUri) {

        this._placeholder = new Image();

        this._placeholder.src = placeholderDataUri;

    }

}
```

```javascript
_p._loadItem = function(queueItem, itemCounter, progressListener) {

    ...

    img.onerror = function() {
    }
};
```



`self._images[queueItem.key] = self._placeholder ? self._placeholder : null;`

`self._onItemLoaded(queueItem, itemCounter, progressListener, false);`

`img.src = queueItem.path;`

现在，如果为 `ImageManager` 提供了占位图，它会创建一个图像，并将其用于 `_images` 数组中那些下载失败的图像。

最后一个要讨论的问题是如何获取图像的 Data URL。最简单的方法是将这个任务交给 Canvas。清单 4-11 展示了我是如何创建占位图像的。

**清单 4-11.** *使用 Canvas 生成 Data URL*

```
ctx.fillStyle = "#CCC";
ctx.fillRect(0, 0, 25, 25);
ctx.fillRect(25, 25, 25, 25);
alert(canvas.toDataURL("image/png"));
```

`toDataURL` 方法会创建 Canvas 内容的“截图”，并按照指定格式进行编码。获得包含 URL 的字符串后，你可以按任意方式使用它——例如，作为占位图，或者将其发送到服务器作为用户的截图存储。这完全取决于你的想象力。

清单 4-12 是我们的 `ImageManager` 类的完整源代码，其中包含了对占位图的支持。

## 第 4 章：动画与精灵

**清单 4-12.** *支持占位图的 ImageManager 完整源代码*

```
function ImageManager(placeholderDataUri) {
   this._images = {};
   if (placeholderDataUri) {
      this._placeholder = new Image();
      this._placeholder.src = placeholderDataUri;
   }
}
_p = ImageManager.prototype;
_p.load = function(images, onDone, onProgress) {
   // 图像队列
   var queue = [];
   for (var im in images) {
      queue.push({
         key: im,
         path: images[im]
      });
   }
   if (queue.length == 0) {
      onProgress && onProgress(0, 0, null, null, true);
      onDone && onDone();
      return;
   }
   var itemCounter = {
      loaded: 0,
      total: queue.length
   };
   for (var i = 0; i < queue.length; i++) {
      this._loadItem(queue[i], itemCounter, onDone, onProgress);
   }
};
_p._loadItem = function(queueItem, itemCounter, onDone, onProgress) {
   var self = this;
   var img = new Image();
   img.onload = function() {
      self._images[queueItem.key] = img;
      self._onItemLoaded(queueItem, itemCounter, onDone, onProgress, true);
   };
   img.onerror = function() {
      self._images[queueItem.key] = self._placeholder ? self._placeholder : null;
      self._onItemLoaded(queueItem, itemCounter, onDone, onProgress, false);
   };
   img.src = queueItem.path;
};
_p._onItemLoaded = function(queueItem, itemCounter, onDone, onProgress, success) {
   itemCounter.loaded++;
   onProgress && onProgress(itemCounter.loaded, itemCounter.total, queueItem.key, queueItem.path, success);
   if (itemCounter.loaded == itemCounter.total) {
      onDone && onDone();
   }
};
_p.get = function(key) {
   return this._images[key];
};
```

#### 图像加载技巧

现在你已经知道了如何从服务器加载图像，以及如何通过 Data URL 将图像数据直接嵌入到 JavaScript 代码中。但哪种方式更好呢：是下载一百张小图，还是下载一张包含所有图形的大图？或者，将所有图像嵌入为一个巨大的 Data URL 并确保它能够被加载，这样是不是更有意义？

##### 尺寸 vs. 数量

管理几十张小图片非常令人头疼。下载一张图片的过程涉及到向服务器发送一个独立的请求，而完成这个请求需要时间。图片越多，意味着请求越多、流量越大、等待时间越长。

另一方面，一张大图片意味着在下载过程中，用户界面无法显示任何有意义的进度。尽管你可以实现重试策略来重新下载损坏的图片，但拥有小块数据还是比一大块数据要好。

当你在决策图像加载策略时，应该考虑到图像是缓存的最佳候选对象。浏览器会非常积极地缓存图像文件，因为图像内容发生变化的可能性极低。

在游戏开发中，图像经常需要更新。你可能需要对游戏进行改版，或者添加新的道具或关卡。你可能想在圣诞节时给主角戴上圣诞帽，或者在背景中添加一棵棕榈树。



