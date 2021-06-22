---
title: ETW 介紹 - 以 Event Tracing Session 為範例
categories:
  - 軟體研討
date: 2020-07-29 10:40:27
tags:
---
<!-- more -->
因為工作需求，開始接觸 Windows System Programming，每次都覺得 MSDN 寫的很混亂，有時候太注重細節，對於系統沒有全面性的介紹；有時又只有概論，沒有程式碼或實作內容；有些甚至把不同的文件連結在一起就結束了。即使是 Google 找到的資料也比日本製的壓縮機還稀少。最後只能花時間慢慢測試，聽部門的人說，有好幾次都是他們自己試出很 tricky 的用法，跟微軟的技術人員聊一聊，結果居然變成 MSDN 的內容，不知道該哭還是笑。

# 什麼是 ETW ？

ETW(Event Tracing for Windows) 是 Windows 提供的一種事件追蹤的方式，利用 ETW 可以實現 real-time 的高速事件追蹤，讓程式設計師可以利用事件做除錯或是效能優化。

![](https://i.imgur.com/aOTJ0dc.png)

ETW 分為三個部分，Controller、Provider 和 Consumer，Controller 可以開關 Provider，Provider 會提供事件（Event）本身，而 Consumer 則是消費（Consume）事件的人，也就是我們的角色。

另外，Controller 可以註冊 Session 並設定要負責的 Provrider，Provider 會把本身的事件發給 Session，Session 可以把事件直接發給 Consumer（Real-time mode），或是存到一個 Trace File，Consumer 再去這個檔案拿事件（File Mode）。

## Sessions

Session 會從一個或多個 Providers 記錄事件，並且也負責管理和清除 buffer 的資料，而 Session 本身的屬性則是由 Controller 設定的。

Session 分為很多種：
- Event Tracing Session
- SystemTraceProvider Session
  - NT Kernel Logger Session
- AutoLogger Session
  - Global Logger Session
- Private Logger Session

我們在這裡先探討如何簡單地使用 Event tracing sessions 把 Events 存到 Log file。

# 使用 Event Tracing Session

我們參考 MSDN 上的範例，[Example that Creates a Session and Enables a Manifest-based or Classic Provider](https://docs.microsoft.com/en-us/windows/win32/etw/example-that-creates-a-session-and-enables-a-manifest-based-provider)


## 定義常數
首先定義 Log file 的位置，以及 Session 的名稱。
```C++
#define LOGFILE_PATH L"<FULLPATHTOLOGFILE.etl>"
#define LOGSESSION_NAME L"My Event Trace Session"
```

接下來是 Session 的 GUID 以及 Provider 的 GUID，其中 Session 的 GUID 是自己設定的唯一值。
Provider 的 GUID 則可以透過在 cmd 下 logman query providers 找到。

```C++
// {AE44CB98-BD11-4069-8093-770EC9258A12}
static const GUID SessionGuid = 
{ 0xae44cb98, 0xbd11, 0x4069, { 0x80, 0x93, 0x77, 0xe, 0xc9, 0x25, 0x8a, 0x12 } };

// {D8909C24-5BE9-4502-98CA-AB7BDC24899D}
static const GUID ProviderGuid = 
{ 0xd8909c24, 0x5be9, 0x4502, {0x98, 0xca, 0xab, 0x7b, 0xdc, 0x24, 0x89, 0x9d } };
```

## 初始化空間

首先要為 Session 建立一個空間，儲存 Session 的結構是 EVENT_TRACE_PROPERTIES (Evntrace.h)，但我們還需要儲存上面定義的 Logfile Path 以及 Session name，所以總長會是三者相加。之後再用 malloc 向系統要一塊記憶體。


```C++
ULONG status = ERROR_SUCCESS;
TRACEHANDLE SessionHandle = 0;
EVENT_TRACE_PROPERTIES* pSessionProperties = NULL;
ULONG BufferSize = 0;
BOOL TraceOn = TRUE;

// Allocate memory for the session properties. The memory must
// be large enough to include the log file name and session name,
// which get appended to the end of the session properties structure.
    
BufferSize = sizeof(EVENT_TRACE_PROPERTIES) + sizeof(LOGFILE_PATH) + sizeof(LOGSESSION_NAME);
pSessionProperties = (EVENT_TRACE_PROPERTIES*) malloc(BufferSize);    
if (NULL == pSessionProperties)
{
    wprintf(L"Unable to allocate %d bytes for properties structure.\n", BufferSize);
    goto cleanup;
}
```

## 配置 Session

我們剛才已經將記憶體空間準備好了，現在要來設置當中的設定。\
關於詳細的結構內容可以參考 [EVENT_TRACE_PROPERTIES structure](https://docs.microsoft.com/en-us/windows/win32/api/evntrace/ns-evntrace-event_trace_properties) \
我們不需要完整設定全部的內容，除了必要的欄位，其他的欄位會在啟動時被填入預設值。

首先我們利用 ZeroMemory() 初始化這塊空間，ZeroMemory 是微軟對 memset 的封裝，將目標空間填 0。詳細請參考 [Why do ZeroMemory, etc. exist when there are memset, etc. already?](https://stackoverflow.com/questions/3038302/why-do-zeromemory-etc-exist-when-there-are-memset-etc-already)

接下來開始填入 pSessionProperties 的內容。\
EVENT_TRACE_PROPERTIES 結構中有一個 Wnode 的結構，我們必須設定它的 BufferSize、Flags 以及 Guid， ClientContext 則是選擇性的。Guid 填入我們所定義的 SessionGuid，Flags 則必須包含 WNODE_FLAG_TRACED_GUID。詳細請參考 [WNODE_HEADER structure](https://docs.microsoft.com/en-us/windows/win32/etw/wnode-header)。

接著是 LogFileMode，設定我們要採用什麼模式去紀錄 Event tracing session，可以是記錄到 log file，也可以即時呼叫 callback，或兩者同時採用。其設定請參考 [Logging Mode Constants](https://docs.microsoft.com/en-us/windows/win32/etw/logging-mode-constants)。在範例中，EVENT_TRACE_FILE_MODE_SEQUENTIAL 代表將事件循序寫入 Log file 中，直到我們設定的檔案大小，也就是 MaximumFileSize。值得注意的是，如果我們把儲存 log file 的路徑設定在和作業系統同一個磁碟區，ETW 會另外檢查是否有 200MB 的空間可以用。即，我們設定 100MB，實際的磁碟空間要求會是 300MB。


```C++
// Set the session properties. You only append the log file name
// to the properties structure; the StartTrace function appends
// the session name for you.

ZeroMemory(pSessionProperties, BufferSize);
pSessionProperties->Wnode.BufferSize = BufferSize;
pSessionProperties->Wnode.Flags = WNODE_FLAG_TRACED_GUID;
pSessionProperties->Wnode.ClientContext = 1; //QPC clock resolution
pSessionProperties->Wnode.Guid = SessionGuid; 
pSessionProperties->LogFileMode = EVENT_TRACE_FILE_MODE_SEQUENTIAL;
pSessionProperties->MaximumFileSize = 1;  // 1 MB
pSessionProperties->LoggerNameOffset = sizeof(EVENT_TRACE_PROPERTIES);
pSessionProperties->LogFileNameOffset = sizeof(EVENT_TRACE_PROPERTIES) + sizeof(LOGSESSION_NAME); 
StringCbCopy((LPWSTR)((char*)pSessionProperties + pSessionProperties->LogFileNameOffset), sizeof(LOGFILE_PATH), LOGFILE_PATH);
```

## 建立 Session
我們已經把 Session 的內容都寫好了，就像前面所述，Session 是由 Controller 操控，所以接下來，我們要利用 Controller 的 API 來建立、修改或啟動 Session。

### Controller API
Controller 的 API 主要有 

-  StartTrace()
    - 啟動 Session。
-  ControlTrace()
    - 控制 Session，包括修改與中止。
-  EnableTraceEx2()
    - 在 Windows 8.1 與 Windows Server 2012 R2 之後的版本才呼叫 EnableTraceEx2()，更之前有 EnableTrace() 及 EnableTraceEx()。


要把這個 Session 實際建立起來，直接呼叫 StartTrace，填入 TRACEHANDLE、Session name 以及我們剛剛配置好的結構 EVENT_TRACE_PROPERTIES 便可以了。

```C++
status = StartTrace((PTRACEHANDLE)&SessionHandle, LOGSESSION_NAME, pSessionProperties);
if (ERROR_SUCCESS != status)
{
    wprintf(L"StartTrace() failed with %lu\n", status);
    goto cleanup;
}
```

## 啟動 Provider

我們利用 EnableTraceEx2() 將 Session 和 Provider 綁在一起，並啟動 Provider、設定要接收的事件等級，在範例中設定的是 TRACE_LEVEL_INFORMATION。詳細請參考 [EnableTraceEx2 function](https://docs.microsoft.com/en-us/windows/win32/api/evntrace/nf-evntrace-enabletraceex2) 的 Level，總共有五個等級。

```C++
status = EnableTraceEx2(
    SessionHandle,
    (LPCGUID)&ProviderGuid,
    EVENT_CONTROL_CODE_ENABLE_PROVIDER,
    TRACE_LEVEL_INFORMATION,
    0,
    0,
    0,
    NULL
    );

if (ERROR_SUCCESS != status)
{
    wprintf(L"EnableTrace() failed with %lu\n", status);
    TraceOn = FALSE;
    goto cleanup;
}
```

## Cleanup

在 EnableTraceEx2() 傳入 EVENT_CONTROL_CODE_DISABLE_PROVIDER 將 Provider 關閉，再用 ControlTrace 傳入 EVENT_TRACE_CONTROL_STOP 關閉 Session。

```C++
if (SessionHandle)
{
    if (TraceOn)
    {
        status = EnableTraceEx2(
            SessionHandle,
            (LPCGUID)&ProviderGuid,
            EVENT_CONTROL_CODE_DISABLE_PROVIDER,
            TRACE_LEVEL_INFORMATION,
            0,
            0,
            0,
            NULL
            );
    }

    status = ControlTrace(SessionHandle, LOGSESSION_NAME, pSessionProperties, EVENT_TRACE_CONTROL_STOP);

    if (ERROR_SUCCESS != status)
    {
        wprintf(L"ControlTrace(stop) failed with %lu\n", status);
    }
}

if (pSessionProperties)
{
    free(pSessionProperties);
    pSessionProperties = NULL;
}
```

## 完整程式碼

```C++
#include <windows.h>
#include <stdio.h>
#include <conio.h>
#include <strsafe.h>
#include <wmistr.h>
#include <evntrace.h>

#define LOGFILE_PATH L"<FULLPATHTOLOGFILE.etl>"
#define LOGSESSION_NAME L"My Event Trace Session"

// GUID that identifies your trace session.
// Remember to create your own session GUID.

// {AE44CB98-BD11-4069-8093-770EC9258A12}
static const GUID SessionGuid = 
{ 0xae44cb98, 0xbd11, 0x4069, { 0x80, 0x93, 0x77, 0xe, 0xc9, 0x25, 0x8a, 0x12 } };

// GUID that identifies the provider that you want
// to enable to your session.

// {D8909C24-5BE9-4502-98CA-AB7BDC24899D}
static const GUID ProviderGuid = 
{ 0xd8909c24, 0x5be9, 0x4502, {0x98, 0xca, 0xab, 0x7b, 0xdc, 0x24, 0x89, 0x9d } };

void wmain(void)
{
    ULONG status = ERROR_SUCCESS;
    TRACEHANDLE SessionHandle = 0;
    EVENT_TRACE_PROPERTIES* pSessionProperties = NULL;
    ULONG BufferSize = 0;
    BOOL TraceOn = TRUE;

    // Allocate memory for the session properties. The memory must
    // be large enough to include the log file name and session name,
    // which get appended to the end of the session properties structure.
    
    BufferSize = sizeof(EVENT_TRACE_PROPERTIES) + sizeof(LOGFILE_PATH) + sizeof(LOGSESSION_NAME);
    pSessionProperties = (EVENT_TRACE_PROPERTIES*) malloc(BufferSize);    
    if (NULL == pSessionProperties)
    {
        wprintf(L"Unable to allocate %d bytes for properties structure.\n", BufferSize);
        goto cleanup;
    }
    
    // Set the session properties. You only append the log file name
    // to the properties structure; the StartTrace function appends
    // the session name for you.

    ZeroMemory(pSessionProperties, BufferSize);
    pSessionProperties->Wnode.BufferSize = BufferSize;
    pSessionProperties->Wnode.Flags = WNODE_FLAG_TRACED_GUID;
    pSessionProperties->Wnode.ClientContext = 1; //QPC clock resolution
    pSessionProperties->Wnode.Guid = SessionGuid; 
    pSessionProperties->LogFileMode = EVENT_TRACE_FILE_MODE_SEQUENTIAL;
    pSessionProperties->MaximumFileSize = 1;  // 1 MB
    pSessionProperties->LoggerNameOffset = sizeof(EVENT_TRACE_PROPERTIES);
    pSessionProperties->LogFileNameOffset = sizeof(EVENT_TRACE_PROPERTIES) + sizeof(LOGSESSION_NAME); 
    StringCbCopy((LPWSTR)((char*)pSessionProperties + pSessionProperties->LogFileNameOffset), sizeof(LOGFILE_PATH), LOGFILE_PATH);

    // Create the trace session.

    status = StartTrace((PTRACEHANDLE)&SessionHandle, LOGSESSION_NAME, pSessionProperties);
    if (ERROR_SUCCESS != status)
    {
        wprintf(L"StartTrace() failed with %lu\n", status);
        goto cleanup;
    }

    // Enable the providers that you want to log events to your session.

    status = EnableTraceEx2(
        SessionHandle,
        (LPCGUID)&ProviderGuid,
        EVENT_CONTROL_CODE_ENABLE_PROVIDER,
        TRACE_LEVEL_INFORMATION,
        0,
        0,
        0,
        NULL
        );

    if (ERROR_SUCCESS != status)
    {
        wprintf(L"EnableTrace() failed with %lu\n", status);
        TraceOn = FALSE;
        goto cleanup;
    }

    wprintf(L"Run the provider application. Then hit any key to stop the session.\n");
    _getch();

cleanup:

    if (SessionHandle)
    {
        if (TraceOn)
        {
            status = EnableTraceEx2(
                SessionHandle,
                (LPCGUID)&ProviderGuid,
                EVENT_CONTROL_CODE_DISABLE_PROVIDER,
                TRACE_LEVEL_INFORMATION,
                0,
                0,
                0,
                NULL
                );
        }

        status = ControlTrace(SessionHandle, LOGSESSION_NAME, pSessionProperties, EVENT_TRACE_CONTROL_STOP);

        if (ERROR_SUCCESS != status)
        {
            wprintf(L"ControlTrace(stop) failed with %lu\n", status);
        }
    }

    if (pSessionProperties)
    {
        free(pSessionProperties);
        pSessionProperties = NULL;
    }
}
```


# 結語

最後會得到一個 Log file，副檔名是 etl，像是這樣

![](https://i.imgur.com/bA7cvXl.jpg)

如果有裝 Windows Performance Analyzer，就可以打開來看。圖中是收 TCP/IP 的 Event。

![](https://i.imgur.com/B6GuX7z.jpg)

以上便是最簡單的 ETW 使用方式，在 Windows 收 Event 還有很多不同的做法，有機會再與大家分享。
