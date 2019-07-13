---
title: watchOS 在 Xamarin 中的主動式建議
description: 本文說明如何在提升參與度 watchOS 3 應用程式中使用主動式建議，讓系統主動向使用者會自動顯示有用的資訊。
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 0c0bf6058b2ec7a8e3ef606bef9f725a476abffe
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865924"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>watchOS 在 Xamarin 中的主動式建議

_本文說明如何在提升參與度 watchOS 3 應用程式中使用主動式建議，讓系統主動向使用者會自動顯示有用的資訊。_


新 watchOS 3，主動式建議存在新方法讓使用者洽詢 Xamarin.iOS 應用程式，藉由主動存在很有幫助資訊給使用者會自動在適當的時間。


## <a name="about-proactive-suggestions"></a>關於主動式建議

WatchOS 3 的新手`NSUserActivity`包含`MapItem`屬性，可讓應用程式提供可用在其他內容中的位置資訊。 例如，如果應用程式顯示旅館檢閱，並提供`MapItem`位置，如果使用者切換至地圖應用程式，他們已檢視的旅館的位置會是可用。

應用程式公開這項功能才能使用的技術集合，例如系統`NSUserActivity`，MapKit、 Media Player 和 UIKit。 此外，應用程式提供主動式建議支援，它會取得更深入的 Siri 整合免費。

## <a name="location-based-suggestions"></a>依據建議位置。

WatchOS 3 的新手`NSUserActivity`類別包含`MapItem`屬性，可讓開發人員提供可用於其他內容的位置資訊。 例如，如果應用程式會顯示餐廳的評論，開發人員可以設定`MapItem`屬性到使用者在應用程式中檢視餐廳的位置。 如果使用者切換至地圖應用程式時，餐廳的位置就會是可供使用。

如果應用程式支援的應用程式搜尋，它可以使用的新位址元件`CSSearchableItemAttributesSet`類別，以指定的使用者可能想要造訪的位置。 藉由設定`MapItem`屬性，其他屬性則會自動填滿的。

除了設定`Latitude`並`Longitude`位址元件內容中，但仍建議應用程式提供`NamedLocation`和`PhoneNumbers`屬性，因此 Siri 可以起始位置的呼叫。

## <a name="contextual-siri-reminders"></a>內容 Siri 提醒

可讓使用者使用 Siri 快速地將目前檢視中的應用程式在日後若要檢視內容提醒。 例如，如果他們已檢視餐廳評論應用程式中，他們就可以叫用 Siri 然後說出 *」 提醒我一次有關此當我回家。 」* Siri 會產生與檢閱的連結，提醒您，在應用程式。

## <a name="implementing-proactive-suggestions"></a>實作主動式建議

主動式建議 Xamarin.iOS 應用程式的支援是通常與實作幾個 Api，或擴充應用程式可能會實作的一些 Api 一樣容易。

主動式建議會使用三種主要方式的應用程式：

- **`NSUserActivity`** -可協助了解哪些使用者目前正在與螢幕的資訊系統。
- **位置建議**-如果應用程式提供或取用應用程式之間共用這項資訊的位置資訊，這些 API 延伸模組供應項目新方式。

和應用程式中實作下列支援：

- **內容 Siri 提醒**-在 iOS 10，`NSUserActivity`已經擴充，以允許 Siri 快速地建立檢視的內容目前檢視中的應用程式在日後的提醒。
- **位置的建議**-iOS 10 增強`NSUserActivity`來擷取應用程式內檢視的位置，並將它們升級在整個系統的許多地方。
- **內容 Siri 要求** -  `NSUserActivity`提供內容，讓使用者可取得方向或位置呼叫是叫用 Siri 從應用程式內，應用程式呈現給 Siri 的資訊。

