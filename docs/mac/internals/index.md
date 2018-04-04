---
title: 背後原理
description: 查看 Xamarin.Mac 的內部運作方式
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 74721e880bb0d3ada3f3940a4074d06f55601c0e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="under-the-hood"></a>背後原理

_查看 Xamarin.Mac 的內部運作方式_

## <a name="ahead-of-time-compilation-aotaotmd"></a>[繼續的時間編譯 (AOT)](aot.md)

繼續的時間 (AOT) 編譯的改善啟動效能的功能強大的最佳化技術。 不過，它也會影響您的建置時間，應用程式的大小和程式執行深遠的方式，因此需要了解其運作方式。

## <a name="mac-architecturearchitecturemd"></a>[Mac 架構](architecture.md)

Objective C，包括概念，例如編譯、 選取器、 註冊機構、 應用程式啟動和產生器 Xamarin.Mac 的關聯性。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac 登錄器](registrar.md)

Xamarin.Mac 橋接器受管理的環境與 Cocoa 的執行階段中，允許呼叫 unmanaged 的 OBJECTIVE-C 類別，並在事件發生時呼叫的 managed 的類別之間的差距。 若要執行此"magic"所需的工作由機構註冊網域，但了解 「 背後原理 」 狀況有時會很有幫助。
