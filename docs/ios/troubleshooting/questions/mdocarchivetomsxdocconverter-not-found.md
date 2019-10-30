---
title: 找不到 MDocArchiveToMsxDocConverter.exe rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3d21dfdbf6c9be00fe6851bb288268faccd74308
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030975"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>找不到 MDocArchiveToMsxDocConverter.exe rver.BaseCommand.OnRequest

> [!IMPORTANT]
> 此問題已在最新版本的 Xamarin 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

## <a name="error-message"></a>錯誤訊息

此錯誤可能會出現在*Mac 伺服器記錄*檔中 Visual Studio：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此訊息中有2個不同的問題：

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    此錯誤很無害，但也會誤導。 它[將](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)在未來的版本中移除。

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    這個錯誤是真正的問題。 可惜的是，由於[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)，此例外狀況堆疊追蹤不*完整*。 如果您注意到 Mac 伺服器記錄檔中出現不完整的堆疊追蹤，您可以檢查 Mac 組建主機上的 `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` 檔案，以找出完整的堆疊追蹤。
