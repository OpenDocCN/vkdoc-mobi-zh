# 第 4 章：在应用中保存内容

```
NSString *userName = [[NSUserDefaults standardUserDefaults] objectForKey:@"userName"];
```

**注意：** 如果你请求的键在用户默认设置字典中不存在，你将得到 `nil`。请务必考虑到这种可能性。

并非所有对象都可以使用 `NSUserDefaults` 保存。可以保存的对象包括 `NSArray`、`NSData`、`NSDate`、`NSDictionary`、`NSNumber` 和 `NSString`。这些是 iOS 知道如何写入属性列表（Property List）的对象类型，属性列表是一种基于 XML 的特殊文件，用于编码这些对象。你刚才看到的将 `userName` 保存到用户默认设置的代码，将生成以下属性列表文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  ["http://www.apple.com/DTDs/PropertyList-1.0.dtd">](http://www.apple.com/DTDs/PropertyList-1.0.dtd)
<plist version="1.0">
<dict>
    <key>userName</key>
    <string>Jeff</string>
</dict>
</plist>
```

`Property list` 在此处缩写为 plist。在开头的 `<plist>` 和结尾的 `</plist>` 标签之间是一系列对象。属性列表有一个根对象，通常是一个数组或字典，此处用 `dict` 指定。字典包含键-值对，通过交替出现的 `<key>` 标签和值标签指定。由于我们指定了一个字符串，值标签是 `<string>`，但也可以出现其他类型。字典和数组可以相互嵌套。这是一个包含两个字典的数组的属性列表：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  ["http://www.apple.com/DTDs/PropertyList-1.0.dtd">](http://www.apple.com/DTDs/PropertyList-1.0.dtd)
<plist version="1.0">
<array>
    <dict>
        <key>userName</key>
        <string>Jeff</string>
    </dict>
    <dict>
        <key>userName</key>
        <string>Amanda</string>
    </dict>
</array>
</plist>
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

