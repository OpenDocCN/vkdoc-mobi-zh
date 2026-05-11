# 第 8 章：综合应用

282

第 5 行设置填充颜色，确保填充后的文本字符可见。

第 6 行及之后将新上下文推入栈中，将文本写入该上下文，然后将其弹出。

现在，以标准方式创建 OpenGL 纹理：第 7 行及之后做准备，第 8 行实际生成纹理。请注意，为保持良好协作，新纹理的名称仅临时绑定，并在第 9 行恢复先前绑定的纹理。

跳转到该对象中的唯一另一个方法`renderAtPoint()`，我们使用一种略有不同且更简单的纹理绘制方式，即`glDrawTexfOES()`。虽然苹果支持此方法，但请记住，`OES`后缀意味着无法保证其他 OpenGL ES 实现也支持此调用，因此使用风险自负。此外，你也不能对它进行任何变换。

`glDrawTexfOES()`无法识别用于让 OpenGL ES 识别高分辨率 Retina 显示屏的`contentScaleFactor`。因此，我们必须在第 10 行手动进行缩放。

`glDrawTexfOES()`在第 11 行引入了一个新的纹理参数`GL_TEXTURE_CROP_RECT_OES`，并使用所提供的`boxRect`。这可以让你裁剪纹理的一部分用于显示。在这里，我们指定要使用整个纹理内容。

最后，标签被绘制到缓冲区中。注意第 12 行在 x 和 y 方向上都使用了缩放因子。

接下来是轮廓本身的加载器/渲染器。由于剩余代码篇幅较长，且其中很大一部分与星形模块类似，因此这里只摘录重点部分进行说明。

从 plist 读取时，每个星座轮廓的数据会被转换为 OpenGL 可识别的浮点数数组，然后作为`NSData`对象存回原始字典中。这样，原始数据在需要时得以保留，同时又能与同一数据的 OpenGL 表示轻松关联，如清单 8-16 所示（同样位于`OpenGLOutlines`中）。

### 清单 8-16. 分配顶点缓冲区

```
coordArray=[dict objectForKey:@"coordinates"];
numpoints=[coordArray count];
numbytes=numpoints*3*sizeof(GLfloat);
data=(GLfloat *)malloc(numbytes);
for(j=0;j<numpoints;j++)
{
   coords=[coordArray objectAtIndex:j];
   ra=(NSNumber *)[coords objectForKey:@"ra"];
   dec=(NSNumber *)[coords objectForKey:@"dec"];
```

[www.it-ebooks.info](http://www.it-ebooks.info)



