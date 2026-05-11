# 命令行：
- **内核模式：** 否
- **随机种子：** 19430
- **线程数：** 0

---

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 12 章：使用 CTK 开发测试代码

**开始测试：** `"Demodrvr perfomace test"`，线程数=0，种子=19430

**异步 IO 挂起，已写入 65536 字节**

**此测试通过。**

**结束测试：** `"Demodrvr perfomace test"`，已通过，耗时=0.139

---

**测试完成**

**测试名称：** `Demodrvr perfomace test`

**测试 ID：** 2

**库路径：** `\release\demodrvrtest.dll`

**命令行：**

**内核模式：** 否

**结果：** 通过

**随机种子：** 19430

**线程数：** 1

**执行时间：** 0:00:00.139

---

`</TESTCASE RESULT="PASSED">`

**结束组：** `Demodrvrtest.DLL`

---

### 内存信息

- **内存总计：** 366,153,728 字节
- **内存已用：** 11,980,800 字节
- **内存空闲：** 354,172,928 字节

- **内核已用：** 491,520 字节
- **水位线：** 86,451 页

- **存储总计：** 133,971,968 字节
- **存储已用：** 1,003,520 字节
- **存储空闲：** 132,968,448 字节

---

### 套件摘要

- **通过：** 2
- **失败：** 0
- **跳过：** 0
- **中止：** 0
- **总计：** 2

---

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章：使用 CTK 开发测试代码

- **累计测试执行时间：** 0:00:00.302
- **Tux 套件总执行时间：** 0:00:00.329
- **CPU 空闲时间：** 0:00:00.003

`</TESTGROUP>`

*图 12-12. 查看测试结果*

### 章节总结

测试是开发中至关重要的一环，如今已成为大多数供应商的必备实践。如果不对设备驱动程序进行测试和验证，就无法获得 ISO 9000 认证。微软要求对 Windows Embedded Compact 7 的设备驱动程序进行测试和认证。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 12 章：使用 CTK 开发测试代码

在 Windows Embedded Compact 7 中，可通过 TUX 工具对设备驱动程序进行测试。虽然并非强制使用 TUX，但它是一个可用的选择，并且能与 Windows Embedded Compact 测试工具包（CTK）服务器良好配合。它提供了丰富的功能和全面的图形用户界面。您可以在 TUX 中开发测试用例，并使用代码骨架 DLL 作为开发起点来创建测试 DLL。CTK 生成的日志文件有助于分析结果，从而提供更可靠、性能更佳的设备驱动程序。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 索引

**数字与符号**

**B**

`!demo` 命令，209

`BAD`（总线错误检测），59

`!module` 命令，202

`BAT` 文件，32

批处理文件，命令行构建，37–38

**A**

板级支持包（BSP），23，47

访问检查，111–112

BSP 驱动程序，47

`ACKNAK`，58–59

BSP 环境变量，33

`ActivateDeviceEx`，72，85，94–95，102，115

BSP_NO 环境变量，33

BSP（板级支持包），23，47

Active 键，16–17，74

存储桶，小型转储和，225

`AdvertiseInterface`，84–85

构建选项节点，200

播发接口，85

构建系统，30–38

`ALD`（仲裁丢失检测），58

命令行构建，32

`AllocPhysMem`，95，122

环境变量，33

仲裁丢失检测（ALD），58

主构建工具，33–34

嵌入式操作系统的架构

概述，30–31

微内核，2–3

准备开发环境，34–38

整体式，3–4

`Build.exe` 工具，34–35

概述，1

命令行构建批处理文件，37–38

Windows CE 和 I/O 处理，5–12

`DIRS` 文件，35

`ASSERT`，195

`SOURCES` 文件，35–36

`ASSERTMSG`，195



## 索引

### A

`SOURCES.cmn` 文件，36–37 页

`SYSGEN` 进程，32–33 页

`Build.exe` 活动，34 页

`Build.exe` 工具，34–35 页

`Bus Error Detected`（总线错误检测），59 页

`Bus Master DMA`（总线主控 DMA），65–66 页

`BusEnum`，16, 72, 102–103, 115, 121 页

### C

`C++` 类，116 页

`CAB` 文件，221 页

`CancelIoEx`，93 页

`Catalog Items View`（目录项视图）窗口，23–24 页

`CE Test Kit` (CETK)，227 页

`CEDDK` 动态链接库，39 页

`CeDebugX` 扩展，202–203 页

`Central Processing Units`（中央处理器），1, 64, 205 页

`CETK`（CE 测试工具包），227 页

`CloseHandle`，94, 101, 110 页

命令行构建，32, 37–38 页

`COMMON` 子目录，28 页

`Compact` 文件，6 页

`Compact Test Kit`（紧凑型测试工具包）。*参见* CTK

`Compile`（编译）阶段，31 页

`CompleteAsyncIo`，106, 109 页

`Connection Output`（连接输出）窗口，228 页

`Connection View`（连接视图）选项卡，228 页

控制寄存器，59–60 页

`CreateAsyncIoHandle`，102, 106–107 页

`CreateFile` 函数，71–72, 91, 94, 99, 109 页

`CreateInstance`，136 页

`CTK`（紧凑型测试工具包），52–53, 227–253 页
- 概览，227–228 页
- 测试
  - 设计，234–235 页
  - 通过，230–233 页
  - 性能，243–248 页
  - TUX 测试框架，235–239 页
  - 查看和分析结果，240–241 页
- 用户界面，228–229 页

`CurrentMacAddress`，168, 184 页

### D

数据链路层，网络设备驱动程序

数据寄存器，60 页

物理数据传输，182 页

`DBGPARAM` 结构体，196, 198 页

`DDI`（设备驱动程序接口），81, 83 页

`DDK`（设备驱动程序开发工具包）工具，21, 38–39 页

`DDSI`（设备驱动程序服务提供程序接口），83 页

调试区域，194–199 页
- 更改，197–199 页
- 宏，195 页
- 注册，195–197 页

`DEBUGCHK`，195 页

`Debugger Extensions`（调试器扩展）对话框，209 页

调试，191–225 页
- 调试消息，调试区域，193–199 页
- 硬件辅助，203–208 页
  - eXDI，208 页
  - TRACE 32 工具，204–207 页
- 内核调试器，200–203 页
- 概览，191–192 页
- 事后调试与 Dr. Watson 调试器，220–225 页
  - 错误报告控制面板，223–224 页
  - 错误报告生成器组件，221–223 页
  - 错误报告传输驱动程序，223 页
  - 小内存转储与存储桶，225 页
  - 报告上传客户端，224 页
