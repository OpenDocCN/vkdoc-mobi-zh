# 手势识别器与自定义实现

`touches` 会持续触发，但与缩放不同，它会以弧度为单位提供旋转值，让你能够根据用户输入旋转视图。

这些类允许你大幅自定义 UI 的行为。通过组合手势识别器，你可以在不编写任何直接处理用户触摸的代码的情况下，实现复杂的用户界面和交互模式。

再加上使用系统提供的手势识别器所带来的用户期望的一致行为，你就能获得打造成功 UI 的法宝。

## 自定义 `UIGestureRecognizer`

尽管内置的手势识别器已深度支持你应用中所需的大部分手势，但偶尔你仍需要实现一些略有不同的功能。在这种情况下，你可以创建 `UIGestureRecognizer` 的自定义子类来完成识别。例如，如果你想实现一个能识别“大于号”（`>`）形状向下滑动的手势识别器，你可以自行实现。

**注意：** 要创建 `UIGestureRecognizer` 的子类，你必须在实现文件中导入一个额外的头文件：`UIGestureRecognizerSubclass.h`。此文件重新定义了常规头文件中的部分属性，允许你对它们进行设置。

我们将这个示例 `UIGestureRecognizer` 子类命名为 `LCTGreaterThanGestureRecognizer`。其头文件无需任何修改：

```
//
//  LCTGreaterThanGestureRecognizer.h
//  GreaterThanGesture
//
//  Created by Jeff Kelley on 2/2/12.
//  Copyright (c) 2012 Jeff Kelley. All rights reserved.
//

#import <UIKit/UIKit.h>
#import <UIKit/UIGestureRecognizerSubclass.h>

@interface LCTGreaterThanGestureRecognizer : UIGestureRecognizer

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

