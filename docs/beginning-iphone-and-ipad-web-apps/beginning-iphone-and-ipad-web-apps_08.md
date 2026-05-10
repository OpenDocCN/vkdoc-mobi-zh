# Web 应用的架构剖析

导航体验不佳或应用体验不完善的应用程序，会带来诸多问题。例如，用户能否通过一个 URL 直接轻松地返回 Web 应用的某个特定部分，这一点至关重要。不过，我不想过多剧透后面的章节，但接下来我们将介绍至少两种方法，用于创建无需刷新页面、即可通过唯一 URL 进行导航的 Web 应用。其中一种方法依赖于广泛使用的 JavaScript 方法（`window.location.hash`），另一种则使用前沿的 CSS3 工具（`:target` 伪选择器）。这些方法的一大优势在于，即使用户收藏了某个页面并在之后返回，也能显示与 URL 相关的正确内容，而不仅仅是默认加载的内容。

### 配置视口

iOS 上的视口概念并不代表浏览器客户区的尺寸，甚至也不是屏幕的尺寸。它是一个可以显示网页的逻辑区域，可能大于或小于设备屏幕。当用户进入一个页面时，整体内容会被缩放，以在保持原始宽高比的前提下最佳适配视口。视口的默认宽度被设置为 980 像素，这适用于当今绝大多数网站。然而，我们知道 Web 应用的方法是不同的。你不希望页面被缩放：用户应该能以最少的操作直接访问他们想要的内容。让用户一开始就进行缩放操作是不好的。你必须构建页面来防止这种情况。

视口选项可以在网页的 `<head>` 标签内，使用视口 meta 标签进行定义。下面展示的代码在大多数情况下都能满足你的需求，我们建议你在大多数页面中使用它。它的作用是将初始缩放比例设置为 1.0，并禁止用户缩放：

```html
<meta name="viewport" content="initial-scale=1.0; maximum-scale=1.0; user-scalable=no">
```

因此，你的应用在加载时不会被缩放，无论它是在 iPhone 上还是 iPad 上，都会以你决定的最佳尺寸显示。现在，你可能会想，如果默认视口宽度是 980 像素，而你的 Web 应用没有被缩放，页面该如何正确显示呢？诀窍在于，视口 meta 标签还有其它选项，我们这里没有使用，因为它们可以根据我们已写的参数推导出来。在这里，`width` 选项（你可以将其设置为任何你想要的数值）被假定为等于设备屏幕宽度，因为 `initial-scale` 被设置为 1.0。因此，上面的 meta 规则等同于下面这条：

```html
<meta name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=no">
```

我们建议你尽可能在 meta 标签中使用 `device-width` 和 `device-height` 常量，而不是硬编码的值。这是保证你的页面在设备屏幕上正确占满空间的最佳方法，即使在从竖屏切换到横屏或反之亦然时也是如此。同时，让你的页面具有流动性（fluid）也是个好主意，也就是说，让它们占据屏幕上所有可用的空间，以确保你的应用无论用户如何配置，都能以全屏方式显示。

所有参数都是可选的，这意味着 Mobile Safari 会为你未指定的参数猜测值。`content` 属性的规则可以按任何顺序声明，只要用分号加空格分隔即可。表 4-2 列出了视口 meta 标签可用的所有属性和值。这些属性自 iOS 第一个版本起就已存在。

**表 4-2.** 视口属性

| 属性 | 描述 |
| --- | --- |
| `width` | 视口的宽度，单位为像素。默认值为 980，取值范围为 200 到 10,000。此属性也可以接受 `device-width` 和 `device-height` 常量。 |
| `height` | 视口的高度，单位为像素。默认值根据 `width` 属性的值和设备的宽高比计算得出。取值范围为 223 到 10,000 像素。此属性也可以接受 `device-width` 和 `device-height` 常量。 |
| `initial-scale` | 视口的初始缩放比例，为一个乘数。默认值经过计算，以使网页适配可见区域。取值范围由 `minimum-scale` 和 `maximum-scale` 属性决定。在此处设置为 1.0 会自动将 `width` 的默认值更改为 `device-width`。 |
| `minimum-scale` | 指定视口的最小缩放比例。默认值为 0.25。取值范围为 0 到 10.0。 |
| `maximum-scale` | 指定视口的最大缩放比例。默认值为 1.6。取值范围为 0 到 10.0。 |
| `user-scalable` | 决定用户是否可以执行缩放操作。默认值为 "yes"。设置为 "yes"（或 1）允许缩放，设置为 "no"（或 0）禁止缩放。 |

### 认真做应用：使用独立模式

