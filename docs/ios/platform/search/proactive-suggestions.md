---
title: 在 Xamarin.iOS 中的主動式建議簡介
description: 這篇文章示範如何使用磁碟機 engagement Xamarin.iOS 應用程式中的主動式建議，讓系統主動向使用者會自動顯示有用的資訊。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2ab0147f918b36dc47ef6eed7d9bf1b6295d9733
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408156"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>在 Xamarin.iOS 中的主動式建議簡介

_這篇文章示範如何使用磁碟機 engagement Xamarin.iOS 應用程式中的主動式建議，讓系統主動向使用者會自動顯示有用的資訊。_

新 iOS 10，主動式建議存在新方法讓使用者洽詢 Xamarin.iOS 應用程式，藉由主動存在很有幫助資訊給使用者會自動在適當的時間。

iOS 10 會呈現駕駛 engagement 應用程式的新方式讓系統主動很有幫助的資訊會自動向使用者呈現在適當的時間。 就如同 iOS 9 提供能夠使用 Spotlight、 遞移式及 Siri 建議應用程式中新增深層搜尋 (請參閱[新的搜尋服務 Api](~/ios/platform/search/index.md))，應用程式可以使用 iOS 10 公開可呈現給使用者，系統內的功能下列位置：

- 應用程式切換器
- 在鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議

應用程式公開這項功能才能使用的技術集合，例如系統`NSUserActivity`，網頁標記中，核心焦點、 MapKit、 Media Player 和 UIKit。 此外，應用程式提供主動式建議支援，它會取得更深入的 Siri 整合免費。

## <a name="location-based-suggestions"></a>依據建議位置。

IOS 10 的新手`NSUserActivity`類別包含`MapItem`屬性，可讓開發人員提供可用於其他內容的位置資訊。 例如，如果應用程式會顯示餐廳的評論，開發人員可以設定`MapItem`屬性到使用者在應用程式中檢視餐廳的位置。 如果使用者切換至地圖應用程式時，餐廳的位置就會是可供使用。

如果應用程式支援的應用程式搜尋，它可以使用的新位址元件`CSSearchableItemAttributesSet`類別，以指定的使用者可能想要造訪的位置。 藉由設定`MapItem`屬性，其他屬性則會自動填滿的。

除了設定`Latitude`並`Longitude`位址元件內容中，但仍建議應用程式提供`NamedLocation`和`PhoneNumbers`屬性，因此 Siri 可以起始位置的呼叫。

## <a name="web-markup-based-suggestions"></a>Web 標記架構建議

