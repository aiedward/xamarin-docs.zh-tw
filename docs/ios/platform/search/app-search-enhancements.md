---
title: 應用程式搜尋 Xamarin.iOS 中的增強功能
description: 本文件涵蓋的增強功能 Apple iOS 10，以及如何實作它們 Xamarin.iOS 中，已進行的應用程式內搜尋。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 06c405a15c26e02908d609bc27cac2c0509e5028
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787880"
---
# <a name="app-search-enhancements-in-xamarinios"></a>應用程式搜尋 Xamarin.iOS 中的增強功能

_本文件涵蓋的增強功能 Apple iOS 10，以及如何實作它們 Xamarin.iOS 中，已進行的應用程式內搜尋。_

在 iOS 10 中，Apple 對數個增強功能的應用程式搜尋，例如 Crowdsourced 深層連結、 在應用程式搜尋，搜尋接續和視覺效果的驗證結果。 本文將討論 Xamarin.iOS 應用程式中實作這些功能。

## <a name="about-app-search-enhancements"></a>關於應用程式搜尋的增強功能

在 iOS 10 中的核心精選應用程式搜尋提供數個增強功能，例如：

- **（與差異隱私權） Crowdsourced 深層連結普及率**-提供方法來升級深層連結應用程式在搜尋結果中的內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內 Spotlight 搜尋功能類似郵件、 訊息和附註的應用程式的運作方式。
- **搜尋接續**-可讓使用者在精選或 Safari，開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-可讓共用中 （透過訊息應用程式擴充功能） 的訊息出現在 Spotlight 搜尋所提供的常用在應用程式映像。

下列各節將討論更多詳細資料中的下列主題。

## <a name="crowdsourced-deep-link-popularity"></a>Crowdsourced 深層連結的人氣指數

iOS 10 提供一個機制，計數頻率到應用程式的熱門深層連結後面的使用者，同時仍受到保護的使用者身分識別，藉由使用這項資訊來改善應用程式的順位的搜尋結果中的內容*差異隱私權*。

用於應用程式的使用`NSUserActivity`提供深層連結 Url，並讓物件`EligibleForPublicIndexing`屬性設定為`true`，iOS 10 送出的子集*差異隱私權雜湊*Apple 的伺服器。 這項資訊接著用於升級搜尋結果中的常用在應用程式內容。

如需有關如何實作 Xamarin.iOS 應用程式深層連結的詳細資訊，請參閱我們[搜尋 NSUserActivity](~/ios/platform/search/nsuseractivity.md)文件。

## <a name="in-app-searching"></a>應用程式內搜尋

藉由實作新[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)類別，應用程式可以提供精選的搜尋和尋找本身內，使用者不需將應用程式 （類似於如何郵件、 訊息和附註應用程式的內容比對規則技術工作）。

一般而言，應用程式，支援`CSSearchQuery`不需要維護自己的個別的搜尋索引。 

## <a name="search-continuation"></a>搜尋接續

在 iOS 9，Apple 導入了搜尋應用程式開發介面 (例如核心精選`NSUserActivity`和 web 標記) 來提供深度的喜好的內容，以允許使用者使用精選和 Safari 搜尋介面該內容中搜尋應用程式中。 請參閱我們[新搜尋 Api](~/ios/platform/search/index.md)文件以取得詳細資料。

在 iOS 中 10 個 Apple 根據這項功能允許 Spotlight 或 Safari 中, 啟動搜尋，然後繼續進行搜尋，應用程式中開啟時，使用者。 

若要實作這項功能，請編輯 應用程式的`Info.plist`file、 add`CoreSpotlightContinuation`索引鍵的型別**布林**並將其值設定為`YES`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](app-search-enhancements-images/search01.png "編輯 CoreSpotlightContinuation Info.plist 檔案中")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](app-search-enhancements-images/searchw01.png "編輯 CoreSpotlightContinuation Info.plist 檔案中")](app-search-enhancements-images/search01.png#lightbox)

-----

若要回應使用者繼續搜尋結果 (`NSUserActivity`)，編輯`AppDelegate.cs`檔案，並覆寫`ContinueUserActivity`方法。 例如: 

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

此程式碼會尋找查詢接續動作類型 (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`)，然後再讀取使用者的目前的查詢從`NSUserActivity`類別的使用者資訊字典 (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`)。 從這裡，您必須採取動作來繼續使用者的搜尋應用程式。

如需搜尋 Xamarin.iOS 應用程式中使用的詳細資訊，請參閱我們[核心 Spotlight 搜尋](~/ios/platform/search/corespotlight.md)文件。

## <a name="visualization-of-validation-results"></a>驗證結果的視覺效果

Apple 的[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站的標記和深層連結的視覺表示法 (包括在這類定義標記[Schema.org](http://schema.org/)) 時 preforming 測試。

使用驗證工具，開發人員可以看到 Applebot Web 編目程式，已編製索引例如標題、 描述、 URL 和任何其他站台的資訊支援的項目。

如需使用 Web 標記的詳細資訊，請參閱我們[Web 標記的 搜尋](~/ios/platform/search/web-markup.md)文件。

## <a name="message-app-image-sharing"></a>共用的訊息應用程式影像

如果訊息的應用程式擴充功能提供映像的訊息中的共用，擴充功能可以設定為允許使用者不需離開應用程式即可執行常用的映像從訊息中的 Spotlight 搜尋中。

若要啟用這項功能，執行下列作業：

1. 建立訊息應用程式擴充功能。
2. 新增`com.apple.developer.associated-domains`至應用程式的權利，包括裝載共用訊息應用程式擴充功能的映像的網路網域的清單。 針對每個網域中，指定`spotlight-image-search`服務。
3. 新增`apple-app-site-association`網站裝載映像檔案。 這個檔案包含的字典`spotlight-image-search`服務，並包含應用程式的識別碼，也就是小組 ID 或應用程式識別碼前置詞後面接著套件組合識別碼。 檔案可以包含最多 500 名的路徑和模式，將以精選編製索引，而且包含在受歡迎的映像搜尋。 如需詳細資訊，請參閱 Apple[建立和上傳關聯檔案](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)文件。
4. 允許 Applebot 来編目的網站。 請參閱 Apple[有關 Applebot](https://support.apple.com/HT204683)文件。

請參閱我們[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)文件以取得詳細資料。

## <a name="summary"></a>總結

這篇文章已涵蓋增強功能 Apple iOS 10，以及如何實作它們 Xamarin.iOS 中，已進行的應用程式內搜尋。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
