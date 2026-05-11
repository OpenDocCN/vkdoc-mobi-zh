# 排版后的文档

**图 5-8**展示了我们的表格视图在启用重排控件时的效果。

**图 5-8.** 我们的列表表格视图在编辑模式内外的重排控件效果

如图所示，重排控件是一个由三条线组成的方框。进入编辑模式后，它会替换右侧的展开指示器，成为表格视图单元格的辅助视图。要启用重排控件，我们无需在表格视图上设置属性、调用方法或执行类似操作。只需实现用户在移动行时调用的方法即可：表格视图会检测到我们已实现该方法，并自动显示控件。打开 `PossessionListViewController.m`，添加以下表格视图数据源方法：

```objective-c
- (void)tableView:(UITableView *)tableView
moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath
toIndexPath:(NSIndexPath *)destinationIndexPath
{
    id movingObject = [_possessions objectAtIndex:[sourceIndexPath row]];
    [_possessions removeObjectAtIndex:[sourceIndexPath row]];
    [_possessions insertObject:movingObject atIndex:[destinationIndexPath row]];
    [self savePossessionsToDisk];
}
```

在此方法中，我们只需修改 `_possessions` 数组以匹配用户所做的更改，然后将其保存到磁盘以持久化更改。表格视图会自动处理所有 UI 更新。

**注意：** 如果你的表格视图中某些行无法移动或某些目标位置无效，请实现数据源方法 `tableView:canMoveRowAtIndexPath:` 以逐行禁用重排控件，并实现较为冗长的委托方法 `tableView:targetIndexPathForMoveFromRowAtIndexPath:toProposedIndexPath:` 来在用户拖拽时调整目标行。

现在，用户已经可以重排内容，完全掌控自己的数据，因此我们正朝着打造一款广受欢迎的优秀应用迈进。MyStuff 在视觉体验方面还有很长的路要走，但其功能性已经相当扎实。

## 总结

本章我们介绍了多种让应用更灵敏响应用户触摸的方法。从实现自定义 `UIView` 子类处理触摸，到使用 `UIGestureRecognizer`，你拥有丰富的选项来打造卓越的用户体验。我们还介绍了几种内置的用户交互方式，你可以利用它们为用户提供跨应用一致的功能体验，从而让用户无需学习即可上手使用你的应用。下一章我们将充分利用这些知识，借助设备的网络连接功能，打造一款出色的 Twitter 客户端。

---