- 远程工具，211–219 页
  - 桌面插件实现，214–219 页
  - 设备端实现，212 页
- 目标控制服务，209 页
- 技术，192 页

`DEBUGLED`，195 页

`DEBUGMSG`，193–195, 197 页

`DEBUGREGISTER` 宏，195, 197 页

`DEBUGZONE`，195–196 页

`DemoBSP`，46 页

`DEMODRIVER.REG`，77 页

`DemoDrvr` 驱动程序，46, 103, 140, 200 页

`DemoDrvrAsynIOTest`，231–232, 240 页

`Demodrvr_SI.cpp` 文件，202 页

`_DEPTREES` 环境变量，33 页

远程工具的桌面插件实现，214–219 页

`DEV_BROADCAST_DEVICEINTERFACE`

开发环境，为构建系统做准备，34–38 页
- `Build.exe` 工具，34–35 页
- 命令行构建批处理文件，37–38 页
- `DIRS` 文件，35 页
- `SOURCES` 文件，35–36 页
- `SOURCES.cmn` 文件，36–37 页

`DEVFLAGS_LOAD_AS_USERPROC`，111 页

`DEVFLAGS_NAKEDENTRIES` 标志，75, 93 页

设备专用内存，64 页

`Device Driver Development Kit`（设备驱动程序开发工具包，DDK）工具，21, 38–39 页

`Device Driver Interface`（设备驱动程序接口，DDI），81, 83 页

`Device Driver Service Provider Interface`（设备驱动程序服务提供程序接口，DDSI），83 页

`Device Driver Wizard`（设备驱动程序向导），最佳实践，43–44 页

设备驱动程序，1–19, 45–53 页
- 模式决策，48–49 页
  - 内核模式，48 页
  - 用户模式，48–49 页
- 为测试而设计，52–53 页
- 特性，49–50 页
- 筛选器，114–118 页
- 混合，82 页
- IOCTL，50 页
- 内核模式，111–113 页
  - 访问检查，111–112 页
  - 封送处理，113 页
- 结构，92–111 页
- 分层，14 页
  - 单体设备驱动程序与分层设备驱动程序，14 页
  - 对比单体设备驱动程序，83 页
- 类型，49, 81–89 页
  - 设备接口类，84–85 页
  - 混合设备驱动程序，82 页
  - 接口通知，85–89 页
  - 流接口设备驱动程序，82 页
  - 单体与分层设备驱动程序，83 页
  - 本机设备驱动程序，81–82 页
  - 流设备驱动程序，82 页
- 用户模式，119–125 页
  - 实现，120 页
- 在 Windows Embedded Compact 7 中，13–14 页
- 加载与卸载，16–19 页
  - I/O 资源管理器，18–19 页
  - 流接口设备驱动程序，16–19 页
- 位置，46–48 页
  - BSP，47 页
  - PUBLIC 目录树，48 页
  - 特定操作系统设计，47–48 页
- 单体，49, 69–80 页
- 注册表设置，49, 69–80 页
  - 设备驱动程序文件名，71–72 页
  - 条目，74–80 页
  - 键，74–76 页
  - 加载顺序，72–73 页
  - 概览，69–71 页
- 流接口，72–73, 82 页
  - 筛选器，114–118 页
  - 内核模式，111–113 页
  - 用户模式，119–125 页

`Device Manager`（设备管理器），16–17 页

文件名，17–18 页

`CreateInstance`，136 页

`CurrentMacAddress`，168, 184 页

`CloseHandle`，94, 101, 110 页

`CompleteAsyncIo`，106, 109 页

`CreateAsyncIoHandle`，102, 106–107 页

`CreateFile` 函数，71–72, 91, 94, 99, 109 页

`CancelIoEx`，93 页

`CAB` 文件，221 页

`C++` 类，116 页

`CE Test Kit`（CE 测试工具包，CETK），227 页

`CEDDK` 动态链接库，39 页

`CeDebugX` 扩展，202–203 页

`Central Processing Units`（中央处理器，CPUs），1, 64, 205 页

`CETK`（CE 测试工具包），227 页

`Compact Test Kit`（紧凑型测试工具包）。*参见* CTK

`CTK`（紧凑型测试工具包），52–53, 227–253 页
- 概览，227–228 页
- 测试设计，234–235 页
- 测试通过，230–233 页
- 性能测试，243–248 页
- TUX 测试框架，235–239 页
- 查看和分析结果，240–241 页
- 用户界面，228–229 页

`Debugger Extensions`（调试器扩展）对话框，209 页

`DEMODRIVER.REG`，77 页

`DemoDrvr` 驱动程序，46, 103, 140, 200 页

`DemoDrvrAsynIOTest`，231–232, 240 页

`Demodrvr_SI.cpp` 文件，202 页

`_DEPTREES` 环境变量，33 页

`DEV_BROADCAST_DEVICEINTERFACE`

`Device Driver Development Kit`（设备驱动程序开发工具包，DDK）工具，21, 38–39 页

`Device Driver Interface`（设备驱动程序接口，DDI），81, 83 页

`Device Driver Service Provider Interface`（设备驱动程序服务提供程序接口，DDSI），83 页

`Device Driver Wizard`（设备驱动程序向导），最佳实践，43–44 页

`Device Manager`（设备管理器），16–17 页

`DEVFLAGS_LOAD_AS_USERPROC`，111 页

`DEVFLAGS_NAKEDENTRIES` 标志，75, 93 页

`DIRS` 文件，35 页

`嵌入式操作系统架构`
- 微内核，2–3 页
- 单体，3–4 页
- 概览，1 页
- Windows CE 与 I/O 处理，5–12 页

`错误报告控制面板`，223–224 页

`错误报告生成器` 组件，221–223 页

`错误报告传输驱动程序`，223 页

`eXDI`，208 页

`筛选器` 设备驱动程序，114–118 页

`混合` 设备驱动程序，82 页

`I/O 资源管理器`，18–19 页

`IOCTL`，50 页

`内核调试器`，200–203 页

`内核模式` 设备驱动程序，111–113 页
- 访问检查，111–112 页
- 封送处理，113 页

`分层` 设备驱动程序，14 页
- 对比单体设备驱动程序，83 页

`加载与卸载` 设备驱动程序，16–19 页

