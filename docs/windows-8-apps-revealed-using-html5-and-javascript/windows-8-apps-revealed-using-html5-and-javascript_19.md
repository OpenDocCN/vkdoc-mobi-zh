# 两种徽章模板

两种徽章模板可用。第一种将显示 1 到 99 之间的数值，第二种将显示由 Windows 定义的有限范围内的小图像。

数值和图标模板使用相同的 XML 表示，如列表 4-15 所示，它们比用于磁贴的模板简单得多。

***列表 4-15.*** 数值和图像徽章的模板

```
<badge value=""/>
```

目标是将`value`属性设置为数值或图标名称。如果购物清单上有三个或更少的项目，我会显示数值徽章。如果有超过三个项目，我会使用图标来指示用户应关注其购物义务的范围。

创建徽章的过程始于选择模板。两种模板类型是`Windows.UI.Notifications.BadgeTemplateType`：对于数值徽章，使用`badgeNumber`模板；对于图标，使用`badgeGlyph`模板。你可以在两种情况下使用相同的模板，因为它们返回相同的 XML。这在以后的版本或更新中可能会改变，因此即使内容相同，谨慎选择正确的模板也是明智的。

下一步是定位 XML 中的`value`属性，并将其设置为数值或图标名称。徽章的数值范围非常具体：从 1 到 99。如果你将值设置为小于 1，徽章将根本不会显示。任何大于 99 的值都会导致徽章显示 99。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 4 章 ■ 布局和磁贴

图标列表同样具有规定性。你不能使用自己的图标，必须从 Windows 支持的十个图标列表中选择。你可以在 http://msdn.microsoft.com/en-us/library/windows/apps/hh761458.aspx 查看图标列表。

在此示例中，我选择了警报图标，它看起来像一个星号。一旦 XML 填充完毕，就创建一个新的`BadgeNotification`对象并使用它来发布更新。与磁贴一样，我发现并非所有徽章更新都会被处理，因此我重复更新五次以确保其通过：

```
...
for (var i = 0; i < 5; i++) {
    var badgeNotification = new tn.BadgeNotification(badgeXml); 
    tn.BadgeUpdateManager.createBadgeUpdaterForApplication()
        .update(badgeNotification);
}
...
```

剩下的就是确保我的徽章更新被创建。为此，我更改了购物清单事件的事件处理程序，以便磁贴和徽章一起更新。我还需要在`/js/default.js`文件中添加对`sendBadgeUpdate`方法的调用，你可以在列表 4-16 中看到。这确保了应用启动时徽章立即更新。

***列表 4-16.*** 应用启动时更新徽章

```
...
function performInitialSetup(e) {
    WinJS.UI.processAll().then(function () {
        UI.List.displayListItems();
        UI.List.setupListEvents();
        UI.AppBar.setupButtons();
        UI.Flyouts.setupAddItemFlyout();
        ViewModel.State.bind("selectedItemIndex", function (newValue) {
            WinJS.Utilities.empty(itemDetailTarget)
            var url = newValue == -1 ? "/html/noSelection.html" :
                "/pages/itemDetail/itemDetail.html"
            WinJS.UI.Pages.render(url, itemDetailTarget);
        });
        WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); 
        function setOrientationClass() {
            if (Windows.UI.ViewManagement.ApplicationView.value
                == Windows.UI.ViewManagement.ApplicationViewState
                .fullScreenPortrait) {
                WinJS.Utilities.addClass(contentGrid, "flex"); 
            } else {
                WinJS.Utilities.removeClass(contentGrid, "flex");
            }
        };
        window.addEventListener("resize", setOrientationClass); 
        setOrientationClass();
        unsnapButton.addEventListener("click", function (e) {
            Windows.UI.ViewManagement.ApplicationView.tryUnsnap();
        });
        Tiles.sendTileUpdate();
        Tiles.sendBadgeUpdate();
    });
}
...
```

你可以在图 4-13 中看到四种不同的徽章/磁贴配置：宽磁贴和方形磁贴，带有数字和图标徽章。

***图 4-13.*** 在磁贴上显示徽章

## 总结

在本章中，我向你展示了如何适应 Windows 8 的贴靠和填充布局，以及如何使用磁贴向用户提供启动应用的诱因或避免启动所需的数据。这些功能对于交付一个融入更广泛 Windows 8 体验的应用至关重要。

你可能会觉得贴靠布局中的可用空间过于有限，无法提供任何严肃的功能，但经过仔细考虑，可以专注于你所提供的服务的本质，并省略其他所有内容。要充分利用磁贴和徽章，也需要仔细考虑。精心设计的徽章可以显著提高应用的吸引力或实用性，但考虑不周的磁贴则令人烦恼或完全无用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

