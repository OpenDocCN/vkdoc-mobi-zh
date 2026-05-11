# `-(void) update:(ccTime)delta`

```
{
    if (self.parent.visible)
    {
        NSAssert([self.parent isKindOfClass:[Enemy class]], @"not a Enemy");
        Enemy* enemy = (Enemy*)self.parent;
        self.scaleX = enemy.hitPoints / (float)enemy.initialHitPoints;
    }
    else if (self.visible)
    {
        self.visible = NO;
    }
}
```

