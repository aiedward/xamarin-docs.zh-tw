---
title: "主動式建議"
description: "本文示範如何在磁碟機 engagement watchOS 3 應用程式中使用主動式建議，藉由使用系統自動向使用者主動顯示有用的資訊。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: ca2476eef120c7d86b939934ec4b286e871d6a78
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="proactive-suggestions"></a>主動式建議

_本文示範如何在磁碟機 engagement watchOS 3 應用程式中使用主動式建議，藉由使用系統自動向使用者主動顯示有用的資訊。_


新 watchOS 3，主動式建議存在新聞方式，讓使用者透過主動存在有用的資訊給使用者會自動在適當的時間洽詢 Xamarin.iOS 應用程式。


## <a name="about-proactive-suggestions"></a>關於主動式建議

新手 watchOS 3，`NSUserActivity`包含`MapItem`屬性，可讓應用程式提供可用於其他內容的位置資訊。 例如，如果應用程式顯示旅館檢閱，並提供`MapItem`位置，如果使用者切換至對應的應用程式，他們已檢視，讓旅館的位置將是可用。

應用程式公開此功能，以使用技術的集合，例如系統`NSUserActivity`，MapKit、 Media Player 和 UIKit。 此外，藉由提供主動式建議支援應用程式，它會取得 Siri 的整合更加深入免費。

## <a name="location-based-suggestions"></a>依據建議位置。

新手 watchOS 3，`NSUserActivity`類別包含`MapItem`屬性，可讓開發人員提供可用於其他內容的位置資訊。 例如，如果應用程式會顯示對餐廳的評論，開發人員可以設定`MapItem`屬性為使用者在應用程式中檢視餐廳的位置。 如果使用者切換至對應的應用程式時，餐廳的位置就是自動可供使用。

如果應用程式支援應用程式內搜尋，它可以使用新的位址元件`CSSearchableItemAttributesSet`類別，以指定的使用者可能想要造訪的位置。 藉由設定`MapItem`屬性，其他屬性則會自動填滿的。

除了設定`Latitude`和`Longitude`位址的元件屬性，但仍建議應用程式提供`NamedLocation`和`PhoneNumbers`屬性，因此 Siri 可以撥打至的位置。

## <a name="contextual-siri-reminders"></a>內容 Siri 提醒

可讓使用者使用 Siri 快速將它們目前正在檢視的應用程式中在日後若要檢視內容提醒。 例如，如果他們已檢視餐廳評論的應用程式中，他們就可以叫用 Siri 然後說出*"提醒我有關此當我回家。 」* Siri 便會產生一個連結到檢閱提醒應用程式中。

## <a name="implementing-proactive-suggestions"></a>實作主動式建議

主動式建議 Xamarin.iOS 應用程式的支援是通常只要幾個應用程式開發介面的實作或擴充少數的 api 的應用程式可能會實作。

主動式建議使用三種主要方法中的應用程式：

- **`NSUserActivity`** -有助於了解哪些使用者目前正在使用螢幕的資訊系統。
- **位置建議**-如果應用程式提供或取用基礎的位置資訊，這些 API 擴充功能提供新方式在應用程式之間共用這項資訊。

和應用程式中實作下列支援：

- **內容 Siri 提醒**-在 iOS 10`NSUserActivity`已經擴充，以允許 Siri 快速地建立它們目前正在檢視的應用程式中在日後若要檢視內容提醒。
- **位置建議**-iOS 10 增強`NSUserActivity`擷取檢視應用程式內的位置，並將它們提升在整個系統的許多地方。
- **內容 Siri 要求** -  `NSUserActivity`提供內容，向應用程式內使用 Siri，讓使用者可取得指示或被呼叫的位置叫用 Siri 從應用程式內的資訊。