现在，你已经拥有了一个恰好能适配用户设备屏幕的 Web 应用，你希望你的应用能被真正地像应用一样使用。好消息是，iOS 用户不仅可以选择将网页添加到书签，还可以将网页的快捷方式作为 Web 剪辑（web clip）添加到主屏幕。这使得 Web 应用能够像任何原生应用一样被快速访问，尤其是可以通过 Spotlight 搜索到。用户只需选择“添加到主屏幕”选项，并为快捷方式命名即可。之后，点击 Springboard 上的新图标，就会直接在 Mobile Safari 中打开你的 Web 应用。

#### 展示一个合适的图标

系统为用户主屏幕生成的默认图标是你的应用的屏幕截图，并会被调整大小以匹配页面上其他图标的尺寸。虽然系统会添加一点光泽使其看起来更像图标，但事实上，它并不吸引人，也不容易被识别（见图 4-4）。你希望你的最终用户能够轻松找到你的 Web 应用，并且一眼就能认出来，即使它混杂在其他各种快捷方式中。

**图 4-4.** 使用网页截图构建的默认图标……不太有吸引力

为了实现这一点，你必须为你的 Web 应用制作自己的图标。最好让它能让人联想到你的应用，例如使用相同的颜色、相似的形状等。它将被用于任何转换为 Web 剪辑的页面，就像传统 Web 浏览器中的 favicon 一样，所以你需要一个引人注目的图标。让 iOS 使用你的图标最简单的方法是将它保存为 `apple-touch-icon.png` 格式，并放在你网站的根目录下。

让图标显示在 Springboard 上很好，但让它更有光彩则更好。iPhone GUI 典型的光泽和投影效果会自动叠加在你的图标上。因此，如果你创建一个方形且足够大、能高质量显示的图标，它看起来就会像原生图标一样（见图 4-5）。

iPhone 上的图标宽 57 像素，而默认的 iPad 图标尺寸是 72 像素。构建常见尺寸（如 128 或 256 像素）的图标是一种相当好的做法。只需注意不要自行添加光照效果，以免与操作系统添加的默认效果冲突。

**图 4-5.** Apress 图标在 Springboard 应用样式前后对比

不过，在某些情况下，光泽效果并不适用，或者你想做一些略有不同的效果。如果你制作一个列出东欧原始画家作品地点的应用，你可能需要一个看起来比较扁平（flat-looking）的图标。闪亮的曲线可能会显得格格不入。要阻止操作系统应用全套效果，只需将你的图标重命名为 `apple-touch-icon-precomposed.png` 即可。你的图标仍然会有圆角和投影，但图标本身将保持不变（见图 4-6）。

**图 4-6.** 使用“precomposed”（预合成）选项的图标



当您的网页应用仅提供少量功能时，单个图标便已足够。然而，如果您构建的是一个包含不同分类、板块或服务的标签式应用，为应用的各个部分设置对比鲜明的图标，可以为用户带来额外的价值。您可以通过在页面`head`中使用`<link>`标签（其中包含`rel`属性的特殊值`apple-touch-icon`和`apple-touch-icon-precomposed`）来逐页实现此功能。为一个页面单独使用图标的完整代码如下：

```html
<link rel="apple-touch-icon" href="/path/to/custom-icon.png">
<link rel="apple-touch-icon-precomposed" href="/path/to/custom-icon.png">
```

这些值与对应图标名称的同类属性产生相同的结果，并适用相同的规则。

## 第 4 章：Web 应用的解剖学

### 全屏运行您的应用

您已经朝着“应用而非浏览器”的体验迈出了重要一步。

但是，如果这个图标启动的是嵌有您网页应用的移动版 Safari，那么浏览器的存在感就无法隐藏。这时，另一个`meta`选项就派上用场了。自 iOS 1.1.3 版本起，您可以指示页面默认全屏打开。只有状态栏保持可见；屏幕的其余部分将专用于您的页面。这种所谓的“独立模式”并非在移动版 Safari 中运行您的网页；它使用与移动版 Safari 相同的渲染引擎，用户将能使用移动版 Safari 的功能，但视图本身将是您的应用。

实现方法如下：

```html
<meta name="apple-mobile-web-app-capable" content="yes">
```

切记：您的应用并不会运行在移动版 Safari 或它的“精简”版本中。这是一种不同的模式，应进行全面测试，以确保在显示和行为方面不出问题。同时，务必为用户提供一个清晰高效的导航系统，因为浏览器的导航选项将不复存在。用户进入一个无法进行任何操作或很快陷入困境的应用页面，显然会让他们感到受骗。

如有需要，您可以通过`window.navigator.standalone`布尔值，使用 JavaScript 检查当前是否启用了此模式。这是一个只读属性，因此您无法动态更改它；不过，我们将在本章后面展示如何利用此模式下的选项和操作。

