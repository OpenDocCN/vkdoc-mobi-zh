# 在 Android 中移动数据

最后，为了完善你关于 Android 应用程序构建模块的知识，你需要重点关注内容解析器。Android 并没有像 Brew 那样为 SDK 提供访问手机文件系统的特定权限，也不像 Java ME 那样提供 `RecordStore`。在 Activity、Intent 接收器和服务之间传递数据的主要方法，是通过 `ContentResolver` 超类来实现的。尽管你可以通过文件、偏好设置和其他数据库来存储数据，但内容解析器可以有多种形式，而且 Android 内置了几个重要的内容解析器。以下是本书出版时，你可能需要经常与之交互的主要 Android 内容解析器列表：

- 浏览器
  - 书签
  - 搜索历史
- 电话
  - 通话记录
  - 最近通话
- 联系人
- 系统设置
  - 硬件设置（蓝牙、网络设置）
  - 软件设置

Android 官方文档在此处提供了关于使用联系人内容解析器的优秀演练：[`code.google.com/android/devel/data/contentproviders.html#usingacp`](http://code.google.com/android/devel/data/contentproviders.html#usingacp)。

接下来，我将快速带你了解如何向手机浏览器的书签列表中添加一个书签。首先，你需要搜索当前的书签列表，以确认你的链接是否已存在。其次，如果书签不存在，你就添加它。

> **注意**  
> 你可以创建自己的内容提供器，将 Android 的 SQLite 实现封装起来以提供通用访问。你将在后续章节中学习如何实现这一点。现在，你只需处理这个内容解析器交互的“客户端”部分。

Android 使用自定义的 SQLite 实现来在本地存储信息。如果你不熟悉 SQL 的基础知识，现在可能是复习的好时机。为了便于讲解，我将假设你理解基本的 SQL 搜索命令。如果你需要复习，Apress 在此处有一个很棒的资源：[`apress.com/book/catalog?category=145`](http://apress.com/book/catalog?category=145)。

## 毫不掩饰的自我推销

假设在你的应用的“关于”部分中，你想要一个按钮，可以将你的商业软件页面添加到用户的网页书签中。你需要确保，如果用户不小心再次点击了该按钮，书签不会被添加两次。为了这个简单的演示，当用户按下某个键时，你将在示例应用中触发这个事件。

> **注意**  
> 有趣的是，如果你需要一个证据来证明 Android 作为一个开发平台仍未完全成熟，你只需查看 `android.content.ContentResolver` 中 `getDataFilePath` 方法的文档即可，该方法写着：“不要使用这个函数！！有人添加了它，但他们不该这么做。你无法直接访问内容提供器内部的任何文件。别碰这个。走开。”很高兴看到，即使是 Android 官方文档的技术作家也有幽默感。

### 获取用户的书签

此时，应该很明显，开发者如果能够访问用户的书签，就可以做相当不道德的事情。目前尚不清楚 Android 将采取什么措施来防止此类事情发生。我想这取决于运营商来锁定或监控这种行为。无论如何，你将调用 `managedQuery` 方法，该方法将返回用户的书签列表：

```java
Cursor bookmarks = android.provider.Browser.getAllBookmarks(getContentResolver());
int urlColumn = bookmarks.getColumnIndex(android.provider.Browser.BookmarkColumns.URL);

Cursor results;
String[] proj = new String[]
{
    android.provider.BaseColumns._ID,
    android.provider.Browser.BookmarkColumns.URL,
    android.provider.Browser.BookmarkColumns.TITLE
};

results = managedQuery(android.provider.Browser.BOOKMARKS_URI, proj, null, android.provider.Browser.BookmarkColumns.URL + " ASC");
```

我现在来分解一下发生了什么。你首先获取书签 URL 的列索引。再次强调，由于 Android 以 SQL 格式提供对其大部分内部数据的访问，你应该习惯于以数据库为中心的方式来引用已保存的信息。接下来，你将设置游标（一个类似于 Java ME 中 `RecordStore` 枚举器的对象）并设置你的投影字符串数组。因为你只对包含 URL 的列感兴趣，所以可以保持非常简单。`managedQuery` 方法的调用将返回你的数据。你将传入书签存储的 URI 字符串，提供简单的投影数组，将 where 部分留空，并告诉它按降序对 URL 进行排序。

### 搜索结果

搜索结果非常简单：只需迭代 `Cursor` 对象，并从之前获取的 URL 列 ID 中提取字符串即可：

```java
Cursor results = android.provider.Browser.getAllBookmarks(getContentResolver());
int urlColumn = results.getColumnIndex(android.provider.Browser.BookmarkColumns.URL);

results.first();
do
{
    //url 是一个方法参数，包含我们要寻找的内容
    if(results.getString(urlColumn).equals(url))
        return false;
} while(results.next());
```

你可以根据 URL 的内容做更多事情，但这里我们只查找 [www.apress.com](http://www.apress.com) 这个链接。显然，如果用上述 Apress 的 URL 运行这段代码，你是找不到它的。由于用户想在你的虚构的“关于”部分中添加你公司的 URL，你就需要满足他们的要求。

## 使用内容解析器添加“邪恶”的公司 URL

也许它们并不邪恶，但你还是要添加它们。既然 Apress 可能是世界上最少邪恶的公司之一（请注意，不是我偏心），你就破例一次，允许它们这样做。以下是添加书签记录的奇妙的 `ContentReceiver` 方式：

```java
ContentValues inputValues = new ContentValues();
inputValues.put(android.provider.Browser.BookmarkColumns.BOOKMARK, "1");
inputValues.put(android.provider.Browser.BookmarkColumns.URL, "http://www.apress.com/");
inputValues.put(android.provider.Browser.BookmarkColumns.TITLE, "Apress，不那么邪恶的公司");

ContentResolver cr = getContentResolver();
Uri uri = cr.insert(android.provider.Browser.BOOKMARKS_URI, inputValues);
```

与大多数 SDK 一样，完成同一任务的方法不止一种。之前，我们有一种更复杂的方式来添加书签。这种方法很有用，因为它为你提供了如何通过一个没有辅助函数的 `ContentResolver` 来添加元素的参考。现在，这是简单的方法：

```java
android.provider.Browser.saveBookmark(this, "Apress", url);
```

这个辅助函数将激活一个对话框，询问用户确认添加书签。这可能是最友好的添加书签的方式——除非你想控制对话框的外观。

## 均衡早餐的一部分

在前三个示例中，你探索了 Android 的所有主要构建模块。你首先研究了一个功能性的启动画面。这让你探索了启动、维护以及在不同 Activity 对象间跳转的基本原理。它让你初步了解了 Intent 以及进程间/对象间的通信。在 Activity、服务、内容处理程序和 Intent 接收器之间使用和传递 Intent，很可能是使 Android 与其他移动环境区别开来的最重要特性之一。

掌握了 Activity 和通信基础知识后，你接着学习了服务（Service）和 Intent 接收器。要使用这两个构建模块，你需要...



