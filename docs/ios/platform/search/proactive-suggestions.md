---
title: 在 Xamarin 中提供主動式建議的簡介
description: 本文說明如何在 Xamarin iOS 應用程式中使用主動式建議，藉由讓系統主動將有用的資訊自動提供給使用者來推動參與。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 85879026639dbf92e1c85881e57124e802803bb3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436981"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>在 Xamarin 中提供主動式建議的簡介

_本文說明如何在 Xamarin iOS 應用程式中使用主動式建議，藉由讓系統主動將有用的資訊自動提供給使用者來推動參與。_

最新的 iOS 10：主動式建議提供了新聞，讓使用者能夠在適當的時間，將有用的資訊自動呈現給使用者，以與 Xamarin iOS 應用程式互動。

iOS 10 提供新的方式來推動應用程式的互動，方法是讓系統在適當的時間，自動向使用者呈現有用的資訊。 如同 iOS 9 的功能，您可以使用焦點、遞交與 Siri 建議將深層搜尋新增至應用程式， (查看 [新的搜尋 api](~/ios/platform/search/index.md)) ，使用 iOS 10，應用程式可以公開可由系統向使用者呈現的功能，可由系統在下列位置中顯示：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議

應用程式會使用一系列的技術（例如 `NSUserActivity` web 標記、核心焦點、MapKit、媒體播放機和 UIKit），將這項功能公開給系統。 此外，藉由提供應用程式的主動式建議支援，可免費取得更深入的 Siri 整合。

## <a name="location-based-suggestions"></a>以位置為基礎的建議

IOS 10 的新功能： `NSUserActivity` 類別包含 `MapItem` 屬性，可讓開發人員提供可在其他內容中使用的位置資訊。 例如，如果應用程式顯示餐廳審核，則開發人員可以將 `MapItem` 屬性設定為使用者在應用程式中所看到餐廳的位置。 如果使用者切換至 Maps 應用程式，則會自動使用餐廳的位置。

如果應用程式支援應用程式搜尋，則可以使用類別的新位址元件 `CSSearchableItemAttributesSet` 來指定使用者可能想要流覽的位置。 藉由設定 `MapItem` 屬性，會自動填入其他屬性。

除了設定 `Latitude` `Longitude` 位址元件屬性的和以外，建議應用程式 `NamedLocation` 也提供和 `PhoneNumbers` 屬性，因此 Siri 可以起始對位置的呼叫。

## <a name="web-markup-based-suggestions"></a>以 Web 標記為基礎的建議

