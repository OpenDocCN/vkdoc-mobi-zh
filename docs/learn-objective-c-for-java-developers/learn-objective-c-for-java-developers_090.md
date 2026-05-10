# 第 21 章 ■ 懒初始化模式

使用`+initialize`方法进行类级别的懒初始化时，请牢记以下几点：

- 向类发送的第一条消息，或首次尝试创建类的实例，都会为该类创建类对象并向其发送`+initialize`消息。
- `+initialize`方法每个类仅会被发送一次。但这可能会产生误导，因为 Objective-C 子类会继承其超类的类方法。因此，一个类可能会因为其子类而接收到额外的`+initialize`消息。这就是代码清单 21-4 中的`+initialize`方法会检查`PieceImages`是否已初始化的原因。如果已知`ChessPiece`没有子类，则可以省略此检查。
- 类对象在`+initialize`消息发送之前已完全初始化，因此在`+initialize`方法内部调用其他类方法或创建自身的实例是安全的。

### 总结

懒初始化对于全局变量和实例变量而言都是一种有用的设计模式。它通过仅构建实际需要的信息来优化性能，并将可选和辅助计算推迟到更合适的时机。通过使用`+initialize`类方法来编程构造类运行所需的任何全局变量，可以在很大程度上克服 Objective-C 缺乏静态初始化语句的局限。


