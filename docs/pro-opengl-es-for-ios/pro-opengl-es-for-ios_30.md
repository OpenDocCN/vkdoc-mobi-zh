# 第七章：精美渲染的杂项

**208**

图 7-1. 左侧只有 Hedly 在旋转；中间 Hedly 和他的窗口都开始逆时针旋转；右侧则是整个框架在翻来覆去地旋转。

## 太阳缓冲对象

有了这种相当于 3D 超能力的技术，你可以做许多有趣又奇异的事情。例如，你可以在一台小电视模型上模拟一些动画；可以在地面水洼的倒影或汽车后视镜中显示同一数据的多个视图；更棒的是，你还可以将 OpenGL 渲染某个场景的帧放到太阳系模拟器的太阳上。虽然这不太现实，但确实很酷。

这次大部分内容将留给读者自行探索。我以第 5 章的最终项目作为起点。你需要添加 `FBController` 对象，并在上一个练习中不同的 `drawInRect()` 方法中对其进行初始化。后者是对太阳系中使用的 `execute()` 方法的补充。我将为你提供 `drawInRect()` 方法，如代码清单 7-4 所示，其余部分就交给你了。

**代码清单 7-4.** `drawInRect()` 所需的更改

```
- (void)glkView:(GLKView *)view drawInRect:(CGRect)rect

{

static const GLfloat squareVertices[] =

//1

{

-0.15f, -0.5, 0.0,

-0.15f, 0.5, 0.0,

0.15f, -0.5, 0.0,

0.15f, 0.5, 0.0

};
```

**209**

```
static GLfloat textureCoords1[] =

{

0.0, 0.0,

0.0, 1.0,

1.0, 0.0,

1.0, 1.0

};

static const GLubyte squareColors[] = {

255, 0, 255, 255,

255, 255, 0, 255,

0, 255, 255, 255,

0, 0, 0, 0,

};

static float transY = 0.0;

glBindFramebufferOES(GL_FRAMEBUFFER_OES, [m_FBOController getFBOName]);

glPushMatrix();

glDisable(GL_LIGHTING); //2

glClearColor(1.0, 1.0, 1.0, 1.0);

glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT);

glMatrixMode(GL_MODELVIEW);

glLoadIdentity();

glTranslatef(0.0, (GLfloat)(sinf(transY)/2.0), -2.5);

glEnable(GL_TEXTURE_2D);

glBindTexture(GL_TEXTURE_2D, m_Hedly);

// glColorPointer(4, GL_UNSIGNED_BYTE, 0, squareColors); //3

// glEnableClientState(GL_COLOR_ARRAY);

glTexCoordPointer(2, GL_FLOAT,0,textureCoords1);

glEnableClientState(GL_TEXTURE_COORD_ARRAY);

glVertexPointer(3, GL_FLOAT, 0, squareVertices);

glEnableClientState(GL_VERTEX_ARRAY);

glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);

glPopMatrix();

glBindFramebufferOES(GL_FRAMEBUFFER_OES, m_DefaultFBO);

glEnable(GL_LIGHTING);

transY += 0.075;

}
```

**210**

与之前版本的 `drawInRect()` 相比，不同之处如下：

在第 1 行，我们需要改变“正方形”的尺寸比例，以补偿太阳纹理几何形状短而宽的特性，否则图像会严重变形。

第 2 行禁用了光照，这样无论何种情况都能看到完整的图像。

第 3 行被注释掉了，以关闭着色功能，使 Hedly 的图像更清晰可见。

我希望你能得到类似图 7-2 的效果，Hedly 在太阳上上下弹跳。

图 7-2. 使用屏幕外的帧缓冲区对象在另一个帧上实现纹理动画

挺酷的吧？

## 镜头光晕

