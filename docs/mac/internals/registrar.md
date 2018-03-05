---
title: "Xamarin.Mac 登錄器"
description: "本文件說明 Xamarin.Mac 註冊機構和其不同的使用方式設定的用途。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 8d5061850a4cb73a81e1bf4c93583eb5b6eeefec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac 登錄器

_本文件說明 Xamarin.Mac 註冊機構和其不同的使用方式設定的用途。_

## <a name="overview"></a>總覽

Xamarin.Mac 橋接器 managed (.NET) 世界與 Cocoa 的執行階段中，允許呼叫 unmanaged 的 OBJECTIVE-C 類別，並在事件發生時呼叫的 managed 的類別之間的差距。 若要執行此"magic"所需的工作由註冊機構處理，而且，在一般情況下，檢視中隱藏。

有此登錄，特別是在應用程式啟動時間，效能含意，並了解位元的 「 背後原理 」 狀況有時會很有幫助。

## <a name="configurations"></a>組態

本質上啟動時註冊機構的作業可以分成兩個類別：

- 對於衍生自 NSObject 掃描受管理的每個類別，並收集 Objective C 執行階段對公開的項目清單。
- Objective C 執行階段註冊這項資訊。

經過一段時間，已建立三種不同的註冊機構設定以涵蓋不同的使用案例。 各有不同的建置，且執行時間的結果：

- **動態註冊機構**– 在啟動期間，使用.NET 反映掃描每個載入的型別，請判斷相關項目清單，並通知原生執行階段。 此選項會將組建的時間為零，但非常昂貴計算期間啟動 （最多個秒為單位）。
- **靜態註冊機構**– 在建置期間計算一組項目進行登錄，並產生 Objective C 程式碼來處理註冊。 這段程式碼會啟動，以快速註冊所有項目時叫用。 將從應用程式啟動建置，但可以顯著暫停剪下的大量的時間。
- **「 部分 」 靜態**– 較新的 「 混合 」 方法可帶來大部分兩者的優點。 從匯出自**Xamarin.Mac.dll**常數，預先計算的程式庫，以處理其註冊及連結，在儲存。 使用反映來處理使用者程式庫，但使用者程式庫匯出的平台繫結，這通常就是快速而不是更少的類型。 Neglectable 建置時間影響，並減少大部分的動態的 「 成本 」。

部分靜態今天是偵錯組態的預設值，且靜態發行組態的預設值。

有一些案例：

- 在啟動後載入的類別衍生自 NSObject 外掛程式
- 以動態方式建立衍生自 NSObject 類別執行個體

其中的註冊機構是無法知道它必須註冊在啟動某些類型。 `ObjCRuntime.Runtime.RegisterAssembly`方法提供來通知它有要考量的其他類型的註冊機構。
