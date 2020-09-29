---
title: 在 Xamarin 中 watchOS 主動式建議
description: 本文說明如何在 watchOS 3 應用程式中使用主動式建議，藉由讓系統主動將有用的資訊自動提供給使用者來推動參與。
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 246757ddc43c5480b26ab4c1360a036fd111dcfa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435347"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>在 Xamarin 中 watchOS 主動式建議

_本文說明如何在 watchOS 3 應用程式中使用主動式建議，藉由讓系統主動將有用的資訊自動提供給使用者來推動參與。_

新的 watchOS 3：主動式建議提供新聞，讓使用者能夠在適當的時間，自動向使用者呈現有用的資訊，以與 Xamarin iOS 應用程式互動。

## <a name="about-proactive-suggestions"></a>關於主動式建議

WatchOS 3 的新功能 `NSUserActivity` 包含 `MapItem` 屬性，可讓應用程式提供可在其他內容中使用的位置資訊。 例如，如果顯示旅館的應用程式評論並提供 `MapItem` 位置，則當使用者切換至 Maps 應用程式時，就可以使用他們剛剛觀賞的旅館地點。

應用程式會使用一組技術（例如 `NSUserActivity` 、MapKit、Media Player 和 UIKit）將這項功能公開給系統。 此外，藉由提供應用程式的主動式建議支援，可免費取得更深入的 Siri 整合。

## <a name="location-based-suggestions"></a>以位置為基礎的建議

WatchOS 3 的新功能， `NSUserActivity` 類別包含 `MapItem` 屬性，可讓開發人員提供可在其他內容中使用的位置資訊。 例如，如果應用程式顯示餐廳審核，則開發人員可以將 `MapItem` 屬性設定為使用者在應用程式中所看到餐廳的位置。 如果使用者切換至 Maps 應用程式，則會自動使用餐廳的位置。

如果應用程式支援應用程式搜尋，則可以使用類別的新位址元件 `CSSearchableItemAttributesSet` 來指定使用者可能想要流覽的位置。 藉由設定 `MapItem` 屬性，會自動填入其他屬性。

除了設定 `Latitude` `Longitude` 位址元件屬性的和以外，建議應用程式 `NamedLocation` 也提供和 `PhoneNumbers` 屬性，因此 Siri 可以起始對位置的呼叫。

## <a name="contextual-siri-reminders"></a>內容相關 Siri 提醒

可讓使用者使用 Siri 快速提醒您，在日後查看目前在應用程式中看到的內容。 例如，如果他們在應用程式中看到餐廳評論，他們可以叫用 Siri，然後說「*當我回家時，提醒我這*一點」。 Siri 會產生具有應用程式評論連結的提醒。

## <a name="implementing-proactive-suggestions"></a>實施主動式建議

將主動式建議支援新增至 Xamarin iOS 應用程式，通常就像執行一些 Api，或在應用程式可能已經實行的一些 Api 上擴充一樣簡單。

主動式建議可透過三種主要方式來使用應用程式：

- **`NSUserActivity`** -協助系統瞭解使用者目前在螢幕上使用的資訊。
- **位置建議** -如果應用程式提供或取用以位置為基礎的資訊，則這些 API 擴充功能提供在應用程式之間共用此資訊的新方式。

藉由執行下列動作，在應用程式中支援：

- 內容**Siri 提醒**-在 iOS 10 中，已 `NSUserActivity` 擴充，可讓 Siri 快速地查看目前在應用程式中看到的內容。
- **位置建議** -iOS 10 增強功能，可讓您 `NSUserActivity` 在應用程式內查看位置，並在整個系統中的許多地方推廣這些位置。
- 內容**相關的 Siri 要求**  -  `NSUserActivity`提供應用程式內部所提供資訊的內容給 Siri，讓使用者可以取得指示，或呼叫從應用程式中叫用 Siri 的位置。

所有這些功能都有一個共通的功能，它們都是 `NSUserActivity` 以一種形式使用，或是另一種形式來提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述， `NSUserActivity` 可協助系統瞭解使用者目前在螢幕上使用的資訊。 `NSUserActivity` 是輕量狀態快取機制，可在使用者流覽應用程式時，捕捉使用者的活動。 例如，查看餐廳應用程式：

