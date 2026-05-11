# 第 8 章 设备驱动 I/O 与中断

```
    // Associate the event with the interrupt ID
    //
    if (!InterruptInitialize(pDevCntxt->dwSysIntr,
        pDevCntxt->hIstEvent, NULL,0))
    {
        DEBUGMSG(ZONE_INIT,
            (_T("DMO!IST Create: Failed to associate event ..\r\n")));
        return -1;
    }

    // Create the Interrupt Service Thread
    //
    pDevCntxt->hIst = CreateThread(NULL,0,
        (LPTHREAD_START_ROUTINE)DMOInterruptServiceThread,
        (LPVOID)pDevCntxt, CREATE_SUSPENDED,
        NULL);
    if (pDevCntxt->hIst == NULL)
    {
        DEBUGMSG(ZONE_INIT,(_T("DMO!IST Create: Failed to create IST\r\n")));
        return -1;
    }

    // Set the thread priority
    //
    if( !CeSetThreadPriority(pDevCntxt->hIst,
        pDevCntxt->dwIstPriority))
    {
        DEBUGMSG(ZONE_INIT,
            (_T("DMO!IST Create: Failed to set IST thread priority\r\n")));
        return -1;
    }

    // Set the thread quantum
    //
    if(!CeSetThreadQuantum(pDevCntxt->hIst,
        pDevCntxt->dwIstQuantum))
    {
        DEBUGMSG(ZONE_INIT,
            (_T("DMO!IST Create: Failed to set IST thread quantum\r\n")));
        return -1;
    }

    // Start IST
    //
    ResumeThread(pDevCntxt->hIst);
    return 0;
}
```

IST 本身会阻塞在此事件上。当内核因相应的中断被触发而设置该事件时，IST 通过检索所需数据来处理中断，并调用`InterruptDone`来重新启用相应的硬件中断。清单 8-6 显示了由设备驱动向导生成的、在清单 8-5 中创建的 IST 的实现。

[www.it-ebooks.info](http://www.it-ebooks.info/)

