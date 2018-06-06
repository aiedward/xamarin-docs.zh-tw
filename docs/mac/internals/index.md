---
title: 背後原理 Xamarin.Mac 中
description: 描述 Xamarin.Mac 的內部運作方式的各種指南的這個文件連結。 連結的文件討論在編譯時間、 Xamarin.Mac 架構和 Xamarin.Mac 註冊機構的前面。
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: c940252a675c38247d2c5bb374b9c30237222bda
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792485"
---
# <a name="under-the-hood-in-xamarinmac"></a>背後原理 Xamarin.Mac 中

## <a name="ahead-of-time-compilation-aotaotmd"></a>[繼續的時間編譯 (AOT)](aot.md)

繼續的時間 (AOT) 編譯的改善啟動效能的功能強大的最佳化技術。 不過，它也會影響您的建置時間，應用程式的大小和程式執行深遠的方式，因此需要了解其運作方式。

## <a name="mac-architecturearchitecturemd"></a>[Mac 架構](architecture.md)

Objective C，包括概念，例如編譯、 選取器、 註冊機構、 應用程式啟動和產生器 Xamarin.Mac 的關聯性。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac 登錄器](registrar.md)

Xamarin.Mac 橋接器受管理的環境與 Cocoa 的執行階段中，允許呼叫 unmanaged 的 OBJECTIVE-C 類別，並在事件發生時呼叫的 managed 的類別之間的差距。 若要執行此"magic"所需的工作由機構註冊網域，但了解 「 背後原理 」 狀況有時會很有幫助。
