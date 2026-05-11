# 问题 92：什么是 Fallthrough 语句？它有什么作用？

`Fallthrough` “落入”下一个 case，而不是下一个匹配的 case。这个概念继承自 C 语言的 switch 语句，其中每个 case 都可以被认为是一个 goto 目标标签，而 switch 语句将执行带到第一个匹配的标签。

