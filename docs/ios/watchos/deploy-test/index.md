---
title: "部署及測試"
description: "在裝置上測試並上傳至應用程式市集"
ms.topic: article
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: fdd4311072efd5571724fbe00d12a96921054fa2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="deployment-and-testing"></a>部署及測試

## <a name="deployment-checklist"></a>部署檢查清單

無論您是部署至測試監看式，或上傳至應用程式市集，您需要完成這個頁面上的步驟：

- 在**iOS 開發人員中心**:
  - [應用程式識別碼](#App_IDs)已建立。
  - [應用程式群組](#App_Groups)設定 （如有必要）。
  - [*發佈*佈建的設定檔](#Provisioning_Profiles)建立。

- 在您的方案：

  - 確認[配套識別碼和專案參考](~/ios/watchos/get-started/installation.md)設定。
  - 請檢查您的圖示是否[正確設定](~/ios/watchos/app-fundamentals/icons.md)。
  - 請檢查配套版本數字比對所有專案中。
  - 設定**Entitlements.plist**應用程式群組 （如有必要）。

* 然後依照指示：
  - [為測試部署 Apple Watch](~/ios/watchos/deploy-test/device.md)，或
  - [上傳的應用程式市集](~/ios/watchos/deploy-test/appstore.md)。


## <a name="app-ids"></a>應用程式識別碼

中所述[安裝指示](~/ios/watchos/get-started/installation.md)，監看式應用程式中的所有三個專案有與相關套件組合識別碼，例如：

- Xamarin.iOS 整合專案- `com.xamarin.WatchKitCatalog`
- WatchKit 擴充功能專案- `com.xamarin.WatchKitCatalog.watchkitextension`
- 監看式應用程式專案- `com.xamarin.WatchKitCatalog.watchkitapp`

所有的三個專案需要比對發佈佈建設定檔，使用明確針對每個，應用程式識別碼或萬用字元應用程式識別碼。

### <a name="explicit-app-ids"></a>明確的應用程式識別碼

建立**應用程式識別碼**每個每個專案的套件組合識別碼 （這看起來像這樣 iOS 開發人員中心上）：

![IOS 開發人員中心中的配套識別碼](images/appids-specific-sml.png)

當建立或設定應用程式識別碼，請記得啟用您的應用程式所需的特定功能。 這可能包括推播通知及應用程式群組。

您必須建立發佈佈建設定檔的每個應用程式識別碼。

### <a name="wildcard-app-id"></a>萬用字元應用程式識別碼

或者，您可以在其中建立萬用字元**應用程式識別碼**符合所有的三個專案，例如`com.xamarin.*`。

請注意，某些功能無法搭配萬用字元應用程式識別碼 （例如推播通知）。 如果您的應用程式額外需要這些功能，您應該建立明確的應用程式識別碼。

針對分佈，您只需要建立一個佈建的散發設定檔萬用字元應用程式識別碼。

<a name="app-groups" />

## <a name="app-groups"></a>應用程式群組

您可以使用應用程式群組共用您的 iOS 應用程式與 Watch 擴充功能之間的資料。 您應該確定您的方案包含：

- 設定**應用程式群組**Apple 開發人員入口網站中**憑證、 識別項與設定檔**> 一節。

- 啟用**應用程式群組**(和提供**應用程式群組識別碼**) 中*兩者*iOS 應用程式和監看式延伸模組的**應用程式識別碼**和**Entitlements.plist**。

### <a name="certificates-identifiers--profiles"></a>憑證、 識別項與設定檔

若要使用應用程式群組，在建立項目**應用程式群組**螢幕。 在下列範例中的群組稱為相同反向 DNS 樣式，通常用於應用程式識別碼，不過多了`group.`前置詞 （這是必要）：

![識別項](images/appgroups-new-sml.png)

應用程式群組即會出現在清單中：

![識別項清單](images/appgroups-setup-sml.png)

一旦建立群組之後，可以參考在您**應用程式識別碼**組態。 請記得要將它包含這兩個 iOS 應用程式和監看式副檔名**應用程式識別碼**。

![可用的 consifurations](images/appgroups-sml.png)

請勿**不**啟用應用程式群組，在 Apple Watch 應用程式識別碼中。 您不需要在 監看式本身上啟用。

### <a name="entitlementsplist"></a>Entitlements.plist

某些應用程式功能 （例如。 應用程式群組） 會要求您設定您的權利。
按兩下以編輯**Entitlements.plist**這些專案檔案中：

- iOS 應用程式專案
- 監看式擴充功能專案

。![Entitlements.plist 編輯器](images/entitlements-plist-sml.png)

請勿**不**啟用監看式應用程式專案中的權利。 您不需要在 監看式本身上啟用。



## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
