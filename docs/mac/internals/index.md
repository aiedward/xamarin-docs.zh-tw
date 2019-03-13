---
title: 背後原理 xamarin.mac
description: 本文件所連結至說明 Xamarin.Mac 的內部運作方式的各種輔助線。 連結的文件討論預先編譯、 Xamarin.Mac 架構和 Xamarin.Mac 註冊機構。
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 872f26febf3abbe4d659773d2bf2d27348c64513
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118769"
---
# <a name="under-the-hood-in-xamarinmac"></a>背後原理 xamarin.mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[直接的編譯 (AOT)](aot.md)

預先 (aot) 編譯是改善啟動效能功能強大的最佳化技術。 不過，它也會影響您的建置時間，應用程式大小和程式執行深遠的方式，因此很值得了解其運作方式。

## <a name="mac-architecturearchitecturemd"></a>[Mac 架構](architecture.md)

OBJECTIVE-C、 包括例如編譯、 選取器，註冊機構、 應用程式啟動和產生器的概念 Xamarin.Mac 的關聯性。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac 的註冊機構](registrar.md)

Xamarin.Mac 之間搭建橋樑 managed 的領域和 Cocoa 的執行階段，允許呼叫 unmanaged 的 OBJECTIVE-C 類別，並發生事件時呼叫的 managed 的類別。 若要執行此 「 神奇 」 所需的工作由在註冊機構，但了解什麼 「 幕後 」 上還是有時會很有幫助。
