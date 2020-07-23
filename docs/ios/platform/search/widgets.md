---
title: IOS 10 中的搜尋和主畫面 Widget 增強功能
description: 本檔說明 Apple 在 iOS 10 中對 widget 進行的增強功能，包括搜尋和主畫面 widget 的更新。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: aa9d946f295b46a3aa2d9559d28907d9a30f184b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938966"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>IOS 10 中的搜尋和主畫面 Widget 增強功能

_本文涵蓋 Apple 在 iOS 10 中對 Widget 系統進行的增強功能。_

Apple 在 Widget 系統中引進了數項增強功能，以確保小工具在任何存在於新 iOS 10 鎖定畫面上的背景上看起來很棒。 此外，widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述有多少內容可供使用，並可讓使用者展開和折迭內容。

Widget （也稱為「今日延伸模組」）是一種特殊類型的 iOS 延伸模組，可顯示少量有用的資訊，或及時公開應用程式特有的功能。 例如，新聞應用程式的 widget 會顯示前頭條新聞，而行事曆應用程式會提供兩個不同的小工具：一個用來顯示今天的事件，另一個則顯示即將到來的事件。

Widget 可高度自訂，而且可能包含 UI 元素，例如文字、影像、按鈕等。此外，開發人員也可以進一步自訂其 widget 的版面配置。

