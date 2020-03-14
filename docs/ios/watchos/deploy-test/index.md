---
title: 使用 Xamarin 部署和測試 watchOS 應用程式
description: 本檔說明如何部署和測試以 Xamarin 建立的 watchOS 應用程式。 其中提供部署檢查清單、討論明確和萬用字元應用程式識別碼，並查看應用程式群組。
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: b9b4d201e02d60bd6131c8693d9ac6a233e4fe10
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304916"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>使用 Xamarin 部署和測試 watchOS 應用程式

## <a name="deployment-checklist"></a>部署檢查清單

無論您是部署到測試監看式或上傳至 App Store，都需要完成此頁面上的步驟：

- 在**IOS 開發人員中心**：
  - 已建立[應用程式識別碼](#App_IDs)。
  - 已設定的[應用程式群組](#App_Groups)（如有需要）。
  - 已建立發佈布建設定檔

- 在您的解決方案中：

  - 確認已設定套件組合[識別碼和專案參考](~/ios/watchos/get-started/installation.md)。
  - 檢查您的圖示是否已[正確設定](~/ios/watchos/app-fundamentals/icons.md)。
  - 檢查所有專案中的套件組合版本號碼是否相符。
  - 設定應用程式群組的**plist** （如有必要）。

- 然後依照指示進行：
  - [部署到 Apple Watch 進行測試](~/ios/watchos/deploy-test/device.md)，或
  - [上傳至 App Store](~/ios/watchos/deploy-test/appstore.md)。

<a name="App_IDs"/>

## <a name="app-ids"></a>應用程式識別碼

如[安裝指示](~/ios/watchos/get-started/installation.md)中所述，監看式應用程式中的所有三個專案都有相關的套件組合識別碼，例如：

- Xamarin iOS 整合專案-`com.xamarin.WatchKitCatalog`
- WatchKit 擴充功能專案-`com.xamarin.WatchKitCatalog.watchkitextension`
- 監看應用程式專案-`com.xamarin.WatchKitCatalog.watchkitapp`

這三個專案都需要相符的散發布建設定檔，不論是使用明確的應用程式識別碼或萬用字元應用程式識別碼。

### <a name="explicit-app-ids"></a>明確的應用程式識別碼

針對每個專案的套件組合識別碼（在 iOS 開發人員中心上看起來如下）建立**應用程式識別碼**：

![IOS 開發人員中心內的配套識別碼](images/appids-specific-sml.png)

建立或設定應用程式識別碼時，請記得啟用您的應用程式所需的特定功能。 這可能包括推播通知和應用程式群組。

您將需要為每個應用程式識別碼建立發佈布建設定檔。

### <a name="wildcard-app-id"></a>萬用字元應用程式識別碼

或者，您可以建立一個萬用字元**應用程式識別碼**，以符合這三個專案，例如 `com.xamarin.*`。

請注意，某些功能無法與萬用字元應用程式識別碼（例如推播通知）搭配使用。 如果您的應用程式需要這些功能，您應該建立明確的應用程式識別碼。

針對散發，您只需要為萬用字元應用程式識別碼建立一個發佈布建設定檔。

<a name="App_Groups" />

## <a name="app-groups"></a>應用程式群組

您可以使用應用程式群組，在您的 iOS 應用程式與 Watch 擴充功能之間共用資料。 您應該確定您的解決方案具有：

- 已在 Apple 開發人員入口網站的 [**憑證]、[識別碼 & 設定檔**] 區段中設定**應用程式群組**。

- 在 iOS 應用程式和監看式延伸模組的**應用程式識別碼**和**權利. plist***中，* 啟用**應用程式群組**（並提供**應用程式群組識別碼**）。

### <a name="certificates-identifiers--profiles"></a>憑證、識別碼 & 設定檔

若要使用應用程式群組，請在 [**應用程式群組**] 畫面中建立專案。 在下面的範例中，群組是使用與應用程式識別碼常用的相同反向 DNS 樣式來命名，但使用 `group.` 前置詞（這是必要的）：

![識別碼](images/appgroups-new-sml.png)

應用程式群組就會出現在清單中：

![識別碼清單](images/appgroups-setup-sml.png)

建立群組之後，即可在您的**應用程式識別碼**設定中加以參考。 請記得同時包含 iOS 應用程式和監看式擴充功能**應用程式識別碼**。

![可用的設定](images/appgroups-sml.png)

請勿在 Apple Watch 應用程式識別碼**中啟用應用**程式群組。 不需要在監看式本身上啟用。

### <a name="entitlementsplist"></a>Entitlements.plist

某些應用程式功能（例如 應用程式群組）會要求您設定您的權利。
按兩下以編輯這些專案中的**plist**檔案：

- iOS 應用程式專案
- 監看延伸模組專案

。![Plist 編輯器](images/entitlements-plist-sml.png)

請勿在監看式應用程式專案**中啟用權利**。 不需要在監看式本身上啟用。

## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