`微内核` 架构，2–3 页

`小内存转储与存储桶`，225 页

`单体` 架构，3–4 页

`单体` 设备驱动程序
- 对比分层设备驱动程序，83 页

`本机设备驱动程序`，81–82 页

`事后调试`，220–225 页

`PUBLIC` 目录树，48 页

`设备驱动程序的注册表设置`，49, 69–80 页
- 设备驱动程序文件名，71–72 页
- 条目，74–80 页
- 键，74–76 页
- 加载顺序，72–73 页
- 概览，69–71 页

`远程工具`，211–219 页
- 桌面插件实现，214–219 页
- 设备端实现，212 页

`SYSGEN` 进程，32–33 页

`SOURCES.cmn` 文件，36–37 页

`SOURCES` 文件，35–36 页

`流接口` 设备驱动程序，16–19, 72–73, 82 页

`目标控制服务`，209 页

`TRACE 32` 工具，204–207 页

`TUX 测试框架`，235–239 页

`用户模式` 设备驱动程序，48–49, 119–125 页
- 实现，120 页

`Windows CE` 与 I/O 处理，5–12 页

`Windows Embedded Compact 7` 中的设备驱动程序，13–14 页

`www.it-ebooks.info`



#### 加载与初始化，第 121–125 页

- 与分层设备驱动程序，14
- 限制条件，119
- 对比分层设备驱动程序，83
- Windows Embedded Compact 7，12–
- 原生，12，81–82
- 网络，161–163
- 在内核态或用户态，15
- PDD，51
- [www.it-ebooks.info](http://www.it-ebooks.info/)

## 索引

- 单片式与分层设备驱动程序，14
- `DMA`（直接内存访问），39, 50, 55, 64–66, 68
- 原生设备驱动程序，12
- `DMA`方案，分散聚合，183
- 其中的流接口设备驱动程序，13–14
- `DMAC`（DMA 控制器），65–66
- Dr. Watson 调试器，事后调试与，220–225
- `设备文件`，80
- `设备接口类`，`GUID`，84–85
- 通告接口，85
- `IClass`注册表子项，84–85
- `错误报告控制面板`，223–224
- `错误报告生成器`组件，221–223
- `错误报告传输驱动程序`，223
- `设备管理器`，16–17, 48–49, 71–72, 74–75, 77, 80
- 小型转储与桶，225
- 远程工具的设备端实现，212
- `DriverEntry`函数，163
- 设备特定`IOCTLs`，处理，152–
- `DriverFilterBase`类，116
- `DeviceIoControl`函数，50, 71, 82, 98, 106, 110, 124
- 中间驱动程序，186–187
- `DevMgr.dll`，4
- 内核态驱动程序与用户态驱动程序的对比，4
- `DEVMGR.DLL`，6, 15–16
- 微端口驱动程序，163–179
- `直接内存访问`（`DMA`），39, 50, 55, 64–66, 68
- `DriverEntry`，163
- 目录树，Platform Builder 工具，26–
- `MiniportCheckForHangEx`，177
- `MiniportDevicePnPEventNotify`，170
- `DIRS`文件，34–36
- `DisableThreadLibraryCalls`，197
- `Dll`子项，76
- `MiniportDriverUnload`，172
- `DllEntry`函数，197
- `MiniportHaltEx`，171
- `DLL`（动态链接库），3–4, 17
- `MiniportInitializeEx`，165
- `DLL_THREAD_ATTACH`，197
- `MiniportOidRequest`，174
- `DLL_THREAD_DETACH`，197
- `MiniportPause`，172
- `DMA`控制器（`DMAC`），65–66
- `MiniportResetEx`，178–179
- `MiniportRestart`，173
- [www.it-ebooks.info](http://www.it-ebooks.info/)

## 索引

- `NDIS`内存管理辅助函数，182–183
- 单片式，3–4
- 概述，1
- 物理数据传输，182
- `Windows CE`与 I/O 处理，5–12
- 协议，184–186
- `启用内核调试器`，200
- `ProtocolBindAdapterEx`函数，184
- 流接口设备驱动程序的入口点，93–102
- `ProtocolCloseAdapterCompleteEx`函数，185
- `XXX_Cancel`函数，93–94
- `ProtocolNetPnPEvent`函数，
- `XXX_Close`函数，94
- `ProtocolOpenAdapterCompleteEx`函数，185
- `XXX_Deinit`函数，94–95
- `ProtocolReceiveNetBufferLists`函数，185–186
- `XXX_Init`函数，95–98
- `ProtocolSendNetBufferListsComplete`函数，186
- `XXX_IOControl`函数，98–99
- `ProtocolSetOptions`函数，184
- `XXX_Open`函数，99–100
- `ProtocolUnbindAdapterEx`函数，184
- `XXX_PowerDown`函数，100
- `ProtocolUninstall`函数，185
- `XXX_PowerUp`函数，100
- 用户态框架注册表设置，77
- `XXX_PreClose`函数，100
- 用户态与内核态驱动程序对比，4
- `XXX_PreDeinit`函数，100–101
- `DumpType`键，222
- `XXX_Read`函数，101
- `DWORD`值，75
- `XXX_Write`函数，101–102
- `ERRORMSG`，195
- 环境变量，`_DEPTREES`，33
- `错误报告控制面板`，223–224
- `错误报告生成器`组件，221–
- `错误报告传输驱动程序`，222–223
- `异常类型`，221
- `以太网驱动程序`，75
- 动态链接库（`DLL`），3–4, 17

### E

- `EarlyTxThreshold`，75–76
- `eXDI`（扩展调试接口），208
- 嵌入式操作系统，架构：微内核，2–3
- `扩展调试接口`（`eXDI`），208

### F

- `fAdd`参数，85
- [www.it-ebooks.info](http://www.it-ebooks.info/)

## 索引

- 文件名，设备驱动程序挂载点，71–72
- 文件名，前缀与索引，71
- 文件索引，18
- 文件名，概述，17
- 文件前缀，18
- `文件系统驱动程序`（`FSDs`），49
- `FILESYS.DLL`，6
- 过滤器设备驱动程序，114–118
- 过滤，`SYSGEN`过程，33
- `FirFilter`类，117
- `FIRFilter::FilterInit`入口点，118
- `Flags`子项，76
- `MiniportHaltEx`函数，171
- `MiniportInitializeEx`函数，165
- `MiniportOidRequest`函数，174
- `MiniportPause`函数，172
- `MiniportResetEx`函数，178–179
- `MiniportRestart`函数，173
- `MiniportSetOptions`函数，164
- `MiniportShutdownEx`函数，177
- `NdisMAllocateNetBufferSGList`函数，183
- `NdisMAllocateSharedMemoryAsyncEx`函数，183
- `NdisMDeregisterScatterGatherDma`函数，183
- `NdisMFreeNetBufferSGList`函数，183
- 框架注册表设置，用户态驱动程序，77



## 索引

## 函数

- `NdisMRegisterScatterGatherDma` 函数，第 183 页
- `ProtocolBindAdapterEx` 函数，第 184 页
- `ProtocolCloseAdapterCompleteEx` 函数，第 185 页
- `ProtocolNetPnPEvent` 函数，第 185 页
- `ProtocolOpenAdapterCompleteEx` 函数，第 185 页
- `ProtocolReceiveNetBufferLists` 函数，第 185–186 页
- `ProtocolSendNetBufferListsComplete` 函数，第 186 页
- `ProtocolSetOptions` 函数，第 184 页
- `ProtocolUnbindAdapterEx` 函数，第 184 页
- `ProtocolUninstall` 函数，第 185 页
- `CreateFile` 函数，第 71–72、91、94、99、109、124 页
- `DeviceIoControl` 函数，第 50、71、82、98、106、110、124 页
- `DllEntry` 函数，第 197 页
- `DriverEntry` 函数，第 163 页
- `MiniportCheckForHangEx` 函数，第 175 页
- `MiniportDevicePnPEventNotify` 函数，第 175 页
- `MiniportDriverUnload` 函数，第 172 页
- `XXX_Cancel` 函数，第 93–94 页
- `XXX_Close` 函数，第 94 页
- `XXX_Deinit` 函数，第 94–95 页
- `XXX_Init` 函数，第 95–98 页
- `XXX_IOControl` 函数，第 98–99 页
- `XXX_Open` 函数，第 99–100 页
- `XXX_PowerDown` 函数，第 100 页
- `XXX_PowerUp` 函数，第 100 页
- `XXX_PreClose` 函数，第 100 页
- `XXX_PreDeinit` 函数，第 100–101 页
- `XXX_Read` 函数，第 101 页
- `XXX_Write` 函数，第 101–102 页
- `FSDMGR.DLL`，第 6 页
- `HalTranslateBusAddress` 函数，第 142–143 页
- 函数表，第 237、246–247 页

### F

- FSD（文件系统驱动程序），第 49 页

### G

- GCAD（通用呼叫地址检测），第 59 页
- 千兆以太网接口，第 40 页
- 全局唯一标识符 (GUID)，第 6、14、16、84–85 页
- 图形窗口和事件系统 (GWES)，第 12、49、81、88 页
- GUID（全局唯一标识符），第 6、14、16、84–85 页
- GWES（图形窗口和事件系统），第 12、49、81、88 页
- `GWES.dll`，第 4、6、15 页

### H

- 硬件，第 55–67 页
    - 辅助调试，第 203–208 页
        - eXDI，第 208 页
        - TRACE 32 工具，第 204–207 页
    - DMA，第 65–66 页
        - 总线主控，第 65–66 页
        - 系统，第 65 页
- `hAsyncRef` 句柄，第 106 页
- 堆遍历器显示，第 25 页
- 辅助函数，内存管理，第 182–183 页
- 基于配置单元的注册表，第 70–71 页
- `HKEY_LOCAL_MACHINE` 项，第 69、72、75、77–79 页
- `HKLMrivers` 注册表项，第 16 页
- 混合设备驱动程序，第 82 页

### I

- I/O 设备
    - 中断，第 63–64 页
    - 优先级，第 63 页
    - 信号机制，第 63–64 页
    - 向量，第 63 页
    - 内存，第 64 页
    - 寄存器，第 56–62 页
        - 访问，第 60–62 页
        - 控制，第 59–60 页
        - 数据，第 60 页
        - 状态，第 57–59 页
    - 介绍，第 55–56 页
    - PCI 总线，第 66–67 页
- I/O 处理
    - 异步，第 103–111 页
    - 以及 Windows CE，第 5–12 页
        - 设备驱动程序相关系统组件，第 5–6 页
        - 中断处理，第 10–11 页
        - 中断，第 63–64、127–144 页
        - ISR，第 11–12 页
        - Windows Embedded Compact 7 内存架构，第 6–10 页
    - I/O 映射，第 139–144 页
        - 内存映射，第 142–144 页
        - 端口映射，第 140–142 页
    - 模型，第 127–128 页
    - 优先级，第 63 页
    - 处理，第 10–11、128–138 页
        - ISR，第 129–137 页
            - 可安装的，第 12、134–137 页
            - 嵌套的，第 137 页
            - OAL 与中断处理，第 130 页
        - IST，第 137–138 页
    - 信号机制，第 63–64 页
    - 向量，第 63 页
- I/O 端口寄存器，第 60 页
- I/O 资源管理器，第 18–19 页
- `IClass` 注册表子项，第 84–85 页
- IDE（集成开发环境），Platform Builder。*参见* Platform Builder 工具
- 索引
    - 文件名，第 18 页
    - 前缀及设备驱动程序文件名，第 71 页
- 信息科学与技术 (IST)，第 11 页
- 输入输出控制。*参见* IOCTL
- 可安装 ISR，第 134–137 页
- 集成开发环境 (IDE)，Platform Builder。*参见* Platform Builder 工具
- 接口通知，第 85–89 页
    - 消息队列点对点，第 85–87 页
    - 通过 `WM_DEVICECHANGE` 消息，第 87–89 页
- 接口，通告，第 85 页
- 中间驱动程序，第 186–187 页
- 中断服务例程。*参见* ISR
- 中断服务线程 (IST)，第 137–138 页
- 中断共享，可安装 ISR 及，第 12 页
- 中断支持，第 50 页
- 中断，第 63–64、127–144 页
- IOCTL（输入输出控制），第 50、145–
    - 描述，第 145–149 页
    - 设备特定，第 152–155 页
- ISR（中断服务例程），第 129–137 页
    - 可安装的，第 12、134–137 页
    - 与 IST，第 11 页
    - 嵌套的，第 137 页
    - OAL 与中断处理，第 130 页
- IST（信息科学与技术），第 11 页
- IST（中断服务线程），第 137–138 页

### K

- 内核调试器，第 200–203 页
- 内核 IOCTL，第 146–149 页
- 内核模式，第 48 页
    - 设备驱动程序，第 111–113 页
        - 访问检查，第 111–112 页
        - 封送处理，第 113 页
    - 与用户模式驱动程序对比，第 4 页
    - Windows Embedded Compact 7 中的，第 15 页
- 内核空间，第 7–8 页

### M

- 内存，I/O 设备，第 64 页
- 内存管理辅助函数，NDIS
    - 分散聚合 DMA 方案，第 183 页
    - 共享内存方案，第 182–183 页
- 内存映射 I/O，第 142–144 页
- 内存映射寄存器，第 60 页

### U

- 用户空间，第 9–10 页

[www.it-ebooks.info](http://www.it-ebooks.info/)



## 索引

### L

- 微内核架构, 2–3
- 分层设备驱动程序
  - 和时间关键型系统, 3
  - 单片设备驱动程序与, 在
  - 和 Windows CE, 2–3
- Windows Embedded Compact 7,
- 小型转储和桶, 225
- 微型端口驱动程序, 163–183
  - 功能, 163–179
- 库
  - `DriverEntry`, 163
  - `CEDDK` 动态链接, 39
  - `MiniportCheckForHangEx`, 177
  - 注册表助手, 39
  - `MiniportDevicePnPEventNotify`,
- 加载顺序, 流设备驱动程序, 72–
  - `MiniportDriverUnload`, 172

### M

- 宏, 用于调试区域, 195
  - `MiniportHaltEx`, 171
- 映射, I/O, 139–144
  - `MiniportInitializeEx`, 165
- 内存映射, 142–144
  - `MiniportOidRequest`, 174
- 端口映射, 140–142
  - `MiniportPause`, 172
- 封送处理, 指针, 113
  - `MiniportResetEx`, 178–179
- 主构建工具, 33–34
  - `MiniportRestart`, 173
- 内存架构
  - `MiniportSetOptions`, 164
  - Windows Embedded Compact 7, 6–
    - `MiniportShutdownEx`, 177
  - 内核空间, 7–8
    - NDIS 内存管理助手, 182–183
  - 用户空间, 8–10
    - 分散聚合 DMA 方案, 183
  - 虚拟内存, 10–11
  - 共享内存方案, 182–183
    - 功能, 163–179
  - 物理数据传输, 182

- NDIS 内存管理助手, 182–183
  - `MiniportCheckForHangEx` 函数, 177
  - 物理数据传输, 182
  - `MiniportDevicePnPEventNotify` 函数,
- 网络设备驱动程序层, 161–163
  - `MiniportDriverUnload` 函数, 172
- 概述, 159–161
  - `MiniportHaltEx` 函数, 171
- 协议驱动程序, 184–186
  - `MiniportInitializeEx` 函数, 165
  - `ProtocolBindAdapterEx` 函数,
  - `MiniportOidRequest` 函数, 174
  - `ProtocolCloseAdapterCompleteEx` 函数, 185
  - `MiniportPause` 函数, 172
  - `ProtocolNetPnPEvent` 函数,
  - `MiniportResetEx` 函数, 178–179
  - `ProtocolOpenAdapterCompleteEx` 函数, 185
  - `MiniportRestart` 函数, 173
  - `ProtocolReceiveNetBufferLists` 函数, 185–186
  - `MiniportSetOptions` 函数, 164
  - `ProtocolSendNetBufferListsComplete` 函数, 186
  - `MiniportShutdownEx` 函数, 177
  - `ProtocolSetOptions` 函数, 184
- 模型, 用于中断, 127–128
  - `ProtocolUnbindAdapterEx` 函数, 184
- 模式, 决定设备驱动程序, 48–49
  - `ProtocolUninstall` 函数, 185
  - 内核模式, 48
- 注册表设置, 187–189
  - 用户模式, 48–49
- 单片架构, 3–4
  - `NdisMAllocateNetBufferSGList` 函数,
- 单片设备驱动程序
  - `NdisMAllocateSharedMemoryAsyncEx` 函数, 183
  - 与分层设备驱动程序, 83
  - `NdisMDeregisterScatterGatherDma` 函数, 183
  - 和分层设备驱动程序, 在 Windows Embedded Compact 7,
- 挂载点, 71–72
  - `NdisMFreeNetBufferSGList` 函数, 183
  - `NdisMRegisterScatterGatherDma` 函数, 183

### N

- 原生设备驱动程序, 12, 81–82
- NDIS (网络驱动程序接口规范), 159–189
  - 中间驱动程序, 186–187
  - 微型端口驱动程序, 163–183
  - 功能, 163–179
- 嵌套 ISR (中断服务例程),
- 平台构建器向导, 28–29
  - 和 Visual Studio 2008 工具, 22–25
- `PLATFORM` 目录, 28
- 插件实现, 桌面, 214–
- 点到点通知, 消息队列, 85–87
- 网络层, 网络设备驱动程序,
- 指针, 113
- 通知, 点到点, 消息队列, 85–87
- 端口映射 I/O, 140–142

### O

- OAL (OEM 适配层), 130
  - 错误报告控制面板, 223–224
- 对象存储, 70
  - 错误报告生成器组件, 221–223
  - 错误报告传输驱动程序, 223
  - 小型转储和桶, 225
  - 报告上传客户端, 224

### P

- PCI (外围组件互连) 总线, 66–67
- PDD (物理设备驱动程序), 51
  - 第二测试过程, 243–248
- 前缀
  - 文件名, 18
  - 和设备驱动程序文件名的索引, 71
- 优先级, 中断, 63
- PIO (编程输入/输出), 64
- 物理层, 网络设备驱动程序,
  - `ProtocolBindAdapterEx` 函数, 184
  - `ProtocolCloseAdapterCompleteEx` 函数, 185
  - `ProtocolNetPnPEvent` 函数,
  - `ProtocolOpenAdapterCompleteEx` 函数, 185
  - `ProtocolReceiveNetBufferLists` 函数, 185–186
  - `ProtocolSendNetBufferListsComplete` 函数, 186
  - `ProtocolSetOptions` 函数, 184
  - `ProtocolUnbindAdapterEx` 函数, 184
  - `ProtocolUninstall` 函数, 185
- 电源管理支持, 50, 155
- 协议驱动程序, 184–186



#### 平台构建器与开发工具

##### 平台构建器工具

`Platform Builder`工具（第 26–29 页）提供了一个`directory tree`（第 26–28 页）和一个`IDE`（第 28–29 页）。它包含一个`registry editor`（第 29 页）和`remote tools`（第 29 页）。

#### NDIS 协议函数

- `ProtocolNetPnPEvent`函数（第 185 页）
- `ProtocolOpenAdapterCompleteEx`函数（第 185 页）
- `ProtocolReceiveNetBufferLists`函数（第 185–186 页）
- `ProtocolSendNetBufferListsComplete`函数（第 186 页）
- `ProtocolSetOptions`函数（第 184 页）
- `ProtocolUnbindAdapterEx`函数（第 184 页）
- `ProtocolUninstall`函数（第 185 页）
- `ProtocolBindAdapterEx`函数（第 184 页）
- `ProtocolCloseAdapterCompleteEx`函数（第 185 页）

#### 注册表

##### 设备驱动程序注册表

- `device driver`（第 49 页）
- `entries`（第 74–77 页）
- `user mode framework registry`（第 77 页）

##### 编辑器

`Platform Builder tool`编辑器（第 29 页）

##### IClass 子项

`IClass`子项（第 84–85 页）

##### 注册表设置

- `entries`（第 77–80 页）
- `registry helper library`（第 39 页）
- `files`（第 77–80 页）
- `NDIS`（第 187–189 页）
- `registry types`（第 69–70 页）

#### 远程工具

- `remote tools`（第 25–26 页）
- 用于`debugging`（第 211–219 页）
- `desktop plug-in implementation`（第 214–219 页）
- `device side implementation`（第 212 页）
- `framework for`（第 26 页）
- `Platform Builder tool`（第 29 页）

##### 注册调试区域

`registering debug zones`（第 195–197 页）

##### 报告与上传客户端

`reporting upload clients`（第 224 页）

#### 寄存器

##### I/O 设备寄存器

- `accessing registers`（第 60–62 页）
- `control registers`（第 59–60 页）
- `data registers`（第 60 页）
- `status registers`（第 57–59 页）

### S

#### 分散/聚集 DMA

`scatter gather DMA scheme`（第 183 页）

#### 共享内存方案

`shared memory scheme`（第 182–183 页）

#### 信号机制

`signaling mechanisms interrupt`（第 63–64 页）

#### SOURCES 文件

`SOURCES file`（第 35–37 页）

### T

#### 目标控制服务

`target control service debug extensions`

#### 测试通过模板

`templates for test pass`（第 230–233 页）

#### 测试用例

`test cases`（第 230–231 页）

#### 测试

`testing designing device drivers for`（第 52–53 页）

#### 测试项

- `designing`（第 234–235 页）
- `passes`（第 230–233 页）
- `template for`（第 230–233 页）
- `test case`（第 230–231 页）
- `performance second test procedure`（第 243–248 页）

#### TUX 测试工具

`TUX test harness`（第 235–239 页）
- `implementing DLL for`（第 235 页）
- `running test`（第 238–239 页）
- `viewing and analyzing results of`（第 240–243 页）

#### 流设备驱动程序

`stream device drivers`（第 72–73、82 页）

#### 流接口设备驱动程序

`Device Manager`
- `architecture of`（第 16–17 页）
- `registry keys for`（第 17 页）
- `file names`（第 17–18 页）
- `filter`（第 114–118 页）
- `kernel mode`（第 111–113 页）
  - `access checking`（第 111–112 页）
  - `marshalling`（第 113 页）
- `structure of`（第 92–111 页）
  - `asynchronous I/O request handling`（第 103–111 页）
  - `loading stream interface device drivers`（第 102–103 页）
  - `stream interface entry points`（第 93–102 页）
- `user mode`（第 119–125 页）
  - `implementing`（第 120 页）
  - `loading and initializing`（第 121–125 页）
  - `restrictions on`（第 119 页）
- `in Windows Embedded Compact 7`（第 13–14 页）

#### 子项

- `device driver registry`（第 75–76 页）
- `IClass registry`（第 84–85 页）

#### 系统 DMA

`System DMA`（第 65 页）

#### SYSGEN 过程

`SYSGEN process`（第 32–33 页）

### U

#### 上传客户端

`upload clients reporting`（第 224 页）

#### 用户界面

`user interfaces for CTK`（第 228–229 页）

#### 用户模式

- `device drivers`（第 119–125 页）
- `framework registry settings`（第 77 页）
- `implementing`（第 120 页）
- `versus kernel-mode drivers`（第 4 页）
- `loading and initializing`（第 121–125 页）
- `restrictions on`（第 119 页）
- `overview`（第 48–49 页）
- `framework for`（第 26 页）
- `Platform Builder tool`（第 29 页）

#### 用户空间

`user space`（第 9–10 页）

### V

#### 向量

`vectors interrupt`（第 63 页）

#### Visual Studio 2008 工具

- `overview`（第 21 页）
- `and Platform Builder IDE`（第 22–25 页）
- `remote tools`（第 25–26 页）

##### Visual Studio 2008

- `overview`（第 21 页）
- `and Platform Builder IDE`（第 22–25 页）
- `remote tools`（第 25–26 页）
- `automating debugging procedure`（第 204–207 页）

##### TRACE32 工具

`TRACE32 tool`（第 39–42 页）
- `overview`（第 39–41 页）
- `preparing for`（第 41–42 页）

### W

#### WINCEROOT 变量

`WINCEROOT variable`（第 26–28、33、36–38 页）

#### Windows CE

- `and I/O handling`（第 5–12 页）
- `device driver-related system components`（第 5–6 页）
- `interrupt processing`（第 10–11 页）
- `ISRs`（第 11–12 页）
- `memory architecture`（第 6–10 页）
- `microkernel architecture and`（第 2–3 页）
- `monolithic architecture and`（第 4 页）

#### Windows Embedded Compact 7

- `in kernel or user mode`（第 15 页）
- `memory architecture`（第 6–10 页）
- `monolithic and layered device drivers`
- `native device drivers`（第 12 页）
- `stream interface device drivers in`（第 13–14 页）

#### 向导

`Platform Builder tool`向导（第 28–29 页）
- `OS Design wizard`（第 28 页）
- `Subproject wizard`（第 29 页）

### WM_DEVICECHANGE 消息

`WM_DEVICECHANGE message interface notifications via`（第 87–89 页）

### X, Y, Z

#### XXX_Cancel 函数

`XXX_Cancel function`（第 93–94 页）

#### XXX_Close 函数

`XXX_Close function`（第 94 页）

#### XXX_Deinit 函数

`XXX_Deinit function`（第 94–95 页）

#### XXX_Init 函数

`XXX_Init function`（第 95–98 页）

#### XXX_IOControl 函数

`XXX_IOControl function`（第 98–99 页）

#### XXX_Open 函数

`XXX_Open function`（第 99–100 页）

#### XXX_PowerDown 函数

`XXX_PowerDown function`（第 100 页）

#### XXX_PowerUp 函数

`XXX_PowerUp function`（第 100 页）

#### XXX_PreClose 函数

`XXX_PreClose function`（第 100 页）

#### XXX_PreDeinit 函数

`XXX_PreDeinit function`（第 100–101 页）

#### XXX_Read 函数

`XXX_Read function`（第 101 页）

#### XXX_Write 函数

`XXX_Write function`（第 101–102 页）

### R

#### 基于 RAM 的注册表

`RAM-Based Registry`（第 70 页）

### 其他

#### 事务

`transactions physical data`（第 182 页）

#### 线程化 Linux 测试工具

`Threaded Linux test harness`（参见 `TUX`）

#### 时间关键系统

`time-critical systems microkernel architecture and`（第 3 页）

#### 流接口入口点

`stream interface entry points`（第 93–102 页）

### 分散/聚集 DMA 方案

`scatter gather DMA scheme`（第 183 页）

#### 共享内存方案

`shared memory scheme`（第 182–183 页）

#### SOURCES 文件

`SOURCES file`（第 35–37 页）

#### 状态寄存器

`status registers`（第 57–59 页）

#### 信号机制

`signaling mechanisms interrupt`（第 63–64 页）

##### 注册表类型

`registry types`（第 69–70 页）

##### 注册表辅助库

`registry helper library`（第 39 页）

##### 注册表设置

`registry settings`（第 77–80 页）

##### 注册表键

- `for Device Manager`（第 17 页）
- `Device Manager`（第 74 页）

#### PREfast 工具

`PREfast tool`

#### 准备开发环境

`preparing development environment`（第 34–38 页）

#### 公共目录

`PUBLIC directory`（第 28 页）

#### 公共树

`PUBLIC tree`（第 48 页）

### D

#### DDK

`DDK`（第 38–39 页）

#### 设备驱动程序向导

- `best practice`（第 43–44 页）
- `overview`（第 43 页）

#### 设备管理器

`Device Manager`

#### 目录树

`directory tree`（第 26–28 页）

### I

#### IDE

`IDE`

##### IClass 子项

`IClass subkeys`（第 84–85 页）

#### 中断向量

`interrupt vectors`（第 63 页）

##### I/O 设备寄存器

`I/O device registers`（第 56–62 页）

### B

#### 构建系统

`build system`（第 30–38 页）
- `command line build`（第 32 页）
- `environment variables`（第 33 页）
- `Master Build Tool`（第 33–34 页）
- `overview`（第 30–31 页）
- `SYSGEN process`（第 32–33 页）

### A

#### 异步 I/O 请求处理

`asynchronous I/O request handling`（第 103–111 页）

#### 访问检查

`access checking`（第 111–112 页）

### C

#### 命令行构建

`command line build`（第 32 页）

#### 控制寄存器

`control registers`（第 59–60 页）



### 目录

- `GetFullPageImage`
- `前页内容`
  - `扉页`
  - `版权页`
  - `内容概览`
  - `目录`
  - `关于作者`
  - `关于技术审稿人`
  - `致谢`
  - `引言`
- `正文`
  - `第 1 章：Windows Embedded Compact 设备驱动程序开发基础`
    - `本章内容：`
    - `嵌入式操作系统架构`
      - `微内核架构`
        - `微内核架构与 Windows CE`
        - `微内核架构与时间关键型系统`
      - `宏内核架构`
        - `宏内核架构与 Windows CE`
        - `宏内核架构中的用户模式与内核模式驱动程序`
    - `Windows CE 系统架构与 I/O 处理`
      - `设备驱动程序相关系统组件`
      - `Windows Embedded Compact 7 内存架构`
        - `内核空间虚拟内存`
        - `用户空间虚拟内存`
      - `输入/输出处理`
        - `中断处理`
        - `ISR 与 IST`
        - `可安装 ISR 与中断共享`
    - `Windows Embedded Compact 设备驱动程序模型`
      - `原生 Windows CE 设备驱动程序`
      - `Windows Embedded Compact 中的流接口设备驱动程序`
      - `宏内核与分层设备驱动程序`
    - `Windows Embedded Compact 内核模式或用户模式设备驱动程序`
      - `内核模式设备驱动程序`
      - `用户模式设备驱动程序`
    - `加载与卸载设备驱动程序`
      - `加载流接口设备驱动程序`
        - `设备管理器架构`
        - `设备管理器注册表项`
        - `设备文件名`
        - `I/O 资源管理器`
    - `本章小结`
- `正文(1)`
  - `第 2 章：工具概述`
    - `本章内容：`
    - `Visual Studio 2008`
      - `Visual Studio 2008 与 Platform Builder IDE`
      - `远程工具`
        - `远程工具框架`
    - `Platform Builder`
      - `Platform Builder 目录树`
        - `PLATFORM 目录`
        - `PUBLIC 目录`
      - `Platform Builder IDE`
      - `Platform Builder 向导`
        - `Platform Builder 远程工具`
        - `Platform Builder 注册表编辑器`
    - `构建系统`
      - `概述`
      - `构建工具`
        - `命令行构建`
        - `理解 SYSGEN 过程`
        - `环境变量与 DEPTREES`
        - `主构建工具`
      - `如何准备开发环境`
        - `Build.exe`
        - `DIRS 文件`
        - `SOURCES 文件`
        - `SOURCES.CMN`
        - `创建命令行构建批处理文件`
    - `设备驱动程序开发套件`
      - `CEDDK 动态链接库`
      - `注册表辅助库`
    - `TRACE32-ICD`
      - `概述`
      - `如何准备追踪工具`
    - `设备驱动程序向导`
      - `概述`
      - `最佳实践`
    - `本章小结`
- `正文(2)`
  - `第 3 章：优先设计你的设备驱动程序！`
    - `本章内容`
    - `设备驱动程序位置`
      - `BSP`
      - `特定操作系统设计`
      - `PUBLIC 树`
    - `模式选择`
      - `内核模式`
      - `用户模式`
    - `注册表`
    - `设备驱动程序类型`
    - `设备驱动程序特性`
      - `直接内存访问`
      - `中断支持`
      - `电源管理支持`
    - `IO 控制代码`
    - `设计物理设备驱动程序 (PDD)`
    - `为测试而设计`
    - `本章小结`
- `正文(3)`
  - `第 4 章：掌握硬件环境`
    - `本章内容：`
    - `简介`
    - `I/O 设备寄存器`
      - `状态寄存器`
      - `控制寄存器`
      - `数据寄存器`
        - `I/O 端口寄存器`
        - `内存映射寄存器`
    - `I/O 设备中断`
      - `中断优先级`
      - `中断向量`
      - `信号机制`
    - `I/O 设备内存`
      - `程序化 I/O (PIO)`



### 目录

- 设备专用内存
    - 直接内存访问 – DMA
        - 系统 DMA
        - 总线主控 DMA
    - PCI 总线
    - 章节小结

- 全文(4)
    - 第 5 章：设备驱动注册表设置
        - 本章内容包括：
        - 注册表概述
            - 注册表类型
            - 对象存储
            - 基于 RAM 的注册表
            - 基于配置单元的注册表
            - 小结
        - 设备驱动文件名
            - 设备文件命名空间 - 前缀和索引
            - 设备文件命名空间 - 挂载点
        - 加载顺序
            - 流设备驱动的加载顺序
        - 设备管理器注册表键
            - 活动注册表键
        - 注册表项
            - 必填项
            - 可选项
                - 设备驱动注册表子键
            - 用户模式驱动框架注册表设置
        - 为设备驱动创建注册表项
            - 创建注册表设置文件
        - 章节小结

- 全文(5)
    - 第 6 章：理解设备驱动类型
        - 本章内容包括：
        - 原生设备驱动
        - 流设备驱动
        - 混合设备驱动
        - 单体驱动与分层驱动
        - 设备接口类
            - 设备接口 GUID
                - IClass 注册表子键
                - 通告接口
        - 设备接口通知
            - 消息队列点对点通知
            - 通过 WM_DEVICECHANGE 的通知
        - 章节小结

- 全文(6)
    - 第 7 章：流设备驱动的本质
        - 本章内容包括
        - 流接口设备驱动
            - 流接口设备驱动的结构
                - 流接口入口点
                - 流接口设备驱动的加载
                - 异步 I/O 请求处理
        - 内核模式设备驱动
            - 访问检查
            - 封送处理
                - 同步使用的指针
                - 异步使用的指针
        - 过滤器设备驱动
        - 用户模式设备驱动
            - 对用户模式设备驱动的限制
            - 实现用户模式设备驱动
            - 加载和初始化用户模式设备驱动
        - 章节小结

- 全文(7)
    - 第 8 章：设备驱动 I/O 与中断
        - 本章内容包括
        - 中断模型
            - 中断架构
        - 中断处理
            - 中断服务例程 - ISR
                - OAL 与中断处理
                - 可安装 ISR
                - 嵌套 ISR
            - 中断服务线程 - IST
        - I/O 内存映射
            - 端口映射 I/O
            - 内存映射 I/O
        - 章节小结

- 全文(8)
    - 第 9 章：设备 I/O 控制处理
        - 本章内容包括
        - 什么是 IOCTL
            - 内核 IOCTL
        - 添加设备特定 IOCTL
        - 处理设备特定 IOCTL
            - 电源管理支持
        - 章节小结

- 全文(9)
    - 第 10 章：网络驱动接口规范与网络设备驱动
        - 本章内容包括
        - 概述
            - NDIS
            - 网络设备驱动层
                - 数据链路层
                - 网络层
                - 传输层
        - NDIS 微型端口驱动
            - NDIS 微型端口驱动函数
                - DriverEntry
                - MiniportSetOptions
                - MiniportInitializeEx
                - MiniportHaltEx
                - MiniportDriverUnload
                - MiniportPause
                - MiniportRestart
                - MiniportOidRequest
                - MiniportDevicePnPEventNotify
                - MiniportShutdownEx
                - MiniportCheckForHangEx
                - MiniportResetEx
            - 物理数据传输
            - NDIS 内存管理辅助函数
                - 共享内存
                - 分散聚合 DMA
        - NDIS 协议驱动
            - ProtocolSetOptions


```markdown
*   `ProtocolBindAdapterEx`
*   `ProtocolUnbindAdapterEx`
*   `ProtocolOpenAdapterCompleteEx`
*   `ProtocolCloseAdapterCompleteEx`
*   `ProtocolNetPnPEvent`
*   `ProtocolUninstall`
*   `ProtocolReceiveNetBufferLists`
*   `ProtocolSendNetBufferListsComplete`
*   `NDIS 中间层驱动程序`
*   `注册表设置`
*   `章节小结`
*   `全文(10)`
    *   `第 11 章 调试设备驱动程序`
        *   `本章内容`
        *   `调试工具与技术概述`
            *   `调试技术`
            *   `调试工具`
        *   `简单有效的调试技术`
        *   `调试消息`
            *   `调试区域`
                *   `调试区域宏`
                *   `注册调试区域`
                *   `更改调试区域`
        *   `内核调试器`
            *   `CeDebugX`
        *   `硬件辅助调试`
            *   `TRACE 32`
                *   `自动化调试过程`
            *   `eXDI`
        *   `目标控制`
            *   `创建调试扩展`
        *   `远程工具`
            *   `设备端实现`
            *   `桌面远程工具插件实现`
        *   `事后调试与 Dr. Watson`
            *   `错误报告生成器`
            *   `错误报告传输驱动程序`
            *   `错误报告控制面板`
            *   `报告上传客户端`
            *   `小型转储与存储桶`
        *   `章节小结`
*   `全文(11)`
    *   `第 12 章：使用 CTK 开发测试代码`
        *   `本章内容`
        *   `Windows Embedded Compact 测试工具包`
            *   `概述`
            *   `用户界面`
            *   `创建测试过程`
                *   `创建测试过程模板`
                *   `创建测试用例`
                *   `从模板创建测试过程`
        *   `设计测试`
        *   `TUX 测试框架`
            *   `实现 TUX 测试 DLL`
            *   `运行测试`
        *   `查看与分析测试结果`
        *   `性能测试`
            *   `添加第二个测试过程`
        *   `章节小结`
*   `后页内容`
    *   `索引`
```