我们都见过这种效果。那些幽灵般、发光且轻如薄纱的光点，在电视画面中舞动，或者每当摄像机对准太阳时侵入图像。这是因为太阳光在相机镜头中欢快地来回反射，产生了大量的二次成像。这些成像既可以表现为明亮宽阔的光晕，也可以表现为许多较小的光斑。图 7-3a 展示了 1971 年阿波罗 14 号登月任务中的一张图像，光晕遮住了登月舱的大部分。即便是 iPhone 也有类似的问题，如图 7-3b 所示。尽管阿波罗任务在月球上使用的哈苏相机是当时世界上最好的，也无法克服镜头光晕。不幸的是，它已成为计算机图形学中最常见的陈词滥调之一，被用作一种工具来呼喊：“嘿！这不是伪造的计算机图像，因为它有镜头光晕！”然而，镜头光晕确实有其用途，尤其是在太空模拟领域，因为虚拟图像经常要对着假想的太阳。在这种情况下，无论是有意识还是下意识，你都期望有一些视觉提示表明你正在观看一个非常非常明亮的物体。它还有助于为图像增添额外的深度感。光晕是在离用户很近的镜头中产生的，而目标却在数十亿英里之外。

**211**

图 7-3. 左侧是阿波罗 14 号在月球上的视图，右侧是 iPhone 4 拍摄的图像。

根据特定的镜头及其内部的不同镀膜，光晕可以呈现多种不同的形式，但最终通常只是一堆大小和色调各异的幽灵般的多边形。在接下来的练习中，我们将创建一个简单的镜头光晕项目，演示如何在 3D 环境中使用 2D 图像。由于设置的代码较多，我在这里只强调关键部分。你需要访问 `www.apress.com` 获取完整的项目代码。

从几何角度看，镜头光晕通常非常简单，因为它们具有对称性。它们表现出两个主要特征：所有镜头光晕都需要一个非常明亮的光源，并且它们会沿着一条穿过屏幕中心的假想对角线排列，如图 7-4 所示。

**212**

图 7-4. 镜头光晕是由相机镜头内明亮光源的内部反射引起的。

既然光晕图像是 2D 的，我们该如何将它们放入 3D 空间呢？回到最初的示例，弹跳的正方形也是一个 2D 对象。但它的显示依赖于对象如何映射到屏幕的一些默认设置。这里我们将更加具体地处理。

还记得第 3 章我提到的透视投影与正射投影的区别吗？前者是我们感知物体维度的方式；后者用于需要精确尺寸和形状时，消除了透视给场景带来的变形。因此，在绘制 2D 对象时，你通常需要确保其视觉尺寸不受世界中其他 3D 元素的影响。

在生成镜头光晕时，你需要一小批不同形状的图像来模拟实际镜头的部分结构。六边形或五边形的图像代表了用来调节入射光强度的光圈，如图 7-5 所示。由于用于保护镜头或过滤掉不需要波长的各种镀膜，它们也会呈现出不同的色调。

**213**

图 7-5. 六叶片光圈（图片由 Dave Fischer 提供）

生成光晕集需要以下步骤：

1.  导入各种图像。
2.  检测源对象在屏幕上的位置。
3.  创建穿过屏幕中心的假想向量，用于放置各个光晕元素。
4.  在该向量上随机散布十几个或更多图像，并设置其随机的大小、颜色和透明度。
5.  支持触摸拖动，以便在不同位置进行测试。



我从标准模板开始，并增加了对触摸和拖拽视觉元素的支持。接着，在启动时加载太阳图像，并在 `drawInRect()` 方法中将其绘制在用户手指的当前位置，如代码清单 7-5 所示。

**代码清单 7-5\. 顶层 `drawInRect()` 方法**

