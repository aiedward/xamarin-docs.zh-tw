---
title: 在 Xamarin 中 tvOS 資源和資料存放區
description: 本文說明如何在以 Xamarin 建立的 tvOS 應用程式中使用資源和持續性資料儲存。 它討論 iCloud 資料儲存體和隨選資源。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 8619fa73a4dbaabe1e161c634b6a794b701d5135
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570853"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>在 Xamarin 中 tvOS 資源和資料存放區

_本文涵蓋在 tvOS 應用程式中使用資源和持續性資料儲存。_

<a name="tvOS-Resource-Limitations"></a>

## <a name="tvos-resource-limitations"></a>tvOS 資源限制

不同于 iOS 裝置，新的 Apple TV 針對 tvOS 應用程式或資料提供非常有限的持續性本機儲存體。 對於非常小的專案（例如使用者喜好設定），您的 tvOS 應用程式仍可存取， `NSUserDefaults` [限制為 500 KB 的資料](https://forums.developer.apple.com/message/50696#50696)。 不過，如果您的 tvOS 應用程式需要保存較大量的資訊，它就必須從[iCloud](#iCloud-Data-Storage)儲存和取出該資料。

此外，tvOS 會將 Apple TV 應用程式的大小限制為 200 MB。 如果您的應用程式需要超過此大小的資源，則必須使用[隨選資源](#On-Demand-Resources)（最多2個）來封裝和載入它們。 基於這些限制，請務必正確地下載額外的資產，以提供最佳的應用程式使用者體驗。 如需詳細資訊，請參閱 Apple 的[隨選資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads"></a>

## <a name="non-persistent-downloads"></a>非持續性下載

每個 tvOS 應用程式都會提供一個暫存快取目錄，其中會將其額外的資源和資產下載至其中。 只要應用程式仍在執行中，就會保留此目錄。 不過，當 Apple 電視需要釋出空間供其他應用程式或系統使用時，可以刪除此快取。

如此一來，您的應用程式就無法依賴先前下載的內容，以在下次啟動時使用。 您的 tvOS 應用程式應該一律檢查所需的資源是否存在，並視需要下載。

> [!IMPORTANT]
> 雖然您可以視需要下載其他資產和資源，但 Apple 會警告您在應用程式快取中使用所有的空間，因為這可能會導致無法預期的結果。

<a name="Managing-Resources"></a>

## <a name="managing-resources"></a>管理資源

如上所述，因為可供 tvOS 應用程式使用的資訊有限、非持續性儲存，所以這些限制需要謹慎規劃，才能為您的 tvOS 應用程式建立絕佳的使用者體驗。

<a name="iCloud-Data-Storage"></a>

### <a name="icloud-data-storage"></a>iCloud 資料存放區

由於 Apple 電視上的儲存空間有限，而不只是有非常有限的持續性本機儲存體，因此，您的應用程式不保證在下一次執行之前下載的任何資訊都可供使用。

因此，您的 tvOS 應用程式必須在 iCloud 資料存放區中儲存任何使用者資料。 Apple 針對您的 tvOS apps 提供兩個 iCloud 型資料儲存選項：

- **ICloud 金鑰-值儲存體（KVS）** -對於您的應用程式可能需要的小型資訊（少於1mb）（例如使用者喜好設定），您可以使用 ICloud KVS 儲存體。 iCloud KVS 資料會自動同步處理到雲端，以及所有使用者執行相同應用程式的裝置。 如需詳細資訊，請參閱[Icloud 簡介](~/ios/data-cloud/introduction-to-icloud.md)檔的索引[鍵/值儲存](~/ios/data-cloud/introduction-to-icloud.md)區一節，或 Apple[針對 Icloud 檔中的索引鍵/值資料的設計](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)。
- **CloudKit** -若要儲存較大的資訊（大於1mb），請使用 Apple 的 CloudKit 架構。 不同于 iCloud KVS 儲存體，可以在應用程式的所有使用者之間共用 CloudKit 資料（以及對單一使用者的私用）。 如需詳細資訊，請參閱我們[的 CloudKit 檔簡介](~/ios/data-cloud/intro-to-cloudkit.md)或 Apple 的[CloudKit 快速入門](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="On-Demand-Resources"></a>

### <a name="on-demand-resources"></a>隨選資源

隨選資源提供應用程式內容和資產（獨立于應用程式套件組合），會裝載于應用程式存放區，並視您應用程式的需要下載。 最多可使用隨選資源來提供額外的 2 GB 資料。 它們可讓初始應用程式下載較小（tvOS 應用程式最多可達 200 MB），同時仍會提供所需的豐富資產。

當 tvOS 應用程式要求隨選資源時，系統會自動管理將此內容下載並儲存至應用程式的快取目錄。 您的應用程式必須等候此內容下載完成，並可供使用，才能繼續。

這些資源可能會繼續在整個應用程式的多個啟動時，在 Apple 電視上快取，進而加速啟動迴圈。 不過，您的應用程式無法依賴任何先前下載的內容，以在下次啟動時使用。 如需詳細資訊，請參閱上面的[非持續性下載](#Non-Persistent-Downloads)一節。

您可以使用 Xcode 來建立與提供資源標記相關聯的相關內容組合（例如，遊戲層級2的所有資產）。 稍後，您的應用程式會藉由指定此資源標記來要求隨選資源。 您的應用程式應該會向使用者呈現 UI，指出正在下載內容。 如需詳細資訊，請參閱 Apple 的[隨選資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 請小心在應用程式必須下載隨選資源的次數和個別下載的大小之間取得適當的平衡。 如果遊戲經常中斷以下載新的內容，或如果單次下載花費太多時間，使用者可能會對您的應用程式感到挫折。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋了 tvOS 系統在 tvOS 應用程式上所放置的大小、資源和資料儲存限制。 它已提供解決這些限制和建議的選項，可為您的應用程式建立絕佳的使用者體驗。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