iOS 9 新增至能夠在網站中豐富的使用者在焦點以及 Safari 的搜尋結果中看到的內容中包含結構化的資料標記 (請參閱[搜尋 Web 標記](~/ios/platform/search/web-markup.md))。 iOS 10 加入的功能包括以位置為基礎的標記 (例如[省略](http://schema.org/PostalAddress)所定義[Schema.org](http://schema.org/)) 來進一步加強使用者體驗。 比方說，如果使用者檢視的位置標示為網站上的頁面上，系統可以建議相同的位置時開啟對應。

## <a name="text-based-suggestions"></a>以文字為主的建議

在 iOS 10，以包含已擴充 UIKit [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)屬性[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)類別，以指定的內容語意意義的文字區域中。 在利用此資訊的地方，系統可以通常會自動選取適當的鍵盤類型、 改善自動校正建議和主動整合來自其他應用程式和網站的資訊。

例如，如果使用者標記的文字欄位中輸入文字時`UITextContentType.FullStreetAddress`，系統可以建議自動填滿 欄位的使用者最近檢視的位置。

## <a name="media-based-suggestions"></a>媒體基礎的建議

如果應用程式會播放媒體使用[MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API，iOS 10 可讓使用者檢視專輯封面，並在鎖定畫面上播放媒體透過應用程式。

## <a name="contextual-siri-reminders"></a>內容 Siri 提醒

可讓使用者使用 Siri 快速地將目前檢視中的應用程式在日後若要檢視內容提醒。 例如，如果他們已檢視餐廳評論應用程式中，他們就可以叫用 Siri 然後說出 *」 提醒我一次有關此當我回家。 」* Siri 會產生與檢閱的連結，提醒您，在應用程式。

## <a name="contact-based-suggestions"></a>請連絡架構建議

允許此應用程式才會出現在連絡人 （和相關的連絡資訊）**連絡**儲存在系統中所有使用者的連絡人以及在 iOS 裝置上的應用程式。

## <a name="ride-sharing-based-suggestions"></a>共用的賽車架構建議

如果 rani 應用程式使用[MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API，iOS 10 會將它顯示在應用程式切換器中選擇有時當使用者可能會想要好好體驗吧。 應用程式必須也註冊為 rani 應用程式藉由指定`MKDirectionsModeRideShare`for [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)中的索引鍵及其`Info.plist`檔案。

應用程式只支援共用好好體驗吧，如果系統建議會以開頭 *「...取得要好好體驗吧 」*，如果支援其他類型的路由 （例如 Walking 或自行車） 的方向，系統會使用 *「...取得指示 」*

> [!IMPORTANT]
> [MKMapItem](xref:MapKit.MKMapItem)的應用程式收到的物件可能不會包含經度和緯度的資訊，而且將會需要地理編碼。

## <a name="implementing-proactive-suggestions"></a>實作主動式建議

主動式建議 Xamarin.iOS 應用程式的支援是通常與實作幾個 Api，或擴充應用程式可能會實作的一些 Api 一樣容易。

主動式建議會使用三種主要方式的應用程式：

- **`NSUserActivity` 和 Schema.org**  -  `NSUserActivity`可協助了解哪些使用者目前正在與螢幕的資訊系統。 Schema.org 會將類似的功能加入至網頁。
- **位置建議**-如果應用程式提供或取用應用程式之間共用這項資訊的位置資訊，這些 API 延伸模組供應項目新方式。
- **媒體的應用程式建議**-系統可以提升應用程式和 iOS 裝置的使用者互動內容為基礎的媒體內容。

和應用程式中實作下列支援：

- **遞交** -  `NSUserActivity`支援，可讓開發人員開始活動，在一部裝置，然後繼續在另一個遞移式 iOS 8 中新增了 (請參閱[遞移式簡介](~/ios/platform/handoff.md))。
- **Spotlight 搜尋**-iOS 9 新增能力以提升應用程式內容中使用 Spotlight 搜尋結果`NSUserActivity`(請參閱[Core Spotlight 搜尋](~/ios/platform/search/corespotlight.md))。
- **內容 Siri 提醒**-在 iOS 10，`NSUserActivity`已經擴充，以允許 Siri 快速地讓使用者在目前應用程式中檢視在日後若要檢視內容提醒。
- **位置的建議**-iOS 10 增強`NSUserActivity`來擷取應用程式內檢視的位置，並將它們升級在整個系統的許多地方。
- **內容 Siri 要求** -  `NSUserActivity`提供內容，讓使用者可取得方向或位置呼叫是叫用 Siri 從應用程式內，應用程式呈現給 Siri 的資訊。
- **連絡互動**-在 iOS 10 中新`NSUserActivity`允許通訊應用程式升級的連絡人卡片 （在連絡人應用程式中） 做為替代的通訊方法。

所有這些功能都有個共通，它們都會使用`NSUserActivity`在一個表單或另一個則是提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`可協助了解哪些使用者目前正在與螢幕的資訊系統。 `NSUserActivity` 輕量級狀態快取機制來擷取使用者的活動，當他們瀏覽應用程式。 比方說，看看餐廳的應用程式：

[![](proactive-suggestions-images/activity02.png "快取機制 NSUserActivity 輕量級狀態")](proactive-suggestions-images/activity02.png#lightbox)

使用下列的互動：

1. 使用者應用程式時，處理`NSUserActivity`用來重新建立更新版本的應用程式的狀態。
2. 如果使用者搜尋餐廳，會遵循建立活動的相同的模式。
3. 同樣地，當使用者檢視結果。 在最後這個情況下，使用者在檢視的位置，並在 iOS 10 中，系統會更加留意某些概念 （例如位置或通訊的互動）。

仔細看看最後一個畫面中：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 詳細資料")](proactive-suggestions-images/activity03.png#lightbox)

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

若要回應使用者點選的搜尋結果 (`NSUserActivity`) 應用程式中，編輯**AppDelegate.cs**檔案，並覆寫`ContinueUserActivity`方法。 例如: 

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

開發人員必須確定這是相同的活動型別識別項 (`com.xamarin.platform`) 為上述所建立的活動。 應用程式使用中儲存的資訊`NSUserActivity`還原狀態傳回給使用者停止的地方。

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
2. 當使用者移動離開應用程式使用多工作業的應用程式切換器中，系統會自動顯示建議 （在畫面底部） 以取得到餐廳，使用其最愛的瀏覽應用程式的指示進行。
3. 如果使用者切換到在 Messages 應用程式，並開始輸入 *「 讓我們在開會 」*，QuickType 鍵盤會自動建議貼到餐廳的位址。
4. 如果使用者切換成地圖應用程式，餐廳的位址會自動建議做為目的地。
5. 這甚至適用於第 3 方應用程式 (支援`NSUserActivity`)，因此使用者可以切換至 rani 應用程式和餐廳的位址會自動建議做為目的地那里以及。
6. 它也會提供內容 Siri，讓使用者可以叫用 Siri，餐廳的應用程式內，並詢問 *[取得說明...]* Siri 會提供指示給餐廳使用者檢視。

在一般的所有上述功能有一件事，但所有那樣的建議原本來自何處。 在上述範例中，它是虛構的餐廳檢閱應用程式。

iOS 10 已增強以啟用這項功能的應用程式透過數個小型的修改和新增至現有的架構：

- `NSUserActivity` 有其他欄位，用於擷取檢視應用程式內的位置資訊。
- 幾項功能進行了 MapKit 和 CoreSpotlight 擷取位置。
- 位置感知功能已新增 Siri、 地圖、 鍵盤、 多工作業及系統內的其他應用程式。

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

基礎的文字描述的位置則需要文字為基礎 （例如 QuickType 鍵盤） 的執行個體：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性的但確保使用者會路由至傳送給他們，想應用程式，因此它應該包含的確切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以存取 Siri 讓使用者可以叫用 Siri 從應用程式，類似於 *」 呼叫此位置 」*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可能表示執行個體則適合用於導覽和電話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>實作連絡的互動

新增在 iOS 10 中，通訊應用程式會緊密整合的連絡人卡片的連絡人應用程式。 針對已實作連絡人互動的應用程式，使用者可以將指定的應用程式資訊新增至特定人員在他們的連絡人。 和附加的應用程式如果，比方說，它們會叫用將訊息傳送卡片頂端的 [快速動作] 按鈕，將會列為選項傳送的訊息。

如果選取的第 3 方應用程式時，它可以記住並顯示訊息指定的個人使用者想要連絡他們下一次的預設方式。

連絡人的互動實作在應用程式中使用`NSUserActivity`和 iOS 10 中導入新的 「 意圖 」 架構。 如需有關使用對應方式的詳細資訊，請參閱我們[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)並[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)輔助線。

#### <a name="donating-interactions"></a>捐贈的互動

看看如何應用程式可以捐贈互動：

[![](proactive-suggestions-images/activity04.png "捐贈互動概觀")](proactive-suggestions-images/activity04.png#lightbox)

應用程式建立`INInteraction`物件，其中包含**意圖**(`INIntent`)，**參與者**並**中繼資料**。 **意圖**代表使用者的動作，例如進行視訊通話或傳送簡訊。 **參與者**包括收到通訊的人員。 **中繼資料**定義其他資訊，例如成功傳送的訊息，依此類推。

開發人員永遠不會直接建立的執行個體`INIntent`或`INIntentResponse`，它們將使用其中一個 （根據應用程式會完成代表使用者的工作） 的特定子類別繼承自父類別。 例如，`INSendMessageIntent`和`INSendMessageIntentResponse`傳送文字訊息。 

互動會完全擴展之後, 再呼叫`DonateInteraction`方法，以通知之系統的互動時可供使用。

當使用者與連絡人卡片的應用程式互動時，互動取得隨附於`NSUserActivity`，然後用來啟動應用程式：

[![](proactive-suggestions-images/activity05.png "互動取得隨附 NSUserActivity 用來啟動應用程式")](proactive-suggestions-images/activity05.png#lightbox)

看看下列範例中傳送的訊息對應方式：

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

查看這個程式碼的詳細資料，它會建立並於其中填入執行個體`NSUserActivity`(如中所示[建立活動](#creating-an-activity)上一節)。 接下來，它會建立的執行個體`INSendMessageIntent`(繼承自`INIntent`)，並填入所傳送之訊息的詳細資料：

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`建立，並傳遞`NSUserActivity`上面所建立：

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`從傳送訊息目的所建立 (`INSendMessageIntent`) 和回應 (`INSendMessageIntentResponse`) 剛建立：

```csharp
var interaction = new INInteraction (intent, response);
```

最後，系統會通知的互動：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

完成處理常式會傳入其中應用程式可以回應捐贈成功或失敗。

### <a name="activities-best-practices"></a>活動的最佳作法

使用活動時，Apple 建議下列最佳作法：

- 使用`NeedsSave`延遲的承載更新。
- 請確定將目前活動的強式參考。
- 只會傳輸小型承載會包含剛好足夠的資訊，以還原狀態。
- 請確定活動型別識別項唯一和描述性使用反向 DNS 標記法來指定它們。 

## <a name="schemaorg"></a>Schema.org

如上所示，`NSUserActivity`可協助了解哪些使用者目前正在與螢幕的資訊系統。 Schema.org 會將類似的功能加入至網頁。

Schema.org 可以提供相同類型的位置為基礎的網站的互動。 Apple 設計新的位置建議，在原生應用程式中一樣，在 Safari 中檢視時一樣運作。

某些 Schema.org 背景：

- 它提供一種開放式 web 標記詞彙標準。
- 其運作方式包含在網頁上的結構化中繼資料。
- 有超過 500 個的結構描述表示可用的各種概念。
- 開發人員可以藉由實作它的網站上，取得使用的優點`NSUserActivity`原生應用程式中。

結構描述會以類似結構，其中特定類型，例如樹狀結構排列*餐廳*，例如繼承自更泛用型別*當地商業*。 如需詳細資訊，請參閱[Schema.org](http://schema.org)。

例如，如果網頁上包含下列資料：

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

如果使用者瀏覽此網頁在 Safari 中的，然後切換到另一個應用程式會擷取並提供為位置建議系統的其他部分 （如同在上述的活動） 從頁面的位置資訊。

Safari 會擷取符合下列結構描述屬性的任何網頁上的任何項目：

- **PostalAddress**
- **GeoCoordinates**
- 此電話屬性中。

如需詳細資訊，請參閱我們[搜尋 Web 標記](~/ios/platform/search/web-markup.md)指南。

## <a name="consuming-location-suggestions"></a>使用位置的建議

這個下一節將討論使用來自系統 （例如對應應用程式中） 或其他第 3 方應用程式的其他組件的位置建議。

有兩種主要方式，應用程式可以使用位置的建議：

- 透過 QuickType 鍵盤。
- 直接由耗用路由的應用程式中的位置資訊。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置的建議和 QuickType 鍵盤

如果應用程式處理以文字為基礎格式的位址，應用程式可以利用透過 QuickType UI 位置建議。 iOS 10 展開 QuickType UI 使其具備下列功能：

- 應用程式可以在 UI 中加入文字欄位的語意方面的提示。
- 應用程式可以取得主動式建議應用程式中。
- 應用程式可以受益於增強的自動校正。

新`TextContentType`在 iOS 10 中的文字欄位控制項的屬性可讓開發人員定義的語意的目的，使用者要在指定的欄位中輸入的值。 例如：

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

會告訴系統應用程式需要使用者在指定的欄位中輸入完整街道地址。 這可讓 QuickType 鍵盤而不會自動提供鍵盤位置的建議，當使用者在此欄位中輸入值。

以下是幾個較常見的類型可在開發人員`UITextContentType`靜態類別：

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由的應用程式和位置的建議

本章節將看看使用位置建議直接從路由的應用程式內。 路由應用程式中加入這項功能，開發人員將會利用現有`MKDirectionsRequest`framework，如下所示：

- 若要升級多工作業中的應用程式。
- 若要註冊應用程式做為路由的應用程式。
- 若要處理啟動應用程式與 MapKit`MKDirectionsRequest`物件。
- 若要讓 iOS 能夠了解如何提供給使用者的應用程式建議在適當的時間，取決於使用者參與。

應用程式啟動時是 MapKit`MKDirectionsRequest`物件，它應該會自動開始讓使用者指示要求的位置，或呈現 UI，可讓您更輕鬆地開始取得指示使用者。 例如: 


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

新增在 iOS 10 中，應用程式可以傳送沒有開發人員都需要將編碼的位址且導致地理座標的位址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>媒體的應用程式建議

如果應用程式會處理任何類型的媒體，例如 podcast 應用程式或資料流的媒體內容，例如音訊或視訊，請使用 iOS 10，它可以利用媒體建議的系統中。

針對處理媒體的應用程式，iOS 會支援下列行為：

- iOS 會將升級的使用者可能會使用其先前的行為為基礎的應用程式。
- 焦點和目前檢視中，將會看到應用程式相關的建議。
- 如果應用程式符合下列觸發程序的其中一個，它可能會提高鎖定畫面建議：
    - 之後插入耳機或藍芽裝置建立的連接。
    - 之後在車上取得。
    - 之後到達在家裡或工作。 

藉由包含簡單的 API 呼叫，在 iOS 10 中，開發人員可以建立更吸引人的鎖定畫面體驗媒體應用程式的使用者。 使用`MPPlayableContentManager`類別來管理媒體的播放、 完整的媒體控制項 （例如那些由 Music 應用程式） 將會看到應用程式在鎖定畫面上。


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>總結

這篇文章已涵蓋主動式建議，並示範如何開發人員可以使用它們來促進前往 Xamarin.iOS 應用程式的流量。 它涵蓋實作主動式建議的步驟，並提供使用指導方針。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
