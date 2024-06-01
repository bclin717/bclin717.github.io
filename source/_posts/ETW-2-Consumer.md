---
title: ETW 介紹 2 - Consumer
categories:
  - 軟體研討
date: 2020-07-30 11:20:27
tags:
---

前一篇文章介紹了 ETW 和簡單的使用方式，這次我們要加入 Consumer，使得我們可以即時接收事件。
<!-- more -->
![](https://i.imgur.com/aOTJ0dc.png)

# Consumer 

顧名思義，Consumer 就是事件的消費者，消費的方式主要有兩種，從 Log file，或以 Real-time mode 從 Provider 收事件。在這裡，我們會先用到 [EVENT_TRACE_LOGFILE](https://docs.microsoft.com/en-us/windows/win32/api/evntrace/ns-evntrace-event_trace_logfilea) 這個結構。

## 原來的設定

我們需要先更改 pSessionProperties 的 LogFileMode，讓其支援 REAL TIME MODE：

```C++
pSessionProperties->LogFileMode = EVENT_TRACE_REAL_TIME_MODE | EVENT_TRACE_FILE_MODE_SEQUENTIAL;
```


## 初始化結構
首先我們要宣告一組 EVENT_TRACE_LOGFILE，並先定義收發的模式。
再結構中，ProcessTraceMode 其實是 union 的一員，另外還有一個 LogFileMode，但 MSDN 表示這個只是保留，勿使用 LogFileMode。

我們要使用 Real-time 模式，這個值要設為 PROCESS_TRACE_MODE_REAL_TIME，另外因為我們要用 EventRecordCallback，所以還必須要加上 PROCESS_TRACE_MODE_EVENT_RECORD。

接下來，我們要設定從哪一個 Session 即時地收事件，把 LoggerName 設成你自己設的 Session 就可以了，這裡要注意的是，如果有設 LoggerName，那LogFileName 就必須設成 NULL (在C++中應該是 nullptr)。

接著把 EventRecordCallback 設成你要被 Callback 的 function。

最後用 OpenTrace() 把這個 Consumer 叫起來。

```C++
EVENT_TRACE_LOGFILE TraceLogFile = {};
ZeroMemory(&TraceLogFile, sizeof(EVENT_TRACE_LOGFILE));
TraceLogFile.ProcessTraceMode = PROCESS_TRACE_MODE_REAL_TIME | PROCESS_TRACE_MODE_EVENT_RECORD;
TraceLogFile.LogFileName = NULL; // ETWLOGPATH;
TraceLogFile.LoggerName = LPWSTR(LOGSESSION_NAME);
TraceLogFile.EventRecordCallback = ETW::EventRecordCallback;

hTraceHandle = OpenTrace(&TraceLogFile);
if (hTraceHandle == INVALID_PROCESSTRACE_HANDLE)
{
    printf_s("OpenTrace fail: %lu\n", GetLastError());
    return FALSE;
}
```

## Callback
Callback function 這邊，我設了一個 function 叫做 EventRecordCallback，一旦我們收到事件，就會呼叫這個 Callback function，建立一個文字檔，寫進 Hello event!

```C++
VOID WINAPI EventRecordCallback(EVENT_RECORD*);

void ETW::EventRecordCallback(EVENT_RECORD* pEventRecord)
{
    auto& header = pEventRecord->EventHeader;

    DWORD cbWritten;

    const HANDLE hFile = CreateFileW(L"C:\\test\\callback_log.txt", FILE_ALL_ACCESS, 0, 0, OPEN_ALWAYS, FILE_ATTRIBUTE_NORMAL, NULL);
    if (hFile != INVALID_HANDLE_VALUE)
    {
        WriteFile(hFile, "Hello event!\n", sizeof("Hello event!\n"), &cbWritten, nullptr);
        CloseHandle(hFile);
    }

    printf_s("ProcessId: %ul \n", header.ProcessId);
    printf_s("ThreadId: %ul \n", header.ThreadId);
    printf_s("ProviderId: %ul-%ul-%ul-%s \n", header.ProviderId.Data1, header.ProviderId.Data2, header.ProviderId.Data3, header.ProviderId.Data4);
    printf_s("EventDescriptor.Id: %d \n", header.EventDescriptor.Id);
}
```

## 收 Event
要收 Event 的時候，我們要呼叫 ProcessTrace()，呼叫之後就會開始收 Event 叫 Callback 了。直到 BufferCallback （另一種 callback）回傳 FALSE，或是 CloseTrace 被呼叫。所以基本上，這裡我們應該開另一個 Thread 來處理才對。

```C++
wprintf_s(L"ProcessTraceThread start.\n");
const auto status = ProcessTrace(&hTraceHandle, 1, 0, 0);
if (status != ERROR_SUCCESS)
{
    wprintf_s(L"ProcessTrace error: 0x%x\n", status);
    return FALSE;
}

wprintf_s(L"ProcessTraceThread Finish.\n");
return TRUE;
```

至於 Close，基本上大同小異，叫 CloseTrace() 來處理。這樣一下 ProcessTrace() 就會關閉了。

```C++
if (hSession)
{
    wprintf_s(L"Call CloseTrace().\n");
    CloseTrace(hSession);
    hSession = NULL;
}
```