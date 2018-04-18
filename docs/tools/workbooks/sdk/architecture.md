---
title: 架構概觀
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 5a836ec6e2e46316c46e4b6d6986d08601f84db1
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="architecture-overview"></a>架構概觀

Xamarin 活頁簿功能必須在與每個其他工作的兩個主要元件：_代理程式_和_用戶端_。

## <a name="interactive-agent"></a>互動式的代理程式

代理程式元件為.NET 應用程式的內容中執行的小型平台專屬組件。

Xamarin 活頁簿提供平台，例如 iOS、 Android、 Mac 和 WPF 預先建立"empty"的應用的程式。 這些應用程式明確裝載代理程式。

在即時檢查 （Xamarin 偵測器），代理程式被插入透過 IDE 偵錯工具至現有的應用程式做為一般的開發和偵錯工作流程的一部分。

## <a name="interactive-client"></a>互動式用戶端

用戶端是原生殼層 （Cocoa Mac 上的，在 Windows 上的 WPF） 裝載呈現活頁簿/REPL 介面的 web 瀏覽器介面。 SDK 的觀點而言，所有用戶端整合會實作 JavaScript 和 CSS。

用戶端要負責 （透過 Roslyn) 為小型的組件編譯原始程式碼和傳送至執行的已連線的代理程式。 執行結果會轉譯傳送回用戶端。 活頁簿中的每個資料格會產生一個組件參考的組件的上一個儲存格。

由於代理程式可以在任何類型的.NET 平台上執行，且有權存取任何內容中執行的應用程式，必須小心序列化結果無從驗證平台的方式。