---
title: 在 Xamarin 中使用 NSUserActivity 進行搜尋
description: 本檔說明如何編制 NSUserActivity 的索引，使其可在焦點和 Safari 中進行搜尋。 它討論如何在搜尋結果中回應 NSUserActivity 的選取專案。
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: c6ceb6e10abc4dbd26bffecbb6fefa5835f3d630
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031547"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>在 Xamarin 中使用 NSUserActivity 進行搜尋

`NSUserActivity` 是在 iOS 8 中引進，用來提供用於遞交的資料。
它可讓您在應用程式的特定部分中建立活動，然後將其傳遞至另一個在不同 iOS 裝置上執行的應用程式實例。 然後接收的裝置就可以繼續在先前裝置上啟動的活動，並在使用者離開的位置向右挑選。 如需有關使用遞交的詳細資訊，請參閱我們的[遞交簡介](~/ios/platform/handoff.md)檔。

IOS 9 的新手，`NSUserActivity` 可以編制索引（公開或私下），並從焦點搜尋和 Safari 進行搜尋。 藉由將 `NSUserActivity` 標示為可搜尋並新增可編制索引的中繼資料，活動可以列在 iOS 裝置上的搜尋結果中。

[![](nsuseractivity-images/apphistory01.png "The App History overview")](nsuseractivity-images/apphistory01.png#lightbox)

如果使用者從您的應用程式選取屬於活動的搜尋結果，則會啟動應用程式，並將 `NSUserActivity` 所描述的活動重新開機並呈現給使用者。

以下是用來支援應用程式搜尋的 `NSUserActivity` 屬性：

- `EligibleForHandoff` –如果 `true`，此活動可以用於遞交作業。
- `EligibleForSearch` –如果 `true`，此活動將會新增至裝置上的索引，並顯示在搜尋結果中。
- `EligibleForPublicIndexing` –如果 `true`，此活動將會新增至 Apple 的雲端式索引，並呈現給尚未在其 iOS 裝置上安裝應用程式的使用者（透過 [搜尋]）。 如需詳細資訊，請參閱下方的[公用搜尋索引](#public-search-indexing)一節。
- `Title` –提供活動的標題，並顯示在搜尋結果中。 使用者也可以搜尋標題本身的文字。
- `Keywords` –是用來描述您的活動的字串陣列，而使用者會對其編制索引並讓其可供搜尋。
- `ContentAttributeSet` –是用來進一步描述您的活動，並在搜尋結果中提供豐富內容的 `CSSearchableItemAttributeSet`。
- `ExpirationDate` –如果您只想要在特定日期顯示活動，您可以在這裡提供該日期。
- `WebpageURL` –如果可在 web 上查看活動，或您的應用程式支援 Safari 的深層連結，您可以設定連結以前往這裡流覽。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入門

請遵循下列指示，在您的應用程式中執行可搜尋的 `NSUserActivity`：

- [建立活動類型識別碼](#creatingtypeid)
- [建立活動](#createactivity)
- [回應活動](#respondactivity)
- [公用搜尋索引](#indexing)

使用 `NSUserActivity` 來讓您的內容可供搜尋，還有一些[額外的好處](#benefits)。

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>建立活動類型識別碼

建立搜尋活動之前，您必須先建立_活動類型識別碼_來識別它。 活動類型識別碼是一個簡短字串，已新增至應用程式**plist**檔案的 `NSUserActivityTypes` 陣列，用來唯一識別指定的使用者活動類型。 在陣列中，應用程式支援的每個活動都會有一個專案，並公開至應用程式搜尋。 

Apple 建議針對活動類型識別碼使用反向 DNS 樣式的標記法，以避免發生衝突。 例如： `com.company-name.appname.activity` 特定應用程式的活動，或可跨多個應用程式執行之活動的 `com.company-name.activity`。

建立 `NSUserActivity` 實例以識別活動類型時，會使用活動類型識別碼。 當使用者按下搜尋結果而繼續執行活動時，活動類型（以及應用程式的小組識別碼）會決定要啟動哪一個應用程式，以繼續進行活動。

若要建立必要的活動類型識別碼以支援此行為，請編輯**plist**檔案，並切換至**來源**視圖。 新增 `NSUserActivityTypes` 金鑰，並以下列格式建立識別碼：

[![](nsuseractivity-images/type01.png "The NSUserActivityTypes key and required identifiers in the plist editor")](nsuseractivity-images/type01.png#lightbox)

在上述範例中，我們為搜尋活動（`com.xamarin.platform`）建立了一個新的活動類型識別碼。 建立您自己的應用程式時，請將 `NSUserActivityTypes` 陣列的內容取代為您的應用程式支援的活動特有的活動類型識別碼。

<a name="createactivity" />

## <a name="creating-an-activity"></a>建立活動

以下範例示範如何針對裝置上的索引託管搜尋建立活動：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

我們可以藉由設定 `NSUserActivity` 的 `ContentAttributeSet` 屬性來新增進一步的詳細資料，如下所示：

[![](nsuseractivity-images/apphistory02.png "Addition Search Details overview")](nsuseractivity-images/apphistory02.png#lightbox)

藉由使用 `ContentAttributeSet` 您可以建立豐富的搜尋結果，以吸引使用者與他們互動。

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>回應活動

若要回應使用者點擊應用程式的搜尋結果（`NSUserActivity`），請編輯**AppDelegate.cs**檔案，並覆寫 `ContinueUserActivity` 方法。 例如:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

請注意，這是用來回應遞交要求的相同方法覆寫。 現在，如果使用者在焦點搜尋結果中按一下來自應用程式的連結，我們的應用程式將會帶入前景（或已啟動（如果尚未執行），而且會顯示該連結所代表的內容、導覽或功能：

[![](nsuseractivity-images/apphistory03.png "Restore Previous State from Search")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>公用搜尋索引

如先前所見，iOS 9 可讓您輕鬆地提供搜尋存取權給使用者已探索並用於指定 iOS 裝置上的應用程式內容和功能。 使用公用索引時，iOS 9 提供了一種方法，讓尚未探索到內容或功能的使用者（或甚至尚未安裝應用程式）在其搜尋中取得這些結果。

公用索引會以下列方式運作：

1. 當您建立應用程式的活動時，您可以將其標示為公用。
2. 公用活動會傳送至 Apple，並在雲端中編制索引。
3. 當有更多使用者在裝置上與您的應用程式互動，並使用活動所代表的特定功能或內容時，它會依順位順序上升。
4. 即使未安裝應用程式，其他使用者也可以使用受歡迎的公用結果。
5. 這些公開結果會顯示在焦點搜尋和 Safari 中（如果活動包含 URL）。

我們可以使用上面所建立的私用搜尋活動，並將其擴充為公用：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

就是因為設定 `EligibleForPublicIndexing = true`來設定公開索引的活動，並不表示它會自動新增到 Apple 的公用雲端索引。 必須先符合下列條件：

1. 它必須出現在搜尋結果中，並由許多使用者選取。 結果會保持為私用，直到符合活動參與閾值為止。
2. 應用程式布建可防止任何使用者特定的資料進行索引並設為公用。

<a name="benefits" />

## <a name="additional-benefits"></a>其他權益

藉由在應用程式中透過 `NSUserActivity` 採用應用程式搜尋，您也可以取得下列功能：

- **遞交**-由於應用程式搜尋會使用與遞交（`NSUserActivity`）相同的機制來公開內容、流覽及/或功能，因此您可以輕鬆地讓應用程式的使用者在一個裝置上啟動活動，並在另一個裝置上繼續執行。
- **Siri 建議**-以及 Siri 建議正常執行的標準建議，可以自動建議您的應用程式在職員工。
- **Siri 智慧型提醒**-使用者可以要求 Siri 提醒他們來自您應用程式的活動。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [& 範例的 Blog 文章](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
