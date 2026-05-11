# 第 6 章：它会融合吗？

**197**

在分配主图像的代码下方，添加以下内容：

```
if(bumpmapFile!=nil)
    m_BumpMapInfo=[self loadTexture:bumpmapFile];
```

并在头文件中添加此内容：

```
GLKTextureInfo *m_BumpMapInfo;
```

将太阳系视图控制器中的 `initGeometry()` 调用替换为清单 6-7：

```
-(void)initGeometry
{
    m_Eyeposition[X_VALUE]=0.0;
    m_Eyeposition[Y_VALUE]=0.0;
    m_Eyeposition[Z_VALUE]=3.0;

    m_Earth=[[Planet alloc] init:50 slices:50 radius:1.0 squash:1.0
                textureFile:@"earth_light.png" bumpmapFile:@"earth_normal_hc.png"];
    [m_Earth setPositionX:0.0 Y:0.0 Z:0.0];
}
```

同时，使用清单 6-8 作为新的 `execute()` 方法，将其置于 `Planet.m` 中，并从凹凸映射控制器的 `executePlanet()` 例程中调用。该方法主要用于设置纹理组合器并调用 `multiTextureBumpMap()`。

清单 6-8. Planet.m 中修改后的 execute，调用 multiTextureBumpMap() 进行凹凸映射

```
-(bool)execute
{
    glMatrixMode(GL_MODELVIEW);
    glEnable(GL_CULL_FACE);
    glCullFace(GL_BACK);
    glEnable(GL_LIGHTING);

    glFrontFace(GL_CW);

    glEnable(GL_TEXTURE_2D);
    glEnableClientState(GL_VERTEX_ARRAY);
    glVertexPointer(3, GL_FLOAT, 0, m_VertexData);

    glEnableClientState(GL_TEXTURE_COORD_ARRAY);
    glClientActiveTexture(GL_TEXTURE0);

    glBindTexture(GL_TEXTURE_2D, m_TextureInfo.name);

    glTexCoordPointer(2, GL_FLOAT, 0, m_TexCoordsData);

    glClientActiveTexture(GL_TEXTURE1);
    glTexCoordPointer(2, GL_FLOAT,0,m_TexCoordsData);

    glMatrixMode(GL_MODELVIEW);

    glEnableClientState(GL_NORMAL_ARRAY);
    glNormalPointer(GL_FLOAT, 0, m_NormalData);

    [www.it-ebooks.info](http://www.it-ebooks.info)

    # 第 6 章：它会融合吗？

    **198**

    glColorPointer(4, GL_UNSIGNED_BYTE, 0, m_ColorData);
}
```



