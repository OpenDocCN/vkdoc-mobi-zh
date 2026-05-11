# 问题 89：你将如何将未知类型作为参数传递？

你可以使用 `(id)`。

```
-(void) fooMethod:(id)unknownTypeParameter {
    if( [unknownTypeParameter isKindOfClass:[Animal Class]]) {
        Animal *referanceObj = (Animal *) unknownTypeParameter;
        referanceObj.noOfLegs = 4;
    }
}
```

