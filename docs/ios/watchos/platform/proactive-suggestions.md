---
title: 在 Xamarin 中 watchOS 主動式建議
description: 本文說明如何在 watchOS 3 應用程式中使用主動式建議，藉由允許系統以主動方式自動向使用者呈現有用的資訊，以促進參與。
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 4235e6049b9700edbb3974f1e4cbaf7c405f6e83
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937471"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>在 Xamarin 中 watchOS 主動式建議

_本文說明如何在 watchOS 3 應用程式中使用主動式建議，藉由允許系統以主動方式自動向使用者呈現有用的資訊，以促進參與。_

WatchOS 3 的新手，主動式建議提供新聞方式，讓使用者能夠在適當時間自動向使用者顯示有用的資訊，以便與 Xamarin iOS 應用程式互動。

## <a name="about-proactive-suggestions"></a>關於主動式建議

WatchOS 3 的新功能 `NSUserActivity` 包含 `MapItem` 屬性，可讓應用程式提供可在其他內容中使用的位置資訊。 例如，如果顯示飯店的應用程式審查並提供 `MapItem` 位置，而使用者切換至地圖服務應用程式，則只會提供他們剛觀賞的旅館位置。

應用程式會使用 `NSUserActivity` MapKit、媒體播放機和 UIKit 等技術集合，將此功能公開給系統。 此外，藉由提供應用程式的主動式建議支援，它可免費取得更深入的 Siri 整合。

## <a name="location-based-suggestions"></a>以位置為基礎的建議

WatchOS 3 的新功能： `NSUserActivity` 類別包含 `MapItem` 屬性，可讓開發人員提供可在其他內容中使用的位置資訊。 例如，如果應用程式顯示餐廳審查，則開發人員可以將 `MapItem` 屬性設定為使用者在應用程式中看到的餐廳位置。 如果使用者切換至地圖應用程式，餐廳的位置就會自動可用。

如果應用程式支援應用程式搜尋，它可以使用類別的新位址元件 `CSSearchableItemAttributesSet` 來指定使用者可能想要造訪的位置。 藉由設定 `MapItem` 屬性，其他屬性會自動填入。

除了設定 `Latitude` `Longitude` 位址元件屬性的和之外，建議應用程式 `NamedLocation` 也提供和 `PhoneNumbers` 屬性，讓 Siri 可以起始對位置的呼叫。

## <a name="contextual-siri-reminders"></a>內容相關 Siri 提醒

可讓使用者使用 Siri 快速建立提醒，以在日後查看目前在應用程式中看到的內容。 例如，如果他們在應用程式中看到餐廳審核，他們可以叫用 Siri，並說「*當我取得首頁時提醒我。* 」 Siri 會在應用程式中產生具有評論連結的提醒。

## <a name="implementing-proactive-suggestions"></a>執行主動式建議

在 Xamarin iOS 應用程式中新增主動式建議支援，通常就像執行幾個 Api 一樣簡單，或在應用程式可能已經執行的幾個 Api 上擴充。

主動式建議會以三種主要方式與應用程式搭配使用：

- **`NSUserActivity`**-協助系統瞭解使用者目前在螢幕上使用的資訊。
- **位置建議**-如果應用程式提供或使用以位置為基礎的資訊，這些 API 擴充功能會提供在應用程式之間共用此資訊的新方式。

藉由執行下列動作，在應用程式中支援和：

- 內容**Siri 提醒**-在 iOS 10 中， `NSUserActivity` 已擴充為可讓 Siri 快速提醒您在日後查看目前在應用程式中看到的內容。
- **位置建議**-iOS 10 增強 `NSUserActivity` 了可在應用程式內流覽的位置，並在整個系統的許多地方進行升級。
- 內容**相關的 Siri 要求**  -  `NSUserActivity`提供要 Siri 之應用程式內所呈現資訊的內容，讓使用者可以從應用程式內取得指示或撥打電話 Siri。

所有這些功能都有一個共通的東西，它們全都用 `NSUserActivity` 在一個表單或另一個形式，以提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述， `NSUserActivity` 可協助系統瞭解使用者目前在螢幕上使用的資訊。 `NSUserActivity`是輕量狀態快取機制，可在使用者流覽應用程式時加以捕捉。 例如，查看餐廳應用程式：

