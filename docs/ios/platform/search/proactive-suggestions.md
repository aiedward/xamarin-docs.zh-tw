---
title: 主動式建議 Xamarin.iOS 簡介
description: 本文示範如何在磁碟機 engagement Xamarin.iOS 應用程式中使用主動式建議，藉由使用系統自動向使用者主動顯示有用的資訊。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: f736e9dda00546ddef7cf03457813c7e3d10882b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788011"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>主動式建議 Xamarin.iOS 簡介

_本文示範如何在磁碟機 engagement Xamarin.iOS 應用程式中使用主動式建議，藉由使用系統自動向使用者主動顯示有用的資訊。_

新增 iOS 10，主動式建議存在新聞方式，讓使用者透過主動存在有用的資訊給使用者會自動在適當的時間洽詢 Xamarin.iOS 應用程式。

iOS 10 會呈現驅動 engagement 應用程式的新方式讓系統主動有用的資訊會自動向使用者呈現在適當的時間。 就像 iOS 9 提供深層搜尋加入精選、 傳遞和 Siri 建議使用的應用程式的能力 (請參閱[新搜尋 Api](~/ios/platform/search/index.md))，使用 iOS 10 應用程式可以公開可呈現給使用者，系統中的功能下列位置：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議

應用程式公開此功能，以使用技術的集合，例如系統`NSUserActivity`，網頁標記中，核心精選、 MapKit、 Media Player 和 UIKit。 此外，藉由提供主動式建議支援應用程式，它會取得 Siri 的整合更加深入免費。

## <a name="location-based-suggestions"></a>依據建議位置。

新 ios 10`NSUserActivity`類別包含`MapItem`屬性，可讓開發人員提供可用於其他內容的位置資訊。 例如，如果應用程式會顯示對餐廳的評論，開發人員可以設定`MapItem`屬性為使用者在應用程式中檢視餐廳的位置。 如果使用者切換至對應的應用程式時，餐廳的位置就是自動可供使用。

如果應用程式支援應用程式內搜尋，它可以使用新的位址元件`CSSearchableItemAttributesSet`類別，以指定的使用者可能想要造訪的位置。 藉由設定`MapItem`屬性，其他屬性則會自動填滿的。

除了設定`Latitude`和`Longitude`位址的元件屬性，但仍建議應用程式提供`NamedLocation`和`PhoneNumbers`屬性，因此 Siri 可以撥打至的位置。

## <a name="web-markup-based-suggestions"></a>Web 標記基礎的建議

