# 第 1 章：计算机图形学：从过去到现在

19

那么，这里发生了什么？

在第 1 行及后续行（ff 表示“及后续行”）中，定义了一些看起来很奇怪的枚举。这些枚举保存了着色器代码中各种参数的“位置”。我们会在本书后面讨论这一点。

第 2 行及后续行实际定义了用于描述两个立方体的数据。你很少需要像这样在代码中定义任何东西。通常，基本图元（例如球体、立方体和圆锥体）是即时生成的，而更复杂的对象则是从 3D 建模工具生成的文件中加载的。

两个立方体实际上使用的是同一个数据集，只是在不同的方式下操作它。



略有不同。数据分为六个部分，每部分对应一个面，每行定义该面的一个顶点或角。前三个数字是空间中的 `x`、`y` 和 `z` 值，后三个数字是该面的法线（法线是一条线，用于指定面所朝向的方向，并用于计算该面如何被照亮）。如果法线朝向光源，则该面会被照亮；如果背向光源，则处于阴影中。

你会注意到，立方体的顶点要么是 `0.5`，要么是 `-0.5`。这没什么特别的，只是将立方体的尺寸定义为每边 `1.0` 个单位。

这些面实际上由两个三角形组成。`OpenGL ES` 的“老大哥”可以渲染四边形面，但这个版本不行，它只能处理三边形。因此我们必须模拟四边形。这就是为什么这里定义了六个顶点：每个三角形三个。注意其中两个点是重复的。这并非绝对必要，因为仅用四个不同的顶点就可以很好地完成。

第 3 行及之后的行指定了用于旋转和平移（移动）物体的矩阵。在此应用中，矩阵是一种紧凑的三角函数表达式形式，用于描述每个物体的各种变换，以及它们的三维几何结构最终如何映射到我们屏幕的二维表面上。在 `OpenGL ES 1.1` 中，我们很少需要直接引用实际的矩阵，因为系统将它们对我们隐藏起来；而在 `2.0` 下，我们能看到系统所有的内部工作机制，并且必须自行处理各种变换。有时这并不美观。

第 4 行分配了一个 `OpenGL` 上下文。这用于跟踪所有特定状态、命令以及实际在屏幕上渲染内容所需的资源。此行实际上为 `OpenGL ES 2` 分配了一个上下文，这是通过 `initWithAPI` 传递的参数指定的。我们大多数时候会使用 `kEAGLRenderingAPIOpenGLES1`。

在第 5 行，我们获取了该控制器的视图对象。其特殊之处在于使用了 `GLKView` 对象，而不是你可能更熟悉的常见 `UIView`。作为 `iOS5` 的新增特性，`GLKView` 取代了更为混乱的 `EAGLView`。使用前者，只需几行代码就能创建一个 `GLKView` 并指定各种属性；而在 `iOS5` 之前那些黑暗且不宽容的日子里，仅仅做些基本的事情就可能需要几十行代码。除了简化设置外，`GLKView` 还负责调用更新和刷新例程，并增加了便捷的快照功能，用于获取场景截图。

第 6 行声明我们希望视图支持完整的 24 位色彩。

第 7 行出现了第一个仅适用于 2.0 的调用。如上所述，着色器是类似 C 语言的小程序，旨在图形硬件上执行。它们既可以像本练习中这样存放在单独文件中，也可以像一些人喜欢的那样，以文本字符串的形式嵌入代码主体中。

第 8 行展示了 `GLKit` 中的另一个新特性：效果对象。效果对象设计用于保存一些数据和呈现信息，例如创建特效所需的照明、材质、图像和几何体。在 `iOS5` 的初始版本中，只有两种效果可用，一种用于在物体上实现反射，另一种用于提供全景图像：这两种效果在图形学中都很常用，因此受到开发者的欢迎，否则他们需要自己编写。我预计苹果公司和第三方最终会提供更多的效果库。

