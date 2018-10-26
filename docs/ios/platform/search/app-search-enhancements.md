---
title: 在 Xamarin.iOS 中的應用程式搜尋增強功能
description: 本文章涵蓋的增強功能 Apple 對應用程式搜尋在 iOS 10 及如何在 Xamarin.iOS 中實作它們。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110579"
---
# <a name="app-search-enhancements-in-xamarinios"></a>在 Xamarin.iOS 中的應用程式搜尋增強功能

_本文章涵蓋的增強功能 Apple 對應用程式搜尋在 iOS 10 及如何在 Xamarin.iOS 中實作它們。_

在 iOS 10 中，Apple 已對數個增強功能的應用程式搜尋，例如群眾外包深層連結、 應用程式內搜尋，搜尋接續和視覺效果的驗證結果。 這篇文章將涵蓋在 Xamarin.iOS 應用程式中實作這些功能。

## <a name="about-app-search-enhancements"></a>關於應用程式搜尋增強功能

在 iOS 10 中的核心焦點搜尋應用程式提供數個增強功能，例如：

- **群眾外包深層連結的熱門程度 （含差異隱私權）** -可用來提升搜尋結果中的深層連結應用程式內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內的郵件、 訊息和資訊的應用程式的運作方式類似的 Spotlight 搜尋能力。
- **搜尋接續**-可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-讓 Spotlight 搜尋中出現共用 （透過訊息應用程式的擴充功能） 的訊息中提供的熱門應用程式內映像。

下列各節將討論更多詳細資料中的下列主題。

## <a name="crowdsourced-deep-link-popularity"></a>群眾外包的深層連結的人氣指數

iOS 10 提供一個機制來計算到應用程式的熱門深度連結後面的使用者，同時仍然使用保護使用者的身分識別使用這項資訊來改善應用程式的順位的搜尋結果中的內容的頻率*差異隱私權*。

應用程式的使用`NSUserActivity`物件提供深層連結 Url，並讓`EligibleForPublicIndexing`屬性設定為`true`，iOS 10 提交子集*差異隱私權雜湊*至 Apple 的伺服器。 這項資訊接著會用來提升搜尋結果中的熱門應用程式內容中。

如需有關如何實作在 Xamarin.iOS 應用程式的深層連結的詳細資訊，請參閱我們[NSUserActivity 搜尋](~/ios/platform/search/nsuseractivity.md)文件。

## <a name="in-app-searching"></a>應用程式內搜尋

藉由實作新[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)類別，應用程式可以提供精選的搜尋和比對規則的技術，可尋找內容本身，而不需離開應用程式 （類似於如何郵件、 訊息和附註應用程式的使用者工作）。

一般而言，應用程式，支援`CSSearchQuery`不需要維護其本身，不同的搜尋服務索引。 

## <a name="search-continuation"></a>搜尋接續

在 iOS 9 中，Apple 已引進搜尋 Api (例如核心焦點`NSUserActivity`和 web 標記) 提供的內容，以允許使用者使用 Spotlight 和 Safari 搜尋介面內容中搜尋應用程式中的深度喜好。 請參閱我們[新的搜尋服務 Api](~/ios/platform/search/index.md)文件，如需詳細資訊。

在 iOS 10 的 Apple 組建這項功能允許使用者將焦點或 Safari 中開始搜尋，然後開啟應用程式時，請繼續進行搜尋。 

若要實作這項功能，請編輯 應用程式的`Info.plist`檔案中，新增`CoreSpotlightContinuation`類型的索引鍵**布林**並將其值設定為`YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "編輯 Info.plist 檔案內的 CoreSpotlightContinuation")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "編輯 Info.plist 檔案內的 CoreSpotlightContinuation")](app-search-enhancements-images/search01.png#lightbox)

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

此程式碼會尋找查詢的接續動作類型 (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`)，接著會讀取使用者的目前的查詢，從`NSUserActivity`類別的使用者資訊的字典 (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`)。 從這裡開始，您必須採取動作來繼續使用者的搜尋應用程式。

如需有關使用 Xamarin.iOS 應用程式中的搜尋的詳細資訊，請參閱我們[Core Spotlight 搜尋](~/ios/platform/search/corespotlight.md)文件。

## <a name="visualization-of-validation-results"></a>驗證結果的視覺效果

Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站的標記和深層連結的視覺表示法 (包括在這類定義的標記[Schema.org](http://schema.org/)) 時 preforming 測試。

使用驗證工具，開發人員可以看到 Applebot Web 編目程式，已編製索引的站台，例如標題、 描述、 URL 和任何其他的資訊支援的項目。

如需有關使用 Web 標記的詳細資訊，請參閱我們[搜尋與 Web 標記](~/ios/platform/search/web-markup.md)文件。

## <a name="message-app-image-sharing"></a>訊息共用的應用程式映像

如果訊息的應用程式擴充功能會提供映像共用訊息中，擴充功能可以設定為允許使用者執行 Spotlight 搜尋從常用映像中的訊息，而不必離開應用程式中。

若要啟用這項功能，執行下列作業：

1. 建立訊息的應用程式擴充功能。
2. 新增`com.apple.developer.associated-domains`到應用程式的權利，並包含裝載在共用訊息應用程式擴充功能的映像的 web 網域的清單。 針對每個網域中，指定`spotlight-image-search`服務。
3. 新增`apple-app-site-association`網站裝載的映像的檔案。 此檔案包含的字典`spotlight-image-search`服務，並包含應用程式的識別碼，這是小組識別碼或應用程式識別碼前置詞後面接著 「 套件組合識別碼 」。 此檔案可以包含最多 500 個的路徑和模式，會由焦點編製索引，並納入熱門的映像搜尋。 如需詳細資訊，請參閱 Apple[建立及上傳關聯檔案](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)文件。
4. 允許 Applebot 来編目的網站。 請參閱 Apple[關於 Applebot](https://support.apple.com/HT204683)文件。

請參閱我們[訊息的應用程式整合](~/ios/platform/message-app-integration/index.md)文件，如需詳細資訊。

## <a name="summary"></a>總結

這篇文章已涵蓋的增強功能 Apple 對應用程式搜尋在 iOS 10 及如何在 Xamarin.iOS 中實作它們。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
