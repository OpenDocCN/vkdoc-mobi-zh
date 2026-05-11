# `-(void) reset`

```
{
    float parentWidthHalf = self.parent.contentSize.width / 2;
    float parentHeight = self.parent.contentSize.height;
    float selfHeight = self.contentSize.height;
    self.position = CGPointMake(parentWidthHalf, parentHeight + selfHeight);
    self.scaleX = 1.0f;
    self.visible = YES;
}
```