在这个例子中，示例使用“基础效果”来渲染两个立方体中的一个。你很可能不会使用效果类来绘制如此基础的几何体，但这演示了效果如何封装了 `OpenGL ES 1.1` 的微型版本。也就是说，它包含了许多缺失的功能，主要是光照和材质方面，否则在将 `1.1` 代码移植到 `2.0` 时，你将不得不重新实现这些功能。

作为效果设置的一部分，第 9 行展示了如何开启灯光，接着第 10 行使用一个四分量向量实际指定了灯光的颜色。这些字段的顺序是红色、绿色、蓝色和 alpha。颜色值在 `0` 和 `1` 之间标准化，所以这里红色是主色，绿色和蓝色都只有 `40%`。如果你猜这是红色立方体的颜色，那你就对了。第四个分量是 alpha，用于指定透明度，`1.0` 表示完全不透明。

深度测试是 3D 世界的另一个重要部分。它在第 11 行使用，这是一个相当令人头疼的话题，用于遮挡或屏蔽隐藏在其它物体后面的物体。深度测试的作用是在渲染屏幕上的每个物体时为其提供一个深度分量。这被称为 `z-buffer`，它让系统在渲染一个物体时知道是否有物体位于该物体前面。如果是，则该物体（或其一部分）不会被渲染。在早期，z-buffer 速度非常慢且占用大量额外内存，因此只在绝对必要时才启用，但如今除了某些特殊渲染效果外，几乎没有理由不使用它。

第 12 行及后续行（单个 `f` 表示“下一行”）为系统设置了称为 `Vertex Array Objects`（`VAO`）的功能。`VAO` 使您能够将模型及其属性缓存在 `GPU` 本身中，从而大大减少了因每帧通过总线复制数据而产生的开销。直到 `iOS4`，`VAO` 仅在 `OpenGL ES 2` 实现中可用，但现在两个版本都可以使用。这里我们可以看到，我们首先获得一个用于向系统标识数据数组的“名称”（实际上只是一个唯一的句柄）。之后，我们获取该名称“绑定”它，这仅仅是使其成为需要数组的任何调用的当前可用数组。可以通过绑定一个新的数组句柄或使用 `0` 来解除绑定。这种命名和绑定对象的过程在整个 `OpenGL` 中都很常见。

在第 13 行及后续行，重复了相同的过程，但这次是在顶点缓冲区上。区别在于，顶点缓冲区是实际的数据，在本例中，它指向本文件最顶部静态定义的立方体数据。

第 14 行现在向系统提供了立方体数据，指定了数据的大小和位置，然后将其发送到图形子系统。

还记得每个角的 3D `xyz` 坐标是如何与面的法线（指示面朝向的东西）嵌入在一起的吧？实际上，只要数据格式不变，可以在这类数组中嵌入几乎任何数据。第 15 行及后续行告诉系统哪个数据对应什么。第一行说我们使用的 `GLKVertexAttribPosition` 数据由三个浮点值（`x`、`y` 和 `z` 分量）组成，从第 14 行提供的数据起始处偏移 `0` 个字节，每个结构体总长度为 `24` 个字节。这意味着在绘制这个立方体时，它会从缓冲区的开头获取三个数字，跳过后面的 `24` 个字节，再获取接下来的三个数字，依此类推。

法线的处理方式几乎相同，只是它们被称为 `GLKVertexAttribNormal`，并且起始偏移量为 `12` 个字节，也就是紧跟在 `xyz` 数据之后。

第 16 行“关闭”了顶点数组对象。现在，每当我们想要绘制其中一个立方体时，只需绑定这个特定的 `VAO` 并发出绘制命令即可，无需再次提供格式和偏移量信息。

最后，在第 17 行，缓冲区被删除。



如果感到头疼，这完全可以理解。为了绘制几个立方体，确实需要处理大量繁琐的步骤。但视觉世界本身就丰富多彩，需要大量元素来定义它。我们离完成还差得远。不过，其核心原则始终如一。

## 展示场景

