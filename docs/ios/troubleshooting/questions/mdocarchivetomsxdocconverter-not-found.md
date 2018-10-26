---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119757"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest

> [!IMPORTANT]
> 這個問題已經解決最新版 Xamarin。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。


## <a name="error-message"></a>錯誤訊息

此錯誤可能會出現在*Mac 伺服器記錄檔*Visual Studio 中：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此訊息中有 2 個不同的問題：

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    此錯誤是無害的但它也會產生誤導。 它[將會移除](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)未來的版本。

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    此錯誤是真正的問題。 不幸的是，由於要[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)此例外狀況堆疊追蹤會*完整*。 如果您注意到 Mac 伺服器記錄檔中的 像這樣的不完整的堆疊追蹤，您可以檢查`~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log`若要尋找完整的堆疊追蹤在 Mac 組建主機上的檔案。