[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 22 章：工厂模式

工厂模式将创建对象的任务委托给另一个类，即工厂。工厂有多种形式。工厂可以实现便捷构造函数，通过封装重复或繁琐的准备工作来简化常见对象的构造。它们可用于实现单例和可复用的对象池。但最重要的用途是代表客户端决定新对象的类——这便是本章要探讨的模式。（单例工厂模式将在第 23 章中描述）。

当客户端（即创建对象的代码）无法轻松确定新对象的类时，就会采用工厂模式，因为客户端无法知晓或不应知晓要创建哪个类。

通常在多个密切相关的子类之间需要进行选择，而正确的选择取决于对客户端隐藏的决策或实现细节。在 Java 中，这通常通过静态方法或抽象工厂对象来实现。Objective-C 则拥有一种强大的模式，称为类簇，它直接在对象初始化器中实现工厂模式。

### URL 工厂

工厂模式的一个典型例子是 URL 对象工厂。URL 对象可以根据 URI 字符串（例如`"http://www.apress.com/"`）来创建。URL 类库可能定义了多个 URL 类：一个基础的`URL`类，以及专门的子类，如`FileURL`、`HTTPURL`、`SecureURL`等。

要求`URL`类的客户端通过检查字符串来决定实例化哪个子类，将是一个非常糟糕的设计。相反，基础`URL`类会声明一个 URL 对象工厂，如下所示：

```java
public static URL makeURL( String uri );
```

静态方法`URL.makeURL(String)`会解析字符串，确定其协议，然后创建并返回相应类的对象。字符串`"file://users/home/file.data"`可能返回一个`FileURL`实例，而字符串`"https://secure.apress.com/"`则可能返回一个`SecureURL`实例。实际的子类以及用于确定创建哪个类的逻辑，对客户端来说几乎无关紧要。

### 矩阵类

我们将通过类的典型演化过程来探索这种工厂模式。使用的示例是一个`Matrix`类，它封装了数学矩阵的概念并执行简单的矩阵运算。清单 22-1 展示了 Java 版本的初始类，清单 22-2 展示了 Objective-C 中的等效实现。

为了让你能专注于工厂模式，这些清单省略了实际实现中的许多细节。完整的实现可从[`www.apress.com/`](http://www.apress.com/)的源代码/下载部分获取。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**清单 22-1.** Java 中的初始矩阵类

```java
public class Matrix
{
    protected int rows;
    protected int columns;
    double[] values;

    public Matrix( double[] values, int rows, int columns )
    {
        this(values,true,rows,columns);
    }

    protected Matrix( double[] values, boolean copyValues, int rows, int columns )
    {
        this.rows = rows;
        this.columns = columns;
        if (copyValues) {
            this.values = new double[values.length];
            System.arraycopy(values,0,this.values,0,rows*columns);
        } else {
            this.values = values;
        }
    }

    public int getRows()
    {
        return rows;
    }

    public int getColumns()
    {
        return columns;
    }

    public double getValue( int row, int column )
    {
        return values[row*columns+column];
    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**清单 22-1.** (续)

```java
    public boolean isIdentity( )
    {
        if (rows!=columns)
            return false;
        …
        return true;
    }

    public Matrix add( Matrix matrix )
    {
        double[] sumArray = new double[rows*columns];
        …
        return new Matrix(sumArray,false,rows,columns);
    }

    public Matrix multiply( Matrix right )
    {
        double[] productArray = new double[rows*right.columns];
        …
        return new Matrix(productArray,false,rows,right.columns);
    }

    public Matrix multiply( double scalar )
    {
```



```java
double[] productArray = new double[rows*columns];

…

return new Matrix(productArray,false,rows,columns);
}

public Matrix transpose( )
{
    double[] transArray = new double[rows*columns];
    …
    return new Matrix(transArray,false,columns,rows);
}
```

`Matrix`类的基本设计（如清单 22-1 所示）非常直观。它封装了一个包含浮点数的二维矩阵。通过一个一维数组创建一个新的`Matrix`对象，并且该对象是不可变的。通过`row`和`column`属性可以获取矩阵的维度。矩阵操作由`add(Matrix)`、`multiply(Matrix)`、`multiply(double)`和`transpose()`方法执行。如果该对象代表一个单位矩阵，则`identity`属性返回`true`。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 22 章 ■ 工厂模式

### 清单 22-2. Objective-C 中的初始 Matrix 类

```objectivec
@interface Matrix : NSObject {
@protected
    NSUInteger rows;
    NSUInteger columns;
    __strong double *values;
}

- (id)initIdentityWithDimensions:(NSUInteger)dimensions;
- (id)initWithValues:(const double*)valueArray
                rows:(NSUInteger)rowCount
             columns:(NSUInteger)colCount;

@property (readonly) NSUInteger rows;
@property (readonly) NSUInteger columns;
@property (readonly,getter=isIdentity) BOOL identity;

- (double)valueAtRow:(NSUInteger)row column:(NSUInteger)column;
- (Matrix*)addMatrix:(Matrix*)matrix;
- (Matrix*)multiplyMatrix:(Matrix*)matrix;
- (Matrix*)multiplyScalar:(double)scalar;
- (Matrix*)transpose;

@end

@interface Matrix () // private methods
- (id)initWithAllocatedArray:(__strong double*)array
                        rows:(NSUInteger)rowCount
                     columns:(NSUInteger)colCount;
@end

#define VALUE(ARRAY,COLUMNS,ROW,COLUMN) ARRAY[((ROW)*(COLUMNS))+(COLUMN)]
#define IVALUE(ROW,COLUMN) VALUE(values,columns,ROW,COLUMN)
#define SIZEOFARRAY(ROWS,COLUMNS) (sizeof(double)*(ROWS)*(COLUMNS)) 414
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 第 22 章 ■ 工厂模式

#### `Matrix`的实现

```objectivec
@implementation Matrix

- (id)initIdentityWithDimensions:(NSUInteger)dimensions
{
    self = [super init];
    if (self != nil) {
        values = MatrixAllocateEmptyArray(dimensions,dimensions);
        NSUInteger i;
        for (i=0; i<dimensions; i++)
            IVALUE(i,i) = 1.0;
    }
    return self;
}

- (id)initWithValues:(const double*)valueArray
                rows:(NSUInteger)rowCount
             columns:(NSUInteger)colCount
{
    __strong double *dupArray = MatrixCopyArray(valueArray,rowCount,colCount);
    return [self initWithAllocatedArray:dupArray rows:rowCount columns:colCount];
}

- (id)initWithAllocatedArray:(__strong double*)array
                        rows:(NSUInteger)rowCount
                     columns:(NSUInteger)colCount
{
    self = [super init];
    if (self!=nil) {
        rows = rowCount;
        columns = colCount;
        values = array;
    }
    return self;
}

@synthesize rows, columns;

- (BOOL)isIdentity
{
    if (rows!=columns)
        return NO;
    …
    return YES;
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 第 22 章 ■ 工厂模式

```objectivec
- (double)valueAtRow:(NSUInteger)row column:(NSUInteger)column
{
    return IVALUE(row,column);
}

- (Matrix*)addMatrix:(Matrix*)matrix
{
    __strong double *sumArray = MatrixAllocateArray(rows,columns);
    …
    return [[Matrix alloc] initWithAllocatedArray:sumArray
                                             rows:rows
                                           columns:columns];
}

- (Matrix*)multiplyMatrix:(Matrix*)matrix
{
    __strong double *productArray = MatrixAllocateArray(leftMatrix.rows,columns);
    …
    return [[Matrix alloc] initWithAllocatedArray:productArray
                                             rows:leftMatrix.rows
                                           columns:columns];
}

- (Matrix*)multiplyScalar:(double)scalar
{
    __strong double *productArray = MatrixAllocateArray(rows,columns);
    …
    return [[Matrix alloc] initWithAllocatedArray:productArray
                                             rows:rows
                                           columns:columns];
}

- (Matrix*)transpose
{
    __strong double *transArray = MatrixAllocateArray(columns,rows);
    …
    return [[Matrix alloc] initWithAllocatedArray:transArray
                                             rows:columns
                                           columns:rows];
}

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 第 22 章 ■ 工厂模式

#### `MatrixCopyArray`函数

```c
double *MatrixCopyArray( const __strong double *srcArray,
                         NSUInteger rows,
                         NSUInteger columns )
{
    __strong double *duplicateArray = MatrixAllocateArray(rows,columns);
    NSCopyMemoryPages(srcArray,duplicateArray,SIZEOFARRAY(rows,columns));
    return duplicateArray;
}
```


```c
double *MatrixAllocateEmptyArray( NSUInteger rows, NSUInteger columns )
{
    __strong double *emptyArray = MatrixAllocateArray(rows,columns);
    bzero(emptyArray,SIZEOFARRAY(rows,columns));
    return emptyArray;
}

double *MatrixAllocateArray( NSUInteger rows, NSUInteger columns )
{
    __strong double *array = NSAllocateCollectable(SIZEOFARRAY(rows,columns),0);
    return array;
}
```

清单 22-2 中展示的 Objective-C 实现，在功能上与 Java 版本非常相似，尽管存在一些显著的实现差异。它定义了一些实用的 C 函数（`MatrixCopyArray`、`MatrixAllocateEmptyArray` 和 `MatrixAllocateArray`），以便更轻松地创建和操作原始浮点数值数组。它还声明了一些便捷宏（`VALUE`、`IVALUE` 和 `SIZEOFARRAY`），用于高效访问值数组。但除此之外，它的构造器、属性和操作与对应的 Java 版本基本一致。

最后，清单 22-3 展示了一些代码，说明了如何在 Java 和 Objective-C 中创建 Matrix 类并执行矩阵运算。

**清单 22-3\. Matrix 类演示代码**

**Java**
```java
double[] a_values = {
    1.0, 0.0, 2.0,
    -1.0, 3.0, 1.0
};

double[] b_values = {
    3.0, 1.0,
    2.0, 1.0,
    1.0, 0.0
};

double[] i_values = {
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    0.0, 0.0, 1.0
};

Matrix A = new Matrix(a_values,2,3);
Matrix B = new Matrix(b_values,3,2);
Matrix I = new Matrix(i_values,3,3);

System.out.println("A="+A);
System.out.println("B="+B);
System.out.println("I="+I);
System.out.println("B+B="+B.add(B));
System.out.println("A*3="+A.multiply(3.0));
System.out.println("A*B="+A.multiply(B));
System.out.println("A*I="+A.multiply(I));
System.out.println("Atr="+A.transpose());
```

**Objective-C**
```objc
double a_values[] = {
    1.0, 0.0, 2.0,
    -1.0, 3.0, 1.0
};

double b_values[] = {
    3.0, 1.0,
    2.0, 1.0,
    1.0, 0.0
};

double i_values[] = {
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    0.0, 0.0, 1.0,
};

Matrix *A = [[Matrix alloc] initWithValues:a_values rows:2 columns:3];
Matrix *B = [[Matrix alloc] initWithValues:b_values rows:3 columns:2];
Matrix *I = [[Matrix alloc] initWithValues:i_values rows:3 columns:3];

NSLog(@"A=%@",A);
NSLog(@"B=%@",B);
NSLog(@"I=%@",I);
NSLog(@"B+B=%@",[B addMatrix:B]);
NSLog(@"A*3=%@",[A multiplyScalar:3.0]);
NSLog(@"A*B=%@",[A multiplyMatrix:B]);
NSLog(@"A*I=%@",[A multiplyMatrix:I]);
NSLog(@"Atr=%@",[A transpose]);
```

Java 和 Objective-C 中的 Matrix 对象都简单、清晰且紧凑。只有一个问题：随着矩阵维度的增长，两个矩阵相加和相乘所需的计算量会呈指数级增长。在这个特定应用中，我们发现大量矩阵是单位矩阵。如果知道两个矩阵中至少有一个是单位矩阵，那么乘法运算可以得到优化。

一种解决方案是，在运算前先测试两个矩阵，判断是否有任何一个为单位矩阵。当其中一个矩阵是单位矩阵时，这可以显著减少两个矩阵相乘的计算量。然而，这也会给所有乘法运算增加额外的开销，并且仅仅是用线性增长取代了指数级增长。

你认为最有效的解决方案是创建一个表示单位矩阵的 `Matrix` 子类。该子类将用优化版本覆盖数学方法。接下来的两个部分将对比 Java 中使用类工厂方法的解决方案，与 Objective-C 中使用类簇的等效解决方案。

### Java 矩阵工厂

清单 22-4 和 22-5 展示了 Java 中 Matrix 类的更新部分，并高亮显示了重要更改。新版本使用工厂方法来创建 Matrix 对象。

**清单 22-4\. 修改后的 Java Matrix 类**
```java
public class Matrix {
    protected int rows;
    protected int columns;
    double[] values;
```



`public static Matrix makeMatrix( double[] values, int rows, int columns )`

`{`

`return Matrix.makeMatrix(values,false,rows,columns);`

`}`

`protected static Matrix makeMatrix( double[] values, boolean copyValues, int rows, int columns )`

`{`

`if (isIdentityMatrix(values,rows,columns)) {`

`return new IdentityMatrix(values,copyValues,rows);`

`}`

`return new Matrix(values,copyValues,rows,columns);`

`}`

`protected static boolean isIdentityMatrix( double[] values, int rows, int columns )`

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 ■ 工厂模式

`{`

`if (rows!=columns)`

`return false;`

`…`

`return true;`

`}`

`protected Matrix( double[] values, int rows, int columns )`

`{`

`this(values,true,rows,columns);`

`}`

`protected Matrix( double[] values, boolean copyValues, int rows, int columns )`

`{`

`this.rows = rows;`

`this.columns = columns;`

`if (copyValues) {`

`this.values = new double[values.length];`

`System.arraycopy(values,0,this.values,0,rows*columns);`

`} else {`

`this.values = values;`

`}`

`}`

`…`

`public boolean isIdentity( )`

`{`

`return false;`

`}`

`public Matrix add( Matrix matrix )`

`{`

`double[] sumArray = new double[rows*columns];`

`…`

`return Matrix.makeMatrix(sumArray,false,rows,columns);`

`}`

`public Matrix multiply( Matrix right )`

`{`

`return right.leftMultiply(this);`

`}`

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 ■ 工厂模式

`protected Matrix leftMultiply( Matrix leftMatrix )`

`{`

`double[] productArray = new double[leftMatrix.rows*columns];`

`…`

`return Matrix.makeMatrix(productArray,false,leftMatrix.rows,columns);`

`}`

`public Matrix multiply( double scalar )`

`{`

`double[] productArray = new double[rows*columns];`

`…`

`return Matrix.makeMatrix(productArray,false,rows,columns);`

`}`

`public Matrix transpose( )`

`{`

`double[] transArray = new double[rows*columns];`

`…`

`return Matrix.makeMatrix(transArray,false,columns,rows);`

`}`

`}`

以下是 `Matrix` 中的改动：

-   公有的 `Matrix(…)` 构造函数被改为受保护，并由一个公有的静态工厂方法 `makeMatrix(…)` 所取代。任何需要创建 `Matrix` 对象的客户端代码都必须重写，改为调用 `Matrix.makeMatrix(…)` 而非 `new Matrix(…)`。
-   `makeMatrix(…)` 工厂方法会利用旧 `isIdentity()` 测试的静态版本，来确定矩阵中的值是否描述一个单位矩阵。如果确实如此，工厂方法便创建并返回一个 `IdentityMatrix` 实例；否则，它创建一个 `Matrix` 对象。
-   `multiply(Matrix)` 方法经过重新设计，会调用右操作数对象的受保护方法 `leftMultiply(Matrix)`。这使得 `Matrix` 的子类无论作为等式中的左矩阵还是右矩阵，都能够拦截乘法运算。

现在创建了一个 `IdentityMatrix` 子类，如清单 22-5 所示。

**清单 22-5. Java 的 IdentityMatrix 类**

```
class IdentityMatrix extends Matrix

{

protected IdentityMatrix( double[] values, boolean copyValues, int dimensions )

{

super(values,copyValues,dimensions,dimensions);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 22 章 ■ 工厂模式

public boolean isIdentity()

{

return true;

}

public Matrix multiply(Matrix right)

{

return right;

}

protected Matrix leftMultiply(Matrix leftMatrix)

{

return leftMatrix;

}

public Matrix transpose()

{

return this;

}

}
```

`IdentityMatrix` 子类只表示单位矩阵。它用优化后的版本重写了 `Matrix` 的数学方法。通过重写 `multiply(Matrix)` 和 `leftMultiply(Matrix)` 方法，无论它作为左操作数还是右操作数，都能拦截乘法运算。

现在，每当为某个单位矩阵创建 `Matrix` 对象时，`makeMatrix(…)` 方法会转而创建一个 `IdentityMatrix` 实例。这对客户端是完全透明的；客户端将所有对象都当作 `Matrix` 对象来对待。`Matrix` 和 `IdentityMatrix` 对象之间的运算，会通过 `IdentityMatrix` 重写的方法得到优化。

最后，使用 `Matrix` 类的代码被修改为使用新的 `Matrix` 工厂方法，如清单 22-6 所示。

**清单 22-6. 修改后的 Java Matrix 类用法**

```
double[] a_values = {

1.0, 0.0, 2.0,

-1.0, 3.0, 1.0
```


```objective-c
double[] b_values = {
    3.0, 1.0,
    2.0, 1.0,
    1.0, 0.0
};

double[] i_values = {
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    0.0, 0.0, 1.0
};
```

`Matrix A = Matrix.makeMatrix(a_values, 2, 3);`
`Matrix B = Matrix.makeMatrix(b_values, 3, 2);`
`Matrix I = Matrix.makeMatrix(i_values, 3, 3);`

`System.out.println("A=" + A);`
`System.out.println("B=" + B);`
`System.out.println("I=" + I);`

### Objective-C 矩阵类簇

现在考虑在 Objective-C 中实现相同的解决方案。更新后的 Objective-C 矩阵类如列表 22-7 至 22-9 所示。显著的变化已高亮标注。

### 列表 22-7. `Matrix.h` 和 `Matrix+Private.h`

```objective-c
@interface Matrix : NSObject {
@protected
    NSUInteger rows;
    NSUInteger columns;
    __strong double *values;
}

- (id)initWithValues:(const double*)valueArray
                rows:(NSUInteger)rowCount
             columns:(NSUInteger)colCount;

@property (readonly) NSUInteger rows;
@property (readonly) NSUInteger columns;
@property (readonly, getter=isIdentity) BOOL identity;

- (double)valueAtRow:(NSUInteger)row column:(NSUInteger)column;
- (Matrix*)addMatrix:(Matrix*)matrix;
- (Matrix*)multiplyMatrix:(Matrix*)matrix;
- (Matrix*)multiplyScalar:(double)scalar;
- (Matrix*)transpose;

@end

#define VALUE(ARRAY, COLUMNS, ROW, COLUMN) ARRAY[((ROW)*(COLUMNS)) + (COLUMN)]
#define IVALUE(ROW, COLUMN) VALUE(values, columns, ROW, COLUMN)
#define SIZEOFARRAY(ROWS, COLUMNS) (sizeof(double)*(ROWS)*(COLUMNS))
```

```objective-c
@interface Matrix () // 私有方法
- (id)initWithAllocatedArray:(__strong double*)array
                       rows:(NSUInteger)rowCount
                    columns:(NSUInteger)colCount;
- (Matrix*)leftMultiplyMatrix:(Matrix*)leftMatrix;
@end
```

`Matrix` 类的 `@interface` 没有变化。在私有的 `@interface` 扩展中，声明了一个 `-leftMultiplyMatrix:` 方法，与 Java 中的做法相同。

### 列表 22-8. `Matrix.m`

```objective-c
@implementation Matrix

- (id)initWithValues:(const double*)valueArray
                rows:(NSUInteger)rowCount
             columns:(NSUInteger)colCount
{
    __strong double *dupArray = MatrixCopyArray(valueArray, rowCount, colCount);
    return [self initWithAllocatedArray:dupArray rows:rowCount columns:colCount];
}

- (id)initWithAllocatedArray:(__strong double*)array
                       rows:(NSUInteger)rowCount
                    columns:(NSUInteger)colCount
{
    self = [super init];
    if (self != nil) {
        if ([self isMemberOfClass:[Matrix class]]) {
            if (MatrixIsIdentity(array, rowCount, colCount)) {
                return [[IdentityMatrix alloc] initWithAllocatedArray:array
                                                                rows:rowCount
                                                             columns:colCount];
            }
        }
        rows = rowCount;
        columns = colCount;
        values = array;
    }
    return self;
}

…

- (BOOL)isIdentity
{
    return NO;
}

…

- (Matrix*)multiplyMatrix:(Matrix*)matrix
{
    return [matrix leftMultiplyMatrix:self];
}

- (Matrix*)leftMultiplyMatrix:(Matrix*)leftMatrix
{
    __strong double *productArray = MatrixAllocateArray(leftMatrix.rows, columns);
    …
    return [[Matrix alloc] initWithAllocatedArray:productArray
                                            rows:leftMatrix.rows
                                         columns:columns];
}

…

@end

…

BOOL MatrixIsIdentity( const __strong double *array,
                       NSUInteger rows,
                       NSUInteger columns )
{
    if (rows != columns)
        return NO;
    …
    return YES;
}
```

两种实现最显著的区别在于，Objective-C 版本中没有工厂方法。取而代之的是，修改后的 `-[Matrix initWithAllocatedArray:rows:columns:]` 方法会在适当时机，自动将正在创建的 `Matrix` 对象实例替换为 `IdentityMatrix` 的实例。它通过丢弃最初创建的对象，创建一个所需类的新对象，并将这个替代对象返回给调用方来实现。

Objective-C 将此称为**类簇**，之所以这样命名，是因为基类 `Matrix` 是访问子类簇的入口点。创建 `Matrix` 类对象时，可能会返回 `Matrix` 或其某个子类的实例。


`Matrix` 类的其余更改模仿了 Java 实现中的做法。一个本地的 `MatrixIsIdentity()` C 函数用于判断哪些数值数组构成单位矩阵，乘法逻辑则移至 `-leftMultiplyMatrix:` 方法。在更新后的 `Matrix` 类正常运行之前，必须先定义一个 `IdentityMatrix` 子类，如清单 22-9 所示。

**清单 22-9\. `IdentityMatrix.m`**

```objc
@implementation IdentityMatrix

- (BOOL)isIdentity
{
    return YES;
}

- (Matrix*)multiplyMatrix:(Matrix*)matrix
{
    return matrix;
}

- (Matrix*)leftMultiplyMatrix:(Matrix*)leftMatrix
{
    return leftMatrix;
}

- (Matrix*)transpose
{
    return self;
}

@end
```

类簇的真正威力将在你审视（如清单 22-10 所示）在 Objective-C 中使用修改后的 `Matrix` 对象的代码时变得显而易见。

**清单 22-10\. 修改后的 Objective-C Matrix 类用法**

```objc
double a_values[] = {
    1.0, 0.0, 2.0,
    -1.0, 3.0, 1.0
};

double b_values[] = {
    3.0, 1.0,
    2.0, 1.0,
    1.0, 0.0
};

double i_values[] = {
    1.0, 0.0, 0.0,
    0.0, 1.0, 0.0,
    0.0, 0.0, 1.0,
};

Matrix *A = [[Matrix alloc] initWithValues:a_values rows:2 columns:3];
Matrix *B = [[Matrix alloc] initWithValues:b_values rows:3 columns:2];
Matrix *I = [[Matrix alloc] initWithValues:i_values rows:3 columns:3];
NSLog(@"A=%@",A);
NSLog(@"B=%@",B);
NSLog(@"I=%@",I);
```

你在清单 22-10 中应立即注意到的一点是，它与清单 22-3 中的 Objective-C 代码完全相同。类簇的真正威力在于能够在类的构造器中实现一个工厂方法，而无需更改其外部接口。

类簇具有一些重要的影响：
- 可以在不更改创建对象代码的情况下实现类簇。
- 类簇正是遵守 init 契约如此重要的原因。
- 对类簇进行子类化通常很困难。

第一个特性正是类簇如此强大的原因：任何初始化方法都可以转变为类簇。这种更改对于创建对象的现有代码来说在很大程度上是透明的。这在不断演化的设计中极为有用。可以先定义一个简单的基类，之后再将其替换为类簇，而无需对现有代码进行全局更改。

类簇的一个副作用是，它可能使你很难（甚至不可能）创建类簇基类的自定义子类。基类的初始化方法通常是决定创建哪个类对象的地方。由于子类必须在初始化期间调用其超类的初始化方法，因此你的子类初始化方法存在风险：可能会被基类随意替换为不同的对象。设计为可被子类化的类簇通常会有一个指定的初始化方法供非簇子类使用，或者其编写方式使得在被子类调用时能够合理运行。如果你有一个想要子类化的类簇，请参阅该类文档中的“子类化注意事项”部分。

**注意** 一个常见的编程陷阱是：从基类簇的初始化方法中调用子类的初始化方法，而该子类初始化方法又递归地调用基类初始化方法。如果基类初始化方法盲目地创建一个新的子类对象，将导致无限递归循环。`Matrix` 类通过让 `-initWithAllocatedArray:rows:columns:` 方法检查正在初始化的对象的类（即 `if ([self isMemberOfClass:[Matrix class]]) …`）来避免此问题。仅当它是基类时，才会考虑将对象替换为子类。当子类调用基类初始化方法时，基类初始化方法会正常运行。另一种方法是策略性地组织子类初始化方法，使它们不会递归调用实现类簇的基类初始化方法。



