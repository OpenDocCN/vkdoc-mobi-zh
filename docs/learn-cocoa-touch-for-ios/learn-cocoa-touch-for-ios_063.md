# 排版后的内容

`longitude:longitude]`;

`[tweet setLocation:location]`;

}

else {

// 这里，location 是从地理编码器接收到的位置信息
`[tweet setLocation:location]`;

}

`[tweets addObject:tweet]`;

}

if (`handler` != `NULL`) {

`handler(results)`;

`handler(tweets)`;

}

}

else {

`NSLog(@"未找到结果。")`;

}

}

}

else {

`NSLog(@"搜索出错: %@", error)`;

}

}];

}

现在我们已经编写了搜索代码，并且有了一个表示推文的类，接下来在地图上显示一些推文。为此，我们需要添加一个新的视图控制器。点击**文件** ➤ **新建** ➤ **文件…** 或按 **Command+N** 组合键。在左侧列中选择 **Cocoa Touch**，然后在右侧选择 **Objective-C class**。点击**下一步**。

在**子类**中输入 `UIViewController`，然后在**类**中输入 `LCTTweetMapViewController`。取消选中**Targeted for iPad**，但保留 **With XIB for user interface** 的选中状态（如果未选中，请勾选）。点击**下一步**，然后点击**创建**将文件保存到磁盘并打开它。