所有這些功能都有個共通，它們都會使用`NSUserActivity`中某個表單或其他提供的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`有助於了解哪些使用者目前正在使用螢幕的資訊系統。 `NSUserActivity` 輕量級狀態快取機制來擷取使用者的活動，當它們瀏覽應用程式。 例如，查看餐廳應用程式：

[ ![](proactive-suggestions-images/activity02.png "餐廳應用程式")](proactive-suggestions-images/activity02.png)

使用下列的互動：

1. 使用者應用程式時，處理`NSUserActivity`是用來重新建立更新版本的應用程式的狀態。
2. 如果使用者搜尋的餐廳，會遵循建立活動的相同的模式。
3. 同樣地，當使用者檢視結果。 在最後一個案例中，使用者在檢視的位置，且在 iOS 10 中，系統更留意某些概念 （如位置或通訊互動）。

採取的最後一個畫面詳述：

[ ![](proactive-suggestions-images/activity03.png "NSUserActivity 裝載")](proactive-suggestions-images/activity03.png)

應用程式中，建立以下`NSUserActivity`並填入相關資訊，以便稍後重新建立狀態。 應用程式也有包含某些中繼資料，例如位置的名稱和地址。 建立此活動，應用程式可讓 iOS 知道它代表使用者的目前狀態。

如果活動將會通告透過無線方式以遞交、 儲存為暫存位置的建議值或加入至搜尋結果中顯示的裝置上精選索引，再決定應用程式。

如需有關遞移式及 Spotlight 搜尋的詳細資訊，請參閱我們[簡介遞交](~/ios/platform/handoff.md)和[iOS 9 新搜尋 Api](~/ios/platform/search/index.md)輔助線。

### <a name="creating-an-activity"></a>建立活動

之前建立活動，活動型別識別碼將需要建立來識別它。 活動類型識別項是簡短字串加入至`NSUserActivityTypes`陣列的應用程式的`Info.plist`檔案用來唯一識別指定的使用者活動型別。 每個應用程式支援，而且會公開給應用程式內搜尋的活動在陣列中會有一個項目。 請參閱我們[建立活動型別識別項參考](~/ios/platform/search/nsuseractivity.md)如需詳細資訊。

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

使用活動類型識別項，建立新的活動。 接下來，因此可以在日後還原此狀態，會建立定義活動的某些中繼資料。 然後，該活動提供有意義的標題，附加至使用者資訊。 最後，某些功能會啟用並活動傳送到系統。

包含提供給活動的內容進行下列變更的中繼資料時，無法進一步加強上述程式碼：

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

如果開發人員有一個可顯示相同的資訊與應用程式的網站，應用程式可以包含 URL，並可以在沒有安裝 （透過遞移式） 的應用程式的其他裝置上顯示的內容：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>還原活動

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

確定這是相同的活動型別識別項 (`com.xamarin.platform`) 為上面所建立的活動。 應用程式使用中儲存的資訊`NSUserActivity`狀態還原到使用者離開的地方。

### <a name="benefits-of-creating-an-activity"></a>建立活動的優點

少量的以上所顯示的程式碼中，就能夠利用三個新的 iOS 10 功能的應用程式：

- **Handoff**
- **Spotlight 搜尋**
- **內容 Siri 提醒**

下一節會介紹在啟用兩個其他新 iOS 10 功能：

- **位置的建議**
- **內容 Siri 要求**

### <a name="location-based-suggestions"></a>依據建議位置。 

需要上述餐廳搜尋應用程式的範例。 如果它已實作`NSUserActivity`和正確填入所有的中繼資料及屬性，使用者就可以執行下列動作：

1. 尋找他們想要符合在朋友的應用程式中的餐廳。
4. 如果使用者切換至對應的應用程式時，餐廳的位址會自動建議做為目的地。
5. 這甚至適用於第 3 個合作對象應用程式 (支援`NSUserActivity`)，因此使用者可以切換到騎共用應用程式和餐廳的位址會自動建議做為目的地那里以及。
6. 它也提供內容給 Siri，讓使用者可以叫用餐廳應用程式內的 Siri 並詢問*「 取得說明...」* Siri 會提供到 「 餐廳 」 使用者檢視的方向。

所有上述功能共同具有一件事，但是所有建議原本來自何方。 在上述範例中，它可以是虛構的餐廳檢閱應用程式。

若要啟用這項功能的應用程式透過數個小型的修改，以及新增到現有的架構，已增強 watchOS 3:

- `NSUserActivity` 有其他欄位來擷取檢視應用程式內的位置資訊。
- 新增數個項目已 MapKit 和 CoreSpotlight 來擷取位置。
- 位置感知功能已經加入 Siri、 地圖、 多工作業及系統內的其他應用程式。

若要實作建議基礎位置，請啟動與上述相同的活動程式碼：

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

如果應用程式使用 MapKit，就越簡單越加入目前 map`MKMapItem`活動：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果應用程式不使用 MapKit，它可以採用應用程式內搜尋，並指定位置的下列新屬性：

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

查看上述程式碼的詳細資料。 首先，每個執行個體中需要的位置名稱：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然後，在根據的描述所需的文字的文字基礎 （例如 QuickType 鍵盤） 的執行個體：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度，但是確保使用者會路由傳送至應用程式由要傳送至正確的位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以存取 Siri 讓使用者可以叫用 Siri 從應用程式所說類似，*"會呼叫這個位置 」:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可能表示執行個體則適合用於導覽及電話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>活動的最佳作法

使用活動時，Apple 建議以下的最佳作法：

- 使用`NeedsSave`延遲裝載更新。
- 請確定將目前活動的強式參考。
- 僅傳送包含剛好足夠的資訊，還原狀態的小型裝載。
- 請確認活動型別識別項唯一和描述性使用反向 DNS 標記法來指定它們。 

## <a name="consuming-location-suggestions"></a>使用位置的建議

下一節將討論取用來自其他部分 （例如對應應用程式） 系統或其他第 3 個合作對象應用程式的位置建議。

## <a name="routing-apps-and-locations-suggestions"></a>路由的應用程式和位置的建議

本節會看一下直接從位置建議使用路由的應用程式中。 若要加入這項功能的路由應用程式，開發人員將會利用現有`MKDirectionsRequest`架構，如下所示：

- 若要升級多工作業中的應用程式。
- 登錄應用程式做為路由的應用程式。
- 若要處理啟動應用程式與 MapKit`MKDirectionsRequest`物件。
- 可讓 watchOS 若要了解建議的使用者參與為基礎的應用程式。

應用程式啟動時是 MapKit`MKDirectionsRequest`物件，它應該自動啟動讓使用者指示，要求的位置，或呈現 UI，以簡化使用者開始取得指示。 例如: 


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

查看這個程式碼詳細資料中。 它會測試有效的目的地要求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，則它會建立`MKDirectionsRequest`從 URL:

```csharp
var request = new MKDirectionsRequest(url);
```

新 watchOS 3，在應用程式可以傳送沒有開發人員必須要編碼的位址且導致地理座標的位址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>總結

這篇文章已涵蓋主動式建議，並示範如何開發人員可以使用它們來 Xamarin.iOS 應用程式的磁碟機流量的 watchOS。 它涵蓋如何實作主動式建議的步驟，並提供使用指導方針。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
