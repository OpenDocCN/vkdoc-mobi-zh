# 排版后的内容

`blue:(float)blue alpha:(float)alpha`

`{`
```
float scale;
int boxRect[4];

glBindTexture(GL_TEXTURE_2D,m_Name);
glEnable(GL_BLEND);
glBlendFunc(GL_ONE,GL_ONE_MINUS_SRC_ALPHA);
glDisable(GL_DEPTH_TEST);
glEnable(GL_TEXTURE_2D);
glDisable(GL_LIGHTING);
glColor4f(red, green, blue, alpha);
boxRect[0]=0;
boxRect[1]=0;
boxRect[2]=m_Width;
boxRect[3]=m_Height;
scale=[[UIScreen mainScreen] scale]; //10
glTexParameteriv(GL_TEXTURE_2D, GL_TEXTURE_CROP_RECT_OES,(GLint *)boxRect); //11
glDrawTexfOES(point.x*scale, (480-point.y)*scale, depth, m_Width,m_Height); //12
glDisable(GL_TEXTURE_2D);
glEnable(GL_DEPTH_TEST);
glDisable(GL_BLEND);
glEnable(GL_LIGHTING);
```
`}`

`@end`

以下是解析过程：

第 1 行的参数包括字符串、使用`NSString`的实用方法`sizeWithFont()`确定的字符串大小、对齐方式以及`UIFont`。

在第 2 行及之后，所需纹理的尺寸被提升为旧设备所需的 2 的幂次（POT）。你可以检查`APPLE_texture_2D_limited_npot`扩展。如果该扩展存在，则任何尺寸的纹理都能正常工作。

我们需要先使用 CoreGraphics 生成一个包含所需文本的位图（第 3 行），然后将其转换为 OpenGL ES 纹理。第 4 行及之后为实际数据分配内存，然后用该数据块创建一个新的位图上下文。

[www.it-ebooks.info](http://www.it-ebooks.info)

