---
title: 統一 Google Play 服務元件和 NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351479"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>統一 Google Play 服務元件和 NuGet

## <a name="history"></a>歷程

比起以前幾個 Google Play 服務元件和 NuGet 套件：

-   Google Play 服務 (Froyo)
-   Google Play 服務 (Gingerbread)
-   Google Play 服務 」 (ICS)
-   Google Play 服務 (JellyBean)
-   Google Play 服務 (KitKat)

Google 實際上只隨附兩個.jar 檔案 Google Play 服務：

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

因為未正確地告訴我們的工具，存在差異`aapt.exe`已使用指定的應用程式的最大的資源 API 層級。 這表示如果我們嘗試在較低的 API 層級，例如 Gingerbread 上使用 Google Play 服務 (KitKat) 繫結，我們會收到編譯錯誤。

## <a name="unifying-google-play-services"></a>統一 Google Play 服務

在較新版本的 Xamarin.Android 中，我們現在告訴`aapt.exe`若要使用，因此這個問題就會消失的最大資源版本。

這表示，沒有任何對 Gingerbread/ICS/JellyBean/KitKat （不過我們仍需要個別的繫結 froyo 因為它是完全不同的.jar 檔案） 中的個別封裝的實際原因。

為了方便開發人員，我們已現在整合我們的元件和 NuGet 套件分成兩個：

-   Google Play 服務 (Froyo) (繫結`google-play-services-froyo.jar`)
-   Google Play 服務 (繫結`google-play-services.jar`)

### <a name="which-one-should-be-used"></a>應該使用哪一個？

在幾乎每個案例中，您應該使用 Google Play 服務。 若要使用 (Froyo) 封裝的唯一原因是如果您目前的目標 Froyo。 此獨立的.jar 檔案存在，將來自 Google 的唯一理由是因為 Froyo 位於這類一小部分的裝置，他們自行決定要停止支援，因此此.jar 檔案的 Google Play 服務的已凍結，不支援快照集。

### <a name="note-about-gingerbread"></a>關於 Gingerbread 附註

Gingerbread 並沒有支援預設的片段，因為這個緣故，某些繫結中的類別將無法使用在執行階段 Gingerbread 裝置上的應用程式中。 類別類似`MapFragment`不適用於 Gingerbread，以及其支援 variant 應該改用`SupportMapFragment`。 它是由開發人員知道要使用時。 此不相容情況是由 Google 其 Google Play 服務文件中所述。

### <a name="what-happens-to-the-old-componentsnugets"></a>舊元件/NuGet 會發生什麼事？

由於不再需要我們必須停用/Delisted 下列元件/Nuget:

-   Google Play 服務 (Gingerbread)
-   Google Play 服務 (JellyBean)
-   Google Play 服務 (KitKat)

現有_Google Play 服務 」 (ICS)_ 已重新命名為的元件/Nuget _Google Play 服務_和會保持在最新狀態從現在開始。 應該更新所有參考其中一個已停用/Delisted 封裝的專案，以使用這個指令碼。

已停用的元件仍存在，而且應該可還原的已針對它們仍然參考中，以避免中斷它們的專案。 同樣地 delisted 的 NuGet 套件仍然存在，並進行還原。 它們將不會更新從現在開始。
