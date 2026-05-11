# 第 3 章

## 文件操作

在创建应用程序或游戏时，你需要文件 I/O（输入/输出）。你可能不使用数据库，但总会有需要将某些内容保存到磁盘的情况；可能是关卡、高分、玩家解锁的角色，或者与应用内购买相关的信息。

那么，首先让我们来看看我们有哪些提供文件操作功能的函数。Lua 有两组用于文件操作的函数，一组称为*隐式*函数，另一组称为*显式*函数。两者的区别在于，隐式函数作用于 `io` 命名空间提供的默认文件，而显式函数则作用于之前操作（如 `io.open`）提供的文件句柄。

## 隐式函数

本节列出并描述了你将在 Lua 编程工作中使用的隐式函数，并附带简短示例说明每个函数在代码中的用法。

### `io.close ( [file] )`

该函数关闭输出文件，类似于 `file:close()`，但在未传入文件时，它会关闭默认输出文件。你可以看到 `io.close` 是如何用于关闭一个 `fileHandle` 的。为了确认，我们打印 `fileHandle` 的类型来检查文件是打开还是关闭状态。

```
fileHandle = io.open("file.txt", "w")
print(io.type(fileHandle))
fileHandle:write("Hello world")
io.close(fileHandle)
print(io.type(fileHandle))
```

### `io.flush ( )`

该函数对默认文件运行 `file:flush()`。当文件处于缓冲模式时，数据不会立即写入文件；它会保留在缓冲区中，并在缓冲区接近满时写入。此函数强制将缓冲区数据写入文件并清空缓冲区。

### `io.input ( [file] )`

当不带任何参数调用时，此函数返回当前的默认输入文件。传递的参数可以是文件名或文件句柄。当使用文件名调用该函数时，它会将该命名文件的句柄设置为默认输入文件。如果使用文件句柄调用，它只是将默认输入文件句柄设置为这个传入的文件句柄。

### `io.lines ( [filename] )`

此函数以读取模式打开给定的文件名，并返回一个迭代器函数，该函数每次被调用时都从文件中返回新的一行。当迭代器函数检测到已到达文件末尾时，它会关闭文件并返回 `nil`。

当 `io.lines` 在未提供文件名的情况下调用时，它等同于 `io.input():lines()`，并从默认输入文件读取，且在到达文件末尾时不会关闭文件。

以下是访问此函数的一种方式：

```
for line in io.lines(filename) do
    print(line)
end
```

### `io.open ( filename [,mode] )`

此函数是你使用 Lua 读写文件时用到的主要函数。`mode` 是一个字符串值，可以是以下之一：

*   `"r"`：读取模式（默认）。
*   `"w"`：写入模式。
*   `"a"`：追加模式。
*   `"r+"`：更新模式。所有先前数据均保留。
*   `"w+"`：更新模式。所有先前数据均被清除。
*   `"a+"`：追加更新模式。所有先前数据均保留，且只允许在文件末尾写入。

模式末尾还可以加上 `"b"`，表示以二进制模式而不是文本模式打开文件。此函数返回一个可在显式文件操作中使用的文件句柄。

```
file = io.open("myfilename.txt", "r")
```

### `io.output ( [file] )`



## Lua 文件 I/O 函数

此函数类似于 `io.input()`，但操作的是默认输出文件。使用此函数，你可以将输出重定向到你想要的文件。以下是其用法示例：

```
oldIOFile = io.output()
io.output("mynewfile.txt")
io.write("Hello world")
io:output():close()
io.output(oldIOFile)
```

### `io.read ( … )`

此函数等同于 `io.input():read()`。

```
print(io.read())
```

### `io.tmpfile ( )`

此函数返回一个临时文件的句柄；该文件以更新模式打开，并在程序结束时自动删除。

```
fh = io.tmpfile()
fh:write("Some sample data")
fh:flush()
-- 现在读取我们刚刚写入的数据
fh:seek("set", 0)
content = fh:read("*a")
print("We got : ", content)
```

### `io.type ( obj )`

