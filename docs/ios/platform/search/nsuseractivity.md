---
title: 具有 NSUserActivity 搜尋
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 803fcce359bbe27ea19901afa766f5b7f4692e0c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="search-with-nsuseractivity"></a>具有 NSUserActivity 搜尋

`NSUserActivity` iOS 8 中引進，用來提供遞移式的資料。
它可讓您建立活動，您可以接著傳遞另一個執行個體在不同的 iOS 裝置上執行的應用程式的應用程式的特定部分中。 然後接收裝置，可以繼續先前在裝置上，收取右邊使用者離開的地方啟動活動。 如需有關使用遞移式的詳細資訊，請參閱我們[簡介遞交](~/ios/platform/handoff.md)文件。

Ios 9，新`NSUserActivity`可以編製索引 （公開和私人），並從 Spotlight 搜尋和 Safari 搜尋。 標示`NSUserActivity`為可搜尋和新增索引的中繼資料，活動會列在 iOS 裝置上的搜尋結果中。

[![](nsuseractivity-images/apphistory01.png "應用程式記錄概觀")](nsuseractivity-images/apphistory01.png#lightbox)

如果使用者選取從您的應用程式所屬的活動搜尋結果，就會啟動應用程式和活動所描述`NSUserActivity`會重新啟動，並向使用者顯示。

下列屬性的`NSUserActivity`用來支援應用程式內搜尋：

 - `EligibleForHandoff` – 如果`true`，此活動可遞移式作業中。
 - `EligibleForSearch` – 如果`true`，這個活動會新增到裝置上的索引，並呈現在搜尋結果中。
 - `EligibleForPublicIndexing` – 如果`true`，這個活動將會加入至 Apple 以雲端為基礎的索引，並呈現給使用者 （透過搜尋） 已經安裝您的應用程式在其 iOS 裝置上。 請參閱[公用搜尋索引](#Public-Search-Indexing)下面章節以取得詳細資料。
 - `Title` – 提供您活動的標題，而且會顯示在搜尋結果中。 使用者也可以搜尋本身標題的文字。
 - `Keywords` – 是用來描述您的活動，會由終端使用者進行搜尋和編製索引的字串陣列。
 - `ContentAttributeSet` – 為`CSSearchableItemAttributeSet`來進一步說明您的活動詳細資料，並提供豐富的內容，在搜尋結果中。
 - `ExpirationDate` – 如果您要的活動，才會顯示為給定的日期，您可以提供該日期。
 - `WebpageURL` – 如果活動都可在網站上，或是您的應用程式支援 Safari 的深層連結，您可以設定要造訪以下連結。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入門

請遵循下列指示來實作可搜尋`NSUserActivity`應用程式中：

- [建立活動型別識別項](#creatingtypeid)
- [建立活動](#createactivity)
- [回應活動](#respondactivity)
- [公用搜尋索引](#indexing)

有一些[額外的好處](#benefits)使用`NSUserActivity`讓搜尋您的內容。

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>建立活動型別識別項

您可以建立搜尋活動之前，您必須先建立_活動型別識別項_來識別它。 活動類型識別項是簡短字串加入至`NSUserActivityTypes`陣列的應用程式的**Info.plist**檔案用來唯一識別指定的使用者活動型別。 每個應用程式支援，而且會公開給應用程式內搜尋的活動在陣列中會有一個項目。 

Apple 建議使用的活動類型識別項的反向 dns 標記法，避免發生衝突。 例如：`com.company-name.appname.activity`以特定的應用程式為基礎的活動或`com.company-name.activity`跨多個應用程式可以執行的活動。

活動類型識別項建立時會使用`NSUserActivity`識別的活動類型的執行個體。 當使用者點選搜尋結果的結果接續活動時，活動類型 （以及應用程式的小組識別碼） 會決定繼續活動啟動哪些應用程式。

若要建立必要的活動型別識別碼，以支援這個行為，請編輯**Info.plist**檔案，並切換至**來源**檢視。 新增`NSUserActivityTypes`鍵，然後建立識別項，格式如下：

[![](nsuseractivity-images/type01.png "NSUserActivityTypes 索引鍵和 plist 編輯器中所需的識別項")](nsuseractivity-images/type01.png#lightbox)

在上述範例中，我們建立一個新活動類型的識別碼搜尋活動 (`com.xamarin.platform`)。 當建立您自己的應用程式，來取代原始的內容`NSUserActivityTypes`陣列活動類型識別碼的活動特定應用程式支援。

<a name="createactivity" />

## <a name="creating-an-activity"></a>建立活動

建立用來裝載的裝置上索引搜尋的活動的範例如下：

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

我們無法將進一步詳細資料設定`ContentAttributeSet`屬性我們`NSUserActivity`，如下所示：

[![](nsuseractivity-images/apphistory02.png "新增搜尋詳細資料概觀")](nsuseractivity-images/apphistory02.png#lightbox)

使用`ContentAttributeSet`您可以建立一項誘使使用者與其互動的豐富的搜尋結果。

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>回應活動

若要回應使用者點選搜尋結果 (`NSUserActivity`) 應用程式中，編輯**d**檔案，並覆寫`ContinueUserActivity`方法。 例如: 

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

請注意，這是用來回應遞交要求相同的方法覆寫。 現在如果使用者按一下連結，以從我們 Spotlight 搜尋結果中的應用程式時，我們的應用程式，將前景 （或啟動如果尚未執行） 和內容、 瀏覽或功能，由該連結將顯示：

[![](nsuseractivity-images/apphistory03.png "搜尋從還原先前的狀態")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>公用搜尋索引

如我們所見上方，iOS 9 容易提供搜尋應用程式內容和存取權的使用者已探索到並使用指定的 iOS 裝置上功能。 具有公用的索引，iOS 9 的方法可為使用者提供者尚未探索到的內容或功能尚未 （或甚至沒有安裝應用程式） 太取得搜尋結果中的這些結果。

公用索引的運作方式如下：

1. 當您建立您的應用程式的活動時您可以將它標示為公用。
2. 公用活動會傳送至 Apple，並在雲端中建立索引。
3. 當更多使用者與裝置上的應用程式互動，並使用特定功能或由活動的內容，它會上升陣序。
4. 常用的公用結果會提供給其他使用者，即使他們沒有安裝的應用程式。
5. 這些公用的結果會顯示在 Spotlight 搜尋和 Safari （如果活動包含 URL）。

我們可以使用我們在上方，建立私用搜尋活動，並展開它必須是公用：

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

因為已設定公用設定編製索引的某項活動`EligibleForPublicIndexing = true`，它並不表示，它就會自動加入至 Apple 的公用雲端的索引。 必須先符合下列條件：

1. 它必須出現在搜尋結果，而且許多使用者會選取。 之前已符合的活動 engagement 臨界值，則結果會維持私用。
2. 應用程式佈建可防止編製索引及公開任何使用者特定的資料。

<a name="benefits" />

## <a name="additional-benefits"></a>額外的好處

採用透過應用程式搜尋`NSUserActivity`中應用程式，您也可以取得下列功能：

- **遞交**-因為應用程式內搜尋公開 (expose) 的內容，瀏覽及/或功能使用相同的機制，與遞移式 (`NSUserActivity`)，您可以輕鬆地讓您的應用程式啟動一部裝置上的活動，並繼續在另一台的使用者。
- **Siri 建議**-Siri 建議通常使標準的建議，以及在職員工從您的應用程式可以自動建議。
- **Siri 智慧提醒**-使用者可以要求 Siri 提醒他們有關從您的應用程式的活動。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式內搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [部落格文章 （& s) 範例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
