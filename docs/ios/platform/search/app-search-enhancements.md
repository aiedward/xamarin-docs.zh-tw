---
title: Xamarin 中的應用程式搜尋增強功能
description: 本文涵蓋 Apple 對 iOS 10 中的應用程式搜尋進行的增強功能，以及如何在 Xamarin 中加以執行。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 0e05c243d2cebe641f77ada013b04198ee754acc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433201"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Xamarin 中的應用程式搜尋增強功能

_本文涵蓋 Apple 對 iOS 10 中的應用程式搜尋進行的增強功能，以及如何在 Xamarin 中加以執行。_

在 iOS 10 中，Apple 已針對應用程式搜尋進行數個增強功能，例如集結群眾力量深層連結、應用程式內搜尋、搜尋接續和驗證結果的視覺效果。 本文將討論如何在 Xamarin iOS 應用程式中執行這些功能。

## <a name="about-app-search-enhancements"></a>關於應用程式搜尋增強功能

IOS 10 中的核心焦點提供數個應用程式搜尋增強功能，例如：

- **使用差異隱私權) 集結群眾力量深層連結熱門程度 (** -提供在搜尋結果中推廣深層連結應用程式內容的方式。
- **應用程式內搜尋** -使用新的 `CSSearchQuery` 類別來提供應用程式內焦點搜尋功能，類似于 Mail、Messages 和 Notes 應用程式的運作方式。
- **搜尋接續** -可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式並繼續進行搜尋。
- **驗證結果** 的視覺效果-Apple 的 [應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool) 現在會在 preforming 測試時，顯示網站標記和深層連結的視覺標記法。
- **訊息應用程式映射共用** -讓提供的熱門應用程式內映射可透過訊息應用程式延伸模組進行共用 (，) 出現在焦點搜尋中。

下列各節將更詳細地討論這些主題。

## <a name="crowdsourced-deep-link-popularity"></a>集結群眾力量深層連結熱門程度

iOS 10 提供一種機制，可計算熱門深層連結至應用程式的頻率，然後使用者會使用這項資訊來改善搜尋結果中應用程式內容的排名，同時仍會使用 *差異隱私權*來保護使用者的身分識別。

如果應用程式使用 `NSUserActivity` 物件來提供深層連結 url，並將 `EligibleForPublicIndexing` 屬性設定為，則 `true` iOS 10 會將 *差異隱私權雜湊* 子集提交給 Apple 的伺服器。 這項資訊接著會用來提升搜尋結果中熱門的應用程式內內容。

如需在 Xamarin iOS 應用程式中執行深層連結的詳細資訊，請參閱 [使用 NSUserActivity 檔的搜尋](~/ios/platform/search/nsuseractivity.md) 。

## <a name="in-app-searching"></a>應用程式內搜尋

藉由執行新的 [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) 類別，應用程式可以提供焦點搜尋和比對規則技術來尋找本身內的內容，而使用者不需要離開應用程式 (類似于 Mail、Messages 和 Notes 應用程式) 的運作方式。

一般而言，支援的應用程式 `CSSearchQuery` 不需要維護自己的個別搜尋索引。

## <a name="search-continuation"></a>搜尋接續

在 iOS 9 中，Apple 引進了搜尋 Api (例如核心焦點 `NSUserActivity` 和 web 標記) ，以提供應用程式內的內容深度，讓使用者能夠使用焦點和 Safari 搜尋介面搜尋該內容。 如需詳細資料，請參閱我們的 [新搜尋 api](~/ios/platform/search/index.md) 檔。

在 iOS 10 中，Apple 建基於這項功能，方法是允許使用者在焦點或 Safari 中開始搜尋，然後在開啟應用程式時繼續搜尋。

若要執行這項功能，請編輯應用程式的檔案 `Info.plist` ，新增 `CoreSpotlightContinuation` **布林** 數值型別的索引鍵，並將其值設定為 `YES` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![在 plist 檔案中編輯 CoreSpotlightContinuation](app-search-enhancements-images/search01.png)](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![在 plist 檔案中編輯 CoreSpotlightContinuation](app-search-enhancements-images/searchw01.png)](app-search-enhancements-images/search01.png#lightbox)

-----

若要回應使用者繼續搜尋結果 (`NSUserActivity`) ，請編輯檔案 `AppDelegate.cs` 並覆寫 `ContinueUserActivity` 方法。 例如：

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

此程式碼會尋找 () 的查詢接續動作類型 `userActivity.ActivityType == CSSearchQuery.ContinuationActionType` ，然後從 `NSUserActivity` 類別的使用者資訊字典中讀取使用者的目前查詢 (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`) 。 從這裡開始，應用程式必須採取行動，才能繼續進行使用者的搜尋。

如需在 Xamarin iOS 應用程式中使用搜尋的詳細資訊，請參閱 [使用核心焦點的搜尋](~/ios/platform/search/corespotlight.md) 檔。

## <a name="visualization-of-validation-results"></a>驗證結果的視覺效果

Apple 的 [應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool) 現在會顯示網站標記和深層連結 (的視覺標記法，包括 Preforming 測試時在 [Schema.org](https://schema.org/)) 定義的標記。

藉由使用驗證工具，開發人員可以看到 Applebot Web 編目程式為網站編制索引的資訊，例如標題、描述、URL 和任何其他支援的元素。

如需有關使用 Web 標記的詳細資訊，請參閱 [使用 Web 標記檔的](~/ios/platform/search/web-markup.md) 搜尋。

## <a name="message-app-image-sharing"></a>訊息應用程式映射共用

如果訊息應用程式延伸模組提供可在訊息中共用的影像，則可以將延伸模組設定為允許使用者在訊息中執行熱門影像的焦點搜尋，而不需要離開應用程式。

若要啟用這項功能，請執行下列動作：

1. 建立訊息應用程式延伸模組。
2. 將加入 `com.apple.developer.associated-domains` 至應用程式的權利，並包含裝載訊息應用程式延伸模組正在共用之映射的 web 網域清單。 針對每個網域，指定 `spotlight-image-search` 服務。
3. 將檔案新增 `apple-app-site-association` 至裝載映射的網站。 此檔案包含服務的字典， `spotlight-image-search` 其中包含應用程式的識別碼，也就是小組識別碼或應用程式識別碼前置詞，後面接著套件組合識別碼。 檔案最多可包含500個路徑，以及將依焦點編制索引並包含在熱門影像搜尋中的模式。 如需詳細資訊，請參閱 Apple 的 [建立和上傳關聯檔案檔](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) 。
4. 允許 Applebot 對網站進行編目。 請參閱 Apple 的 [關於 Applebot](https://support.apple.com/HT204683) 檔。

如需詳細資料，請參閱我們的 [訊息應用程式整合](~/ios/platform/message-app-integration/index.md) 檔。

## <a name="summary"></a>摘要

本文涵蓋了 Apple 在 iOS 10 中對應用程式搜尋的增強功能，以及如何在 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)