iOS 9 新增了可在網站中包含結構化資料標記的功能，以擴充使用者在焦點和 Safari 搜尋結果中看到的內容 (請參閱 [使用 Web 標記搜尋](~/ios/platform/search/web-markup.md)) 。 iOS 10 新增了包含以位置為基礎之標記的功能 (例如[Schema.org](https://schema.org/)) 所定義的[PostalAddress](https://schema.org/PostalAddress) ，以進一步加強使用者的體驗。 例如，如果使用者在網站上流覽標示為頁面的位置，則系統可以在開啟地圖時建議相同的位置。

## <a name="text-based-suggestions"></a>以文字為基礎的建議

UIKit 已在 iOS 10 中展開，以包含[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)類別的[TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)屬性，以指定文字區域中內容的語義意義。 有了這項資訊之後，系統通常可以自動選取適當的鍵盤類型、改善自動校正的建議，並主動整合其他應用程式和網站的資訊。

例如，如果使用者在標記的文字欄位中輸入文字 `UITextContentType.FullStreetAddress` ，系統可以建議您以使用者最近觀看的位置來自動填入欄位。

## <a name="media-based-suggestions"></a>以媒體為依據的建議

如果應用程式使用 [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API 來播放媒體，則 iOS 10 可讓使用者在鎖定畫面上透過應用程式來查看專輯封面並播放媒體。

## <a name="contextual-siri-reminders"></a>內容相關 Siri 提醒

可讓使用者使用 Siri 快速提醒您，在日後查看目前在應用程式中看到的內容。 例如，如果他們在應用程式中看到餐廳評論，他們可以叫用 Siri，然後說「*當我回家時，提醒我這*一點」。 Siri 會產生具有應用程式評論連結的提醒。

## <a name="contact-based-suggestions"></a>以連絡人為依據的建議

允許應用程式的連絡人 (以及連絡人相關資訊) 出現在 iOS 裝置上的 **contact** 應用程式中，以及儲存在系統中的所有使用者連絡人。

## <a name="ride-sharing-based-suggestions"></a>以共用為基礎的建議

如果共用應用程式使用 [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API，則 iOS 10 會在使用者可能想要的時候，將它顯示為應用程式切換器中的選項。 您也必須在檔案中指定 MKDirectionsApplicationSupportedModes 索引鍵的，以將應用程式註冊為共用應用程式 `MKDirectionsModeRideShare` [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) `Info.plist` 。

如果應用程式僅支援共用的共用，系統建議會從「*取得 ...*」開始，如果有其他類型的路由方向 (例如「行走」或「自行車) 」，系統就會使用「*取得方向*...」

> [!IMPORTANT]
> 應用程式接收的 [MKMapItem](xref:MapKit.MKMapItem) 物件不能包含經度和緯度資訊，而且需要地理編碼。

## <a name="implementing-proactive-suggestions"></a>實施主動式建議

將主動式建議支援新增至 Xamarin iOS 應用程式，通常就像執行幾個 Api，或在應用程式可能已經實行的一些 Api 上擴充一樣簡單。

主動式建議可透過三種主要方式來使用應用程式：

- ** `NSUserActivity` 和 Schema.org**  -  `NSUserActivity`協助系統瞭解使用者目前在螢幕上使用的資訊。 Schema.org 會將類似的功能新增至網頁。
- **位置建議** -如果應用程式提供或取用以位置為基礎的資訊，則這些 API 擴充功能提供在應用程式之間共用此資訊的新方式。
- **媒體應用程式建議** ：系統可以根據使用者與 iOS 裝置互動的內容，升級應用程式及其媒體內容。

藉由執行下列動作，在應用程式中支援：

- **Handoff**  -  交付 `NSUserActivity`已在 iOS 8 中新增以支援交付，可讓開發人員在一部裝置上啟動活動，然後在另一個裝置上繼續進行 (請參閱) [簡介簡介](~/ios/platform/handoff.md)。
- **焦點搜尋** -iOS 9 新增了使用 `NSUserActivity` (查看以 [核心焦點搜尋](~/ios/platform/search/corespotlight.md) 的功能，可從焦點搜尋結果中升級應用程式內容) 。
- 已擴充 iOS 10 中的內容**Siri 提醒**- `NSUserActivity` 可讓 Siri 快速地查看使用者目前在應用程式中所看到的內容。
- **位置建議** -iOS 10 增強功能，可讓您 `NSUserActivity` 在應用程式內查看位置，並在整個系統中的許多地方推廣這些位置。
- 內容**相關的 Siri 要求**  -  `NSUserActivity`提供應用程式內部所提供資訊的內容給 Siri，讓使用者可以取得指示，或呼叫從應用程式中叫用 Siri 的位置。
- **連絡人互動** -iOS 10 的新功能，可 `NSUserActivity` 讓您從 [連絡人] 應用程式) 中的連絡人卡片 (的通訊應用程式，以替代的通訊方法進行升級。

所有這些功能都有一個共通的功能，它們都是 `NSUserActivity` 以一種形式使用，或是另一種形式來提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述， `NSUserActivity` 可協助系統瞭解使用者目前在螢幕上使用的資訊。 `NSUserActivity` 是輕量狀態快取機制，可在使用者流覽應用程式時，捕捉使用者的活動。 例如，查看餐廳應用程式：

[![NSUserActivity 的輕量狀態快取機制](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

具有下列互動：

1. 當使用者使用應用程式時， `NSUserActivity` 會建立，以在稍後重新建立應用程式的狀態。
2. 如果使用者搜尋餐廳，則會遵循相同的建立活動模式。
3. 同樣地，當使用者流覽結果時。 在最後一個案例中，使用者正在觀看位置，而在 iOS 10 中，系統會更清楚特定概念 (例如位置或通訊互動) 。

請仔細查看最後一個畫面：

[![NSUserActivity 詳細資料](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

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

開發人員必須確保這是與上面建立的活動相同的活動類型識別碼 (`com.xamarin.platform`) 。 應用程式會使用中儲存的資訊 `NSUserActivity` ，將狀態還原至使用者停止的位置。

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
2. 當使用者使用多工應用程式切換器移出應用程式時，系統會自動在畫面底部顯示建議 () 使用他們最愛的流覽應用程式來取得餐廳的指示。
3. 如果使用者切換至 [訊息] 應用程式，並開始輸入「 *讓我們開會*」，QuickType 鍵盤將會自動建議您貼上餐廳的位址。
4. 如果使用者切換至 Maps 應用程式，則會自動將餐廳的位址建議為目的地。
5. 這甚至適用于支援) 的協力廠商應用程式 (`NSUserActivity` ，因此使用者可以切換到共用的應用程式，而餐廳的位址也會自動建議為目的地。
6. 它也提供 Siri 的內容，讓使用者可以在餐廳應用程式內叫用 Siri，並要求「 *取得方向 ...* 」，Siri 將會提供指示給使用者正在觀看的餐廳。

上述所有功能都有一個共通的功能，它們全都指出建議的原始來源。 在上述範例的案例中，這是虛構的餐廳審核應用程式。

iOS 10 已增強，可讓應用程式透過數個小型修改和現有架構的新增功能來啟用此功能：

- `NSUserActivity` 具有額外的欄位，可供您用來捕捉在應用程式內查看的位置資訊。
- 已對 MapKit 和 CoreSpotlight 進行數個新增以取得位置。
- 定位感知功能已加入至 Siri、地圖、鍵盤、多工和系統內的其他應用程式。

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

然後，以文字為基礎的實例需要位置的文字描述 (例如 QuickType 鍵盤) ：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性的，但請確定使用者會路由傳送至應用程式想要傳送至的確切位置，因此應該包含：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼，應用程式可以取得 Siri 的存取權，讓使用者可以藉由說出像是「 *撥打電話*」之類的內容，從應用程式叫用 Siri：

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後，應用程式可以指出實例是否適用于流覽和通話：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>執行連絡人互動

IOS 10 的新功能，通訊應用程式會從連絡人卡片緊密整合至 contact 應用程式。 針對已實行連絡人互動的應用程式，使用者可以將指定的應用程式資訊新增至其連絡人中的特定人員。 比方說，如果他們按了卡片頂端的 [快速動作] 按鈕來傳送訊息，則會將附加的應用程式列為傳送訊息的選項。

如果選取協力廠商應用程式，則可將其記憶並呈現為預設的訊息，以在使用者下一次使用者想要聯絡時，為該人員提供訊息。

連絡人互動會使用 `NSUserActivity` 和 iOS 10 引進的新意圖架構在應用程式中執行。 如需使用意圖的詳細資訊，請參閱我們的 [瞭解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md) 和 [執行 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) 指南。

#### <a name="donating-interactions"></a>捐贈互動

請看看應用程式如何能夠捐贈互動：

[![捐贈互動總覽](proactive-suggestions-images/activity04.png)](proactive-suggestions-images/activity04.png#lightbox)

應用程式會建立 `INInteraction` 包含  **意圖** (`INIntent`) 、 **參與者** 和 **中繼資料**的物件。 此 **意圖** 代表使用者動作，例如進行影片呼叫或傳送文字訊息。 **參與者**包括接收通訊的人。 **中繼資料**會定義額外的資訊，例如成功傳送訊息等等。

開發人員永遠不會直接建立或的實例 `INIntent` `INIntentResponse` ，而是會根據應用程式所完成的工作，使用其中一個特定的子類別 (，以代表繼承自這些父類別的使用者) 。 例如， `INSendMessageIntent` 和 `INSendMessageIntentResponse` 用於傳送文字訊息。 

一旦完全擴展互動之後，請呼叫 `DonateInteraction` 方法，通知系統有互動可供使用。

當使用者與連絡人卡片中的應用程式互動時，互動會與相關聯 `NSUserActivity` ，然後用來啟動應用程式：

[![互動會與用來啟動應用程式的 NSUserActivity 配套](proactive-suggestions-images/activity05.png)](proactive-suggestions-images/activity05.png#lightbox)

請看下列的傳送訊息意圖範例：

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

詳細查看此程式碼，它會建立並填入 (的實例， `NSUserActivity` 如上面的 [建立活動](#creating-an-activity) 一節所示) 。 接著，它會建立 `INSendMessageIntent` 繼承自) 的 (實例 `INIntent` ，並在其中填入要傳送之訊息的詳細資料：

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`建立並傳遞 `NSUserActivity` 上面建立的：

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`建立自傳送訊息意圖 (`INSendMessageIntent`) 和回應 (`INSendMessageIntentResponse`) 剛剛建立：

```csharp
var interaction = new INInteraction (intent, response);
```

最後，系統是互動的通知：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

完成處理常式會傳入，讓應用程式可以回應成功或失敗的捐贈。

### <a name="activities-best-practices"></a>活動的最佳作法

在使用活動時，Apple 建議下列最佳作法：

- 用於 `NeedsSave` 延遲承載更新。
- 確定保留對目前活動的強式參考。
- 只傳送包含足夠資訊的小型承載來還原狀態。
- 請使用反向 DNS 標記法來指定活動類型識別碼，以確保活動類型識別碼是唯一的，而且是描述性的。 

## <a name="schemaorg"></a>Schema.org

如上所示， `NSUserActivity` 可協助系統瞭解使用者目前在螢幕上使用的資訊。 Schema.org 會將類似的功能新增至網頁。

Schema.org 可提供與網站相同的位置互動類型。 Apple 設計了新的位置建議，也就是在 Safari 中觀看，如同在原生應用程式中一樣。

部分 Schema.org 背景：

- 它提供開放式 web 標記詞彙標準。
- 其運作方式是將結構化中繼資料包含在 web 網頁上。
- 有超過500的架構代表各種可用的概念。
- 藉由在網站上執行它，開發人員可以取得 `NSUserActivity` 在原生應用程式中使用的一些優點。

架構會在樹狀結構中排列，其中特定類型（例如 *餐廳*）會繼承更多泛型型別，例如 *當地企業*。 如需詳細資訊，請參閱 [Schema.org](https://schema.org)。

例如，如果網頁包含下列資料：

```html
<script type="application/ld+json">
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

如果使用者在 Safari 中流覽過此頁面，然後切換到另一個應用程式，則會在系統的其他部分中，捕捉並提供該頁面的位置資訊，並將其提供做為系統 (其他部分的位置建議，如) 上述活動所示。

Safari 會將符合下列任何架構屬性的網頁上的任何內容解壓縮：

- **PostalAddress**
- **GeoCoordinates**
- 電話屬性。

如需詳細資訊，請參閱 [使用 Web 標記](~/ios/platform/search/web-markup.md) 指南進行搜尋。

## <a name="consuming-location-suggestions"></a>使用位置建議

下一節將討論來自系統其他部分的取用位置建議 (例如地圖應用程式) 或其他協力廠商應用程式。

應用程式有兩種主要的方式可使用位置建議：

- Via QuickType 鍵盤。
- 直接透過在路由應用程式中使用位置資訊。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置建議和 QuickType 鍵盤

如果應用程式使用以文字為基礎的格式來處理位址，應用程式可以透過 QuickType UI 來利用位置建議。 iOS 10 會使用下列功能來擴充 QuickType UI：

- 應用程式可以針對 UI 中的文字欄位，新增語義意圖的提示。
- 應用程式可以在應用程式中取得主動式建議。
- 應用程式可受益于增強的自動校正。

`TextContentType`IOS 10 中文字欄位控制項的新屬性，可讓開發人員針對使用者將在指定欄位中輸入的值定義語義意圖。 例如：

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

會告訴系統應用程式要求使用者在指定的欄位中輸入完整的街道位址。 當使用者在此欄位中輸入值時，這可讓 QuickType 鍵盤自動在鍵盤上提供位置建議。

以下是可供開發人員在靜態類別中使用的一些較常見的類型 `UITextContentType` ：

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由應用程式和位置的建議

本節將直接從路由應用程式內查看使用位置建議。 為了讓路由應用程式加入此功能，開發人員將利用現有的架構，如下所示 `MKDirectionsRequest` ：

- 以多工方式升級應用程式。
- 將應用程式註冊為路由應用程式。
- 使用 MapKit 物件來處理啟動應用程式 `MKDirectionsRequest` 。
- 讓 iOS 能夠根據使用者的互動，在適當的時間將應用程式建議給使用者。

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

IOS 10 的新功能，應用程式可以傳送不具有地理座標的位址，因為這會導致開發人員必須將位址編碼：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>媒體應用程式建議

如果應用程式處理任何類型的媒體（例如播客應用程式或串流媒體內容，例如音訊或影片）（使用 iOS 10），則可以利用系統中的媒體建議。

針對處理媒體的應用程式，iOS 支援下列行為：

- iOS 會根據其先前的行為，升級使用者可能會使用的應用程式。
- 與應用程式相關的建議會顯示在焦點和今天的觀點。
- 如果應用程式符合下列其中一個觸發程式，它可能會提升為鎖定畫面的建議：
  - 插上耳機或 Bluetooth 裝置建立連線之後。
  - 進入車輛之後。
  - 回家或工作之後。 

藉由在 iOS 10 中包含簡單的 API 呼叫，開發人員可以為媒體應用程式的使用者建立更吸引人的鎖定畫面體驗。 藉由使用 `MPPlayableContentManager` 類別來管理媒體播放， (的完整媒體控制項就像是音樂應用程式所呈現的) 會顯示在應用程式的鎖定畫面上。

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

## <a name="summary"></a>摘要

本文涵蓋主動式建議，並示範開發人員如何使用這些建議來推動對 Xamarin iOS 應用程式的流量。 其中涵蓋了實行主動式建議和提供使用指導方針的步驟。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)