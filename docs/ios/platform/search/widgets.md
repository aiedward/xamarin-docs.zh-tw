---
title: 搜尋和主畫面 Widget 增強功能
description: 本文說明 Apple iOS 10 的 Widget 系統所做的增強功能。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e7a64738f29ab2b5c62659d721beb50db7c9adb5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="search-and-home-screen-widget-enhancements"></a>搜尋和主畫面 Widget 增強功能

_本文說明 Apple iOS 10 的 Widget 系統所做的增強功能。_


Apple 已引進了數個增強功能 Widget 系統，以確保 widget 看起來很棒，於任何存在於新的 iOS 10 鎖定畫面的背景。 此外，widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

小工具 （也稱為今天擴充功能） 是資訊的一種特殊的 iOS 擴充功能，顯示少量有用或公開及時的應用程式專屬功能。 例如，新聞應用程式具有顯示頭條小工具，而且行事曆應用程式提供兩個不同的 widget： 一個用來顯示今天的事件，另一種顯示即將發生的事件。

Widget 進行大幅度的自訂，而且可能包含 UI 項目，例如文字、 影像、 按鈕等等。此外，開發人員可以進一步自訂其小工具的版面配置。

[![](widgets-images/widgets01.png "範例 widget")](widgets-images/widgets01.png#lightbox)

有兩個主要的地方，使用者可以檢視並與其互動的應用程式的 Widget:

- **搜尋螢幕**-使用者可以新增它們發現其搜尋螢幕最有用的 Widget。 搜尋螢幕時存取撥動首頁和鎖定畫面上的權限。
- **主畫面**-從首頁畫面中，使用者可以用於 3D Touch 不足的壓力套用至應用程式的圖示來開啟快速動作清單。 快速動作清單上方，將會顯示應用程式的 Widget。 請參閱我們[簡介 3D Touch](~/ios/platform/3d-touch.md)文件的詳細資訊。

## <a name="widgets-developer-suggestions"></a>小工具開發人員建議

在理想情況下，開發人員應該一律再試一次，並設計 Widget，使用者會想要新增至其搜尋螢幕。 為了這個目的，Apple 有下列建議：

- **建立良好，而一目了然經驗**-使用者想要的小工具，提供簡短、 一目了然資訊的狀態更新，或讓他們快速地執行簡單的工作。 這可讓提供的資訊和互動功能的必要資料量。 可能的話，可讓使用者點選執行指定的工作。 此外，由於小工具不支援移動或捲動，這會有在小工具的設計考量。
- **快速顯示內容**-Widget 都是設計成一目了然，讓使用者完全不需等候載入在 Widget 會顯示的內容。 Widget 應該快取其內容，在本機上，全新的內容在背景中載入時，它們可以顯示新的內容。
- **提供適當的填補和邊界**-Widget 應該永遠不會看起來十分擁擠，因此請避免擴充到的小工具檢視邊緣的內容。 永遠應該邊緣與內容之間的數個像素寬邊界。 Apple 也建議使用的應用程式圖示，顯示在頂端的 Widget 中，為已對齊指南。 如果小工具顯示的格線版面配置，請確認有正確的方格中的項目之間的填補，並嘗試限制的四個最大項目數目。
- **使用具有可調適性配置**-Widget 寬度會隨著執行的裝置 」 和 「 裝置方向。 Widget 的高度可以也隨著顯示中摺疊 （預設值） 或 （不支援所有的 Widget） 的展開狀態。 摺疊的 Widget 的高度是大約兩個半標準 iOS 資料表資料列。 開發人員可以在展開的小工具，要求大小，但它在理想情況下應小於螢幕的高度。 在摺疊狀態，此 Widget 應該會顯示只有基本、 獨立的資訊。 當展開、 [] Widget 中應該顯示增強顯示中摺疊狀態的主要內容的補充資訊。 快速動作清單中顯示的小工具只能在摺疊狀態。
- **不要自訂小工具的背景**-Widget 會顯示在系統所提供的少量、 套用模糊效果背景。 這是為了提升 Widget 之間的一致性，並改善其內容的可讀性。 避免使用當做 Widget 背景的映像，因為它無法與使用者的鎖定與主畫面蹓相衝突。
- **使用系統中的字型黑色或暗灰色**-在 Widget 中，系統字型適用於最佳顯示文字時。 字型應該突顯淺色、 模糊 Widget 背景的黑色或暗灰色色彩。
- **提供應用程式存取時適當**-小工具應一律從其應用程式分開運作，不過，如果需要更深入的功能，此 Widget 應該就能夠啟動應用程式，以查看或編輯特定資訊。 永遠不會包含 「 開啟應用程式 」 的按鈕，僅允許使用者點選內容本身，並永遠不會開啟第 3 合作對象應用程式。
- **選取 清除、 簡潔的 Widget 名稱**-應用程式的圖示和 Widget 的名稱會一直顯示透過小工具的內容。 Apple 建議使用其主要的小工具的應用程式的名稱，它會提供任何其他的清楚、 簡潔名稱。 當提供自訂的小工具標題時，它們應該加上 （例如對應附近、 Maps 餐廳等） 的應用程式的名稱。
- **通知驗證將值加入**-如果有其他功能或資訊只有當使用者進行驗證而且登入，這對使用者顯示。 例如，共用應用程式 」 登入活頁簿的賽車"我說的賽車。
- **選取 快速動作清單 Widget** -如果應用程式提供一個以上的小工具，開發人員應該選擇向使用者顯示 快速動作清單將不足的壓力套用至使用 3D Touch 的應用程式的圖示。

如需使用小工具的詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)，[簡介 3D Touch](~/ios/platform/3d-touch.md)文件集和 Apple[應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>使用 Vibrancy

Vibrancy 可確保 Widget 的文字會保持 Widget 的光線，模糊 （由系統所提供） 的背景上呈現時易於閱讀。 開發人員會使用之前 iOS 10 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)的 Widget vibrancy。 例如: 

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

這在 iOS 10 中已取代，應該取代為[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如: 

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用 摺疊和展開 Widget

新增 ios 10 widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

當一開始顯示小工具時，它處於摺疊。 摺疊的 Widget 的高度是大約兩個半標準 iOS 資料表資料列。 開發人員可以在展開的小工具，要求大小，但它在理想情況下應小於螢幕的高度。 

在摺疊狀態，此 Widget 應該會顯示只有基本、 獨立的資訊。 當展開、 [] Widget 中應該顯示增強顯示中摺疊狀態的主要內容的補充資訊。 例如，天氣應用程式會顯示目前的天氣條件摺疊，，並將預測時展開每小時。

快速動作清單中顯示的小工具只能在摺疊狀態。 如果應用程式提供一個以上的小工具，開發人員應該選擇向使用者顯示 快速動作清單將不足的壓力套用至使用 3D Touch 的應用程式的圖示。

下列範例是簡單今天的延伸 (Widget) 處理的摺疊和展開狀態：

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

查看 Widget 的顯示模式的特定程式碼的詳細資料。 對系統通知這個小工具支援的展開狀態，它會使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

若要取得目前顯示模式小工具，它會使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

若要取得的最大摺疊或展開狀態，它會使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

若要處理變更的狀態 （顯示模式），它會使用：

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

除了設定每個狀態 （摺疊或展開） 要求的大小，它也會更新以符合新大小要顯示的內容。

## <a name="summary"></a>總結

這篇文章已涵蓋 Apple iOS 10 的 Widget 系統所做的增強功能，並顯示如何將它們實作 Xamarin.iOS 中。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [延伸模組簡介](~/ios/platform/extensions.md)
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
