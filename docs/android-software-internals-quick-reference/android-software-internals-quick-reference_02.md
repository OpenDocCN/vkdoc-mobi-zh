# 2. Android 版本

Android 1.0 于 2008 年 9 月 23 日发布；自那时起，该操作系统经历了无数次的变革。表 2-1 列出了 Android 的不同版本（从 1.5 版本 Cupcake 开始）。

Android 中的 API 级别定义了该设备上支持的 API 功能。如果使用设备的 shell 提示符（例如，通过命令`adb shell`，该命令是 Android 平台工具的一部分），你可以通过运行`getprop | grep sdk`来返回与当前 API 级别相关的系统属性。这也可以通过已连接机器上的`adb`（Android 调试桥）完成。

*以下是在运行 Android 11 的 Google Pixel 4a 上执行此命令的输出示例：*

```
[ro.build.version.min_supported_target_sdk]: [23]
[ro.build.version.preview_sdk]: [0]
[ro.build.version.preview_sdk_fingerprint]: [REL]
[ro.build.version.sdk]: [30]
[ro.product.build.version.sdk]: [30]
[ro.qti.sdk.sensors.gestures]: [false]
[ro.system.build.version.sdk]: [30]
[ro.system_ext.build.version.sdk]: [30]
[ro.vendor.build.version.sdk]: [30]
```

表 2-1

Android 发布版本

| 版本 | 代号 | API 级别 | 发布日期 | Linux 内核版本 |
| --- | --- | --- | --- | --- |
| 1.5 | Cupcake | 3 | 2009 年 4 月 | 2.6.27 |
| 1.6 | Donut | 4 | 2009 年 9 月 | 2.6.29 |
| 2.0 | Eclair | 5 | 2009 年 10 月 | 2.6.29 |
| 2.0.1 | Eclair | 6 | | |
| 2.1 | Eclair | 7 | | |
| 2.2.x | Froyo | 8 | 2010 年 5 月 | 2.6.32 |
| 2.3 -> 2.3.2 | Gingerbread | 9 | 2010 年 12 月 | 2.6.35 |
| 2.3.3 -> 2.3.7 | Gingerbread | 10 | | |
| 3.0 | Honeycomb | 11 | 2011 年 2 月 | 2.6.36 |
| 3.1 | Honeycomb | 12 | | |
| 3.2.x | Honeycomb | 13 | | |
| 4.0.1 -> 4.0.2 | Ice Cream Sandwich | 14 | 2011 年 10 月 | 3.0.1 |
| 4.0.3 -> 4.0.4 | Ice Cream Sandwich | 15 | | |
| 4.1.x | Jelly Bean | 16 | 2012 年 7 月 | 3.0.31 |
| 4.2.x | Jelly Bean | 17 | | 3.4.0 |
| 4.3.x | Jelly Bean | 18 | | 3.4.39 |
| 4.4 -> 4.4.4 | KitKat | 19 | 2013 年 10 月 | 3.10 |
| 5.0 | Lollipop | 21 | 2014 年 6 月 | 3.16.1 |
| 5.1 | Lollipop | 22 | | |
| 6.0 | Marshmallow | 23 | 2015 年 10 月 | 3.18.10 |
| 7.0 | Nougat | 24 | 2016 年 8 月 | 3.18.48/4.4.0 |
| 7.1 | Nougat | 25 | | |
| 8.0.0 | Oreo | 26 | 2017 年 8 月 | 3.18.72/4.4.83/4.9.44 |
| 8.1.0 | Oreo | 27 | | 3.18.70/4.4.88/4.9.56 |
| 9 | Pie | 28 | 2018 年 8 月 | 4.4.146/4.9.118/4.14.61 |
| 10 | Android 10 (Q) | 29 | 2019 年 9 月 | 4.9.191/4.14.142/4.19.71 |
| 11 | Android 11 (R) | 30 | 2020 年 9 月 | 4.14.y/4.19.y/5.4.y |

