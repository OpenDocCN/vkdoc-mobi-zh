# 向输入设备发送事件。例如，在我的设备上，以下命令将音量设置为 0。
`sendevent /dev/input/event0 0 0 0`

*通过 Monkey 测试工具（UI 模糊测试器）启动应用程序。将数字 1 替换为测试中要执行的随机触摸输入次数：*

```
monkey -p com.android.chrome 1
```

*如果你知道 Activity 名称，可以使用 Activity 管理器启动应用程序：*

```
am start -n com.android.chrome/com.google.android.apps.chrome.Main
```

*以下命令返回制造商、设备名称、版本、名称和日期，以及用户和发布密钥：*

```
getprop ro.build.fingerprint # 例如：google/blueline/blueline:9/PQ3A.190605.003/5524043:user/release-keys

# 返回内核版本
uname -a

# 也返回内核版本以及设备架构。
cat /proc/version
```

*访问应用程序的内部存储（需要 root 权限）：*

```
# 以 root 身份访问应用程序用作内部存储的位置。
cd /data/user/0

# 例如，访问 Chrome 中保存的离线页面，并将其存储在 data/local/tmp 目录中，以便后续从设备中拉取。
su
cd /data/user/0
cd com.android.chrome/cache/Offline Pages/archives
cp 91-a05c-b3f3384516f4.mhtml /data/local/tmp/page.mhtml
chmod 777 /data/local/tmp/page.mhtml
```

*重启设备。应用程序需要`android.permission.REBOOT`权限或 root 权限：*

```
/system/bin/reboot
reboot
svc power reboot
svc power shutdown
```

*以 root 身份将文件系统挂载为读写。* *在旧设备上* *可用于将系统应用程序目录设置为读写。*

```
busybox mount -o remount,rw /system
```

*中断允许接口设备与处理器通信：*

```
cat /proc/interrupts | grep volume
```

*`dumpsys`提供系统服务信息：*

```
dumpsys -l
dumpsys input
dumpsys meminfo
service call procstats 1
```

## 以编程方式运行命令

使用`Runtime`类，可以以编程方式运行 Shell 命令。如果命令需要的权限级别高于应用程序所拥有的权限，则命令将失败——例如，在没有`android.permission.REBOOT`权限的情况下尝试重启设备。

*运行单个命令：*

```
String filesLocation = getApplicationContext().getDataDir().getAbsolutePath();
try {
Runtime.getRuntime().exec("touch "+filesLocation+"/test.txt");
} catch (IOException e) {
e.printStackTrace();
}
```

