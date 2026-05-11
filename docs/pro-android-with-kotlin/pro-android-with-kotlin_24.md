# 通过 URI 规范化的客户端访问一致性

很多时候，查询结果中包含依赖于短期数据库上下文的 ID、列表索引号或其他信息。例如，一个查询可能返回项目 ID 如 23、67 和 56，如果你需要获取某个项目的详细信息，你需要使用包含该 ID 的另一个 URI 再次查询，例如 `content://com.xyz/people/23`。这种 URI 的问题是，客户端通常不会将它们保存起来供以后使用——ID 可能会改变，因此 URI 并不可靠。

为了解决这个问题，内容提供者可以实现 *URI 规范化*。为此，你的内容提供者类需要实现以下两个方法：

- **`canonicalize(url: Uri): Uri`**：让此方法返回规范化的 URI，例如通过添加一些特定于域的查询参数，如下例所示。
- **`uncanonicalize(url: Uri): Uri`**：执行与 `canonicalize()` 完全相反的操作。如果项目已丢失且无法执行反规范化，则返回 `null`。

```
content://com.xyz/people/23  ->
content://com.xyz/people?firstName=John&lastName=Bird&Birthday=20010534&SSN=123-99-1624
```