[![餐廳應用程式](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

具有下列互動：

1. 當使用者使用應用程式時， `NSUserActivity` 會建立，稍後再重新建立應用程式的狀態。
2. 如果使用者搜尋餐廳，則會遵循建立活動的相同模式。
3. 同樣地，當使用者查看結果時。 在最後一個案例中，使用者正在觀看一個位置，而在 iOS 10 中，系統會更清楚特定概念（例如位置或通訊互動）。

請仔細查看最後一個畫面：

[![NSUserActivity 裝載](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

在這裡，應用程式會建立 `NSUserActivity` ，並已填入資訊，以在稍後重新建立狀態。 應用程式也包含一些中繼資料，例如位置的名稱和位址。 建立此活動之後，應用程式可讓 iOS 知道它代表使用者目前的狀態。

應用程式接著會決定是否要以無線方式針對遞交廣告公告活動、儲存為位置建議的暫時值，或新增至裝置焦點索引，以顯示在搜尋結果中。

如需有關遞交和聚焦搜尋的詳細資訊，請參閱我們的課程[簡介](~/ios/platform/handoff.md)和[IOS 9 新搜尋 api](~/ios/platform/search/index.md)指南。

### <a name="creating-an-activity"></a>建立活動

建立活動之前，需要先建立活動類型識別碼來識別它。 活動類型識別碼是新增至應用程式檔案陣列的簡短字串， `NSUserActivityTypes` `Info.plist` 用來唯一識別指定的使用者活動類型。 在陣列中，應用程式支援的每個活動都會有一個專案，並公開至應用程式搜尋。 如需詳細資訊，請參閱我們的[建立活動類型識別碼參考](~/ios/platform/search/nsuseractivity.md)。

查看活動的範例：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

使用活動類型識別碼建立新的活動。 接下來，會建立一些定義活動的中繼資料，因此可以在日後還原此狀態。 然後，活動會獲得有意義的標題，並附加到使用者資訊。 最後，會啟用某些功能，並將活動傳送給系統。

上述程式碼可以進一步增強，以包含中繼資料，藉由進行下列變更來提供內容給活動：

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

如果開發人員所擁有的網站能夠顯示與應用程式相同的資訊，應用程式可以包含 URL，而且內容可以顯示在未安裝應用程式的其他裝置上（透過遞交）：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>還原活動

若要回應使用者點擊搜尋結果（ `NSUserActivity` ）的應用程式，請編輯**AppDelegate.cs**檔案，並覆寫 `ContinueUserActivity` 方法。 例如：

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

請確定這是與上面所建立活動相同的活動類型識別碼（ `com.xamarin.platform` ）。 應用程式會使用儲存在中的資訊 `NSUserActivity` ，將狀態還原回使用者停止的位置。

### <a name="benefits-of-creating-an-activity"></a>建立活動的優點

透過上述最少量的程式碼，應用程式現在可以利用三項新的 iOS 10 功能：

- **遞交**
- **焦點搜尋**
- **內容相關 Siri 提醒**

下一節將探討如何啟用其他兩個新的 iOS 10 功能：

- **位置建議**
- **內容相關的 Siri 要求**

### <a name="location-based-suggestions"></a>以位置為基礎的建議 

請使用上述餐廳搜尋應用程式的範例。 如果已實 `NSUserActivity` 作用並正確填入所有的中繼資料和屬性，使用者就能夠執行下列動作：

1. 在應用程式中尋找他們想要在其中符合 friend 的餐廳。
2. 如果使用者切換至地圖應用程式，則會自動建議餐廳的位址作為目的地。
3. 這甚至適用于協力廠商應用程式（支援 `NSUserActivity` ），因此使用者可以切換到「分享」應用程式，而餐廳的位址也會自動建議作為目的地。
4. 它也會提供內容給 Siri，讓使用者可以在餐廳應用程式中叫用 Siri，並詢問「*取得方向 ...* 」，而 Siri 會為使用者正在觀賞的餐廳提供方向。

上述所有功能都有一個共通的東西，它們全都指出建議原本來自何處。 在上述範例中，這是虛構的餐廳審查應用程式。

watchOS 3 已增強，可透過幾個對現有架構的小型修改和新增功能，為應用程式啟用這項功能：

- `NSUserActivity`有額外的欄位，可用於捕捉在應用程式內查看的位置資訊。
- 已對 MapKit 和 CoreSpotlight 新增數個專案來取得位置。
- 位置感知功能已新增至系統內的 Siri、地圖、多工和其他應用程式。

若要執行以位置為基礎的建議，請從上面顯示的相同活動程式碼開始：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

如果應用程式使用 MapKit，就像是將目前的對應加入 `MKMapItem` 至活動一樣簡單：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果應用程式未使用 MapKit，它可以採用應用程式搜尋，並為位置指定下列新屬性：

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

如需詳細資訊，請參閱上述程式碼。 首先，每個實例都需要位置的名稱：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然後，文字型實例（如 QuickType 鍵盤）所需的文字型描述：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性的，但請確定使用者已路由傳送至應用程式想要將其傳送到的確切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以取得 Siri 的存取權，讓使用者可以從應用程式叫用 Siri，方法是說： * 「呼叫此位置」。。

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可以指出實例是否適合用於流覽和撥打電話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>活動最佳做法

在使用活動時，Apple 建議下列最佳作法：

- 用於 `NeedsSave` 延遲承載更新。
- 請務必保留對目前活動的強式參考。
- 只傳送包含足夠資訊的小型承載以還原狀態。
- 使用反向 DNS 標記法加以指定，以確定活動類型識別碼是唯一的，而且是描述性的。 

## <a name="consuming-location-suggestions"></a>使用位置建議

下一節將涵蓋來自系統的其他部分（例如 Maps 應用程式）或其他協力廠商應用程式的使用位置建議。

## <a name="routing-apps-and-locations-suggestions"></a>路由應用程式和位置的建議

本節將直接從路由應用程式中查看使用位置建議。 若要讓路由應用程式新增這種功能，開發人員可以利用現有的架構，如下所示 `MKDirectionsRequest` ：

- 以多工方式升級應用程式。
- 將應用程式註冊為路由應用程式。
- 使用 MapKit 物件來處理啟動應用程式 `MKDirectionsRequest` 。
- 讓 watchOS 能夠瞭解如何根據使用者參與來建議應用程式。

以 MapKit 物件啟動應用程式時 `MKDirectionsRequest` ，應該會自動開始將使用者指示提供給要求的位置，或呈現可讓使用者輕鬆開始取得指示的 UI。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }
}
```

請參閱此程式碼的詳細資訊。 它會進行測試，以查看它是否為有效的目的地要求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，則它會 `MKDirectionsRequest` 從 URL 建立：

```csharp
var request = new MKDirectionsRequest(url);
```

WatchOS 3 的新功能，應用程式可以傳送不具有地理座標的位址，因為這會導致開發人員需要將位址編碼：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>總結

本文涵蓋主動式建議，並示範開發人員如何使用它們來驅動 watchOS 的 Xamarin iOS 應用程式流量。 其中涵蓋了實施主動式建議和呈現使用指導方針的步驟。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