所有這些功能都有個共通，它們都會使用`NSUserActivity`在一個表單或另一個則是提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`可協助了解哪些使用者目前正在與螢幕的資訊系統。 `NSUserActivity` 輕量級狀態快取機制來擷取使用者的活動，當他們瀏覽應用程式。 比方說，看看餐廳的應用程式：

[![](proactive-suggestions-images/activity02.png "餐廳的應用程式")](proactive-suggestions-images/activity02.png#lightbox)

使用下列的互動：

1. 使用者應用程式時，處理`NSUserActivity`用來重新建立更新版本的應用程式的狀態。
2. 如果使用者搜尋餐廳，會遵循建立活動的相同的模式。
3. 同樣地，當使用者檢視結果。 在最後這個情況下，使用者在檢視的位置，並在 iOS 10 中，系統會更加留意某些概念 （例如位置或通訊的互動）。

仔細看看最後一個畫面中：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 承載")](proactive-suggestions-images/activity03.png#lightbox)

建立應用程式的以下`NSUserActivity`和它已填入資訊，稍後再重新建立狀態。 應用程式也有包含一些中繼資料，例如位置的名稱和地址。 建立此活動，應用程式可讓 iOS 知道它代表使用者的目前狀態。

如果活動即將遞交的公告無線、 儲存為暫存位置的建議值或加入至搜尋結果中顯示的裝置上 Spotlight 索引，然後決定應用程式。

如需有關遞移式及 Spotlight 搜尋的詳細資訊，請參閱我們[遞移式簡介](~/ios/platform/handoff.md)並[iOS 9 新搜尋 Api](~/ios/platform/search/index.md)輔助線。

### <a name="creating-an-activity"></a>建立活動

之前建立活動，活動型別識別項會需要建立來識別它。 活動的型別識別項是簡短字串加入至`NSUserActivityTypes`陣列的應用程式的`Info.plist`檔案用來唯一識別指定的使用者活動型別。 對於每個應用程式支援，並公開給應用程式內搜尋的活動陣列中會有一個項目。 請參閱我們[建立活動型別識別項參考](~/ios/platform/search/nsuseractivity.md)如需詳細資訊。

看看活動的範例：

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

使用活動型別識別項，建立新的活動。 接下來，因此可以在日後還原此狀態，會建立一些定義活動的中繼資料。 然後，活動會提供有意義的標題，並附加至使用者資訊。 最後，會啟用某些功能，並在活動傳送至系統。

上述程式碼無法進一步增強以包括中繼資料提供給活動的內容，進行下列變更：

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

如果開發人員必須能夠與應用程式中顯示的相同資訊的網站，應用程式可以包含 URL，並可以在沒有安裝 （透過遞移式） 的應用程式的其他裝置上顯示的內容：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>還原活動

若要回應使用者點選的搜尋結果 (`NSUserActivity`) 應用程式中，編輯**AppDelegate.cs**檔案，並覆寫`ContinueUserActivity`方法。 例如：

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

請確定這是相同的活動型別識別項 (`com.xamarin.platform`) 為上述所建立的活動。 應用程式使用中儲存的資訊`NSUserActivity`還原狀態傳回給使用者停止的地方。

### <a name="benefits-of-creating-an-activity"></a>建立活動的優點

使用以上所顯示的程式碼的最少，就能夠利用三個新的 iOS 10 功能的應用程式：

- **Handoff**
- **Spotlight 搜尋**
- **內容 Siri 提醒**

下一節將看看啟用兩個其他新 iOS 10 功能：

- **位置的建議**
- **內容 Siri 要求**

### <a name="location-based-suggestions"></a>依據建議位置。 

採取上述餐廳搜尋應用程式的範例。 如果它已實作`NSUserActivity`並正確地填入所有的中繼資料和屬性，使用者就能夠執行下列動作：

1. 他們想要符合在朋友的應用程式中找到一家餐廳。
2. 如果使用者切換成地圖應用程式，餐廳的位址會自動建議做為目的地。
3. 這甚至適用於第 3 方應用程式 (支援`NSUserActivity`)，因此使用者可以切換至 rani 應用程式和餐廳的位址會自動建議做為目的地那里以及。
4. 它也會提供內容 Siri，讓使用者可以叫用 Siri，餐廳的應用程式內，並詢問 *[取得說明...]* Siri 會提供指示給餐廳使用者檢視。

在一般的所有上述功能有一件事，但所有那樣的建議原本來自何處。 在上述範例中，它是虛構的餐廳檢閱應用程式。

watchOS 3 已增強以啟用這項功能的應用程式透過數個小型的修改和新增至現有的架構：

- `NSUserActivity` 有其他欄位，用於擷取檢視應用程式內的位置資訊。
- 幾項功能進行了 MapKit 和 CoreSpotlight 擷取位置。
- 位置感知功能已新增 Siri、 地圖、 多工作業及系統內的其他應用程式。

若要實作位置為基礎的建議，開始提供上述的相同活動程式碼：

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

如果應用程式使用 MapKit，很簡單，只要加入目前 map`MKMapItem`活動：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果應用程式不使用 MapKit，它可以採用應用程式搜尋，並指定位置的下列新屬性：

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

查看上述程式碼在詳細資料。 首先，每個執行個體中需要的位置名稱：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然後，根據的描述所需的文字的文字型執行個體 （例如 QuickType 鍵盤）：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性，但請確定使用者會路由傳送至應用程式會想要傳送至正確的位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以存取 Siri 讓使用者可以叫用 Siri 從應用程式，類似於 * 」 呼叫此位置 」:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可能表示執行個體則適合用於導覽和電話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>活動的最佳作法

使用活動時，Apple 建議下列最佳作法：

- 使用`NeedsSave`延遲的承載更新。
- 請確定將目前活動的強式參考。
- 只會傳輸小型承載會包含剛好足夠的資訊，以還原狀態。
- 請確定活動型別識別項唯一和描述性使用反向 DNS 標記法來指定它們。 

## <a name="consuming-location-suggestions"></a>使用位置的建議

這個下一節將討論使用來自系統 （例如對應應用程式中） 或其他第 3 方應用程式的其他組件的位置建議。

## <a name="routing-apps-and-locations-suggestions"></a>路由的應用程式和位置的建議

本章節將看看使用位置建議直接從路由的應用程式內。 路由應用程式中加入這項功能，開發人員將會利用現有`MKDirectionsRequest`framework，如下所示：

- 若要升級多工作業中的應用程式。
- 若要註冊應用程式做為路由的應用程式。
- 若要處理啟動應用程式與 MapKit`MKDirectionsRequest`物件。
- 讓 watchOS 能夠了解如何根據使用者參與應用程式的建議。

應用程式啟動時是 MapKit`MKDirectionsRequest`物件，它應該會自動開始讓使用者指示要求的位置，或呈現 UI，可讓您更輕鬆地開始取得指示使用者。 例如：


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

查看這段程式碼在詳細資料。 它會測試有效的目的地要求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，則它會建立`MKDirectionsRequest`從 URL:

```csharp
var request = new MKDirectionsRequest(url);
```

新 watchOS 3，在應用程式可以傳送沒有開發人員都需要將編碼的位址且導致地理座標的位址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>總結

這篇文章已涵蓋主動式建議，並示範如何開發人員可以使用它們來促進前往 Xamarin.iOS 應用程式的流量進行 watchOS。 它涵蓋實作主動式建議的步驟，並提供使用指導方針。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
