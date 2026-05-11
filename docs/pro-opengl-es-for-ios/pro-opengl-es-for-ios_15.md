# 第 3 章：构建 3D 世界

### 67

## 定义视锥体

最后一步，我们需要使用 `glFrustumf()` 来指定顶点如何映射到屏幕的细节，如列表 3-5 所示。回想一下，视锥体由六个平面组成，这些平面包围了一个体积，定义了我们可以看到的范围，类似于相机的镜头。

**列表 3-5.** 创建观察视锥体，添加到视图控制器文件中

```
-(void)setClipping
{
    float aspectRatio;
    const float zNear = .1; //1
    const float zFar = 1000; //2
    const float fieldOfView = 60.0; //3
    GLfloat size;

    CGRect frame = [[UIScreen mainScreen] bounds]; //4

    //高度和宽度值将视场角限制在高度上；翻转后它将相对宽度而言。
    //因此，如果我们希望视场角为 60 度（类似于广角镜头），它将基于窗口的高度而非宽度。
    //在渲染到非方形屏幕时，了解这一点非常重要。

    aspectRatio=(float)frame.size.width/(float)frame.size.height; //5

    //设置 OpenGL 投影矩阵。
    glMatrixMode(GL_PROJECTION); //6
    glLoadIdentity();

    size = zNear * tanf(GLKMathDegreesToRadians (fieldOfView) / 2.0); //7

    glFrustumf(-size, size, -size /aspectRatio, size /aspectRatio, zNear, zFar); //8
    glViewport(0, 0, frame.size.width, frame.size.height); //9

    //将 OpenGL 模型视图矩阵设为默认矩阵。
    glMatrixMode(GL_MODELVIEW); //10
}
```

[www.it-ebooks.info](http://www.it-ebooks.info)

