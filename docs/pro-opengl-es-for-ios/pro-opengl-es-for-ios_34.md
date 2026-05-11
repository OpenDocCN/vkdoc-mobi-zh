# 第 7 章：完美渲染杂项

[www.it-ebooks.info](http://www.it-ebooks.info)

```
glDisable( GL_LIGHTING ); //4
glLoadIdentity();
glTranslatef(0.0,m_WorldY,m_WorldZ); //5
[self drawPlatform:0.0 y:0.0 z:0.0]; //6
if(frameNumber>(lightsOnFlagTrigger/2)) //7
   lightsOnFlag=false;
else
   lightsOnFlag=true;
if(frameNumber>lightsOnFlagTrigger)
   frameNumber=0;
[self drawLight: GL_LIGHT0]; //8
glDisable( GL_DEPTH_TEST ); //9
[self calculateShadowMatrix]; //10
if(lightsOnFlag)
   [self drawShadow]; //11
glShadeModel( GL_SMOOTH );
glEnableClientState(GL_VERTEX_ARRAY); //12
glVertexPointer( 3, GL_FLOAT, 0, m_CubeVertices );
glEnableClientState(GL_COLOR_ARRAY);
glColorPointer(4, GL_UNSIGNED_BYTE, 0, m_CubeColors);
glRotatef(m_WorldRotationX, 1.0, 0.0, 0.0); //13
glRotatef(m_WorldRotationY, 0.0, 1.0, 0.0);
glTranslatef(0.0,m_TransY, 0.0); //14
glRotatef( m_SpinZ, 0.0, 0.0, 1.0 ); //15
glRotatef( m_SpinY, 0.0, 1.0, 0.0 );
glRotatef( m_SpinX, 1.0, 0.0, 0.0 );
glEnable( GL_DEPTH_TEST ); //16
glFrontFace(GL_CCW);
glDrawElements( GL_TRIANGLE_FAN, 6 * 3, GL_UNSIGNED_BYTE, m_Tfan1);
glDrawElements( GL_TRIANGLE_FAN, 6 * 3, GL_UNSIGNED_BYTE, m_Tfan2);
glDisable(GL_BLEND);
glEnable(GL_LIGHTING);
transY+=.1; //17
frameNumber++;
```

[www.it-ebooks.info](http://www.it-ebooks.info)