如果 `obj` 指定的句柄是一个有效的文件句柄，此函数返回结果。如果 `obj` 是一个打开的文件句柄，则返回字符串 `"file"`；如果 `obj` 是一个已关闭的文件句柄，则返回字符串 `"closed file"`。如果 `obj` 不是文件句柄，则返回 `nil`。

```
print(io.type(fh)) -- 因为 fh 为 nil，所以打印 nil
fh = io.input()
print(io.type(fh))
```

### `io.write ( … )`

此函数等同于 `io.output():write()`。

```
io.write("Hello World")
```

## 显式函数

本节列出并描述了你将在 Lua 编程工作中使用的显式函数。

### `file:close ( )`

此函数关闭由该文件引用的文件。

```
file = io.open("myFile.txt", "w")
file:write("Some Data")
file:close()
```

### `file:flush ( )`

此函数将所有尚未写入（在缓冲区中的）数据保存到文件中。

```
fh = io.tmpfile()
fh:write("Some sample data")
fh:flush()
```

### `file:lines ( )`

此函数返回一个迭代器函数，每次调用时都会从文件中返回一个新行。它类似于 `io.lines()`，区别在于此函数不会在循环结束时关闭文件。

```
local file = io.open(filename)
for line in file:lines() do print(line) end
file:close()
```

### `file:read ( [format] )`

此函数根据给定的格式从文件中读取数据。默认情况下，`read` 读取整个下一行。可用的读取格式如下：

- `'*n'`：读取一个数字；返回一个数字而非字符串
- `'*a'`：从当前位置读取整个文件
- `'*l'`：读取下一行；当当前位置位于文件末尾时返回 `nil`（默认格式）
- `number`：读取由该数字指定的字符数组成的字符串，在文件末尾返回 `nil`。

以下是使用这些格式的一些示例：

```
local filename = "data_test.txt"
local file = io.open(filename)
local contents = file:read("*a")
file:close()
print("Contents of the file:", contents)
```

如果此文件以二进制模式打开，并且我们需要仅获取特定数量的字符，那么我们将按如下方式使用：

```
local filename = "test.zip"
local file = io.open(filename,"rb")
local contents = file:read(2)
file:close()
print("File Header:", contents)       -- 所有 ZIP 文件的前两个字符都是 PK
```

### `file:seek ( [whence] [, offset] )`

此函数设置并获取当前文件位置。`whence` 是一个字符串值，可以是以下任意一个：

- `'set'`：基位置 0（文件开头）
- `'cur'`：当前位置（默认值）
- `'end'`：文件末尾

传递的 `offset` 值是以字节为单位从根据这三个选项之一指定的基准位置开始测量的。

`file:seek()` 是处理二进制文件和固定格式时最重要的函数之一；它允许开发者来回迭代以覆盖或更新文件的特定部分。

```
fh = io.tmpfile()
fh:write("Some sample data")
fh:flush()
-- 现在读取我们刚刚写入的数据
fh:seek("set", 0)
content = fh:read("*a")
print("We got : ", content)
```

### `file:setvbuf (mode [, size] )`

此函数设置输出文件的缓冲模式。Lua 中有三种可用的缓冲模式，它们的值以字符串形式传递：

- `'no'`：无缓冲，因此任何操作的输出都会立即显示。
- `'full'`：完全缓冲，因此仅当缓冲区满时才执行输出操作。你可以强制刷新以写入缓冲区。
- `'line'`：行缓冲，因此输出会被缓冲，直到找到换行符（Enter）。

```
io.output():setvbuf("no")
```

### `file:write( … )`

此函数将传递给函数的每个参数写入文件。参数必须是字符串或数字。要写入其他值，你可能需要将它们转换为字符串格式。

```
file = io.open("myfile.txt","w")
file:write("Writing to a file")
file:close()
```

**注意**：Lua 的纯实现缺少文件系统访问所需的函数和命令。这通常作为可添加的外部库提供。它被称为 *Lua 文件系统 (LFS)*。截至撰写本文时，你可以将其与所有框架集成（但这需要的工作量取决于框架）。

