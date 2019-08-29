---
title: Xamarin 中的主動式建議簡介
description: 本文說明如何在 Xamarin iOS 應用程式中使用主動式建議, 藉由允許系統以主動方式自動向使用者呈現有用的資訊, 以促進參與。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 294616e7766a6613a014495e5f9d124f74465072
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121418"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Xamarin 中的主動式建議簡介

_本文說明如何在 Xamarin iOS 應用程式中使用主動式建議, 藉由允許系統以主動方式自動向使用者呈現有用的資訊, 以促進參與。_

IOS 10 的新手, 主動式建議提供新聞方式, 讓使用者能夠在適當時間自動向使用者顯示有用的資訊, 以便與 Xamarin iOS 應用程式互動。

iOS 10 提供新的方式來推動應用程式的參與, 方法是允許系統在適當的時間內, 自動向使用者顯示有用的資訊。 就像 iOS 9 一樣, 使用焦點、遞交和 Siri 建議 (請參閱[新的搜尋 api](~/ios/platform/search/index.md)) 將深入搜尋新增至應用程式的功能, 應用程式可以公開可由系統從下列位置呈現給使用者的功能。:

- 應用程式切換器
- 鎖定畫面
- 了 carplay
- 地圖
- Siri 互動
- QuickType 建議

應用程式會使用一系列的技術`NSUserActivity`(例如 web 標記、核心焦點、MapKit、媒體播放機和 UIKit) 將此功能公開給系統。 此外, 藉由提供應用程式的主動式建議支援, 它可免費取得更深入的 Siri 整合。

## <a name="location-based-suggestions"></a>以位置為基礎的建議

IOS 10 的`NSUserActivity`新功能類別`MapItem`包含屬性, 可讓開發人員提供可在其他內容中使用的位置資訊。 例如, 如果應用程式顯示餐廳審查, 則開發人員可以將`MapItem`屬性設定為使用者在應用程式中看到的餐廳位置。 如果使用者切換至地圖應用程式, 餐廳的位置就會自動可用。

如果應用程式支援應用程式搜尋, 它可以使用`CSSearchableItemAttributesSet`類別的新位址元件來指定使用者可能想要造訪的位置。 藉由設定`MapItem`屬性, 其他屬性會自動填入。

除了設定位址元件屬性`Latitude`的`Longitude`和之外, 建議應用程式也提供`NamedLocation`和`PhoneNumbers`屬性, 讓 Siri 可以起始對位置的呼叫。

## <a name="web-markup-based-suggestions"></a>以 Web 標記為基礎的建議