[![餐廳應用程式](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

具有下列互動：

1. 當使用者使用應用程式時， `NSUserActivity` 會建立，以在稍後重新建立應用程式的狀態。
2. 如果使用者搜尋餐廳，則會遵循相同的建立活動模式。
3. 同樣地，當使用者流覽結果時。 在最後一個案例中，使用者正在觀看位置，而在 iOS 10 中，系統會更清楚特定概念 (例如位置或通訊互動) 。

請仔細查看最後一個畫面：

[![NSUserActivity 承載](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

應用程式會在此建立 `NSUserActivity` ，並已填入資訊以供稍後重新建立狀態。 此應用程式也包含一些中繼資料，例如位置的名稱和位址。 建立此活動之後，應用程式會讓 iOS 知道它代表使用者的目前狀態。

然後，應用程式會決定是否要以無線方式公告以進行遞交、儲存為位置建議的暫存值，或新增至裝置焦點索引以在搜尋結果中顯示。

如需有關遞交和焦點搜尋的詳細資訊，請參閱我們的《 [提交簡介](~/ios/platform/handoff.md) 》和 [IOS 9 新搜尋 api](~/ios/platform/search/index.md) 指南。

### <a name="creating-an-activity"></a>建立活動

建立活動之前，需要建立活動類型識別碼來識別它。 活動類型識別碼是加入至應用程式檔案陣列的簡短字串， `NSUserActivityTypes` `Info.plist` 用來唯一識別指定的使用者活動類型。 應用程式支援的每個活動都會有一個專案，而且會對應用程式搜尋公開此專案。 如需詳細資料，請參閱我們的 [建立活動類型識別碼參考](~/ios/platform/search/nsuseractivity.md) 。

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

使用活動類型識別碼建立新活動。 接下來，會建立一些定義活動的中繼資料，以便日後可以還原此狀態。 然後，活動會獲得有意義的標題，並附加至使用者資訊。 最後，會啟用部分功能，並將活動傳送至系統。

您可以進一步增強上述程式碼，以包含可透過進行下列變更來提供活動內容的中繼資料：

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

如果開發人員所擁有的網站能夠顯示與應用程式相同的資訊，應用程式可以包含 URL，而且內容可以顯示在未透過「遞交)  (安裝應用程式的其他裝置上：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>還原活動

若要回應使用者按下搜尋結果 (`NSUserActivity`) 應用程式，請編輯 **AppDelegate.cs** 檔案，並覆寫 `ContinueUserActivity` 方法。 例如：

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

請確定這與 `com.xamarin.platform` 上面建立的活動 () 相同的活動類型識別碼。 應用程式會使用中儲存的資訊 `NSUserActivity` ，將狀態還原至使用者停止的位置。

### <a name="benefits-of-creating-an-activity"></a>建立活動的優點

由於上述的程式碼數量最少，因此應用程式現在能夠利用三種新的 iOS 10 功能：

- **Handoff**
- **焦點搜尋**
- **內容相關 Siri 提醒**

下一節將探討如何啟用其他兩個新的 iOS 10 功能：

- **位置建議**
- **內容相關的 Siri 要求**

### <a name="location-based-suggestions"></a>以位置為基礎的建議 

採用上述的餐廳搜尋應用程式範例。 如果它已執行 `NSUserActivity` 並正確填入所有中繼資料和屬性，則使用者可以執行下列動作：

1. 在應用程式中尋找想要與朋友見面的餐廳。
2. 如果使用者切換至 Maps 應用程式，則會自動將餐廳的位址建議為目的地。
3. 這甚至適用于支援) 的協力廠商應用程式 (`NSUserActivity` ，因此使用者可以切換到共用的應用程式，而餐廳的位址也會自動建議為目的地。
4. 它也提供 Siri 的內容，讓使用者可以在餐廳應用程式內叫用 Siri，並要求「 *取得方向 ...* 」，Siri 將會提供指示給使用者正在觀看的餐廳。

上述所有功能都有一個共通的功能，它們全都指出建議的原始來源。 在上述範例的案例中，這是虛構的餐廳審核應用程式。

watchOS 3 已增強，可讓應用程式透過數個小型修改和現有架構的新增功能來啟用此功能：

- `NSUserActivity` 具有額外的欄位，可供您用來捕捉在應用程式內查看的位置資訊。
- 已對 MapKit 和 CoreSpotlight 進行數個新增以取得位置。
- 定位感知功能已加入至 Siri、地圖、多工和系統內的其他應用程式。

若要執行以位置為基礎的建議，請從上述的相同活動代碼開始：

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

如果應用程式使用 MapKit，就像將目前的對應新增 `MKMapItem` 至活動一樣簡單：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果應用程式未使用 MapKit，則可採用應用程式搜尋，並為位置指定下列新屬性：

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

請詳細查看上述程式碼。 首先，每個實例都需要位置的名稱：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然後，以文字為基礎的實例所需的文字型描述 (如 QuickType 鍵盤) ：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性的，但請確定使用者會路由傳送至應用程式想要傳送給他們的確切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以取得 Siri 的存取權，讓使用者可以藉由說出像是「撥打電話」之類的內容，從應用程式叫用 Siri：

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可以指出實例是否適用于流覽和通話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>活動的最佳作法

在使用活動時，Apple 建議下列最佳作法：

- 用於 `NeedsSave` 延遲承載更新。
- 確定保留對目前活動的強式參考。
- 只傳送包含足夠資訊的小型承載來還原狀態。
- 請使用反向 DNS 標記法來指定活動類型識別碼，以確保活動類型識別碼是唯一的，而且是描述性的。 

## <a name="consuming-location-suggestions"></a>使用位置建議

下一節將討論來自系統其他部分的取用位置建議 (例如地圖應用程式) 或其他協力廠商應用程式。

## <a name="routing-apps-and-locations-suggestions"></a>路由應用程式和位置的建議

本節將直接從路由應用程式內查看使用位置建議。 為了讓路由應用程式加入此功能，開發人員將利用現有的架構，如下所示 `MKDirectionsRequest` ：

- 以多工方式升級應用程式。
- 將應用程式註冊為路由應用程式。
- 使用 MapKit 物件來處理啟動應用程式 `MKDirectionsRequest` 。
- 讓 watchOS 能夠學習根據使用者參與建議應用程式。

使用 MapKit 物件啟動應用程式時 `MKDirectionsRequest` ，應該會自動開始將使用者導向至要求的位置，或提供 UI 讓使用者可以輕鬆地開始取得指示。 例如：

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

請參閱此程式碼的詳細資訊。 它會進行測試，看看它是否為有效的目的地要求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，則會 `MKDirectionsRequest` 從 URL 建立：

```csharp
var request = new MKDirectionsRequest(url);
```

WatchOS 3 的新功能，應用程式可以傳送不具有地理座標的位址，因為這會導致開發人員必須將位址編碼：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>摘要

本文涵蓋主動式建議，並示範開發人員如何使用這些建議來推動適用于 watchOS 的 Xamarin iOS 應用程式流量。 其中涵蓋了實行主動式建議和提供使用指導方針的步驟。

## <a name="related-links"></a>相關連結

- [watchOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)