# `ChessPiece` 类与惰性初始化

`ChessPiece` 类（如代码清单 21-1 和 21-2 所示）通过定义两个类属性 `+[ChessPiece colorNames]` 和 `+[ChessPiece pieceNames]` 来实现这一点。这两个属性返回一个不可变的字符串集合，可用于将颜色或棋子枚举（`int`）转换为字符串。它们在首次被请求时，采用**惰性初始化**来创建并保存这些不可变数组。

## 代码清单 21-1：`ChessPiece.h`

```objective-c
#import <Cocoa/Cocoa.h>

typedef enum {
    White=0,
    Black
} Color;

typedef enum {
    Pawn=0,
    Rook,
    Bishop,
    Knight,
    Queen,
    King
} Piece;

@interface ChessPiece : NSObject {
    Color color;
    Piece piece;
}

+ (NSArray*)colorNames;
+ (NSArray*)pieceNames;
+ (NSDictionary*)images;
+ (NSString*)nameOfPiece:(Piece)piece color:(Color)color;

- (id)initWithPiece:(Piece)thePiece color:(Color)theColor;

@property (readonly) Color color;
@property (readonly) Piece piece;
@property (readonly) NSString *name;
@property (readonly) NSImage *image;

@end
```

## 代码清单 21-2：`ChessPiece.m`

```objective-c
#import "ChessPiece.h"

static NSArray *ColorNames;
static NSArray *PieceNames;
static NSDictionary *PieceImages;

@implementation ChessPiece

+ (NSArray*)colorNames
{
    if (ColorNames==nil) {
        ColorNames = [NSArray arrayWithObjects:
                      @"White",
                      @"Black",
                      nil];
    }
    return ColorNames;
}

+ (NSArray*)pieceNames
{
    if (PieceNames==nil) {
        PieceNames = [NSArray arrayWithObjects:
                      @"Pawn",
                      @"Rook",
                      @"Bishop",
                      @"Knight",
                      @"Queen",
                      @"King",
                      nil];
    }
    return PieceNames;
}

+ (NSDictionary*)images
{
    if (PieceImages==nil) {
        // 加载整组棋子图片
        NSMutableDictionary *images = [NSMutableDictionary new];
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
    return PieceImages;
}

+ (NSString*)nameOfPiece:(Piece)piece color:(Color)color
{
    return [NSString stringWithFormat:@"%@ %@",
            [[ChessPiece colorNames] objectAtIndex:color],
            [[ChessPiece pieceNames] objectAtIndex:piece]];
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
    return [[ChessPiece images] objectForKey:[self name]];
}

@end
```

它还定义了一个类属性 `+[ChessPiece images]`，其中包含所有棋子的图像。该属性在 `ChessPiece` 对象首次尝试获取其图形表示时进行惰性初始化。这是一个耗时且资源密集的初始化过程，并且需要应用程序完全初始化并正常运行，因为它依赖于在其应用包中查找图像资源文件。

### 类的 `+initialize` 方法

Objective-C 类本身也是惰性初始化的。定义类的结构直到第一条消息发送给该类时才构建。这使得运行时更加敏捷，能够以最少的初始化量启动。但 Objective-C 更进一步：在初始化 `Class` 对象之后、发送任何其他消息之前，运行时会向新创建的 `Class` 对象发送一条单独的 `+initialize` 消息。你可以重写此方法以执行额外的类级别初始化。

通过利用 `+initialize` 消息，`ChessPiece` 类可以显著简化，如代码清单 21-3 和 21-4 所示。

## 代码清单 21-3：使用 `+initialize` 的 `ChessPiece.h`

```objective-c
#import <Cocoa/Cocoa.h>

typedef enum {
    White=0,
    Black
} Color;

typedef enum {
    Pawn=0,
    Rook,
    Bishop,
    Knight,
    Queen,
    King
} Piece;

@interface ChessPiece : NSObject {
    Color color;
    Piece piece;
}

+ (NSString*)nameOfPiece:(Piece)piece color:(Color)color;
```



- (id)initWithPiece:(Piece)thePiece color:(Color)theColor; 407

[www.it-ebooks.info](http://www.it-ebooks.info/)

