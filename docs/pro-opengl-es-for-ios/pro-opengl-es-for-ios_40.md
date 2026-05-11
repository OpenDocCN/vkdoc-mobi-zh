# 第 9 章
## 性能与技巧

一分性能胜过十分承诺。

—— 梅·韦斯特

我的速度如此之快，以至于昨晚我在酒店房间里关掉电灯开关后，在房间变暗之前就已经躺到床上了。

—— 穆罕默德·阿里

在处理 3D 世界时，性能几乎始终是一个问题，因为即便是简单的场景也需要大量的数学运算。如果你只需要渲染一个弹跳的立方体，那倒不必担心；但如果你想展示整个宇宙，你就必须时刻关注性能。

到目前为止，所有的练习都力求清晰明了（希望如此），但并不一定追求高效。而遗憾的是，高效的代码往往不是最清晰、最容易理解的。因此，现在我们将开始研究一些略显凌乱的内容，并探讨如何将它们整合到你的应用程序中。

在苹果的 OpenGL ES 手册中，有一个名为“最佳实践”的章节。本章将详细讲解这部分内容，然后介绍一些苹果专门为开发者提供的、用于分析和调试 OpenGL 程序的工具。

## 顶点缓冲对象

性能优化的两个主要方向是：最小化图形硬件之间的数据传输，以及最小化数据本身。顶点缓冲对象（VBO）属于前者的范畴。当你生成几何体并将其愉快地发送到显示设备时，通常的做法是告诉系统在哪里可以找到每个所需的数据块，启用要使用的数据（顶点、法线、颜色和纹理坐标），然后进行绘制。每次调用 `glDrawArrays()` 或 `glDrawElements()` 时，都必须将所有数据打包并发送到图形处理单元（GPU）。每一次数据传输都需要消耗有限的时间，而如果某些数据能够缓存在 GPU 上，性能就会得到提升。顶点缓冲对象就是一种在 GPU 上分配常用数据的方法，这些数据可以在显示时被调用，而无需每次都重新提交。

创建和使用 VBO 的过程你应该很熟悉，因为它模仿了纹理的创建过程：首先生成一个“名称”，为数据分配空间，加载数据，然后在需要使用时调用 `glBindBuffer()`。

清单 9-1 展示了我如何从行星数据中创建一个 VBO。由于大多数行星的形状基本相同，即大致呈球形，因此我们可以在 CPU 上缓存一个球体模型，并将其用于任何行星或卫星——当然，火卫一、火卫二、土卫七、冥卫二和天卫二等不规则天体除外。

#### 清单 9-1. 为行星模型创建 VBO

```
-(void)createVBO
{
    int numXYZElements=3;
    int numNormalElements=3;
    int numColorElements=4;
    int numTextureCoordElements=2;
    long totalXYZBytes;
    long totalNormalBytes;
    long totalTexCoordinateBytes;
    int numBytesPerVertex;

    glGenBuffers(1, &m_VBO_SphereDataName); //1
    glBindBuffer(GL_ARRAY_BUFFER, m_VBO_SphereDataName);

    numBytesPerVertex=numXYZElements; //2
    if(m_UseNormals)
        numBytesPerVertex+=numNormalElements;
    if(m_UseTexture)
        numBytesPerVertex+=numTextureCoordElements;
    numBytesPerVertex*=sizeof(GLfloat);

    //3
    glBufferData(GL_ARRAY_BUFFER, numBytesPerVertex*m_NumVertices, 0, GL_STATIC_DRAW);

    //4
    GLubyte *vboBuffer=(GLubyte *)glMapBufferOES(GL_ARRAY_BUFFER, GL_WRITE_ONLY_OES);

    //5
    totalXYZBytes=numXYZElements*m_NumVertices*sizeof(GLfloat);
}
```

[www.it-ebooks.info](http://www.it-ebooks.info)



