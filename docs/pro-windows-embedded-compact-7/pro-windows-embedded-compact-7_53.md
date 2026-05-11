# 第 11 章 ■ 调试设备驱动程序

*清单 11-7. 扩展 DLL 实现的演示代码*

```
#include <intsafe.h>
#include <wdbgexts_ce.h>

WINDBG_EXTENSION_APIS ExtensionApis = {0};
USHORT g_SavedMajorVersion;
USHORT g_SavedMinorVersion;
EXT_API_VERSION g_ApiVersion = { 3, 5, EXT_API_VERSION_NUMBER32, 0 };

void WDBGAPI WinDbgExtensionDllInit(
    PWINDBG_EXTENSION_APIS lpExtensionApis,
    USHORT MajorVersion, USHORT MinorVersion)
{
    // 将 lpExtensionApis 复制到 ExtensionAPis，以便扩展
    // 函数可以访问 Platform Builder 调试器
    // 扩展 API。
    ExtensionApis = *lpExtensionApis;
    g_SavedMajorVersion = MajorVersion;
    g_SavedMinorVersion = MinorVersion;
}

void WDBGAPI WinDbgExtensionDllShutdown()
{
}

LPEXT_API_VERSION ExtensionApiVersion()
{
    return &g_ApiVersion;
}

DECLARE_API(demo)
{
    static ULONG Frames = 0;
```



