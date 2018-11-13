---
title: 部署和測試 xamarin 的 watchOS 應用程式
description: 本文件說明如何部署和測試以 Xamarin 建置的 watchOS 應用程式。 它提供的部署檢查清單，然後討論明確和萬用字元應用程式識別碼，並探討應用程式群組。
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7d626b8a968835813d87c93e3cead57a00c14000
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528594"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>部署和測試 xamarin 的 watchOS 應用程式

## <a name="deployment-checklist"></a>部署檢查清單

無論您是部署至測試監看式，或上傳至 App Store，您需要完成的步驟，在此頁面上：

- 在  **iOS 開發人員中心**:
  - [應用程式識別碼](#App_IDs)已建立。
  - [應用程式群組](#App_Groups)設定 （如有必要）。
  - 發佈佈建設定檔建立

- 在您的解決方案：

  - 請確認[套件組合識別碼 」 和 「 專案參考](~/ios/watchos/get-started/installation.md)設定。
  - 檢查您的圖示是否[正確設定](~/ios/watchos/app-fundamentals/icons.md)。
  - 請檢查所有專案中的套件組合版本數字相符。
  - 設定**Entitlements.plist**應用程式群組 （如有必要）。

* 然後遵循指示：
  - [為測試部署的 Apple Watch](~/ios/watchos/deploy-test/device.md)，或
  - [上傳至 App Store](~/ios/watchos/deploy-test/appstore.md)。

<a name="App_IDs"/>

## <a name="app-ids"></a>應用程式識別碼

中所述[安裝指示](~/ios/watchos/get-started/installation.md)，Watch 應用程式中的所有三個專案的相關套件組合識別碼，例如：

- Xamarin.iOS 統一專案- `com.xamarin.WatchKitCatalog`
- WatchKit 擴充功能專案- `com.xamarin.WatchKitCatalog.watchkitextension`
- 監看式應用程式專案- `com.xamarin.WatchKitCatalog.watchkitapp`

這三個專案需要比對發佈佈建設定檔，使用明確的應用程式識別碼，針對每個或萬用字元應用程式識別碼。

### <a name="explicit-app-ids"></a>明確的應用程式識別碼

建立**應用程式識別碼**每個專案的套件組合識別碼 （這看起來像這樣在 ios 開發人員中心）：

![IOS 開發人員中心中的套件組合識別碼](images/appids-specific-sml.png)

當建立或設定應用程式識別碼，請記得啟用您的應用程式需要特定的功能。 這可能包括推播通知和應用程式群組。

您必須為每個應用程式識別碼。 建立發佈佈建設定檔

### <a name="wildcard-app-id"></a>萬用字元應用程式識別碼

或者，您可以在其中建立萬用字元**應用程式識別碼**符合所有的三個專案，例如`com.xamarin.*`。

請注意，某些功能無法搭配萬用字元應用程式識別碼 （例如推播通知）。 如果您的應用程式額外需要這些功能，您應該建立明確的應用程式識別碼。

散發，您只需要建立一個發佈佈建設定檔的萬用字元應用程式識別碼。

<a name="App_Groups" />

## <a name="app-groups"></a>應用程式群組

您可以使用 「 應用程式群組共用您的 iOS 應用程式與監控擴充功能之間的資料。 您應該確定您的方案有：

- 設定**應用程式群組**在 Apple 開發人員入口網站**憑證、 識別碼與設定檔**一節。

- 已啟用**應用程式群組**(並提供**應用程式群組識別碼**) 中*兩者*iOS 應用程式與監控擴充功能的**應用程式識別碼**和**Entitlements.plist**。

### <a name="certificates-identifiers--profiles"></a>憑證、 識別碼與設定檔

若要使用 「 應用程式群組，建立中的項目**應用程式群組**螢幕。 在下列範例中的群組會命名為使用相同的反向 DNS 樣式即常用的應用程式識別碼，但與`group.`前置詞 （這是必要）：

![識別項](images/appgroups-new-sml.png)

應用程式群組即會出現在清單中：

![識別項清單](images/appgroups-setup-sml.png)

一旦建立群組時，它可以參考在您**應用程式識別碼**組態。 將它包含這兩個 iOS 應用程式與監控擴充功能，請記得**應用程式識別碼**。

![可用的組態](images/appgroups-sml.png)

請勿**不**啟用應用程式群組中的 Apple Watch 應用程式識別碼。 您不需要在 watch 本身上啟用。

### <a name="entitlementsplist"></a>Entitlements.plist

（例如某些應用程式功能。 應用程式群組） 會要求您先設定權利。
按兩下以編輯**Entitlements.plist**在這些專案中的檔案：

- iOS 應用程式專案
- 監看式延伸模組專案

。![Entitlements.plist 編輯器](images/entitlements-plist-sml.png)

請勿**不**啟用 Watch 應用程式專案中的權利。 您不需要在 watch 本身上啟用。

## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
