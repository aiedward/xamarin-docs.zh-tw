---
title: Xamarin 中的應用程式搜尋增強功能
description: 本文涵蓋 Apple 在 iOS 10 中對應用程式搜尋的增強功能，以及如何在 Xamarin 中執行。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: f0d638d566290dd2ae0d8453133ee340d5b4ce3f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031587"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Xamarin 中的應用程式搜尋增強功能

_本文涵蓋 Apple 在 iOS 10 中對應用程式搜尋的增強功能，以及如何在 Xamarin 中執行。_

在 iOS 10 中，Apple 已對應用程式搜尋進行數項增強，例如集結群眾力量深層連結、應用程式內搜尋、搜尋接續和驗證結果的視覺效果。 本文將討論如何在 Xamarin iOS 應用程式中執行這些功能。

## <a name="about-app-search-enhancements"></a>關於應用程式搜尋增強功能

IOS 10 中的核心焦點為應用程式搜尋提供了數項增強功能，例如：

- **集結群眾力量深層連結的熱門程度（具有差異隱私權）** -提供在搜尋結果中升級深層連結應用程式內容的方法。
- **應用程式內搜尋**-使用新的 `CSSearchQuery` 類別來提供應用程式內焦點搜尋功能，類似于郵件、訊息和便箋應用程式的工作方式。
- **搜尋接續**-允許使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式並繼續進行搜尋。
- **驗證結果的視覺效果**-Apple 的[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站標記的視覺標記法，以及 preforming 測試時的深層連結。
- **訊息應用程式映射共用**-允許提供常用的應用程式內映射，以在訊息中共用（透過訊息應用程式延伸模組），以顯示在焦點搜尋中。

下列各節將更詳細地討論這些主題。

## <a name="crowdsourced-deep-link-popularity"></a>集結群眾力量深層連結的熱門程度

iOS 10 提供了一種機制，可計算熱門的應用程式連結到使用者的頻率，並使用這項資訊來改善搜尋結果中應用程式內容的排名，同時仍可使用差異來保護使用者的身分識別。 *隱私權*。

對於使用 `NSUserActivity` 物件來提供深層連結 Url，並將 `EligibleForPublicIndexing` 屬性設定為 `true`的應用程式，iOS 10 會將*差異隱私權雜湊*的子集提交至 Apple 的伺服器。 這項資訊接著會用來在搜尋結果中升級熱門的應用程式內內容。

如需在 Xamarin iOS 應用程式中執行深層連結的詳細資訊，請參閱[使用 NSUserActivity 的搜尋](~/ios/platform/search/nsuseractivity.md)檔。

## <a name="in-app-searching"></a>應用程式內搜尋

藉由執行新的[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)類別，應用程式可以提供焦點的搜尋和比對規則技術，以尋找本身內的內容，而不需要使用者離開應用程式（類似于郵件、郵件和便箋應用程式的工作方式）。

一般來說，支援 `CSSearchQuery` 的應用程式不需要維護自己的個別搜尋索引。

## <a name="search-continuation"></a>搜尋接續

在 iOS 9 中，Apple 引進了搜尋 Api （例如核心焦點、`NSUserActivity` 和 web 標記），以在應用程式中提供內容的深度，讓使用者能夠使用焦點和 Safari 搜尋介面來搜尋該內容。 如需詳細資訊，請參閱我們的[新搜尋 api](~/ios/platform/search/index.md)檔。

在 iOS 10 中，Apple 建基於這項功能，可讓使用者在焦點或 Safari 中開始搜尋，然後在開啟應用程式時繼續搜尋。

若要執行這項功能，請編輯應用程式的 `Info.plist` 檔案、新增**Boolean**類型的 `CoreSpotlightContinuation` 機碼，並將其值設定為 `YES`：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

-----

若要回應使用者繼續搜尋結果（`NSUserActivity`），請編輯 `AppDelegate.cs` 檔案，並覆寫 `ContinueUserActivity` 方法。 例如:

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

此程式碼會尋找查詢接續動作類型（`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`），然後從 `NSUserActivity` 類別的使用者資訊字典（`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`）讀取使用者目前的查詢。 從這裡開始，應用程式必須採取動作，才能繼續進行使用者的搜尋。

如需在 Xamarin iOS 應用程式中使用搜尋的詳細資訊，請參閱[使用核心焦點的搜尋](~/ios/platform/search/corespotlight.md)檔。

## <a name="visualization-of-validation-results"></a>驗證結果的視覺效果

Apple 的[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站標記的視覺標記法，以及 preforming 測試時的深層連結（包括標記，例如在[Schema.org](https://schema.org/)中定義）。

藉由使用驗證工具，開發人員可以查看 Applebot Web 編目程式為網站編制索引的資訊，例如標題、描述、URL 和任何其他支援的元素。

如需使用 Web 標記的詳細資訊，請參閱[使用 Web 標記的](~/ios/platform/search/web-markup.md)搜尋檔。

## <a name="message-app-image-sharing"></a>訊息應用程式映射共用

如果訊息應用程式延伸模組提供要在訊息中共用的影像，延伸模組可以設定為允許使用者從郵件內執行熱門影像的焦點搜尋，而不需要離開應用程式。

若要啟用這項功能，請執行下列動作：

1. 建立訊息應用程式延伸模組。
2. 將 `com.apple.developer.associated-domains` 新增至應用程式的權利，並包含裝載訊息應用程式延伸模組所共用之影像的 web 網域清單。 針對每個網域，指定 `spotlight-image-search` 服務。
3. 將 `apple-app-site-association` 檔案新增至裝載映射的網站。 此檔案包含 `spotlight-image-search` 服務的字典，並包含應用程式的識別碼，也就是小組識別碼或應用程式識別碼前置詞，後面接著套件組合識別碼。 此檔案最多可包含500個路徑和模式，其將以焦點編制索引，並包含在熱門影像搜尋中。 如需詳細資訊，請參閱 Apple 的[建立和上傳關聯檔案檔](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)。
4. 允許 Applebot 對網站進行編目。 請參閱 Apple 的[關於 Applebot](https://support.apple.com/HT204683)檔。

如需詳細資訊，請參閱我們的[訊息應用程式整合](~/ios/platform/message-app-integration/index.md)檔。

## <a name="summary"></a>總結

本文涵蓋了 Apple 在 iOS 10 中對應用程式搜尋的增強功能，以及如何在 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
