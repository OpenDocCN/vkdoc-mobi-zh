# 图 7-10. 使用 Windows CE 设备驱动程序向导创建筛选器驱动程序

清单 7-17 展示了 `Init` 入口点的一个简化基本实现，该入口点是筛选器驱动程序公开的唯一函数。它所做的一切就是创建 `FIRFilter` 类的一个新实例，保存指向下一个筛选器（实际上是指向流设备驱动程序的一个指针）的指针，并返回指向筛选器驱动程序对象的指针。

**清单 7-17. 筛选器驱动程序的示例 Init 入口点。**

```cpp
extern "C" PDRIVER_FILTER FIRFilterInit(LPCTSTR lpcFilterRegistryPath, LPCTSTR
lpcDriverRegistryPath, PDRIVER_FILTER pNextFilter)
{
    FIRFilter* pFIRFilter = NULL;
    InitializeCriticalSection(&g_CriticalSection);
    DEBUGMSG(ZONE_INIT,
        (L"FIRFilter: 正在为 <%s> 创建新的筛选器驱动程序\r\n",
        lpcDriverRegistryPath));
    EnterCriticalSection(&g_CriticalSection);
    pFIRFilter = new FIRFilter(lpcFilterRegistryPath,
        lpcDriverRegistryPath,pNextFilter);
    if (!pFIRFilter)
    {
        DEBUGMSG(ZONE_ERROR,
            (L"FIRFilter: 错误，无法分配内存。%s 无法初始化\r\n",
            lpcDriverRegistryPath));
        goto done;
    }
done:
    pFIRFilter->pNextFilter = pNextFilter;
    LeaveCriticalSection(&g_CriticalSection);
    return (PDRIVER_FILTER)pFIRFilter;
}
```

下面的清单 7-18 是一个 `FIRFilter::FilterInit` 入口点实现的示例，它使用我们保存的指向流设备驱动程序的指针来调用其 `Init` 入口点，在本例中是 `TST_Init`。

注意，不要为可以打开多个实例的设备驱动程序实现筛选器驱动程序。

**清单 7-18. 示例 FilterInit 入口点**

```cpp
DWORD FIRFilter::FilterInit(DWORD dwContext,LPVOID lpParam)
{
    DWORD dwRet = 0;
    m_initReturn = pNextFilter->fnInit(dwContext, lpParam,
        this);
    return m_initReturn;
}
```

---

