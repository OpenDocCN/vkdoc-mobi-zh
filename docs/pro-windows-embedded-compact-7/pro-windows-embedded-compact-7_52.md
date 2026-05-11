# 第 11 章 ■ 调试设备驱动程序

*图 11-10. 用于无 KITL 调试的 eXDI 架构*

### 目标控制

目标控制服务提供了一个命令行接口，用于执行调试、性能分析和日志记录。

目标控制命令允许执行和终止进程、转储内存、获取信息等操作。然而，目标控制服务最有趣的功能是执行调试器扩展 DLL 的命令。Microsoft 提供了这样一个调试器扩展模块 `CeDebugX`，我们在内核调试器部分已经讨论过。不仅如此，您还可以创建自定义的调试命令来满足自身需求。

### 创建调试扩展

Microsoft 能做到的，您也能做到。如果您想定制特定命令，创建自己的调试器扩展相当直接。尽管相关文档在指导您创建在开发工作站上运行的 Win32 DLL 过程时非常有帮助，但还是有几点需要注意。Platform Builder 是一个 32 位程序，它安装在 `<InstallationDisk\Program Files (x86)\Microsoft Platform Builder>` 目录下，因此您创建的 DLL 也必须是 32 位 DLL。您应该配置附加包含目录，使其包含 `< \Program Files (x86)\Microsoft Platform Builder\7.00\cepb\SDK\Include>`，这是 `WDbgExts_CE.h` 文件所在的位置。

您应在链接器命令行中添加 `/FORCE:MULTIPLE`，以避免在链接 DLL 时出现与符号多次定义相关的生成错误。添加一个模块定义文件以导出扩展库函数。清单 11-8 展示了一个示例模块定义文件。当您以调试模式将 Platform Builder 连接到设备并下载操作系统映像后，可以从“调试”菜单中启动“Windows CE 调试器扩展”对话框。这是一个标准的“打开文件”对话框，允许您定位并加载扩展 DLL。加载后，您就可以使用扩展命令。清单 11-7 展示了扩展 DLL 所需的最低代码量。图 11-11 展示了在目标控制中调用 `!demo` 命令。

[www.it-ebooks.info](http://www.it-ebooks.info/)