在清单 1-2 中，我们终于可以将数据绘制到屏幕上，并看到一些漂亮的图像了。这里使用了两种不同的方式来显示内容。第一种方式将所有内容隐藏在 iOS5 及更高版本提供的新 `GLKit` 框架之下。它隐藏了 OpenGL ES 2.0 通常暴露的所有着色器和其他内容，并通过新的 `GLKBaseEffect` 类来实现。第二种方式则直接使用纯粹的 2.0 技术。两者结合，展示了这两种不同方法如何共存于同一个渲染循环中。

但请记住，使用效果类来渲染一个简单的立方体是大材小用，这有点像开车去 6 英尺外的邮箱取信。

**注意：** Apple 倡导使用 `GLKBaseEffect` 来帮助 1.1 用户将其代码移植到 2.0，因为它提供了 2.0 所没有的光照、材质和其他功能。但它实际上并不适合简单的迁移，因为它的限制太多，无法承载大多数 OpenGL 应用在 1.1 环境下的全部功能。

**清单 1-2：将场景渲染到显示器上。**

```
- (void)update //1
{
    //2
    float aspect = fabsf(self.view.bounds.size.width / self.view.bounds.size.height);
    GLKMatrix4 projectionMatrix =
    GLKMatrix4MakePerspective(GLKMathDegreesToRadians(65.0f), aspect, 0.1f, 100.0f);

    self.effect.transform.projectionMatrix = projectionMatrix; //3

    GLKMatrix4 baseModelViewMatrix = //4
    GLKMatrix4MakeTranslation(0.0f, 0.0f, -4.0f);
    
    baseModelViewMatrix = //5
    GLKMatrix4Rotate(baseModelViewMatrix, _rotation, 0.0f, 1.0f, 0.0f);

    // 为使用 GLKit 渲染的对象计算模型视图矩阵。
    GLKMatrix4 modelViewMatrix = //6
    GLKMatrix4MakeTranslation(0.0f, 0.0f, -1.5f);

    modelViewMatrix = //7
    GLKMatrix4Rotate(modelViewMatrix, _rotation, 1.0f, 1.0f, 1.0f);

    modelViewMatrix = //8
    GLKMatrix4Multiply(baseModelViewMatrix, modelViewMatrix);

    self.effect.transform.modelviewMatrix = modelViewMatrix; //9

    // 为使用 ES2 渲染的对象计算模型视图矩阵。
    modelViewMatrix =
    GLKMatrix4MakeTranslation(0.0f, 0.0f, 1.5f); //10
    
    modelViewMatrix =
    GLKMatrix4Rotate(modelViewMatrix, _rotation, 1.0f, 1.0f, 1.0f);
    
    modelViewMatrix =
    GLKMatrix4Multiply(baseModelViewMatrix, modelViewMatrix);

    _normalMatrix = //11
    GLKMatrix3InvertAndTranspose(GLKMatrix4GetMatrix3(modelViewMatrix), NULL);

    //12
    _modelViewProjectionMatrix = GLKMatrix4Multiply(projectionMatrix, modelViewMatrix);

    _rotation += self.timeSinceLastUpdate * 0.5f; //13
}

- (void)glkView:(GLKView *)view drawInRect:(CGRect)rect
{
    glClearColor(0.65f, 0.65f, 0.65f, 1.0f); //14
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    glBindVertexArrayOES(_vertexArray); //15

    // 使用 GLKit 渲染对象。
    [self.effect prepareToDraw]; //16

    glDrawArrays(GL_TRIANGLES, 0, 36); //17

    // 使用 ES2 再次渲染对象。
    glUseProgram(_program); //18

    glUniformMatrix4fv(uniforms[UNIFORM_MODELVIEWPROJECTION_MATRIX], 1, 0, _modelViewProjectionMatrix.m);
    glUniformMatrix3fv(uniforms[UNIFORM_NORMAL_MATRIX], 1, 0, _normalMatrix.m);

    glDrawArrays(GL_TRIANGLES, 0, 36); //19
}
```

让我们看看这里发生了什么：

第 1 行，`update` 方法的开始，实际上是来自新 `GLKViewController` 对象的委托调用之一。这支持帧率提示，例如，“我希望我的新游戏《危险贵宾犬》能够以每秒 60 帧的速度更新”。



