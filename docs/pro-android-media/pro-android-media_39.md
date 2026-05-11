# 69

```
0, contrast, 0, 0, brightness,
0, 0, contrast, 0, brightness,
0, 0, 0, contrast, 0 });
```

`paint.setColorFilter(new ColorMatrixColorFilter(cm));`

此操作的结果如图 3–16 所示。

**图 3–16.** *每种颜色的强度翻倍，但亮度降低以调节对比度，而无需改变整体亮度的 ColorMatrix*

## 调整饱和度

幸运的是，我们无需了解每一种想要实现的操作的具体公式。例如，`ColorMatrix` 内置了一个调整饱和度的方法。

```
ColorMatrix cm = new ColorMatrix();
cm.setSaturation(.5f);
paint.setColorFilter(new ColorMatrixColorFilter(cm));
```

传入大于 1 的数字会增加饱和度，而传入 0 到 1 之间的数字会降低饱和度。值为 0 时将生成灰度图像。

## 图像合成

合成是指将两张图像组合在一起，使得两张图像的特征都能被看到的过程。

