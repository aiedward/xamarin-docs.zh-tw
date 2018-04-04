---
title: 資源和資料存放區
description: 本文件涵蓋使用資源和永續性資料儲存在 Xamarin.tvOS 應用程式。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b4d96ef50498b454da583a955169b9d51c29dd01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="resources-and-data-storage"></a>資源和資料存放區

_本文件涵蓋使用資源和永續性資料儲存在 Xamarin.tvOS 應用程式。_

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS 資源限制

不同於 iOS 裝置，新的 Apple TV 提供非常有限的持續性的本機儲存體 tvOS 應用程式或資料。 非常小的項目 （例如使用者喜好設定），如 tvOS 應用程式仍然可以存取`NSUserDefaults`與[500 KB 的資料限制](https://forums.developer.apple.com/message/50696#50696)。 不過，如果 Xamarin.tvOS 應用程式需要更大量的資訊會保存，它必須儲存和擷取該資料從[iCloud](#iCloud-Data-Storage)。

此外，tvOS Apple TV 應用程式設為 200 MB 的大小限制。 如果您的應用程式需要超過此大小的資源，他們必須是封裝，並且使用載入[視資源](#On-Demand-Resources)（最多額外的 2 GB)。 提供這些限制，務必您正確時間下載的其他資產的應用程式的使用者提供最佳體驗。 如需詳細資訊，請參閱 Apple[視資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>非持續的下載項目

每個 tvOS 應用程式會提供它的其他資源和資產下載到暫存快取目錄。 只要應用程式仍在執行中，將會保留此目錄。 不過，在 Apple TV 需求以釋出空間給其他應用程式或系統使用，可以刪除此快取。

如此一來，您的應用程式不能依賴的下次啟動時無法使用先前下載的內容。 Xamarin.tvOS 應用程式一定要檢查所需的資源存在並視需要下載它們。

> [!IMPORTANT]
> 雖然您可以下載其他資產和所需資源，Apple 警告針對取用所有的空間，您的應用程式快取中，因為它會導致無法預期的結果。




<a name="Managing-Resources" />

## <a name="managing-resources"></a>管理資源

如上所述，因為有限，非持續性儲存資訊供 tvOS 應用程式，這些限制都需要仔細規劃，以建立絕佳的使用者經驗 Xamarin.tvOS 應用程式。

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>iCloud 資料存放區

由於 Apple TV 上的儲存體皆有限，不只是非常有限的持續性的本機儲存體，您的應用程式有不保證任何先前下載的資訊將可執行下一次。

如此一來，Xamarin.tvOS 應用程式必須將任何使用者資料儲存在資料存放區 iCloud。 Apple 提供 tvOS 應用程式的兩個 icloud 的功能為基礎的資料儲存的選項：

- **索引鍵 / 值儲存體 (KVS) iCloud** -小型的資訊 (小於 1 MB)，您的應用程式可能需要 （例如使用者喜好設定），您可以使用 iCloud KVS 儲存體。 iCloud KVS 資料會自動同步到雲端，而且所有執行相同的應用程式的使用者的裝置。 如需詳細資訊，請參閱[機碼值的儲存體](~/ios/data-cloud/introduction-to-icloud.md)區段我們[icloud 的功能簡介](~/ios/data-cloud/introduction-to-icloud.md)文件或 Apple 的[設計 icloud 的功能中的索引鍵-值資料](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)文件。
- **CloudKit** -存放裝置的較大的部分 （大於 1 MB） 的資訊，請使用 Apple 的 CloudKit 架構。 不同於 iCloud KVS 儲存體，您可以在您的應用程式 （以及在單一使用者的私用） 的所有使用者之間共用 CloudKit 資料。 如需詳細資訊，請參閱我們[簡介 CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)文件或 Apple 的[CloudKit 快速入門](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)。

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>視資源

視資源會提供應用程式內容和資產 （不同於應用程式套件組合），且裝載於應用程式市集下載所需的應用程式。 最多額外的 2 GB 的資料可提供使用隨資源。 它們可讓較小的初始應用程式下載 （tvOS 應用程式都限制為 200 MB 的最大值），同時又能提供豐富的資產，視需要。

當 tvOS 應用程式要求在要求資源時，系統將自動管理的下載與這個應用程式的快取目錄內容的儲存體。 您的應用程式必須等到此內容要下載並供才能繼續執行。

這些資源可能會繼續在整個多個啟動的應用程式，因此加速啟動週期 Apple TV 被快取。 不過，您的應用程式不能依賴的下次啟動時無法使用任何先前已下載內容。 請參閱[非持續下載](#Non-Persistent-Downloads)節以取得詳細資料。

您可以使用 Xcode 來建立指定資源標記相關聯 （例如所有資產的遊戲的層級 2） 的相關內容的組合。 稍後您的應用程式會要求隨資源，藉由指定此資源標記。 您的應用程式應該指出該內容對使用者顯示 UI 正在下載。 如需詳細資訊，請參閱 Apple[視資源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 應該小心平衡點之間的應用程式已經下載視資源的次數以及個別下載的大小。 使用者可能會變成您的應用程式 」，如果遊戲設定下載新內容經常中斷，或單一下載花太多時間。




<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋依據 tvOS 系統 Xamarin.tvOS 應用程式的大小、 資源和資料儲存體限制。 它有顯示若要解決這些限制和建議，以建立應用程式的使用者經驗選項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
