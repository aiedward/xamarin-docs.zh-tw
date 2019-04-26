---
title: tvOS 資源並在 Xamarin 中的資料儲存體
description: 本文說明如何使用資源和永續性資料儲存在 tvOS 應用程式中使用 Xamarin 建置的。 它討論 iCloud 資料儲存體或隨選資源。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f8ecc115738fb97f71b4ea6b2cdcc5c2714372d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61208519"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS 資源並在 Xamarin 中的資料儲存體

_本文章涵蓋資源與 Xamarin.tvOS 應用程式中的永續性資料儲存的工作。_

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS 資源限制

不同於 iOS 裝置，新的 Apple TV 提供非常有限的持續性的本機儲存體 tvOS 應用程式或資料。 針對非常小的項目 （例如使用者喜好設定），您的 tvOS 應用程式仍然可以存取`NSUserDefaults`具有[限制為 500 KB 的資料](https://forums.developer.apple.com/message/50696#50696)。 不過，如果 Xamarin.tvOS 應用程式需要保有更大量的資訊，它必須儲存及擷取該資料來源[iCloud](#iCloud-Data-Storage)。

此外，tvOS Apple TV 應用程式設為 200 MB 的大小限制。 如果您的應用程式需要超過此大小的資源，則必須是封裝，並使用載入[隨資源](#On-Demand-Resources)（最多額外的 2 GB)。 指定這些限制，很重要，您正確的時間下載其他資產，以您的應用程式使用者提供最佳的體驗。 如需詳細資訊，請參閱 Apple[隨資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>非持續性的下載項目

每個 tvOS 應用程式會提供其額外的資源和資產下載到暫時快取目錄。 只要應用程式仍在執行中，將會保留此目錄。 不過，Apple TV 需求以釋出空間給其他應用程式或系統使用狀況時，就可以刪除此快取。

如此一來，您的應用程式不能依賴先前下載的內容正在使用下一次啟動。 Xamarin.tvOS 程式一定會檢查所需的資源存在並視需要下載它們。

> [!IMPORTANT]
> 雖然您可以下載其他資產和資源所需的能力，Apple 會在它可能會導致無法預期的結果時，警告針對取用所有您的應用程式快取中的空間。




<a name="Managing-Resources" />

## <a name="managing-resources"></a>管理資源

如上方所述，因為有限，非持續性儲存體的資訊提供給 tvOS 應用程式，這些限制會需要仔細規劃，以建立 Xamarin.tvOS 應用程式的絕佳的使用者體驗。

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>iCloud 資料儲存體

因為在 Apple 電視上的儲存體有限，不只是非常有限的持續性的本機儲存體，您的應用程式必須保證任何先前下載的資訊將可執行的下一次。

如此一來，Xamarin.tvOS 應用程式必須儲存在 iCloud 資料存放區中的任何使用者資料。 Apple 提供兩個您的 tvOS 應用程式的 iCloud 為基礎的資料儲存體選項：

- **iCloud 索引鍵-值存放區 (KVS)** -針對小部分的您的應用程式可能需要 （例如使用者喜好設定），您可以使用 iCloud KVS 儲存體的資訊 (小於 1 MB)。 iCloud KVS 資料會自動同步至雲端，以及所有使用者的裝置執行相同的應用程式。 如需詳細資訊，請參閱[機碼值儲存體](~/ios/data-cloud/introduction-to-icloud.md)一節我們[iCloud 簡介](~/ios/data-cloud/introduction-to-icloud.md)文件或 Apple[設計 iCloud 中的索引鍵-值資料](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)文件。
- **CloudKit** -儲存體的大型部分 （大於 1 MB） 的資訊，請使用 Apple 的 CloudKit 架構。 不同於 iCloud KVS 儲存體，您的應用程式 （以及要為單一使用者的私用） 的所有使用者之間可以共用 CloudKit 資料。 如需詳細資訊，請參閱我們[CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)文件或 Apple [CloudKit 快速入門](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>在隨選資源

視資源會提供應用程式內容和資產 （獨立於應用程式套件組合），且裝載在 App Store 下載所需的應用程式。 最多額外的 2 GB 的資料可提供使用隨資源。 它們可讓較小的初始應用程式下載 （tvOS 應用程式都限制為 200 MB 的最大值），同時又能提供豐富的資產所需。

當 tvOS 應用程式要求視資源時，系統將自動下載並儲存此應用程式的快取目錄的內容管理。 您的應用程式必須等候下載及開放，它才能繼續此內容。

這些資源可能會繼續在整個多個啟動您的應用程式，因此加速啟動週期的 Apple 電視上快取。 不過，您的應用程式不能依賴闕的下次啟動任何先前已下載內容。 請參閱[非持續性下載](#Non-Persistent-Downloads)一節以取得詳細資料。

您可以使用 Xcode 來建立為資源標記相關聯 （例如遊戲的層級 2 的所有資產） 的相關內容的組合。 稍後您的應用程式會要求隨資源，藉由指定這個資源標記。 您的應用程式應該向使用者指出該內容的 UI 正在下載。 如需詳細資訊，請參閱 Apple[隨資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 應該小心找出折衷點的應用程式將有下載隨選資源的次數和個別的下載大小之間的平衡點。 使用者可能會變得無法使用您的應用程式，遊戲不斷地中斷，以下載新內容，則為單一下載花太多時間。




<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋由 tvOS 系統置於 Xamarin.tvOS 應用程式的大小、 資源和資料儲存體限制。 它說明若要解決這些限制和建議來建立絕佳的使用者體驗，您的應用程式的選項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
