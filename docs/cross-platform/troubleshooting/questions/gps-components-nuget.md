---
title: 元件和 NuGet 統一 Google Play 服務
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.openlocfilehash: cfd417f4fc01b07b4334259c45472eb24b73abd8
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="unifying-google-play-services-components-and-nuget"></a>元件和 NuGet 統一 Google Play 服務

### <a name="history"></a>歷程

那里使用數個 Google 播放服務元件並 NuGet 封裝：

-   Google Play 服務 (Froyo)
-   Google Play 服務 (Gingerbread)
-   Google Play 服務 (ICS)
-   Google Play 服務 (JellyBean)
-   Google Play 服務 (KitKat)

Google 實際上只隨附兩個 d 檔案 Google Play 服務：

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

因為未正確地告訴我們的工具，存在於差異`aapt.exe`是用於給定的應用程式的最大資源應用程式開發介面層級。 這表示如果我們嘗試在較低的應用程式開發介面層級，例如 Gingerbread 上使用 Google Play 服務 (KitKat) 繫結，我們會收到編譯錯誤。

### <a name="unifying-google-play-services"></a>統一的 Google Play 服務

在較新版本的 Xamarin.Android，我們現在要求`aapt.exe`若要使用，因此我們會隨即消失此問題最大資源版本。

這表示，沒有任何理由 Gingerbread/ICS/JellyBean/KitKat （不過我們仍然需要個別的繫結的 Froyo 因為它是完全不同檔案） 中的個別封裝。

為了方便開發人員，我們已現在統一我們元件和 NuGet 套件分成兩個：

-   Google Play 服務 (Froyo) (繫結`google-play-services-froyo.jar`)
-   Google Play 服務 (繫結`google-play-services.jar`)

### <a name="which-one-should-be-used"></a>應該使用哪一個？

在大多數情況下，應該使用 Google Play 服務。 若要使用 (Froyo) 封裝的唯一理由是如果您的主動目標 Froyo。 此個別檔案存在，將來自 Google 的唯一理由是因為 Froyo 有這類一小部分的裝置，他們自行決定要停止支援，因此這個檔案是 Google Play 服務的已凍結、 不支援快照集。

### <a name="note-about-gingerbread"></a>關於 Gingerbread 附註

Gingerbread 沒有依預設，支援的片段，因為這個緣故，某些繫結中的類別都無法使用在執行階段 Gingerbread 裝置上的應用程式中。 類別`MapFragment`不適用於 Gingerbread，和其支援 variant 應該改用`SupportMapFragment`。 它是最多要知道哪些使用開發人員。 由 Google 註這個不相容性其 Google Play 服務文件中。

### <a name="what-happens-to-the-old-componentsnugets"></a>舊元件/NuGet 的發生什麼事？

因為不再需要我們已停用/Delisted 下列元件/NuGets:

-   Google Play 服務 (Gingerbread)
-   Google Play 服務 (JellyBean)
-   Google Play 服務 (KitKat)

現有_Google 播放服務 (ICS)_ Nuget 元件/重新命名為_Google Play 服務_並將保存在最新狀態從現在開始。 所有專案參考其中一個已停用/Delisted 封裝應該都更新為使用這個帳戶。

已停用的元件仍存在，而且應該是可還原的專案仍然參考這些中，以避免破壞它們。 同樣地 delisted 的 NuGet 封裝仍然存在，而且可以進行還原。 它們不會更新接下來。
