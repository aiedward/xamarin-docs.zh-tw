---
title: Xamarin. Mac 註冊機構
description: 本檔說明 Xamarin 和其動態、靜態和部分靜態（混合式）使用方式設定的用途。
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: 991d9b2d911b5aa4ac07225fd1df34877451df49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017313"
---
# <a name="xamarinmac-registrar"></a>Xamarin. Mac 註冊機構

_本檔說明 Xamarin 註冊機構的用途及其不同的使用方式設定。_

## <a name="overview"></a>總覽

Xamarin 會橋接受控（.NET）世界和 Cocoa 執行時間之間的差距，讓 managed 類別能夠呼叫非受控的目標 C 類別，並在發生事件時回呼。 執行此「魔術」所需的工作是由註冊機構處理，而且一般會隱藏于 view。

這項註冊有效能上的影響，特別是在應用程式啟動時間，並瞭解「幕後」的一些內容有時可能會有説明。

## <a name="configurations"></a>組態

基本上，在啟動時，註冊機構的作業可以分成兩個類別：

- 針對衍生自 NSObject 的每個受管理類別進行掃描，並收集要公開給目標-C 執行時間的專案清單。
- 向目標-C 執行時間註冊此資訊。

經過一段時間，已建立三個不同的註冊機構設定來涵蓋不同的使用案例。 每個都有不同的組建和執行時間結果：

- **動態註冊機構**–在啟動期間，使用 .net 反映來掃描每一個載入的類型、判斷相關專案的清單，以及通知原生執行時間。 此選項會將零時間新增至組建，但在啟動期間計算的成本很高（最多秒）。
- **靜態註冊機構**–在組建期間，會計算要註冊的專案集合，並產生客觀-C 程式碼來處理註冊。 這段程式碼會在啟動期間叫用，以快速註冊所有專案。 會將大量暫停新增至組建，但可能會從應用程式啟動中減少相當長的時間。
- 「**部分」靜態**-這是一種較新的「混合式」方法，它帶來了兩者的優點。 因為來自**Xamarin**的匯出是常數，所以請儲存預先計算程式庫來處理其註冊，並在中連結。 使用反映來處理使用者程式庫，但隨著使用者程式庫的匯出，平臺系結的類型通常會快很多。 Neglectable 的組建時間會受到影響，並減少動態的大部分「成本」。

[今天的部分靜態] 是 Debug 設定的預設值，而 [靜態] 則是發行設定的預設值。

在某些案例中：

- 以衍生自 NSObject 的類別啟動之後載入的外掛程式
- 以動態方式建立衍生自 NSObject 的類別實例

註冊機構無法得知它必須在開始時註冊某種類型。 系統會提供 `ObjCRuntime.Runtime.RegisterAssembly` 方法，通知註冊機構有其他要考慮的類型。
