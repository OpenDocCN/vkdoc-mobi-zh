# 用户体验与界面设计指南

尽管不打断用户正在进行的操作似乎是显而易见的原则，但弹出模态对话框仍然很容易干扰用户。在这种情况下，你始终应为用户提供一种简便的关闭对话框的方式，并让后续操作清晰明了。

开发者可能会倾向于通过要求用户输入（无论是文本框还是单选按钮）来让应用对最终用户更个性化。但在 iPhone 上，这会降低用户体验速度，并延迟他们达成目标的时间。

在 iPad 上，这种后果可能更令人恼火。苹果建议 iPad 应用应以非线性方式运行，这意味着用户应在主视图中看到任务内容，并能从屏幕的导航区域访问选项和交互工具。也就是说，无论用户在应用中做什么，都应能完成大多数任务。即使这对你来说似乎合理，在要求用户做出选择时也要谨慎，因为这既打断了用户在主视图中的操作流程，也可能导致导航区域的某些选项被切断，从而带来挫败感。例如，在邮件应用中，你不希望因为用户正在阅读邮件而失去“撰写”选项。作为经验法则，你应谨慎假设用户所处的上下文以及用户可能想要做什么。

还需谨记，设备制造商已经仔细考虑过其设备的可用性。因此，你不应重新创建已经存在且用户已经习惯使用的功能。

### 精心展示广告

很多网站提供免费内容，并依赖广告作为收入来源。如果你的计划如此，就应认真思考如何实现。在传统网页上，多项研究已证明用户普遍被“训练”得对广告视而不见。虽然你可能认为在 iPhone 上这不是大问题，因为广告必然会占据更多屏幕空间，且显示通常不那么杂乱，但你不应过度加载页面。强制用户滚动掠过无关内容或缩放远离广告，很可能迅速将用户从你的 Web 应用驱离。

对于 Web 应用和原生应用，广告标准早已确立；通常你会在页面顶部或底部显示一个小横幅。然而，原生应用更进一步，在大多数情况下会在短暂延迟后自动隐藏广告，将全部空间留给实际内容。这具有既不突兀又能吸引用户注意力的优点。将这种行为引入 Web 应用将是一个积极的举措，尤其因为使用 CSS 过渡，你几乎无需使用任何 JavaScript。以下是一个简单的示例，你可以使用本书前面创建的模板轻松测试。首先，将以下代码添加到页面的 `<head>` 中：

```css
.ads {
  z-index: -1;
  position: relative;
  -webkit-transition: margin-top 0.35s ease-out;
  border-bottom: 0;
  margin: 0 auto 34px;
  width: 320px;
}

.ads img { display: block; }

.ads .tab:before {
  color: red;
  content: 'AD ';
}

.ads .tab {
  position: absolute;
  background-image: -webkit-gradient(
    linear, left top, left bottom,
    from(black), to(#666));
  color: white;
  font: bold 11px/24px verdana;
  height: 24px;
  bottom: -24px;
  left: 0;
  width: 100%;
  padding: 0 5px;
  -webkit-box-sizing: border-box;
}
```

首先，你通过修改广告的 `z-index` 和 `position` 值来定义样式，使广告堆叠在页眉下方。然后，你创建应用于广告区域和广告出现后仍需可见区块的样式。与过渡相关的代码以粗体显示，表明当 `margin-top` 属性发生变化时，应发生一次平滑的 35ms 过渡。

接下来，你依靠脚本来触发动画：

```javascript
function hideAds() {
  setTimeout(hide, 5000);
}

function hide() {
  var ads = document.querySelector(".ads");
  ads.style.marginTop = (-ads.offsetHeight) + "px";
}

function reveal() {
  var ads = document.querySelector(".ads");
  ads.style.marginTop = "";
  hideAds();
}
```

**注意：** `querySelector()` 方法返回匹配作为参数传递的 CSS 选择器的第一个元素。它是 W3C 选择器 API 的一部分，由 Mobile Safari 完全支持。

通过这种方式，无论何时你希望隐藏广告，只需调用 `hide()` 函数，它会根据横幅的高度确定新的 `margin-top` 值并启动动画。

`reveal()` 函数允许你通过恢复 `margin-top` 的初始值并再次触发动画来重新显示广告。以下是相关的 HTML 标记：

```html
<body onload="hideAds()">
  ...
  <div class="header-wrapper">
    <h1>Web App Header</h1>
  </div>
  <div class="ads">
    <img src="some-ad.gif" alt="Advertisement" width="320" height="50">
    <div class="tab" onclick="reveal()">
      Apress sales. 20% off on all books.
    </div>
  </div>
  ...
  <img src="index-116_1.jpg" alt="">
  <img src="index-116_2.jpg" alt="">
```

图 5–2 展示了结果。用户将能够点击标签再次查看广告，而隐藏过程将在五秒后再次发生。第 9 章将更详细地介绍 CSS 过渡。

**图 5–2.** 广告在几秒后自动隐藏

此外，你应避免使用弹出元素。如前所述，你应限制任何对用户体验流程产生负面影响的内容。因此，你应仅在特定时刻考虑使用弹出元素，例如首次页面加载时。在所有情况下，都应为用户提供明显且简便的方式关闭广告——使用 iPhone 界面默认的 X 按钮是一个合理的选择（图 5–3）。总的来说，你应彻底考虑广告对应用的影响。

**图 5–3.** 你应始终为用户提供简便的方式隐藏广告

在 iPad 上，可以使用类似的过程，只是格式不同。尽管 iPad 的大屏幕更接近桌面网页，但正如前一章所述，用户与设备之间建立了一种特殊的连接。因此，你应考虑采用新的、适合的方式将广告引入页面。你所拥有的各种可能性，包括声音、视频和丰富的交互，应促使你让用户成为推广内容的核心角色。

无论你想出什么点子，都要牢记本书中已经介绍和将要介绍的使用特性，以及你从自身 iPhone 用户体验中收集到的要素。

### 让用户自行决定

作为开发者，在引导用户通过