```
- (void)glkView:(GLKView *)view drawInRect:(CGRect)rect
{
    GLfloat cx,cy;
    CGPoint centerRelative;
    CGRect frame = [[UIScreen mainScreen] bounds];
    glClearColor(0.0, 0.0, 0.0, 1.0);
    glClear(GL_COLOR_BUFFER_BIT);
    cx=(frame.size.width/2.0);
    //1
    cy=(frame.size.height/2.0);
    centerRelative=CGPointMake(m_PointerLocation.x-cx,cy-m_PointerLocation.y);
    //2
    [[OpenGLCreateTexture getObject]renderTextureAt:centerRelative name:m_FlareSource size:3.0 r:1.0 g:1.0 b:1.0 a:1.0];
    //3
    [m_LensFlare execute:[[UIScreen mainScreen]applicationFrame] source:m_PointerLocation];
}
```

我将创建和渲染纹理的辅助例程放在它们自己的模块 `renderTextureAt()` 中，这将在代码清单 7-6 中介绍。不过，有三行需要注意：

- 第 1 行及后续行获取屏幕中心，并创建用指针跟踪光晕光源（太阳）所需的信息。
- 第 2 行渲染光晕的光源对象，通常是太阳。
- 第 3 行调用在天空中绘制我们的 2D 太阳图形的辅助例程。

**代码清单 7-6\. 渲染 2D 纹理**

```
-(void)renderTextureAt:(CGPoint)position name:(GLuint)name size:(GLfloat)size r:(GLfloat)r g:(GLfloat)g b:(GLfloat)b a:(GLfloat)a; //1
{
    float scaledX,scaledY;
    GLfloat zoomBias=.1;
    GLfloat scaledSize;
    static const GLfloat squareVertices[] =
    {
        -1.0, -1.0, 0.0,
        1.0, -1.0, 0.0,
        -1.0, 1.0, 0.0,
        1.0, 1.0, 0.0,
    };
    static GLfloat textureCoords[] =
    {
        0.0, 0.0,
        1.0, 0.0,
        0.0, 1.0,
        1.0, 1.0
    };
    CGRect frame = [[UIScreen mainScreen] bounds];
    float aspectRatio=frame.size.height/frame.size.width;
    scaledX=2.0*position.x/frame.size.width; //2
    scaledY=2.0*position.y/frame.size.height;
    glDisable(GL_DEPTH_TEST); //3
    glDisable(GL_LIGHTING);
    glMatrixMode(GL_PROJECTION); //4
    glPushMatrix();
    glLoadIdentity();
    glOrthof(-1,1,-1.0*aspectRatio,1.0*aspectRatio, -1, 1); //5
    glMatrixMode(GL_MODELVIEW); //6
    glLoadIdentity();
    glTranslatef(scaledX,scaledY,0); //7
    scaledSize=zoomBias*size; //8
    glScalef(scaledSize,scaledSize, 1); //9
    glVertexPointer(3, GL_FLOAT, 0, squareVertices);
    glEnableClientState(GL_VERTEX_ARRAY);
    glEnable(GL_TEXTURE_2D); //10
    glEnable(GL_BLEND);
    glBlendFunc(GL_ONE,GL_ONE_MINUS_SRC_COLOR);
    glBindTexture(GL_TEXTURE_2D,name);
    glTexCoordPointer(2, GL_FLOAT,0,textureCoords);
    glEnableClientState(GL_TEXTURE_COORD_ARRAY);
    glColor4f(r,g,b,a); //11
    glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
    glMatrixMode(GL_PROJECTION); //12
    glPopMatrix();
    glMatrixMode(GL_MODELVIEW);
    glPopMatrix();
    glEnable(GL_DEPTH_TEST);
    glEnable(GL_LIGHTING);
}
```

那么，以下是具体说明：

- 第 1 行中，position 是以像素为单位的纹理原点，相对于屏幕中心，稍后会将其转换为归一化值。size 是相对值，需要调整以找到最合适的值。最后的参数是颜色和 alpha 值。如果你不希望有任何着色，可以给所有颜色值传递 1.0。这行之后，你会识别出我们的老朋友——顶点和纹理坐标。
- 第 2 行将像素位置基于 frame 的宽度和高度转换为相对值。这些值被缩放 2 倍，因为我们的视口宽高将各为 2 个单位，从-1 到 1 变化。



