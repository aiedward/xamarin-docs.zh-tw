---
title: 在 Xamarin 中使用 NSUserActivity 搜尋
description: 本檔說明如何編制 NSUserActivity 索引，使其在焦點和 Safari 中可供搜尋。 它會討論如何在搜尋結果中回應 NSUserActivity 的選取專案。
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 29ccf115facf9a086db473301f7dfb548a80dc9f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431112"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>在 Xamarin 中使用 NSUserActivity 搜尋

`NSUserActivity` 是在 iOS 8 中引進的，用來提供資料來進行交付。
它可讓您在應用程式的特定部分中建立活動，然後將這些活動傳遞給在不同 iOS 裝置上執行的另一個應用程式實例。 然後，接收的裝置就可以繼續在先前裝置上啟動的活動，向右挑選使用者離開的位置。 如需有關使用遞交的詳細資訊，請參閱我們的 [交付檔簡介](~/ios/platform/handoff.md) 。

IOS 9 的新手， `NSUserActivity` 可 (公開和私用的) ，並從焦點搜尋和 Safari 搜尋。 藉由將 `NSUserActivity` 視為可搜尋並新增可編制索引的中繼資料，活動可以列在 iOS 裝置上的搜尋結果中。

[![應用程式歷程記錄總覽](nsuseractivity-images/apphistory01.png)](nsuseractivity-images/apphistory01.png#lightbox)

如果使用者選取的搜尋結果屬於您的應用程式中的活動，則會啟動應用程式，並將會重新開機所描述的活動並 `NSUserActivity` 向使用者顯示。

下列屬性 `NSUserActivity` 是用來支援應用程式搜尋：

- `EligibleForHandoff` –如果 `true` 為，則此活動可用於提交作業。
- `EligibleForSearch` –如果為 `true` ，則會將此活動新增至裝置上的索引，並顯示在搜尋結果中。
- `EligibleForPublicIndexing` –如果為 `true` ，則會將此活動新增至 Apple 的雲端式索引，並向使用者顯示在其 iOS 裝置上尚未安裝您的應用程式的 (via 搜尋) 。 如需詳細資料，請參閱下面的「 [公用搜尋索引](#public-search-indexing) 」一節。
- `Title` –提供活動的標題，並顯示在搜尋結果中。 使用者也可以搜尋標題本身的文字。
- `Keywords` –這是用來描述活動的字串陣列，該活動會由使用者進行編制索引並可供搜尋。
- `ContentAttributeSet` –是 `CSSearchableItemAttributeSet` 用來進一步描述您的活動，並在搜尋結果中提供豐富的內容。
- `ExpirationDate` –如果您只想要顯示特定日期的活動，您可以在此提供日期。
- `WebpageURL` –如果可在 web 上查看活動，或您的應用程式支援 Safari 的深層連結，您可以在這裡設定連結。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入門

請遵循下列指示， `NSUserActivity` 在您的應用程式中執行可搜尋：

- [建立活動類型識別碼](#creatingtypeid)
- [建立活動](#createactivity)
- [回應活動](#respondactivity)
- [公開搜尋索引](#indexing)

使用來讓您的內容可供搜尋，還有一些 [額外的優點](#benefits) `NSUserActivity` 。

<a name="creatingtypeid"></a>

## <a name="creating-activity-type-identifiers"></a>建立活動類型識別碼

建立搜尋活動之前，您必須先建立 _活動類型識別碼_ 來識別它。 活動類型識別碼是加入至應用程式 plist 檔案陣列的簡短字串， `NSUserActivityTypes` 用**Info.plist**來唯一識別指定的使用者活動類型。 應用程式支援的每個活動都會有一個專案，而且會對應用程式搜尋公開此專案。 

Apple 建議對活動類型識別碼使用反向 DNS 樣式表示法，以避免發生衝突。 例如： `com.company-name.appname.activity` 適用于特定應用程式的活動 `com.company-name.activity` ，或可跨多個應用程式執行的活動。

建立 `NSUserActivity` 實例以識別活動類型時，會使用活動類型識別碼。 當某個活動因為使用者按下搜尋結果而繼續時，活動類型 (以及應用程式的小組識別碼) 會判斷要啟動哪個應用程式才能繼續活動。

若要建立必要的活動類型識別碼以支援此行為，請編輯 **plist** 檔案，並切換至 **來源** 視圖。 新增 `NSUserActivityTypes` 金鑰，並以下列格式建立識別碼：

[![Plist 編輯器中的 NSUserActivityTypes 索引鍵和必要的識別碼](nsuseractivity-images/type01.png)](nsuseractivity-images/type01.png#lightbox)

在上述範例中，我們為搜尋活動建立了一個新的活動類型識別碼 (`com.xamarin.platform`) 。 建立您自己的應用程式時，請將陣列的內容取代為 `NSUserActivityTypes` 應用程式支援的活動專屬的活動類型識別碼。

<a name="createactivity"></a>

## <a name="creating-an-activity"></a>建立活動

以下範例示範如何為裝載搜尋的裝置索引建立活動：

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

我們可以設定的屬性，以新增更多詳細資料 `ContentAttributeSet` `NSUserActivity` ，如下所示：

[![新增搜尋詳細資料總覽](nsuseractivity-images/apphistory02.png)](nsuseractivity-images/apphistory02.png#lightbox)

藉由使用 `ContentAttributeSet` ，您可以建立豐富的搜尋結果，以吸引終端使用者與其互動。

<a name="respondactivity"></a>

## <a name="responding-to-an-activity"></a>回應活動

若要回應使用者按下搜尋結果 (`NSUserActivity`) 針對我們的應用程式，請編輯 **AppDelegate.cs** 檔案，並覆寫 `ContinueUserActivity` 方法。 例如：

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

請注意，這是用來回應交付要求的相同方法覆寫。 現在，如果使用者按一下焦點搜尋結果中的應用程式連結，我們的應用程式將會進入前景 (或啟動（如果尚未執行) ，而且會顯示該連結所代表的內容、流覽或功能）：

[![從搜尋還原先前的狀態](nsuseractivity-images/apphistory03.png)](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing"></a>

## <a name="public-search-indexing"></a>公開搜尋索引

如前文所述，iOS 9 可讓您輕鬆地提供搜尋存取權，讓使用者在指定的 iOS 裝置上找到並使用應用程式內容和功能。 使用公用索引時，iOS 9 提供一種方式，讓尚未探索到內容或功能 (或尚未安裝應用程式) 的使用者也能在搜尋中取得這些結果。

公用索引的運作方式如下：

1. 當您為應用程式建立活動時，可以將它標示為公用。
2. 公用活動會傳送至 Apple，並在雲端中編制索引。
3. 當有更多使用者在裝置上與您的應用程式互動，並使用活動所代表的特定功能或內容時，就會提高排名。
4. 受歡迎的公用結果將可供其他使用者使用，即使他們未安裝應用程式。
5. 如果活動包含) 的 URL，這些公開結果將會顯示在焦點搜尋和 Safari (中。

我們可以取得我們在上面建立的私用搜尋活動，並將其展開為公用：

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

因為活動已設定為透過設定進行公用索引 `EligibleForPublicIndexing = true` ，所以不表示它會自動加入至 Apple 的公用雲端索引。 必須先符合下列條件：

1. 它必須出現在搜尋結果中，並且由許多使用者選取。 結果會保持私用，直到達到活動參與閾值為止。
2. 應用程式布建可防止將任何使用者專屬資料編制索引，並將其設為公用。

<a name="benefits"></a>

## <a name="additional-benefits"></a>其他優點

藉由 `NSUserActivity` 在您的應用程式中採用應用程式搜尋，您也可以取得下列功能：

- **提交** -因為應用程式搜尋會使用與 () 的相同機制來公開內容、導覽及/或功能 `NSUserActivity` ，所以您可以輕鬆地讓應用程式的使用者在一部裝置上啟動活動，並在另一個裝置上繼續進行。
- **Siri 建議** -除了 Siri 建議一般進行的標準建議之外，還可以自動建議您應用程式的在職員工。
- **Siri 智慧型提醒** -使用者將能夠要求 Siri 提醒他們有關您應用程式的活動。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Blog post & 範例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)