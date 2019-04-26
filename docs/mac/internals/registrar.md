---
title: Xamarin.Mac registrar
description: 本文件說明 Xamarin.Mac 的註冊機構與它動態、 靜態及部分的靜態 （混合式） 的目的使用方式設定。
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 21e1a2c6ae5a9ad8b6acf520851ea92a340da887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032493"
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac registrar

_本文件說明 Xamarin.Mac 的註冊機構和其不同的使用方式設定的用途。_

## <a name="overview"></a>總覽

Xamarin.Mac 之間搭建橋樑 managed (.NET) 領域和 Cocoa 的執行階段，允許呼叫 unmanaged 的 OBJECTIVE-C 類別，並發生事件時呼叫的 managed 的類別。 若要執行此 「 神奇 」 所需的工作由在註冊機構處理，而且，在一般情況下，從檢視中隱藏。

此註冊，特別是在應用程式啟動時間，對效能的影響，並了解發生什麼情況 「 幕後 」 一堆有時會很有幫助。

## <a name="configurations"></a>組態

基本上在啟動時註冊機構的作業可以分成兩個類別：

- 對於衍生自 NSObject 掃描每個受管理的類別，並收集要向 OBJECTIVE-C 執行階段公開的項目清單。
- 這項資訊向 OBJECTIVE-C 執行階段。

經過一段時間，已建立三種不同的註冊機構設定以涵蓋不同的使用案例。 每個有不同的組建，並執行時間的結果：

- **動態註冊機構**– 在啟動期間，請使用.NET 反映來掃描每個載入的型別，判斷相關項目清單，並通知原生執行階段。 此選項將組建的時間為零，但非常昂貴計算期間啟動 （最多個秒為單位）。
- **靜態註冊機構**– 在建置期間計算註冊，並產生 OBJECTIVE-C 程式碼來處理註冊的項目組。 此程式碼會叫用期間啟動，以快速註冊的所有項目。 新增重大暫停組建，但可以從應用程式啟動剪下大量的時間。
- **「 部分 」 的靜態**– 較新的 「 混合式 」 方法可將大部分的這兩者的優點。 因為匯出**Xamarin.Mac.dll**是常數，會儲存預先計算的程式庫，來處理其註冊，並連結中。 使用反映來處理使用者程式庫，但使用者程式庫匯出的平台繫結，這通常就是相當快速的少很多型別。 Neglectable 建置時間影響，並減少大部分的動態 「 成本 」。

部分的靜態今天是偵錯組態的預設值和靜態是發行組態的預設值。

有一些案例：

- 啟動後載入類別衍生自 NSObject 的外掛程式
- 以動態方式建立衍生自 NSObject 的類別執行個體

其中的註冊機構是無法知道它必須註冊在啟動部分類型。 `ObjCRuntime.Runtime.RegisterAssembly`方法提供來通知它有要考慮的其他類型的註冊機構。
