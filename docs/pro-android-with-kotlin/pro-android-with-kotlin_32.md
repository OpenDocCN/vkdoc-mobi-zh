# 保护内容安全

从你在 `AndroidManifest.xml` 中声明一个内容提供者并将其 `exported` 属性设置为 `true` 的那一刻起，其他应用就被允许访问该提供者暴露的完整内容。对于敏感信息，这可能不是你想要的。作为补救措施，为了对全部或部分内容施加限制，你可以向 `<provider>` 元素或其子元素添加与权限相关的属性。

你基本上有以下几种选项：

## 1. 通过单一标准保护所有内容

为此，请使用 `<provider>` 的 `permission` 属性，如下所示：

```
<provider ... android:permission="PERMISSION-NAME" ... />
```

其中 `PERMISSION-NAME` 是一个系统权限或你在应用的 `<permission>` 元素中定义的权限。如果这样做，提供者的完整内容仅对成功获取该特定权限的客户端可访问。更准确地说，任何读取或写入操作都要求客户端拥有此权限。如果你需要区分*读取*权限和*写入*权限，可以改用 `readPermission` 和 `writePermission` 属性。如果混合使用，更具体的属性优先。这意味着：

*   `permission` = A → `writePermission` = A, `readPermission` = A
*   `permission` = A, `readPermission` = B → `writePermission` = A, `readPermission` = B
*   `permission` = A, `writePermission` = B → `writePermission` = B, `readPermission` = A
*   `permission` = A, `writePermission` = B, `readPermission` = C → `writePermission` = B, `readPermission` = C

## 2. 保护特定的 URI 路径

通过使用 `<provider>` 的 `<path-permission>` 子元素，你可以对特定的 URI 路径施加限制：

```
<provider ...>
    <path-permission android:path="..." android:permission="..." />
</provider>
```

在 `*permission` 属性中，你指定权限名称和权限范围，正如之前在“通过单一标准保护所有内容”一节中描述的那样。对于路径规范，你只能使用三个可能的属性之一：`path` 用于精确路径匹配，`pathPrefix` 用于匹配路径的开头，`pathPattern` 允许使用通配符：`X*` 表示零到多次出现的任意字符 `X`，`.*` 表示零到多次出现的任意字符。由于你可以使用多个 `<path-permission>` 元素，你可以在内容提供者中构建非常细粒度的权限结构。

## 3. 权限豁免

通过使用 `<provider>` 元素的 `grantUriPermission` 属性，你可以临时授予权限给由拥有该内容提供者的应用通过 `Intent` 调用的组件。如果你将 `grantUriPermission` 设置为 `true`，并且调用其他组件的 `Intent` 是借助以下方式构建的：

```
intent.addFlags(
Intent.FLAG_GRANT_READ_URI_PERMISSION)
/*or*/
intent.addFlags(
Intent.FLAG_GRANT_WRITE_URI_PERMISSION)
/*or*/
intent.addFlags(
Intent.FLAG_GRANT_WRITE_URI_PERMISSION and
Intent.FLAG_GRANT_READ_URI_PERMISSION)
```

被调用的组件将拥有对提供者所有内容的完全访问权限。你也可以将 `grantUriPermission` 设置为 `false` 并添加子元素。



