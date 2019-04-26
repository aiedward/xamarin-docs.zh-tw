---
title: 搜尋和主畫面 Widget 增強功能在 iOS 10
description: 本文說明 Apple 對 iOS 10，包括搜尋和主畫面 widget 的更新中的 Widget 的增強功能。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f693b480fff141c177ed135ced60afd65abd77de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082161"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>搜尋和主畫面 Widget 增強功能在 iOS 10

_本文章涵蓋 Apple 對小工具系統在 iOS 10 中的增強功能。_

Apple 已引進數個增強功能的小工具系統，以確保 widget 好看任何存在於新的 iOS 10 的鎖定畫面的背景。 此外，widget 現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

小工具 （也稱為立即擴充功能） 是一種特殊的 iOS 延伸模組顯示少量的實用資訊或公開 neodpověděl včas 應用程式專屬功能。 比方說，新聞應用程式有一種 widget，顯示最上層的頭條新聞及行事曆應用程式提供兩個不同的 widget： 一個用來顯示今天的事件，另一種顯示即將發生的事件。

小工具高度可自訂，而且可能包含 UI 項目，例如文字、 影像、 按鈕等等。此外，開發人員可以進一步自訂其小工具的版面配置。

[![](widgets-images/widgets01.png "範例小工具")](widgets-images/widgets01.png#lightbox)

有兩個主要的地方，使用者可以檢視並與其互動的應用程式的 Widget:

- **搜尋畫面**-使用者可以加入他們找到最適合他們的搜尋畫面的 Widget。 搜尋畫面存取撥動首頁和鎖定畫面上的權限。
- **首頁 畫面**-從首頁畫面中，使用者可以使用 3D 觸控，開啟 快速動作清單將壓力套用至應用程式的圖示。 應用程式的小工具將會顯示快速動作清單上方。 請參閱我們[3D 觸控簡介](~/ios/platform/3d-touch.md)文件的詳細資訊。

## <a name="widgets-developer-suggestions"></a>小工具開發人員建議

在理想情況下，開發人員應該一律嘗試，並設計 Widget，使用者會想要新增至其搜尋畫面。 為此，Apple 會有下列建議：

- **建立太好了，Glanceable 體驗**-使用者想要的小工具所提供的狀態更新的簡短、 glanceable 資訊或讓他們快速地執行簡單的工作。 這可讓提供的資訊和互動性的基本權限的數量。 可能的話，可讓使用者執行特定的工作，只要點選一下。 此外，因為小工具不支援移動或捲動，這會有在小工具的設計考量。
- **快速顯示的內容**-Widget 都是設計成 glanceable，因此使用者永遠不應該等候要顯示的小工具之後載入內容。 小工具應快取其內容，在本機，以便在背景中載入新鮮的內容時，它們可以顯示最近使用的內容。
- **提供適當的邊框距離及邊界**-Widget 應該永遠不會看起來擁擠，因此請避免 擴充的小工具的檢視邊緣的內容。 應該一律會有數個像素寬的邊界之間的邊緣和內容。 Apple 也建議使用的對齊方式指南 Widget 中，頂端所顯示的應用程式的圖示。 如果小工具顯示格線版面配置，請確定有適當的方格中的項目之間的填補，然後再次嘗試限制到四個最大值的項目數。
- **使用可調整的版面配置**-小工具的寬度會有所不同及其執行所在的裝置和裝置的方向。 小工具的高度可以也有所不同顯示處於摺疊 （預設值） 或 （不支援所有小工具） 的展開狀態。 摺疊小工具有大約兩個半標準 iOS 資料表的資料列的高度。 開發人員可以擴充的小工具的要求大小，但它在理想情況下應小於螢幕的高度。 在摺疊狀態下，小工具應該會顯示唯一基本、 獨立的資訊。 當展開、 小工具應該會顯示增強中摺疊狀態顯示的主要內容的補充資訊。 在 [快速動作] 清單中顯示的小工具才會是摺疊狀態。
- **沒有自訂小工具的背景**-Widget 會顯示在系統所提供的少量、 套用模糊效果背景。 這是為了提升小工具之間的一致性，並改善其內容範圍的可讀性。 請避免使用小工具的背景映像，因為它無法與使用者的鎖定與主畫面蹓衝突。
- **使用黑色或暗灰色的系統字型**-顯示在小工具 」，這是最佳系統字型的運作方式的文字。 字型應該凸顯與套用模糊效果，小工具背景的黑色或暗灰色色彩。
- **提供應用程式存取時適當**-小工具應一律從其應用程式分開運作，不過，如果需要更深入的功能，小工具應該能夠啟動應用程式，以查看或編輯特定資訊。 永遠不會包括 「 開啟應用程式 」 按鈕，只允許使用者點選本身的內容，並永遠不會開啟第 3 方應用程式。
- **選取 清除，簡潔的小工具名稱**-應用程式的圖示和小工具的名稱一定會顯示透過 Widget 的內容。 Apple 建議使用其主要的小工具的應用程式的名稱和它提供任何其他的清楚、 簡潔的名稱。 提供自訂的小工具標題，當它們前面應該要有應用程式的名稱 （例如對應附近，Maps 餐廳等）。
- **通知驗證將值加入**-如果有其他功能或資訊只在驗證使用者時才而且登入，這對使用者顯示。 比方說，大功告成，共用應用程式可能會說 「 登入至活頁簿好好體驗吧 」。
- **選取 快速動作清單 Widget** -如果應用程式提供多個 Widget，開發人員應該選擇呈現時使用者藉由套用不足的壓力，來使用 3D 觸控的應用程式的圖示顯示快速動作清單。

如需有關使用小工具的詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)， [3D 觸控簡介](~/ios/platform/3d-touch.md)文件和 Apple[應用程式擴充功能程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>使用 Vibrancy

Vibrancy 可確保該 Widget 的文字會維持在小工具的光線，套用模糊效果的背景 （由系統提供） 列於時易於閱讀。 開發人員會使用在 iOS 10 之前, [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)的小工具的 vibrancy。 例如：

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

這會將其取代在 iOS 10 中，而且應該取代為[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或是[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如: 

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用 摺疊或展開的小工具

新增至 iOS 10，小工具現在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)屬性，可讓開發人員描述多少內容可供使用，並可讓使用者展開和摺疊的內容。

時最初顯示的小工具，它會處於摺疊狀態。 摺疊小工具有大約兩個半標準 iOS 資料表的資料列的高度。 開發人員可以擴充的小工具的要求大小，但它在理想情況下應小於螢幕的高度。 

在摺疊狀態下，小工具應該會顯示唯一基本、 獨立的資訊。 當展開、 小工具應該會顯示增強中摺疊狀態顯示的主要內容的補充資訊。 例如，天氣預報應用程式目前天氣狀況摺疊時，會顯示，並將預測時展開的每小時。

在 [快速動作] 清單中顯示的小工具才會是摺疊狀態。 如果應用程式提供多個 Widget，開發人員應該選擇呈現時使用者藉由套用不足的壓力，來使用 3D 觸控的應用程式的圖示顯示快速動作清單。

下列範例是簡單今天的延伸模組 (Widget) 處理的摺疊和展開狀態：

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

探討小工具顯示模式的特定程式碼在 詳細資料。 若要通知系統此小工具支援的展開狀態，它會使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

若要取得目前顯示模式的小工具，它會使用：

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

除了設定所要求的大小，每個狀態 （已摺疊或展開），它也會更新以符合新大小要顯示的內容。

## <a name="summary"></a>總結

這篇文章已涵蓋 Apple 對小工具系統在 iOS 10 中的增強功能，並顯示如何在 Xamarin.iOS 中實作它們。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [延伸模組簡介](~/ios/platform/extensions.md)
- [3D 觸控簡介](~/ios/platform/3d-touch.md)
- [應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
