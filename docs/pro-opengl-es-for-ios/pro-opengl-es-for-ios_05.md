# 第 1 章：计算机图形学：从过去到现在

18

```objectivec
- (void)setupGL
{
    [EAGLContext setCurrentContext:self.context]; //7

    [self loadShaders];

    self.effect = [[GLKBaseEffect alloc] init]; //8
    self.effect.light0.enabled = GL_TRUE; //9
    self.effect.light0.diffuseColor = GLKVector4Make(1.0f, 0.4f, 0.4f, 1.0f); //10

    glEnable(GL_DEPTH_TEST); //11

    glGenVertexArraysOES(1, &_vertexArray); //12
    glBindVertexArrayOES(_vertexArray);

    glGenBuffers(1, &_vertexBuffer); //13
    glBindBuffer(GL_ARRAY_BUFFER, _vertexBuffer);
    glBufferData(GL_ARRAY_BUFFER,
                 sizeof(gCubeVertexData), gCubeVertexData, GL_STATIC_DRAW); //14

    glEnableVertexAttribArray(GLKVertexAttribPosition); //15
    glVertexAttribPointer(GLKVertexAttribPosition, 3, GL_FLOAT, GL_FALSE, 24, BUFFER_OFFSET(0));
    glEnableVertexAttribArray(GLKVertexAttribNormal);
    glVertexAttribPointer(GLKVertexAttribNormal, 3, GL_FLOAT, GL_FALSE, 24, BUFFER_OFFSET(12));

    glBindVertexArrayOES(0); //16
}

- (void)tearDownGL //17
{
    [EAGLContext setCurrentContext:self.context];

    glDeleteBuffers(1, &_vertexBuffer);
    glDeleteVertexArraysOES(1, &_vertexArray);

    self.effect = nil;

    if (_program) {
        glDeleteProgram(_program);
        _program = 0;
    }
}
```

[www.it-ebooks.info](http://www.it-ebooks.info)