[![範例 widget](widgets-images/widgets01.png)](widgets-images/widgets01.png#lightbox)

使用者可以透過兩個主要位置來查看並與應用程式的 widget 互動：

- **[搜尋] 畫面**-使用者可以在其搜尋畫面中新增他們最有用的小工具。 您可以直接在主畫面和鎖定螢幕上進行 [搜尋] 畫面的存取。
- **主畫面**-在主畫面中，使用者可以使用 3D Touch，藉由對應用程式的圖示套用壓力來開啟 [快速動作] 清單。 應用程式的 widget 會顯示在 [快速動作] 清單上方。 如需詳細資訊，請參閱我們的[3D Touch 檔簡介](~/ios/platform/3d-touch.md)。

## <a name="widgets-developer-suggestions"></a>小工具開發人員建議

在理想的情況下，開發人員應該一律嘗試並設計 widget，讓使用者加入其搜尋畫面。 為此，Apple 提供下列建議：

- **建立絕佳的 Glanceable 體驗**-使用者想要提供簡短、Glanceable 的狀態更新資訊，或讓他們快速執行簡單的工作。 這可提供正確的資訊和互動性。 可能的話，允許使用者以單鍵執行指定的工作。 此外，因為 Widget 不支援移動流覽或滾動，所以必須將其納入小工具的設計中。
- **快速顯示內容**-Widget 的設計是要 glanceable，因此使用者在顯示小工具之後，絕對不需要等候內容載入。 Widget 應該在本機快取其內容，以便在背景載入新內容時顯示最新的內容。
- **提供適當的填補和邊界**-Widget 看起來永遠不會擁擠，因此請避免將內容延伸到小工具視圖的邊緣。 邊緣和內容之間應該一律有數個圖元寬的邊界。 Apple 也建議使用應用程式的圖示（顯示在 Widget 頂端）做為對齊指南。 如果 Widget 呈現方格版面配置，請確定方格中的專案之間有適當的填補，並嘗試將專案數限制為四個最大值。
- **使用**可調整的版面配置-Widget 的寬度會根據其執行所在的裝置和裝置的方向而有所不同。 Widget 的高度也會根據其是否以折迭（預設值）或展開（不受所有 widget 支援）狀態顯示而有所不同。 折迭的 Widget 具有大約2個和一半標準 iOS 資料表資料列的高度。 開發人員可以要求擴充小工具的大小，但最好小於螢幕的高度。 在折迭狀態中，Widget 應該只顯示必要的獨立資訊。 展開時，Widget 應該會顯示補充資訊，以增強折迭狀態所顯示的主要內容。 [快速動作] 清單中所顯示的小工具只會處於折迭狀態。
- **不要自訂 Widget 的背景小工具**會顯示在系統所提供的淺色、模糊背景上。 這麼做是為了提升 widget 之間的一致性，並改善其內容的可讀性。 避免使用影像做為 Widget 背景，因為它可能會與使用者的鎖定和主畫面壁紙衝突。
- **使用黑色或暗灰色的系統字型**-在 Widget 中顯示文字時，系統字型的效果最佳。 字型應為黑色或深色灰階，以與光線模糊的小工具背景相對應。
- 當適當 Widget 應一律與應用程式分開運作**時，提供應用程式存取權**; 不過，如果需要更深入的功能，Widget 應該能夠啟動應用程式，以查看或編輯特定的資訊片段。 絕對不要包含 [開啟應用程式] 按鈕，只允許使用者點一下內容本身，永遠不會開啟協力廠商應用程式。
- **選取明確、精簡的 Widget 名稱**-應用程式的圖示和 widget 的名稱一律會顯示在 Widget 的內容上。 Apple 建議針對其主要 Widget 使用應用程式的名稱，並針對它所提供的任何其他專案明確且簡潔的名稱。 提供自訂 Widget 標題時，其前面應該會加上應用程式的名稱（例如附近的地圖、地圖餐廳等等）。
- **在驗證增加時通知**-如果只有在使用者經過驗證和登入時才能使用其他功能或資訊，請向使用者顯示此值。 例如，「分享」應用程式可能會顯示「登入書籍」。
- **選取快速動作清單 Widget** -如果應用程式提供多個小工具，開發人員應該在使用者開啟 [快速動作] 清單時選擇要顯示的 widget，方法是使用 3D Touch 將壓力套用至應用程式的圖示。

如需使用 widget 的詳細資訊，請參閱我們的[擴充功能簡介](~/ios/platform/extensions.md)、3D Touch 檔[簡介](~/ios/platform/3d-touch.md)和 Apple 的[應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)。

## <a name="working-with-vibrancy"></a>使用 Vibrancy

Vibrancy 可確保 Widget 的文字會在 Widget 亮度、模糊背景（由系統所提供）上呈現時保持清晰。 在 iOS 10 之前，開發人員會針對 Widget 的 vibrancy 使用[NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) 。 例如：

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

這在 iOS 10 中已被取代，應取代為[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如：

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用折迭和展開的小工具

IOS 10 的新功能： widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述有多少內容可供使用，並可讓使用者展開和折迭內容。

一開始顯示 Widget 時，會處於折迭狀態。 折迭的 Widget 具有大約2個和一半標準 iOS 資料表資料列的高度。 開發人員可以要求擴充小工具的大小，但最好小於螢幕的高度。

在折迭狀態中，Widget 應該只顯示必要的獨立資訊。 展開時，Widget 應該會顯示補充資訊，以增強折迭狀態所顯示的主要內容。 例如，氣象應用程式會在折迭時顯示目前的天氣狀況，並在展開時新增每小時預測。

[快速動作] 清單中所顯示的小工具只會處於折迭狀態。 如果應用程式提供多個小工具，則開發人員應該在使用者開啟 [快速動作] 清單時選擇要顯示的 Widget，方法是使用 3D Touch 將壓力套用至應用程式的圖示。

下列範例是一個簡單的今日延伸模組（Widget），可處理折迭和展開的狀態：

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

請參閱 Widget 顯示模式特定的程式碼詳細資訊。 為了通知系統這個小工具支援擴充的狀態，它會使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

為了取得 Widget 的目前顯示模式，它會使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

為了取得折迭或展開狀態的大小上限，它會使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

而且，若要處理狀態（顯示模式）變更，它會使用：

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

除了針對每個狀態設定所要求的大小（折迭或展開），它也會更新所顯示的內容，以符合新的大小。

## <a name="summary"></a>總結

本文涵蓋了 Apple 在 iOS 10 中對 Widget 系統進行的增強功能，並示範如何在 Xamarin 中執行它們。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [擴充功能簡介](~/ios/platform/extensions.md)
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式擴充程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
