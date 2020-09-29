---
title: IOS 10 中的搜尋和主畫面 Widget 增強功能
description: 本檔說明 Apple 對 iOS 10 widget 的增強功能，包括搜尋和主畫面小工具的更新。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 9a45fcccd693a51230a51600964acd75704e1b93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429717"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>IOS 10 中的搜尋和主畫面 Widget 增強功能

_本文涵蓋 Apple 對 iOS 10 Widget 系統的增強功能。_

Apple 導入了多個 Widget 系統的增強功能，可確保 Widget 在新的 iOS 10 鎖定畫面上的任何背景都看起來很棒。 此外，widget 現在包含 [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) 屬性，可讓開發人員描述有多少內容可用，並允許使用者展開和折迭內容。

Widget (也稱為 Today 延伸模組) 是一種特殊類型的 iOS 延伸模組，可顯示少量有用的資訊，或是及時公開應用程式特有的功能。 例如，「新聞」應用程式有一個小工具，其中顯示最上層頭條新聞，而行事曆應用程式提供兩個不同的小工具：一個用來顯示今天的事件，另一個則顯示即將推出的活動。

Widget 可高度自訂，而且可能包含 UI 元素，例如文字、影像、按鈕等。此外，開發人員也可以進一步自訂其 widget 的版面配置。

[![範例小工具](widgets-images/widgets01.png)](widgets-images/widgets01.png#lightbox)

有兩個主要的位置可供使用者查看並與應用程式的 widget 互動：

- **搜尋畫面** -使用者可以在其搜尋畫面中新增他們認為最有用的小工具。 您可以直接在首頁和鎖定畫面上，透過輕輕來存取搜尋畫面。
- **主畫面** -從主畫面中，使用者可以使用 3D Touch，藉由將壓力套用至應用程式的圖示來開啟快速動作清單。 應用程式的 widget 將會顯示在 [快速動作] 清單上方。 如需詳細資訊，請參閱 3D Touch 檔的 [簡介](~/ios/platform/3d-touch.md) 。

## <a name="widgets-developer-suggestions"></a>小工具開發人員建議

在理想的情況下，開發人員應該一律嘗試並設計 widget，讓使用者想要新增至其搜尋畫面。 為此，Apple 提供下列建議：

- **建立絕佳的 Glanceable 體驗** -使用者希望能提供簡短、Glanceable 的狀態更新資訊，或讓他們快速執行簡單的工作。 如此一來，就能為您提供適當數量的資訊和互動。 可能的話，讓使用者只需點一下，就能執行指定的工作。 此外，因為 Widget 不支援移動或滾動，所以必須將這項功能納入 Widget 的設計中。
- **快速顯示內容** -Widget 的設計目的是要 glanceable，因此在顯示 Widget 之後，使用者絕對不需要等待載入內容。 Widget 應該在本機快取其內容，使其可以在背景中載入新內容時顯示最近的內容。
- **提供適當的填補和邊界** -Widget 絕對不應該看起來很擁擠，因此請避免將內容延伸至 Widget 視圖的邊緣。 邊緣與內容之間應該一律有幾個圖元寬的邊界。 Apple 也建議使用應用程式的圖示（顯示在小工具頂端）作為對齊指南。 如果 Widget 提供方格配置，請確定方格中的專案之間有適當的填補，並嘗試將專案數限制為四個最大值。
- **使用** 可調整的版面配置-Widget 的寬度會根據其執行所在的裝置和裝置的方向而有所不同。 Widget 的高度也會根據其是否顯示在折迭的 (預設) 或展開 (不受所有 Widget) 狀態的支援而改變。 折迭的 Widget 具有大約兩個和半標準 iOS 資料表資料列的高度。 開發人員可以要求擴充小工具的大小，但在理想的情況下，它應該小於螢幕的高度。 在折迭狀態下，小工具應該只顯示必要的獨立資訊。 展開時，Widget 應該會顯示補充資訊，以增強折迭狀態中所顯示的主要內容。 [快速動作] 清單中顯示的小工具只會處於折迭狀態。
- **不要自訂 Widget 的背景-Widget** 會顯示在系統所提供的輕量模糊背景上。 這樣做的目的是要提升 widget 之間的一致性，並改善其內容的可讀性。 請避免使用影像做為 Widget 背景，因為它可能會與使用者的鎖定和主畫面壁紙衝突。
- **使用黑色或暗灰色的系統字型** -顯示 Widget 中的文字時，系統字型的效果最佳。 字型應該是黑色或暗灰色的色彩，才能與淺、模糊的小工具背景相對應。
- 當適當 Widget 的應用程式應該一律與應用程式分開運作時，請**提供應用程式存取權**; 不過，如果需要更深入的功能，小工具應該能夠啟動應用程式，以查看或編輯特定的資訊片段。 絕對不要包含 [開啟應用程式] 按鈕，只要允許使用者點一下內容本身，就不會開啟協力廠商應用程式。
- **選取明確的小工具名稱** -應用程式的圖示和 widget 的名稱一律會顯示在 Widget 的內容上。 Apple 建議您將應用程式的名稱用於其主要 Widget，並為其提供的任何其他專案使用清楚且簡潔的名稱。 提供自訂 Widget 標題時，應以應用程式名稱作為前置詞 (例如地圖附近、地圖餐廳等 ) 。
- **在驗證新增值時通知** -如果其他功能或資訊只有在使用者經過驗證並登入時才可供使用，請向使用者呈現此內容。 例如，共用應用程式可能會顯示「登入書籍」。
- **選取快速動作清單 Widget** -如果應用程式提供多個小工具，則開發人員應該在使用者開啟快速動作清單時選擇要顯示的小工具，方法是使用 3D Touch 將壓力套用至應用程式的圖示。

如需使用 widget 的詳細資訊，請參閱我們的 [延伸模組簡介](~/ios/platform/extensions.md)、3D Touch 檔的 [簡介](~/ios/platform/3d-touch.md) 和 Apple 的 [應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)。

## <a name="working-with-vibrancy"></a>使用 Vibrancy

Vibrancy 可確保 widget 的文字在顯示于 Widget 的光線亮時保持清晰，系統) 提供的模糊背景 (。 在 iOS 10 之前，開發人員會將 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) 用於 Widget 的 vibrancy。 例如：

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