iOS 9 加入功能之網站中的豐富使用者精選和 Safari 的搜尋結果中看到的內容中包含結構化的資料標記 (請參閱[搜尋網頁標記](~/ios/platform/search/web-markup.md))。 iOS 10 增加能力，包括以位置為基礎的標記 (例如[省略](http://schema.org/PostalAddress)所定義的[Schema.org](http://schema.org/)) 來進一步加強使用者體驗。 比方說，如果使用者檢視的位置標示為網站上的頁面上，系統可以建議相同的位置開啟對應時。

## <a name="text-based-suggestions"></a>文字基礎的建議

UIKit 已展開於 iOS 包含 10 [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)屬性[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)類別，以在文字區域中指定內容的語意。 這項資訊的位置，系統通常會自動選取適當的鍵盤類型、 改善 autocorrection 建議和主動整合來自其他應用程式和網站的資訊。

例如，如果使用者標記的文字欄位中輸入文字時`UITextContentType.FullStreetAddress`，系統可以建議自動填滿欄位與使用者檢視最近的位置。

## <a name="media-based-suggestions"></a>媒體基礎的建議

如果應用程式會在播放媒體使用[MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API，iOS 10 可讓使用者檢視專輯封面及鎖定螢幕上播放媒體透過應用程式。

## <a name="contextual-siri-reminders"></a>內容 Siri 提醒

可讓使用者使用 Siri 快速將它們目前正在檢視的應用程式中在日後若要檢視內容提醒。 例如，如果他們已檢視餐廳評論的應用程式中，他們就可以叫用 Siri 然後說出 *"提醒我有關此當我回家。 」* Siri 便會產生一個連結到檢閱提醒應用程式中。

## <a name="contact-based-suggestions"></a>連絡基礎的建議

允許此應用程式才會出現在連絡人 （及相關的連絡資訊）**連絡**儲存在系統中所有使用者的連絡人以及在 iOS 裝置上的應用程式。

## <a name="ride-sharing-based-suggestions"></a>寫共用架構的建議

如果騎共用應用程式使用[MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API，iOS 10 會加以呈現在應用程式切換器的選項有時候使用者可能要騎時。 應用程式必須也登錄為騎共用應用程式藉由指定`MKDirectionsModeRideShare`如[MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)中的索引鍵及其`Info.plist`檔案。

如果應用程式只支援騎共用，會從系統建議 *> 的 < 取得，以及到...*，如果支援其他類型的路由 （例如 Walking 或自行車） 的方向，系統會使用 *> 的 < 取得指示...*

> [!IMPORTANT]
> [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/)應用程式收到的物件可能不包含經度和緯度的資訊，因此將需要地理編碼。

## <a name="implementing-proactive-suggestions"></a>實作主動式建議

主動式建議 Xamarin.iOS 應用程式的支援是通常只要幾個應用程式開發介面的實作或擴充少數的 api 的應用程式可能會實作。

主動式建議使用三種主要方法中的應用程式：

- **`NSUserActivity` 和 Schema.org**  -  `NSUserActivity`有助於了解哪些使用者目前正在使用螢幕的資訊系統。 Schema.org 將類似的功能加入至 web 網頁。
- **位置建議**-如果應用程式提供或取用基礎的位置資訊，這些 API 擴充功能提供新方式在應用程式之間共用這項資訊。
- **媒體應用程式建議**-系統可以升級應用程式和 iOS 裝置的使用者互動的內容為基礎的媒體內容。

和應用程式中實作下列支援：

- **遞交** -  `NSUserActivity`已加入支援，可讓開發人員一個在裝置上，啟動活動，然後繼續在另一個遞移式 iOS 8 (請參閱[簡介遞交](~/ios/platform/handoff.md))。
- **醒目搜尋**-iOS 9 加入升級應用程式內容中使用 Spotlight 搜尋結果的能力`NSUserActivity`(請參閱[核心 Spotlight 搜尋](~/ios/platform/search/corespotlight.md))。
- **內容 Siri 提醒**-在 iOS 10`NSUserActivity`已經擴充，以允許 Siri 快速地建立使用者目前正在檢視的應用程式中在日後若要檢視內容提醒。
- **位置建議**-iOS 10 增強`NSUserActivity`擷取檢視應用程式內的位置，並將它們提升在整個系統的許多地方。
- **內容 Siri 要求** -  `NSUserActivity`提供內容，向應用程式內使用 Siri，讓使用者可取得指示或被呼叫的位置叫用 Siri 從應用程式內的資訊。
- **請連絡互動**-iOS 10 的新`NSUserActivity`允許升級從做為替代的通訊方法 （在連絡人應用程式） 的連絡人卡片通訊應用程式。

所有這些功能都有個共通，它們都會使用`NSUserActivity`中某個表單或其他提供的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述，`NSUserActivity`有助於了解哪些使用者目前正在使用螢幕的資訊系統。 `NSUserActivity` 輕量級狀態快取機制來擷取使用者的活動，當它們瀏覽應用程式。 例如，查看餐廳應用程式：

[![](proactive-suggestions-images/activity02.png "快取機制 NSUserActivity 輕量的狀態")](proactive-suggestions-images/activity02.png#lightbox)

使用下列的互動：

1. 使用者應用程式時，處理`NSUserActivity`是用來重新建立更新版本的應用程式的狀態。
2. 如果使用者搜尋的餐廳，會遵循建立活動的相同的模式。
3. 同樣地，當使用者檢視結果。 在最後一個案例中，使用者在檢視的位置，且在 iOS 10 中，系統更留意某些概念 （如位置或通訊互動）。

採取的最後一個畫面詳述：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 詳細資料")](proactive-suggestions-images/activity03.png#lightbox)

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

開發人員必須確定這是相同的活動型別識別項 (`com.xamarin.platform`) 為上面所建立的活動。 應用程式使用中儲存的資訊`NSUserActivity`狀態還原到使用者離開的地方。

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
2. 使用者移動離開使用多工作業的應用程式切換器的應用程式時，系統會自動顯示建議 （位於螢幕底部） 取得餐廳使用他們最愛的瀏覽應用程式的指示。
3. 如果使用者切換到訊息 」 應用程式，並開始輸入 *」 讓我們在符合"*，QuickType 鍵盤將會自動建議貼上的餐廳的地址。
4. 如果使用者切換至對應的應用程式時，餐廳的位址會自動建議做為目的地。
5. 這甚至適用於第 3 個合作對象應用程式 (支援`NSUserActivity`)，因此使用者可以切換到騎共用應用程式和餐廳的位址會自動建議做為目的地那里以及。
6. 它也提供內容給 Siri，讓使用者可以叫用餐廳應用程式內的 Siri 並詢問 *「 取得說明...」* Siri 會提供到 「 餐廳 」 使用者檢視的方向。

所有上述功能共同具有一件事，但是所有建議原本來自何方。 在上述範例中，它可以是虛構的餐廳檢閱應用程式。

若要啟用這項功能的應用程式透過數個小型的修改，以及新增到現有的架構，已增強 iOS 10:

- `NSUserActivity` 有其他欄位來擷取檢視應用程式內的位置資訊。
- 新增數個項目已 MapKit 和 CoreSpotlight 來擷取位置。
- 位置感知功能已經加入 Siri、 地圖、 鍵盤、 多工作業及系統內的其他應用程式。

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

位置的文字為基礎描述則需要文字為基礎 （例如 QuickType 鍵盤） 的執行個體：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度，但是確保使用者會路由傳送至應用程式由傳送，因此它應該會包含正確的位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以存取 Siri 讓使用者可以叫用 Siri 從應用程式所說類似， *」 呼叫此位置"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可能表示執行個體則適合用於導覽及電話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>實作連絡人的互動

新增在 iOS 10 中，通訊的應用程式深層整合連絡人卡片連絡人應用程式。 針對已經實作連絡人互動的應用程式，使用者可以給特定人員在他們的連絡人加入給定應用程式資訊。 附加的應用程式如果，比方說，他們將訊息傳送卡片頂端的 [快速動作] 按鈕，將會列為選項會將傳送的訊息。

如果選取 3rd 合作對象應用程式時，它可以記住並顯示預設訊息指定的個人使用者想要連絡他們在下一次的方式。

連絡人的互動中的應用程式使用實作`NSUserActivity`和 iOS 10 中導入新的對應方式 framework。 如需使用方式的詳細資訊，請參閱我們[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)和[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)輔助線。

#### <a name="donating-interactions"></a>捐贈互動

看看如何應用程式可以捐贈互動：

[![](proactive-suggestions-images/activity04.png "捐贈互動概觀")](proactive-suggestions-images/activity04.png#lightbox)

應用程式會建立`INInteraction`物件，其中包含**意圖**(`INIntent`)，**參與者**和**中繼資料**。 **意圖**代表的使用者動作，例如視訊電話或傳送簡訊。 **參與者**包含接收通訊的人員。 **中繼資料**定義其他資訊，例如成功傳送訊息等等。

開發人員永遠不會直接建立的執行個體`INIntent`或`INIntentResponse`，它們將會使用其中一種 （根據應用程式代表使用者在完成的工作） 的特定子類別，繼承自父類別。 例如，`INSendMessageIntent`和`INSendMessageIntentResponse`傳送簡訊。 

互動會完全擴展之後, 再呼叫`DonateInteraction`方法，以通知之系統的互動是可供使用。

當使用者與連絡人卡片應用程式互動時，取得會與搭配互動`NSUserActivity`，然後用來啟動應用程式：

[![](proactive-suggestions-images/activity05.png "互動取得隨同 NSUserActivity 用來啟動應用程式")](proactive-suggestions-images/activity05.png#lightbox)

看看下列範例會將傳送的訊息對應方式：

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

查看這個程式碼的詳細資料，它會建立並於其中填入執行個體`NSUserActivity`(如中所示[建立活動](#Creating-an-Activity)上一節)。 接下來，它會建立的執行個體`INSendMessageIntent`(繼承自`INIntent`)，並填入所傳送訊息的詳細資料：

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

最後，系統會是互動的通知：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

完成處理常式會傳入其中應用程式可以回應捐贈成功或失敗。

### <a name="activities-best-practices"></a>活動的最佳作法

使用活動時，Apple 建議以下的最佳作法：

- 使用`NeedsSave`延遲裝載更新。
- 請確定將目前活動的強式參考。
- 僅傳送包含剛好足夠的資訊，還原狀態的小型裝載。
- 請確認活動型別識別項唯一和描述性使用反向 DNS 標記法來指定它們。 

## <a name="schemaorg"></a>Schema.org

如上所示，`NSUserActivity`有助於了解哪些使用者目前正在使用螢幕的資訊系統。 Schema.org 將類似的功能加入至 web 網頁。

Schema.org 可以提供相同的網站的位置基礎互動類型。 Apple 設計新的位置建議在原生應用程式中一樣，在 Safari 中檢視時，請使用也一樣。

某些 Schema.org 背景：

- 它提供開啟 web 標記詞彙標準。
- 其運作方式是包括網頁上的結構化中繼資料。
- 有超過 500 個結構描述表示可用的各種概念。
- 開發人員可以藉由實作該網站上，取得一些優點使用`NSUserActivity`原生應用程式中。

結構描述會排列在樹狀結構，其中特定類型例如像*餐廳*，例如繼承自更泛型型別*本機商務*。 如需詳細資訊，請參閱[Schema.org](http://schema.org)。

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

如果使用者瀏覽此網頁在 Safari 中的，然後切換到另一個應用程式會擷取從頁面的位置資訊，並提供為位置建議系統的其他部分 （如上述的活動中所見）。

Safari 會擷取符合下列結構描述屬性在網頁上的任何項目：

- **省略**
- **GeoCoordinates**
- 此為電話屬性。

如需詳細資訊，請參閱我們[搜尋網頁標記](~/ios/platform/search/web-markup.md)指南。

## <a name="consuming-location-suggestions"></a>使用位置的建議

下一節將討論取用來自其他部分 （例如對應應用程式） 系統或其他第 3 個合作對象應用程式的位置建議。

有兩種主要的方式，應用程式可以取用的位置建議：

- 透過 QuickType 鍵盤。
- 直接由耗用路由的應用程式中的位置資訊。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置的建議和 QuickType 鍵盤

如果應用程式處理的基礎文字格式的位址，應用程式可以利用透過 QuickType UI 的位置建議。 iOS 10 展開 QuickType UI 的下列功能：

- 應用程式可以在 UI 中加入提示語意方面的文字欄位。
- 應用程式可以取得主動式建議應用程式中。
- 應用程式可以從增強 autocorrection 獲益。

新`TextContentType`iOS 10 中的文字欄位控制項的屬性可讓開發人員定義語意的意圖，使用者要在給定欄位中輸入的值。 例如: 

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

會告訴系統應用程式需要使用者在給定欄位中輸入完整街道地址。 這可讓 QuickType 鍵盤而不會自動提供鍵盤上的位置建議，當使用者在此欄位中輸入值。

以下是一些較常見的類型可用來開發人員在`UITextContentType`靜態類別：

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由的應用程式和位置的建議

本節會看一下直接從位置建議使用路由的應用程式中。 若要加入這項功能的路由應用程式，開發人員將會利用現有`MKDirectionsRequest`架構，如下所示：

- 若要升級多工作業中的應用程式。
- 登錄應用程式做為路由的應用程式。
- 若要處理啟動應用程式與 MapKit`MKDirectionsRequest`物件。
- 若要讓 iOS 能夠了解如何在適當的時間，建議應用程式，以使用者為基礎的使用者參與。

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

新增 iOS 10，在應用程式可以傳送沒有開發人員必須要編碼的位址且導致地理座標的位址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>媒體應用程式的建議

如果應用程式可以處理任何類型的媒體，例如播客應用程式或資料流的媒體內容，例如音訊或視訊，請使用 iOS 10，它可以利用媒體建議的系統中。

針對處理媒體的應用程式，iOS 會支援下列行為：

- iOS 會將升級應用程式的使用者可能會使用根據其先前的行為。
- 焦點和目前檢視中，將會出現應用程式相關的建議。
- 如果應用程式符合下列觸發程序的其中一個，可能會將其提升至鎖定螢幕建議：
    - 插入耳機或藍芽裝置之後建立的連接。
    - 之後在車上取得。
    - 之後到達家用或工作。 

藉由加入簡單的 API 呼叫，在 iOS 10 中，開發人員可以建立更吸引人的鎖定螢幕體驗媒體應用程式的使用者。 使用`MPPlayableContentManager`類別以管理播放媒體、 完整媒體控制項 （例如音樂應用程式所呈現的那些） 將會出現在應用程式的鎖定畫面上。


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

這篇文章已涵蓋主動式建議，並示範如何開發人員可以使用它們 Xamarin.iOS 應用程式的磁碟機流量。 它涵蓋如何實作主動式建議的步驟，並提供使用指導方針。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
