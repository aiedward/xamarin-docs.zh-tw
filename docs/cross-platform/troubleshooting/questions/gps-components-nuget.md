---
title: 整合 Google Play Services 元件和 NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 524456a5d5419c9ef2f2cef741135c90e624b620
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521605"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>整合 Google Play Services 元件和 NuGet

## <a name="history"></a>歷程

其中有數個 Google Play Services 元件和 NuGet 套件:

- Google Play Services (Froyo)
- Google Play Services (Gingerbread)
- Google Play Services (ICS)
- Google Play Services (提供 jellybean)
- Google Play Services (KitKat)

Google 實際上只會寄送兩個 Google Play Services 的 .jar 檔案:

- `google-play-services-froyo.jar`
- `google-play-services.jar`

因為我們的工具並未正確分辨`aapt.exe`要針對指定的應用程式使用的資源 API 層級上限, 所以存在差異。 也就是說, 如果我們嘗試在較低的 API 層級 (例如 Gingerbread) 上使用 Google Play Services (KitKat) 系結, 就會收到編譯錯誤。

## <a name="unifying-google-play-services"></a>統一 Google Play Services

在較新版本的 Xamarin 中, 我們現在會告訴`aapt.exe`您要使用的最大資源版本, 因此這個問題會讓我們消失。

這表示, 沒有真正的理由可以針對 Gingerbread/ICS/提供 jellybean/KitKat 使用個別的套件 (不過, 我們仍需要個別的 Froyo 系結, 因為它是一個不同的 .jar 檔案)。

為了讓開發人員更容易, 我們現在將元件和 NuGet 套件整合成兩個:

- Google Play Services (Froyo) ( `google-play-services-froyo.jar`系結)
- Google Play Services ( `google-play-services.jar`系結)

### <a name="which-one-should-be-used"></a>應該使用哪一個？

在幾乎每個案例中, 都應該使用 Google Play Services。 使用 (Froyo) 套件的唯一理由是, 如果您主動將目標設為 Froyo。 這個個別的 .jar 檔案是來自 Google 的唯一原因, 是因為 Froyo 是在這一小部分的裝置上, 他們本身已決定停止支援它, 因此這個 .jar 檔案是一個凍結、不支援的 Google Play Services 快照集。

### <a name="note-about-gingerbread"></a>關於 Gingerbread 的注意事項

Gingerbread 預設不支援片段, 因此, 系結中的某些類別在 Gingerbread 裝置上的執行時間中將無法用於應用程式。 之類`MapFragment`的類別將無法在 Gingerbread 上執行, 而應改用`SupportMapFragment`其支援變異。 開發人員需要知道要使用哪一個。 Google 會在其 Google Play Services 檔中注明此不相容性。

### <a name="what-happens-to-the-old-componentsnugets"></a>舊的元件/NuGet 會發生什麼事？

因為已不再需要它們, 所以我們已停用/Delisted 下列元件/Nuget:

- Google Play Services (Gingerbread)
- Google Play Services (提供 jellybean)
- Google Play Services (KitKat)

現有的_Google Play Services (ICS)_ 元件/Nuget 已重新命名為_Google Play Services_ , 將會持續保持最新狀態。 所有參考其中一個已停用/Delisted 套件的專案, 都應該更新為使用此封裝。

已停用的元件仍然存在, 而且對於仍在中被參考的專案應該是可還原的, 以避免中斷它們。 同樣地, delisted NuGet 套件仍然存在, 而且可以還原。 它們不會繼續更新。
