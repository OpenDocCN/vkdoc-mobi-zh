# 第 12 章 ■ 使用 CTK 开发测试代码

*清单 12-4\. 更新后的 TUX 函数表 (ft.h)*

```c
BEGIN_FTE
FTH(0, "Demodrvr test cases")
FTE(1, "Demodrvr test", 1, 0, TestProc)
FTE(2, "Demodrvr perfomace test", 2, 0, PerfomanceTestProc)
END_FTE
```

将这一新测试添加到测试流程中并运行测试非常直接，如图 12-11 所示，查看结果也与之前相同，如图 12-12 所示。清单 12-5 显示了这两个测试的结果日志文件。

*图 12-11\. 在测试流程中运行添加的测试*

[www.it-ebooks.info](http://www.it-ebooks.info/)

