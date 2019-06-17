---
title: 架構概觀
description: 本文件說明 Xamarin 活頁簿中，檢查 互動式的代理程式和互動式用戶端如何一起運作的架構。
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 352e8d0191512184ffd7d82fa0dfa0bc79fa24ca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61257421"
---
# <a name="architecture-overview"></a>架構概觀

Xamarin Workbooks 功能必須彼此搭配運作的兩個主要元件：_代理程式_並_用戶端_。

## <a name="interactive-agent"></a>互動式的代理程式

代理程式元件是小型的平台特定組件的.NET 應用程式內容中執行。

Xamarin 活頁簿會提供預先建置的 「 空的 」 應用程式，將多個平台，例如 iOS、 Android、 Mac 及 WPF。 這些應用程式明確裝載代理程式。

在即時檢測 (Xamarin Inspector)，代理程式插入透過 IDE 偵錯工具到現有的應用程式一般的開發和偵錯工作流程的一部分。

## <a name="interactive-client"></a>互動式用戶端

用戶端將裝載的 web 瀏覽器介面，來呈現的活頁簿/REPL 介面原生命令介面 （在 Windows 上的 WPF 中的 Cocoa Mac 上的）。 SDK 的觀點而言，所有的用戶端整合功能會實作以 JavaScript 和 CSS。

用戶端就負責 （透過 Roslyn) 的原始程式碼編譯成小型組件，並透過將它們傳送到已連線的代理程式執行。 執行的結果會轉譯傳送回用戶端。 活頁簿中的每個資料格會產生一個組件參考的組件的上一個儲存格。

因為代理程式可以在任何類型的.NET 平台上執行，而且可以存取任何項目中執行的應用程式，必須小心以跨平台的方式序列化結果。
