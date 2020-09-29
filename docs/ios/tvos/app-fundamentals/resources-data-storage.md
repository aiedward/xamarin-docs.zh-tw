---
title: 在 Xamarin 中 tvOS 資源和資料儲存區
description: 本文說明如何在以 Xamarin 建立的 tvOS 應用程式中使用資源和持續性資料儲存。 它會討論 iCloud 資料儲存體和隨選資源。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b7d85af558f5665ae46d9a7d05d26badc313a993
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437057"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>在 Xamarin 中 tvOS 資源和資料儲存區

_本文涵蓋在 tvOS 應用程式中使用資源和持續性資料存放區。_

<a name="tvOS-Resource-Limitations"></a>

## <a name="tvos-resource-limitations"></a>tvOS 資源限制

與 iOS 裝置不同的是，新的 Apple TV 針對 tvOS apps 或資料提供極有限的持續性本機儲存體。 針對非常小的專案 (例如使用者喜好設定) ，您的 tvOS 應用程式仍可存取， `NSUserDefaults` 但 [限制為 500 KB 的資料](https://forums.developer.apple.com/message/50696#50696)。 但是，如果您的 tvOS 應用程式需要保存更大量的資訊，則必須儲存並從 [iCloud](#iCloud-Data-Storage)取得該資料。

此外，tvOS 會將 Apple TV 應用程式的大小限制為200MB。 如果您的應用程式需要超過此大小的資源，則必須使用 [隨選資源](#On-Demand-Resources) 將它們封裝和載入， (多達 2 gb 的) 。 由於有這些限制，因此您必須正確地下載額外的資產，以為應用程式的使用者提供最佳體驗。 如需詳細資訊，請參閱 Apple 的 [隨選資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads"></a>

## <a name="non-persistent-downloads"></a>非持續下載

每個 tvOS 應用程式都會提供一個暫存快取目錄，讓其額外的資源和資產下載至其中。 只要應用程式仍在執行中，就會保留此目錄。 不過，因為 Apple TV 需要釋出空間供其他應用程式或系統使用，所以可刪除此快取。

因此，當您的應用程式下次啟動時，就無法依賴先前下載的內容。 您的 tvOS 應用程式應該一律檢查是否存在必要的資源，並視需要下載。

> [!IMPORTANT]
> 雖然您可以視需要下載其他資產和資源，但 Apple 會警告您如何使用應用程式快取中的所有空間，因為這可能會導致無法預期的結果。

<a name="Managing-Resources"></a>

## <a name="managing-resources"></a>管理資源

如上所述，由於 tvOS apps 可使用的資訊有有限的非持續性儲存空間，因此這些限制需要謹慎規劃，以為您的 tvOS 應用程式建立絕佳的使用者體驗。

<a name="iCloud-Data-Storage"></a>

### <a name="icloud-data-storage"></a>iCloud 資料儲存體

由於 Apple TV 上的儲存空間有限，因此不只會有很大的持續性本機儲存空間，您的應用程式也無法保證在下次執行時，將會提供先前下載的任何資訊。

因此，您的 tvOS 應用程式必須將任何使用者資料儲存在 iCloud 資料存放區中。 Apple 針對您的 tvOS 應用程式提供兩個以 iCloud 為基礎的資料儲存體選項：

- **iCloud 索引鍵/值儲存體 (KVS) ** -針對 (小於 1mb) 您的應用程式可能需要 (例如使用者喜好設定) 等的少量資訊，您可以使用 ICloud KVS 儲存體。 iCloud KVS 資料會自動同步至雲端，以及所有執行相同應用程式的使用者裝置。 如需詳細資訊，請[參閱 icloud 檔](~/ios/data-cloud/introduction-to-icloud.md)中的機[碼值儲存體](~/ios/data-cloud/introduction-to-icloud.md)一節，或 Apple 的[金鑰值資料設計](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)。
- **CloudKit** -若要將較大的資訊片段儲存 (大於1mb 的) ，請使用 Apple 的 CloudKit 架構。 不同于 iCloud KVS 儲存體，CloudKit 資料可在您應用程式的所有使用者之間共用 (以及私人給單一使用者) 。 如需詳細資訊，請參閱我們的 CloudKit 檔或 Apple 的[CloudKit 快速入門](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)[簡介](~/ios/data-cloud/intro-to-cloudkit.md)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="On-Demand-Resources"></a>

### <a name="on-demand-resources"></a>隨選資源

隨選資源會提供應用程式內容和資產， (與應用程式套件組合) 分開，這些是裝載在 App Store 上，並視您的應用程式需要下載。 最多可以使用隨選資源來提供最多 2 GB 的資料。 這些應用程式可讓初始應用程式下載較小 (tvOS 應用程式限制為最多 200MB) ，同時仍提供所需的豐富資產。

當 tvOS 應用程式要求隨選資源時，系統會自動管理此內容的下載和儲存至應用程式的快取目錄。 您的應用程式必須等待下載並提供此內容，才能繼續進行。

這些資源可能會在應用程式的多個啟動時繼續在 Apple TV 上快取，從而加速啟動週期。 不過，您的應用程式在下一次啟動時，無法依賴先前下載的任何內容。 如需詳細資料，請參閱上面的 [非持續性下載](#Non-Persistent-Downloads) 一節。

您可以使用 Xcode 來建立相關內容的配套 (例如與「提供資源標記」相關聯之遊戲層級 2) 的所有資產。 稍後，您的應用程式會藉由指定此資源標記來要求隨選資源。 您的應用程式應該向使用者顯示 UI，指出正在下載內容。 如需詳細資訊，請參閱 Apple 的 [隨選資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 請小心在應用程式必須下載隨選資源的次數和個別下載的大小之間取得適當的平衡。 如果遊戲持續中斷以下載新的內容，或單一下載花費太多時間，使用者可能會對您的應用程式感到挫折。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 tvOS 系統在 tvOS 應用程式上放置的大小、資源和資料儲存體限制。 它提供瞭解決這些限制的選項，以及為您的應用程式建立絕佳使用者體驗的建議。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)