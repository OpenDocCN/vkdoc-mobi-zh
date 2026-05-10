# 第 21 章 ■ 懒初始化模式

@property (readonly) Color color;

@property (readonly) Piece piece;

@property (readonly) NSString *name;

@property (readonly) NSImage *image;

@end

代码清单 21-4 ChessPiece.m 使用+initialize 方法

```
#import "ChessPiece.h"

static NSArray *ColorNames;
static NSArray *PieceNames;
static NSDictionary *PieceImages;

@implementation ChessPiece

+ (void)initialize
{
    if (PieceImages==nil) {
        ColorNames = [NSArray arrayWithObjects:
                      @"White",
                      @"Black",
                      nil];
        PieceNames = [NSArray arrayWithObjects:
                      @"Pawn",
                      @"Rook",
                      @"Bishop",
                      @"Knight",
                      @"Queen",
                      @"King",
                      nil];
        // 加载整套棋子图片
        NSMutableDictionary *images = [NSMutableDictionary dictionary];
        Color color;
        Piece piece;
        for (color=White; color<=Black; color++) {
            for (piece=Pawn; piece<=King; piece++) {
                NSString *name = [ChessPiece nameOfPiece:piece color:color];
                [images setObject:[NSImage imageNamed:name] forKey:name];
            }
        }
        PieceImages = [NSDictionary dictionaryWithDictionary:images];
    }
}

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