### 一个很棒的启动画面

特殊的`<link>`标签还允许您触发一张图片，在应用首次加载时作为启动画面显示。打开页面时的默认行为是显示上次访问时的页面快照，直到页面加载完成足以显示。这不仅相当缺乏个性，还可能让用户感觉应用在启动时卡住了，从而将他们吓跑。

在 iPhone 上，操作系统允许您通过指定启动图片来改变此行为，代码如下所示。但请注意，这在 iPad 上是不可能的；iPad 会系统性地显示您页面的截图。

```html
<link rel="apple-touch-startup-image" href="/path/to/startup-image.png">
```

您的图片尺寸应为 460 像素高、320 像素宽（竖屏方向）。请注意，此高度已将状态栏的高度考虑在内。如果您的图片占据了设备屏幕的全部范围，将有 20 像素被隐藏在平台界面之后。

## 第 4 章：Web 应用的解剖学

### 调整状态栏

您已经注意到，唯一始终对用户可见的 iOS 界面元素是状态栏。虽然它很容易与多种不同的设计融为一体，但您可能仍希望对其外观有所控制。毕竟是苹果公司，您无法真正改变状态栏的外观。不过，您可以在其变体中进行选择，以便更好地满足您的需求。

要更改状态栏的外观，请在页面的`head`中使用另一个`meta`标签。

```html
<meta name="apple-mobile-web-app-status-bar-style" content="default">
```

`content`属性有三个选项。除了`default`，您还可以选择`black`和`black-translucent`。请记住，此选项仅在**全屏模式**下有效。如果应用在加载后才切换到全屏，则无法使用它。同样，这在 iPad 上也是不可行的，无论是独立模式还是原生应用。

### 保持在独立模式

独立模式的主要问题是，当用户点击链接跳转到另一个页面时，原生状态下会以正常模式启动移动版 Safari，这将使您的大部分辛勤工作付诸东流。解决方法是使用 JavaScript 处理所有链接并阻止默认行为。

如果您的应用主要依赖 Ajax 驱动，并且页面上只有少数几个链接，这并非难事。您可以直接从 HTML 代码中为链接绑定事件，如下所示：

```html
<a href="http://www.apress.com/"
onclick="window.location.href=this.href; return false">Apress Web Site</a>
```

这里发生的事情非常直接。`this.href`从`href`属性中获取链接地址；`window.location`告诉浏览器使用给定的地址刷新此页面；最后，`return false`阻止了默认的锚点行为（即打开一个新页面）。您甚至可以通过将部分工作移交给外部函数来进一步扩展，该函数可能如下所示：

```javascript
function openLink(anchor) {
  window.location.href = anchor.href;
  return false;
}
```

然后，您的函数将从每个锚点标签中调用，方式如下：

```html
<a href="http://www.apress.com/" onclick="return openLink(this)"> Apress Web Site</a>
```

尽管如此，如果您的应用像经典网页导航一样更广泛地使用链接，我们建议您使用更高级的链接处理系统。它可能类似于这样：

```javascript
/* Create a document-wide click listener */
document.addEventListener("click", clickHandler, false);

function clickHandler(e) {
  var element = e.target;
  /* handle clicks only on anchor elements */
  if (element.localName.toUpperCase() != 'A') {
    return;
  }
  /* ignore elements with a target value specified since "target"
  cannot be handled in full-screen mode
  those links shall open regularly in Mobile Safari */
  if (!!element.getAttribute('target')) {
    return;
  }
  var url = element.href;
  /* ignore links other than HTTP(S) and to different origin */
  var match = url.match(/^https?:\/\/(.+?)\/.*$/);
  if (!match || match[1] != window.location.host) {
    return;
  }
  /* finally open the link in full-screen view and prevent default behavior */
  window.location.href = url;
  e.preventDefault();
}
```

正如代码中的注释所述，这个特定的脚本不处理复杂（或不太复杂的……）情况。更重要的是，它不处理包含子标签的链接。例如，如果您有一个像这样常见的 HTML 块：

```html
<a href="/pictures/question-mark.png" title="See this picture full screen">
  <img src="/pictures/question-mark.png" alt="A question mark">
</a>
```

那么脚本将无法工作，因为接收事件的将是图片，而不是链接。然而，这个解决方案很容易扩展，其主要优点是不引人注目，即将 HTML 标记和 JavaScript 代码分离。当然，只要您链接到页面的其他部分或属于该 Web 应用的其他页面，它就能安全地让您保持在独立模式。对于外部链接，让操作系统启动适当的浏览器似乎是合理的，因为您无法确定远程站点是否适合独立模式，也不应该希望它们与您自己的网站关联得太紧密。

### 构建您的第一个 Web 应用基础项目



