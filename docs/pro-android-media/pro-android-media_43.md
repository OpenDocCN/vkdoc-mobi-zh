# 第 3 章：图像编辑与处理

- `android.graphics.PorterDuff.Mode.XOR`：源图像和目标图像将绘制在除重叠区域外的所有位置，在重叠区域两者均不绘制。

另外还定义了四条规则，用于描述当一张图像放置在另一张图像之上时，如何将两张图像混合在一起。

- `android.graphics.PorterDuff.Mode.LIGHTEN`：取两张图像在每个位置上最亮的像素进行显示。
- `android.graphics.PorterDuff.Mode.DARKEN`：取两张图像在每个位置上最暗的像素进行显示。
- `android.graphics.PorterDuff.Mode.MULTIPLY`：将每个位置上的两个像素相乘，除以 255，然后使用该值创建一个新的像素进行显示。结果颜色 = 顶部颜色 * 底部颜色 / 255
- `android.graphics.PorterDuff.Mode.SCREEN`：反转每个颜色，执行相同的操作（将它们相乘并除以 255），然后再次反转。结果颜色 = 255 - (((255 - 顶部颜色) * (255 - 底部颜色)) / 255)

让我们通过一个示例应用程序来说明如何使用这些规则。

```
package com.apress.proandroidmedia.ch3.choosepicturecomposite;

import java.io.FileNotFoundException;
import android.app.Activity;
import android.content.Intent;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.graphics.PorterDuffXfermode;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.Display;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.ImageView;

public class ChoosePictureComposite extends Activity implements OnClickListener {
```

我们正在创建一个标准的基于活动的应用程序，我们称之为“选择图片合成”。该活动将实现 `OnClickListener`，以便能够响应 `Button` 的点击。

由于我们将要合成两张图像，我们需要确保用户在选择两张图像后再尝试绘制合成版本。为此，我们将创建两个常量，每个常量对应一个 `Button` 按键，然后使用两个 `Boolean` 变量来跟踪某个 `Button` 是否已被按下。当然，我们也会有两个 `Button` 对象。