這在 iOS 10 中已被取代，應取代為 [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) 或 [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如：

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用折迭和展開的小工具

最新的 iOS 10： widget 現在包含 [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) 屬性，可讓開發人員描述有多少內容可用，並允許使用者展開和折迭內容。

當 Widget 一開始顯示時，就會處於折迭狀態。 折迭的 Widget 具有大約兩個和半標準 iOS 資料表資料列的高度。 開發人員可以要求擴充小工具的大小，但在理想的情況下，它應該小於螢幕的高度。

在折迭狀態下，小工具應該只顯示必要的獨立資訊。 展開時，Widget 應該會顯示補充資訊，以增強折迭狀態中所顯示的主要內容。 例如，氣象應用程式會在折迭時顯示目前天氣狀況，並在展開時新增每小時預測。

[快速動作] 清單中顯示的小工具只會處於折迭狀態。 如果應用程式提供多個小工具，當使用者使用 3D Touch 將壓力套用至應用程式的圖示時，開發人員應該選擇要顯示的小工具。

下列範例是一個簡單的 Today 擴充 (Widget) ，可處理折迭和展開的狀態：

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

請詳細查看 Widget 顯示模式的特定程式碼。 為了通知系統此 Widget 支援擴充狀態，它會使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

為了取得 Widget 目前的顯示模式，它會使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

為了取得折迭或展開狀態的最大大小，它會使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

為了處理狀態 (顯示模式) 變更，它會使用：

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

除了為每個狀態設定要求的大小 (折迭或展開) 之外，也會更新顯示的內容以符合新的大小。

## <a name="summary"></a>摘要

本文涵蓋了 Apple 對 iOS 10 Widget 系統的增強功能，並示範如何在 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [延伸模組簡介](~/ios/platform/extensions.md)
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)