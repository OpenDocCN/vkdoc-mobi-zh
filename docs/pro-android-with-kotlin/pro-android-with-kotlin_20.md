# 内容提供者查询参数

- **projection**：该参数告知实现方请求者感兴趣的列。以 SQL 数据库类型存储数据为例，它列出了结果中应包含的列名。但并非严格要求一一映射——请求者可能要求选择参数"X"，而"X"的值可通过任何你能想到的方式计算得出。如果为`null`，则返回所有字段。该参数对所有变体 A、B、C 含义相同。

- **selection**：仅适用于变体 A 和 B，指定要返回数据的筛选条件。内容提供者框架不要求该筛选参数的具体形式——完全由实现方决定，内容请求者必须遵循实现方的定义。但在许多情况下，你会看到类似 SQL 筛选字符串如"name = Jean AND age < 45"的形式。如果为`null`，则返回所有数据集。

- **selectionArgs**：筛选参数可能包含"?"等占位符。若存在，此数组指定要填入占位符的值。框架同样不对此做严格规定，但大多数情况下"?"作为占位符使用，如 SQL 中的"name = ? AND age < ?"。若无筛选占位符，此参数可为`null`。

- **sortOrder**：仅适用于变体 A 和 B，指定返回数据的排序方式。内容提供者框架不规定具体语法，但针对类似 SQL 的访问，通常会是"name DESC, age ASC"这样的形式。

- **queryArgs**：对于变体 C，筛选条件、筛选参数和排序方式这三个参数均可选择通过`android.os.Bundle`对象来指定。按惯例，对于类似 SQL 的查询，Bundle 的键值为：
    - `ContentResolver.QUERY_ARG_SQL_SELECTION`
    - `ContentResolver.QUERY_ARG_SQL_SELECTION_ARGS`
    - `ContentResolver.QUERY_ARG_SQL_SORT_ORDER`

- **cancellationSignal**：对于变体 B 和 C，如果此参数不为`null`，你可以用它取消当前操作。Android OS 会相应通知请求者。

所有查询方法都应返回一个`android.database.Cursor`对象，以便遍历数据集。按约定，每个数据集都包含一个以"_id"为键的技术主键。请参阅以下章节"基于 AbstractCursor 等的 Cursor 类"和"基于 Cursor 接口的 Cursor 类"，了解如何设计合适的游标对象。

## 修改内容

内容提供者不仅允许读取内容，还允许修改内容。为此提供了以下方法：

```
abstract fun insert(
    Uri uri:Uri,
    values:ContentValues) : Uri
// 无需重写此方法，默认实现会正确遍历输入数组
// 并对每个元素调用 insert(...)
fun bulkInsert(
    uri:Uri,
    values:Array) : Int
abstract fun update(
    uri:Uri,
    values:ContentValues,
    selection:String,
    selectionArgs:Array) : Int
abstract fun delete(
    uri:Uri,
    selection:String,
    selectionArgs:Array) : Int
```

下面我们描述这些参数及其含义：

- **uri**：指定数据空间中的数据类型坐标。内容消费者通过恰当指定此参数来告知其目标数据的*种类*。详细描述请参见"设计内容 URI"一节。注意，对于删除或更新单个数据集，通常假定 URI 路径末尾包含数据的（技术）主键，例如：`content://com.android.contacts/contact/42`。

- **values**：要插入或更新的值。通过此类提供的各种`get*()`方法来访问这些值。

- **selection**：指定要更新或删除数据的筛选条件。内容提供者框架不强制要求该参数的形式——完全由实现方定义，内容请求者必须遵循实现方的定义。但在许多情况下，你会看到类似 SQL 筛选字符串如"name = Jean AND age < 45"的形式。如果为`null`，则作用于数据集中的所有条目。

- **selectionArgs**：筛选参数可能包含"?"等占位符。若存在，此数组指定要填入占位符的值。框架同样不对此做严格规定，但大多数情况下"?"作为占位符使用，如 SQL 中的"name = ? AND age < ?"。若无筛选占位符，此参数可为`null`。

`insert()`方法应返回指定插入数据的 URI。此返回值可为`null`，因此没有严格要求必须返回值。如果返回了值，则应包含技术主键。所有返回`Int`的方法都应返回受影响数据集的数量。如果你不希望内容提供者能修改任何数据，可以为所有插入、更新和删除方法提供空实现，并让它们返回`0`或`null`。

## 完成 ContentProvider 类

为了完成`ContentProvider`类的实现，除了查询、插入、更新和删除方法外，你还必须实现一个方法：

```
abstract getType(uri:Uri) : String
```

该方法将任何可用的 URI 映射到相应的 MIME 类型。例如，对于可能的实现，你可以按如下方式使用 URI：

```
ContentResolver.CURSOR_DIR_BASE_TYPE + "/vnd.<名称>.<类型>"
ContentResolver.CURSOR_ITEM_BASE_TYPE + "/vnd.<名称>.<类型>"
```

分别用于指向*可能*包含多个条目或*至多*一个条目的 URI。对于`<名称>`，请使用全局唯一的名称，可以是公司域名的倒序、包名或其显著部分。对于`<类型>`，使用定义表名或数据域的标识符。

## 注册内容提供者

完成`ContentProvider`实现后，必须在`AndroidManifest.xml`文件中按如下方式注册：

```
<provider
    android:authorities="..."
    android:directBootAware="..."
    android:enabled="..."
    android:exported="..."
    android:grantUriPermissions="..."/>
```

这些属性在表 6-1 中描述。

**表 6-1** `<provider>`元素

| 属性（每个需加`android:`前缀） | 描述 |
|---|---|
| `authorities` | 分号";"分隔的权限列表。多数情况下只有一个权限，通常使用应用（包）名或`ContentProvider`实现类的完整类名。没有默认值，必须至少有一个。 |
| `directBootAware` | 内容提供者是否可在用户解锁设备前运行。默认值为 false。 |
| `enabled` | 内容提供者是否启用。默认值为 true。 |
| `exported` | 其他应用是否可访问此内容。根据应用架构，访问权限可能仅限于同一应用的组件，但通常你希望其他应用能访问内容，因此将此设置为`true`。从 API 级别 17 开始，默认值为 false。在此之前该标记不存在，应用行为等同于设置为 true。 |
| `grantUriPermissions` | 是否可以通过 URI 临时授予其他应用访问此提供者内容的权限。*临时授予*是指当访问内容的客户端通过带有`intent.addFlags(Intent.FLAG_GRANT_URI_PERMISSION)`的 Intent 调用时，临时覆盖由`<permission>`、`<readPermission>`或`<writePermission>`定义的权限拒绝。若将此属性设为`false`，仍可通过设置一个或多个`<grant-uri-permission>`子元素来授予更细粒度的临时权限。默认值为 false。 |



