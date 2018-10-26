---
title: 在 Xamarin.iOS 中 NSUserActivity 搜尋
description: 本文件說明如何編製索引的 NSUserActivity，使得它成為可搜尋的焦點以及 Safari。 它討論如何回應的 NSUserActivity 搜尋結果中選取。
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: acfff90b4b983f92718bb9af1f587a73ec0f8da7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104254"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>在 Xamarin.iOS 中 NSUserActivity 搜尋

`NSUserActivity` iOS 8 中引進，並用來遞移式提供的資料。
它可讓您建立在您接著可以傳遞在不同的 iOS 裝置上執行的應用程式的另一個執行個體的應用程式的特定部分的活動。 接收端裝置則可以繼續上先前的裝置，挑選正確的使用者會離開啟動活動。 如需有關使用遞移式的詳細資訊，請參閱我們[遞移式簡介](~/ios/platform/handoff.md)文件。

IOS 9 的新手`NSUserActivity`可以編製索引 （公開和私密） 並搜尋 Spotlight 搜尋與 Safari。 標示`NSUserActivity`為可搜尋並新增可編製索引中繼資料，活動可以在 iOS 裝置上的搜尋結果中列出。

[![](nsuseractivity-images/apphistory01.png "應用程式歷程記錄概觀")](nsuseractivity-images/apphistory01.png#lightbox)

如果使用者選取屬於 從您的應用程式的 活動搜尋結果，將會啟動應用程式和活動所描述`NSUserActivity`會重新啟動，並向使用者顯示。

下列屬性的`NSUserActivity`用來支援應用程式內搜尋：

 - `EligibleForHandoff` – 如果`true`，此活動可以用於遞移式作業。
 - `EligibleForSearch` – 如果`true`，此活動會新增至裝置上的索引，並顯示在搜尋結果中。
 - `EligibleForPublicIndexing` – 如果`true`，此活動會新增至 Apple 的雲端式索引，並向您尚未安裝您的應用程式在其 iOS 裝置的使用者 （透過搜尋）。 請參閱[公開搜尋索引編製](#Public-Search-Indexing)節以取得詳細資料。
 - `Title` – 提供您活動的標題，並顯示在搜尋結果中。 使用者也可以搜尋標題本身的文字。
 - `Keywords` – 是用來描述您的活動，會編製索引，並由使用者可搜尋的字串陣列。
 - `ContentAttributeSet` – 是`CSSearchableItemAttributeSet`用來進一步說明您的活動詳細資料，並提供豐富的內容，在搜尋結果中。
 - `ExpirationDate` – 如果您要用來只會顯示指定日期的活動時，您可以提供該的日期。
 - `WebpageURL` – 如果活動可以在網頁上檢視，或如果您的應用程式支援 Safari 的深層連結，您可以設定瀏覽以下的連結。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入門

請遵循下列指示來實作可搜尋`NSUserActivity`應用程式中：

- [建立活動型別識別項](#creatingtypeid)
- [建立活動](#createactivity)
- [回應活動](#respondactivity)
- [公用的搜尋索引編製](#indexing)

有一些[額外的好處](#benefits)使用`NSUserActivity`，讓內容可搜尋。

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>建立活動型別識別項

您可以建立搜尋活動之前，您必須建立_活動型別識別項_來識別它。 活動的型別識別項是簡短字串加入至`NSUserActivityTypes`的應用程式的陣列**Info.plist**檔案用來唯一識別指定的使用者活動型別。 對於每個應用程式支援，並公開給應用程式內搜尋的活動陣列中會有一個項目。 

Apple 建議使用的活動型別識別項的反向 DNS 樣式標記法來避免發生衝突。 例如：`com.company-name.appname.activity`以特定的應用程式為基礎的活動或`com.company-name.activity`可以跨多個應用程式執行的活動。

建立時，會使用活動的型別識別項`NSUserActivity`識別的活動類型的執行個體。 當使用者點選搜尋結果的結果，接續的活動時，活動類型 （以及應用程式的小組識別碼） 會決定哪些應用程式，以啟動來繼續活動。

若要建立必要的活動型別識別項，以支援此行為，請編輯**Info.plist**檔案，並切換至**來源**檢視。 新增`NSUserActivityTypes`索引鍵，並建立識別項，格式如下：

[![](nsuseractivity-images/type01.png "Plist 編輯器中所需的識別項與 NSUserActivityTypes 金鑰")](nsuseractivity-images/type01.png#lightbox)

在上述範例中，我們會建立一個新的活動型別識別項，「 搜尋 」 活動 (`com.xamarin.platform`)。 當建立您自己的應用程式，來取代原始的內容`NSUserActivityTypes`陣列與特定活動的活動 」 型別識別項。 您的應用程式支援。

<a name="createactivity" />

## <a name="creating-an-activity"></a>建立活動

建立裝載的裝置上索引的搜尋活動的範例如下：

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

我們可以新增進一步的詳細資料設定`ContentAttributeSet`屬性的我們`NSUserActivity`，如下所示：

[![](nsuseractivity-images/apphistory02.png "新增搜尋詳細資料概觀")](nsuseractivity-images/apphistory02.png#lightbox)

使用`ContentAttributeSet`您可以建立豐富的搜尋結果，誘使使用者與它們互動。

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>回應活動

若要回應使用者點選的搜尋結果 (`NSUserActivity`)，在我們的應用程式中，編輯**AppDelegate.cs**檔案，並覆寫`ContinueUserActivity`方法。 例如: 

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

請注意，這是用來回應遞移式要求相同的方法覆寫。 現在如果使用者按一下連結，以從我們的應用程式在 Spotlight 搜尋結果中，我們的應用程式將會置於前景 （或如果尚未執行） 並將顯示的內容、 瀏覽或該連結代表的功能：

[![](nsuseractivity-images/apphistory03.png "從搜尋還原先前的狀態")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>公用的搜尋索引編製

如我們所見上方，iOS 9 輕鬆地提供搜尋應用程式內容與使用者已探索到並在指定的 iOS 裝置上使用的功能的存取權。 具有公用的索引時，iOS 9，請提供使用者者尚未探索到的內容或功能尚未 （或甚至還沒有安裝應用程式） 的方式太取得其搜尋中的這些結果。

公用編製索引的運作方式如下：

1. 當您建立應用程式的活動時您可以將它標示為公用。
2. 公用的活動會傳送至 Apple，並在雲端中編製索引。
3. 更多使用者互動的裝置上應用程式，並使用特定功能或由活動的內容，它會上升陣序。
4. 熱門的公用結果可以提供給其他使用者，即使他們不需要安裝的應用程式。
5. 這些公用的結果會顯示在 Spotlight 搜尋和 Safari 中 （如果活動包含 URL）。

我們可以採取，前面所建立的私用搜尋活動並將其公開展開：

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

活動已設定公用設定編製索引時，就算`EligibleForPublicIndexing = true`，它並不表示，它將會自動加入至 Apple 的公用雲端的索引。 必須先符合下列條件：

1. 它必須出現在搜尋結果，而且有多位使用者選取。 結果仍然私用，直到達到活動 engagement 臨界值。
2. 應用程式佈建可防止任何使用者特定的資料編製索引和公開。

<a name="benefits" />

## <a name="additional-benefits"></a>其他優點

藉由採用透過應用程式搜尋`NSUserActivity`應用程式中，您也取得下列功能：

- **遞交**-由於應用程式搜尋所公開的內容，瀏覽及/或功能為遞移式使用相同的機制 (`NSUserActivity`)，您可以輕鬆地讓您的應用程式使用者啟動一個裝置上的活動，並繼續在另一個。
- **Siri 建議**-Siri 建議通常使標準的建議，以及從您的應用程式的在職員工可自動建議。
- **Siri 智慧提醒**-使用者能夠要求 Siri 提醒他們從您的應用程式的活動。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [部落格文章和範例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
