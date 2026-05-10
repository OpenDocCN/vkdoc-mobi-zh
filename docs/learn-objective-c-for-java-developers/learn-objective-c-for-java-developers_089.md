# 第 21 章 ■ 懒初始化模式

```
+ (NSString*)nameOfPiece:(Piece)piece color:(Color)color
{
    return [NSString stringWithFormat:@"%@ %@",
            [ColorNames objectAtIndex:color],
            [PieceNames objectAtIndex:piece]];
}

- (id)initWithPiece:(Piece)thePiece color:(Color)theColor
{
    self = [super init];
    if (self != nil) {
        piece = thePiece;
        color = theColor;
    }
    return self;
}

@synthesize color, piece;

- (NSString*)name
{
    return [ChessPiece nameOfPiece:piece color:color];
}

- (NSImage*)image
{
    return [PieceImages objectForKey:[self name]];
}

@end
```

在这个更新版本中，所有全局初始化工作都在`+initialize`方法中一次性完成。可以移除所有用于检查静态变量是否已初始化的测试代码。所有代码都可以安全地假设`+initialize`已在任何其他方法（类方法或实例方法）执行之前运行完毕。额外的好处是，名称和图片缓存集合现在完全私有，只能被`ChessPiece.m`中的代码访问。

从外部来看，代码清单 21-2 和 21-4 所示的实现行为相同：发送`-name`消息返回棋子名称，发送`-image`消息返回棋子图片。内部唯一的显著区别在于时机。在代码清单 21-2 中，图片数组将在首次通过`-image`消息请求时加载。在代码清单 21-4 中，图片数组将在首次使用`ChessPiece`类时创建，无论是否有代码请求图片。

[www.it-ebooks.info](http://www.it-ebooks.info/)

