---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 6d7fe8f48d22c6b5d445fa8356e52f924549f6e0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30775672"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest

> [!IMPORTANT]
> 這個問題已經解決 Xamarin 的最新版的。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。


## <a name="error-message"></a>錯誤訊息

這個錯誤可能會出現在*Mac Server 記錄檔*Visual Studio 中：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此訊息中有 2 個別問題：

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    這個錯誤是無害的但也容易發生錯誤。 它[將會移除](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)未來的版本。

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    這個錯誤是真正的問題。 不幸的是，原因為[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)此例外狀況堆疊追蹤是*不完整*。 如果您發現像這樣的不完整的堆疊追蹤 Mac Server 記錄檔中，您可以檢查`~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log`來尋找完整的堆疊追蹤 Mac 組建主機上的檔案。