已新增 iOS 9, 讓您能夠在網站中包含結構化資料標記, 以來擴充使用者在焦點和 Safari 搜尋結果中看到的內容 (請參閱[使用 Web 標記搜尋](~/ios/platform/search/web-markup.md))。 iOS 10 新增了包含以位置為基礎之標記的功能 (例如[Schema.org](http://schema.org/)所定義的[PostalAddress](http://schema.org/PostalAddress) ), 以進一步加強使用者的體驗。 例如, 如果使用者在網站上查看標示為頁面的位置, 則系統可以在開啟對應時, 建議相同的位置。

## <a name="text-based-suggestions"></a>以文字為基礎的建議

IOS 10 中的 UIKit 已展開, 以包含[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)類別的[TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)屬性, 以指定文字區域中內容的語義意義。 有了這項資訊之後, 系統通常就可以自動選取適當的鍵盤類型, 改善自動校正的建議, 並主動整合其他應用程式和網站的資訊。

例如, 如果使用者在標記`UITextContentType.FullStreetAddress`的文字欄位中輸入文字, 系統可以建議自動填入使用者最近觀看的位置欄位。

## <a name="media-based-suggestions"></a>以媒體為基礎的建議

如果應用程式使用[MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API 來播放媒體, iOS 10 可讓使用者在鎖定畫面上, 透過應用程式來觀看專輯封面並播放媒體。

## <a name="contextual-siri-reminders"></a>內容相關 Siri 提醒

可讓使用者使用 Siri 快速建立提醒, 以在日後查看目前在應用程式中看到的內容。 例如, 如果他們在應用程式中看到餐廳審核, 他們可以叫用 Siri, 並說「*當我取得首頁時提醒我。* 」 Siri 會在應用程式中產生具有評論連結的提醒。

## <a name="contact-based-suggestions"></a>以連絡人為依據的建議

允許應用程式的連絡人 (與連絡人相關資訊) 顯示在 iOS 裝置上的**contact**應用程式中, 以及儲存在系統中的所有使用者連絡人。

## <a name="ride-sharing-based-suggestions"></a>根據分享的建議

如果應用程式使用[MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API, iOS 10 會在使用者可能想要的時候, 將它呈現為應用程式切換器中的一個選項。 應用程式也必須藉由在其`MKDirectionsModeRideShare` `Info.plist`檔案中指定[MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)金鑰的, 註冊為「共用」應用程式。

如果應用程式只支援 [進行中的共用], 系統建議會以「*取得 ...* 」開頭, 如果支援其他類型的路由方向 (例如步行或自行車), 系統就會使用「*取得方向*...」

> [!IMPORTANT]
> 應用程式收到的[MKMapItem](xref:MapKit.MKMapItem)物件可能不包含經度和緯度資訊, 而且需要地理編碼。

## <a name="implementing-proactive-suggestions"></a>執行主動式建議

在 Xamarin iOS 應用程式中新增主動式建議支援, 通常與執行一些 Api 或在應用程式可能已經在執行的幾個 Api 上擴充一樣容易。

主動式建議會以三種主要方式與應用程式搭配使用:

- **`NSUserActivity`而** -  Schema.org`NSUserActivity`可協助系統瞭解使用者目前在螢幕上使用的資訊。 Schema.org 在網頁上加入類似的功能。
- **位置建議**-如果應用程式提供或使用以位置為基礎的資訊, 這些 API 擴充功能會提供在應用程式之間共用此資訊的新方式。
- **媒體應用程式建議**-系統可以根據使用者與 iOS 裝置的互動內容, 來提升應用程式及其媒體內容。

藉由執行下列動作, 在應用程式中支援和:

-  -  IOS 8 中已加入提交`NSUserActivity`功能以支援遞交, 讓開發人員可以在一個裝置上啟動活動, 然後在另一個裝置上繼續執行 (請參閱[遞交簡介](~/ios/platform/handoff.md))。
- **焦點搜尋**-iOS 9 新增了使用`NSUserActivity`從焦點搜尋結果中升級應用程式內容的功能 (請參閱[使用核心焦點搜尋](~/ios/platform/search/corespotlight.md))。
- 內容**Siri 提醒**-在 iOS 10 中`NSUserActivity` , 已擴充為可讓 Siri 快速提醒您, 在日後查看使用者目前在應用程式中看到的內容。
- **位置建議**-iOS 10 增強`NSUserActivity`了可在應用程式內流覽的位置, 並在整個系統的許多地方進行升級。
- **內容相關的 Siri 要求** -  `NSUserActivity`會提供應用程式內所呈現資訊的內容給 Siri, 讓使用者可以從應用程式內取得指示或呼叫 Siri。
- **連絡人互動**-iOS 10 的新功能`NSUserActivity` , 可讓您從連絡人卡片 (在連絡人應用程式中) 升級通訊應用程式, 做為替代的通訊方法。

所有這些功能都有一個共通的東西, 它們全都`NSUserActivity`用在一個表單或另一個形式, 以提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述, `NSUserActivity`可協助系統瞭解使用者目前在螢幕上使用的資訊。 `NSUserActivity`是輕量狀態快取機制, 可在使用者流覽應用程式時加以捕捉。 例如, 查看餐廳應用程式:

[![](proactive-suggestions-images/activity02.png "NSUserActivity 的輕量狀態快取機制")](proactive-suggestions-images/activity02.png#lightbox)

具有下列互動:

1. 當使用者使用應用程式時, `NSUserActivity`會建立, 稍後再重新建立應用程式的狀態。
2. 如果使用者搜尋餐廳, 則會遵循建立活動的相同模式。
3. 同樣地, 當使用者查看結果時。 在最後一個案例中, 使用者正在觀看一個位置, 而在 iOS 10 中, 系統會更清楚特定概念 (例如位置或通訊互動)。

請仔細查看最後一個畫面:

[![](proactive-suggestions-images/activity03.png "NSUserActivity 詳細資料")](proactive-suggestions-images/activity03.png#lightbox)

在這裡, 應用程式會`NSUserActivity`建立, 並已填入資訊, 以在稍後重新建立狀態。 應用程式也包含一些中繼資料, 例如位置的名稱和位址。 建立此活動之後, 應用程式可讓 iOS 知道它代表使用者目前的狀態。

應用程式接著會決定是否要以無線方式針對遞交廣告公告活動、儲存為位置建議的暫時值, 或新增至裝置焦點索引, 以顯示在搜尋結果中。

如需有關遞交和聚焦搜尋的詳細資訊, 請參閱我們的課程[簡介](~/ios/platform/handoff.md)和[IOS 9 新搜尋 api](~/ios/platform/search/index.md)指南。

### <a name="creating-an-activity"></a>建立活動

建立活動之前, 需要先建立活動類型識別碼來識別它。 活動類型識別碼是新增至`NSUserActivityTypes` `Info.plist`應用程式檔案陣列的簡短字串, 用來唯一識別指定的使用者活動類型。 在陣列中, 應用程式支援的每個活動都會有一個專案, 並公開至應用程式搜尋。 如需詳細資訊, 請參閱我們的[建立活動類型識別碼參考](~/ios/platform/search/nsuseractivity.md)。

查看活動的範例:

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

使用活動類型識別碼建立新的活動。 接下來, 會建立一些定義活動的中繼資料, 因此可以在日後還原此狀態。 然後, 活動會獲得有意義的標題, 並附加到使用者資訊。 最後, 會啟用某些功能, 並將活動傳送給系統。

上述程式碼可以進一步增強, 以包含中繼資料, 藉由進行下列變更來提供內容給活動:

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

如果開發人員所擁有的網站能夠顯示與應用程式相同的資訊, 應用程式可以包含 URL, 而且內容可以顯示在未安裝應用程式的其他裝置上 (透過遞交):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>還原活動

若要回應使用者點擊搜尋結果 (`NSUserActivity`) 的應用程式, 請編輯**AppDelegate.cs**檔案, 並覆寫`ContinueUserActivity`方法。 例如：

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

開發人員必須確保此活動類型識別碼 (`com.xamarin.platform`) 與上面所建立的活動相同。 應用程式會使用儲存在中`NSUserActivity`的資訊, 將狀態還原回使用者停止的位置。

### <a name="benefits-of-creating-an-activity"></a>建立活動的優點

透過上述最少量的程式碼, 應用程式現在可以利用三項新的 iOS 10 功能:

- **Handoff**
- **焦點搜尋**
- **內容相關 Siri 提醒**

下一節將探討如何啟用其他兩個新的 iOS 10 功能:

- **位置建議**
- **內容相關的 Siri 要求**

### <a name="location-based-suggestions"></a>以位置為基礎的建議 

請使用上述餐廳搜尋應用程式的範例。 如果已`NSUserActivity`實作用並正確填入所有的中繼資料和屬性, 使用者就能夠執行下列動作:

1. 在應用程式中尋找他們想要在其中符合 friend 的餐廳。
2. 當使用者使用多工的應用程式切換器移出應用程式時, 系統會自動顯示建議 (位於畫面底部), 以使用我的最愛導覽應用程式來取得餐廳的指示。
3. 如果使用者切換至 [訊息] 應用程式, 並開始輸入「*讓我們達到*」, QuickType 鍵盤會自動建議貼到餐廳的位址中。
4. 如果使用者切換至地圖應用程式, 則會自動建議餐廳的位址作為目的地。
5. 這甚至適用于協力廠商應用程式 ( `NSUserActivity`支援), 因此使用者可以切換到「分享」應用程式, 而餐廳的位址也會自動建議作為目的地。
6. 它也會提供內容給 Siri, 讓使用者可以在餐廳應用程式中叫用 Siri, 並詢問「*取得方向 ...* 」, 而 Siri 會為使用者正在觀賞的餐廳提供方向。

上述所有功能都有一個共通的東西, 它們全都指出建議原本來自何處。 在上述範例中, 這是虛構的餐廳審查應用程式。

iOS 10 已增強, 可透過幾個對現有架構的小型修改和新增功能, 為應用程式啟用這項功能:

- `NSUserActivity`有額外的欄位, 可用於捕捉在應用程式內查看的位置資訊。
- 已對 MapKit 和 CoreSpotlight 新增數個專案來取得位置。
- 位置感知功能已新增至系統內的 Siri、地圖、鍵盤、多工和其他應用程式。

若要執行以位置為基礎的建議, 請從上面顯示的相同活動程式碼開始:

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

如果應用程式使用 MapKit, 就像是將目前的對應`MKMapItem`加入至活動一樣簡單:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果應用程式未使用 MapKit, 它可以採用應用程式搜尋, 並為位置指定下列新屬性:

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

如需詳細資訊, 請參閱上述程式碼。 首先, 每個實例都需要位置的名稱:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然後, 文字型實例 (例如 QuickType 鍵盤) 必須要有位置的文字描述:

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

緯度和經度是選擇性的, 但請確定使用者已路由傳送至應用程式想要將其傳送到的確切位置, 因此應包含下列內容:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

藉由設定電話號碼, 應用程式可以取得 Siri 的存取權, 讓使用者可以從應用程式叫用 Siri, 方法是說「*呼叫這個位置*」:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最後, 應用程式可以指出實例是否適合用於流覽和撥打電話:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>執行連絡人互動

IOS 10 的新功能: 通訊應用程式已從連絡人卡片緊密整合到連絡人應用程式。 針對已執行連絡人互動的應用程式, 使用者可以將指定的應用程式資訊新增至連絡人中的特定人員。 比方說, 如果他們按了卡片頂端的 [快速動作] 按鈕來傳送訊息, 則附加的應用程式將會列為傳送訊息的選項。

如果選取了協力廠商應用程式, 則可以記住它, 並以預設方式呈現, 以便在下一次使用者想要連線到指定的人員時, 將其視為訊息。

連絡人互動會使用`NSUserActivity`和 iOS 10 中引進的新意圖架構, 在應用程式中執行。 如需使用意圖的詳細資訊, 請參閱我們的[瞭解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)和實[SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)指南。

#### <a name="donating-interactions"></a>捐贈互動

看一下應用程式可以如何捐贈互動:

[![](proactive-suggestions-images/activity04.png "捐贈互動總覽")](proactive-suggestions-images/activity04.png#lightbox)

應用程式會建立`INInteraction`包含**意圖**(`INIntent`)、**參與者**和**中繼資料**的物件。 **意圖**代表使用者動作, 例如進行影片呼叫或傳送文字訊息。 **參與者**包括接收通訊的人員。 **中繼資料**會定義額外的資訊, 例如成功傳送訊息等等。

開發人員絕對不會直接建立`INIntent`或`INIntentResponse`的實例, 而是會使用繼承自這些父類別的其中一個特定子類別 (以應用程式代表使用者完成的工作為基礎)。 例如, `INSendMessageIntent`和`INSendMessageIntentResponse`用於傳送文字訊息。 

一旦完全填入互動, 請呼叫`DonateInteraction`方法以通知系統有互動可供使用。

當使用者從連絡人卡片與應用程式互動時, 互動會與`NSUserActivity`整合, 然後用來啟動應用程式:

[![](proactive-suggestions-images/activity05.png "互動會與用來啟動應用程式的 NSUserActivity 配套")](proactive-suggestions-images/activity05.png#lightbox)

請看下面的「傳送訊息」意圖範例:

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

查看這段程式碼的詳細資訊, 它會建立並填入`NSUserActivity`的實例 (如上面的[建立活動](#creating-an-activity)一節所示)。 接下來, 它會建立的`INSendMessageIntent`實例 (繼承自`INIntent`), 並在其中填入所傳送之訊息的詳細資料:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

會建立並傳遞上述所`NSUserActivity`建立的: `INSendMessageIntentResponse`

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

是從傳送訊息意圖 (`INSendMessageIntent`) 和回應 (`INSendMessageIntentResponse`) 建立的, 而這是剛建立的: `INInteraction`

```csharp
var interaction = new INInteraction (intent, response);
```

最後, 系統是互動的通知:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

完成處理常式會傳入, 應用程式可以在其中回應「捐贈成功」或「失敗」。

### <a name="activities-best-practices"></a>活動最佳做法

在使用活動時, Apple 建議下列最佳作法:

- `NeedsSave`用於延遲承載更新。
- 請務必保留對目前活動的強式參考。
- 只傳送包含足夠資訊的小型承載以還原狀態。
- 使用反向 DNS 標記法加以指定, 以確定活動類型識別碼是唯一的, 而且是描述性的。 

## <a name="schemaorg"></a>Schema.org

如上所示, `NSUserActivity`可協助系統瞭解使用者目前在螢幕上使用的資訊。 Schema.org 在網頁上加入類似的功能。

Schema.org 可以提供與網站相同的位置型互動類型。 Apple 已設計新的位置建議, 以便在 Safari 中觀看時, 就像在原生應用程式中一樣地運作。

部分 Schema.org 背景:

- 它提供開放式 web 標記詞彙標準。
- 其運作方式是在網頁上包含結構化中繼資料。
- 有超過500的架構代表各種可用的概念。
- 藉由在網站上執行它, 開發人員可以取得在原生應用程式`NSUserActivity`中使用的部分優點。

架構會以樹狀結構 (例如*餐廳*的特定型別) 來排列, 並繼承自更多泛型型別, 例如*當地商家*。 如需詳細資訊, 請參閱[Schema.org](http://schema.org)。

例如, 如果網頁包含下列資料:

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

如果使用者在 Safari 中流覽此頁面, 然後切換至另一個應用程式, 則會在系統的其他部分中, 捕捉該頁面的位置資訊, 並將其當做位置建議提供 (如上述活動所示)。

Safari 會在網頁上解壓縮任何符合下列任何架構屬性的專案:

- **PostalAddress**
- **GeoCoordinates**
- 電話屬性。

如需詳細資訊, 請參閱[使用 Web 標記進行搜尋](~/ios/platform/search/web-markup.md)指南。

## <a name="consuming-location-suggestions"></a>使用位置建議

下一節將涵蓋來自系統的其他部分 (例如 Maps 應用程式) 或其他協力廠商應用程式的使用位置建議。

應用程式可以使用位置建議的主要方式有兩種:

- 透過 QuickType 鍵盤。
- 直接使用路由應用程式中的位置資訊。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置建議和 QuickType 鍵盤

如果應用程式處理以文字為基礎之格式的位址, 應用程式可以透過 QuickType UI 利用位置建議。 iOS 10 會以下列功能擴展 QuickType UI:

- 應用程式可以針對 UI 中的文字欄位, 加入有關語義意圖的提示。
- 應用程式可以在應用程式中取得主動式建議。
- 應用程式可受益于增強型自動校正。

IOS 10 `TextContentType`中文字欄位控制項的新屬性, 可讓開發人員定義使用者要在指定欄位中輸入之值的語義意圖。 例如：

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

會告訴系統應用程式要求使用者在指定欄位中輸入完整的街道位址。 這可讓 QuickType 鍵盤在使用者于此欄位輸入值時, 自動在鍵盤上提供位置建議。

以下是可供開發人員在`UITextContentType`靜態類別中使用的一些較常見類型:

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由應用程式和位置的建議

本節將直接從路由應用程式中查看使用位置建議。 若要讓路由應用程式新增這種功能, 開發人員可以利用`MKDirectionsRequest`現有的架構, 如下所示:

- 以多工方式升級應用程式。
- 將應用程式註冊為路由應用程式。
- 使用 MapKit `MKDirectionsRequest`物件來處理啟動應用程式。
- 讓 iOS 能夠根據使用者參與, 學習在適當的時間向使用者建議應用程式。

以 MapKit `MKDirectionsRequest`物件啟動應用程式時, 應該會自動開始將使用者指示提供給要求的位置, 或呈現可讓使用者輕鬆開始取得指示的 UI。 例如：


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

請參閱此程式碼的詳細資訊。 它會進行測試, 以查看它是否為有效的目的地要求:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是, 則它會`MKDirectionsRequest`從 URL 建立:

```csharp
var request = new MKDirectionsRequest(url);
```

IOS 10 的新功能, 應用程式可以傳送不具有地理座標的位址, 因為這會導致開發人員需要將位址編碼:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>媒體應用程式建議

如果應用程式處理任何類型的媒體, 像是播客應用程式或串流媒體內容 (例如音訊或影片) 與 iOS 10, 它可以利用系統中的媒體建議。

針對處理媒體的應用程式, iOS 支援下列行為:

- iOS 會根據其先前的行為來升級使用者可能會使用的應用程式。
- 與應用程式相關的建議會以焦點和今天的觀點呈現。
- 如果應用程式符合下列其中一個觸發程式, 它可能會提升為鎖定畫面的建議:
  - 插入耳機或藍牙裝置之後, 會建立連接。
  - 進入汽車之後。
  - 抵達家裡或工作之後。 

藉由在 iOS 10 中包含簡單的 API 呼叫, 開發人員可以為媒體應用程式的使用者建立更吸引人的鎖定畫面體驗。 藉由使用`MPPlayableContentManager`類別來管理媒體播放, 系統會在應用程式的鎖定畫面上顯示完整的媒體控制項 (例如音樂應用程式所呈現的控制項)。


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

本文涵蓋主動式建議, 並示範開發人員如何使用它們來驅動 Xamarin iOS 應用程式的流量。 其中涵蓋了實施主動式建議和呈現使用指導方針的步驟。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
