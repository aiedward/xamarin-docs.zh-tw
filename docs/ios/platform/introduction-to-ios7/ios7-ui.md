---
title: iOS 7 使用者介面概觀
description: iOS 7 引進了眾多的使用者介面變更。 本文強調一些較大型的變更，包括控制項的視覺外觀，以及支援新設計的 Api。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 724d6ab8d39a5c0054302685c5f9f4ba0f3d9908
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431731"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 使用者介面概觀

_iOS 7 引進了眾多的使用者介面變更。本文強調一些較大型的變更，包括控制項的視覺外觀，以及支援新設計的 Api。_

iOS 7 著重在 chrome 上的內容。 IOS 7 中的使用者介面專案會藉由移除多餘的框線、狀態列和導覽列等屬性（例如，減少內容視圖所使用的螢幕空間量）來消除強調 chrome。 在 iOS 7 中，內容是設計來使用整個畫面。

iOS 7 引進了幾項其他變更：色彩是用來區分使用者介面專案，而不是像是按鈕框線的屬性。 許多專案（例如導覽列和狀態列）現在都是模糊、半透明或透明，而內容視圖會將區域放在其下方。 這些內容視圖會透過模糊的橫條轉譯，在使用者介面中傳達深度的感覺。

本文涵蓋 iOS 7 中使用者介面專案的幾項變更，以及與新使用者介面設計相關的各種 Api。

## <a name="view-and-control-changes"></a>View 和 Control 變更

UIKit 中的所有觀點都符合 iOS 7 的新外觀和風格。 本節將重點說明這些視圖的一些變更，以及已變更為支援新 UI 的相關 Api。

### <a name="uibutton"></a>UIButton

從類別建立的按鈕 `UIButton` 現在是無邊框的，預設沒有背景，如下所示：

 ![範例 UIButton](ios7-ui-images/button.png)

`UIButtonType.RoundedRect`樣式已被取代。 如果用於 iOS 7， `UIButtonType.RoundedRect` 將會導致 `UIButtonType.System` 使用，這會產生不含背景或可見邊緣的預設按鈕樣式，如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

類似于 `UIButton` ，橫條按鈕也是無邊框的，預設為如下 `UIBarButtonItemStyle.Plain` 所示的新樣式：

 ![範例 UIBarButtonItem](ios7-ui-images/barbuttonplain.png)

此外， `UIBarButtonItemStyle.Bordered` 樣式已被取代。 `UIBarButtonItemStyle.Bordered`IOS 7 中的設定會導致 `UIBarButtonItemStyle.Plain` 使用樣式。

`UIBarButtonItemStyle.Done`樣式尚未被取代。 不過，它也會建立未加上邊框的按鈕，而只有粗體文字樣式如下所示：

 ![完成樣式中的範例 UIBarButtonItem](ios7-ui-images/barbuttondone.png)

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 外觀和操作的樣式變更之外，警示視圖也不再支援透過子視圖自訂。 雖然 `UIAlertView` 繼承自 `UIView` ，但 `AddSubview` 在上呼叫 `UIAlertView` 不會有任何作用。 例如，請參考下列程式碼：

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

這會產生標準的警示視圖，並忽略子視圖，如下所示：

 ![範例 UIAlertView](ios7-ui-images/alert.png)

 注意： UIAlertView 在 iOS 8 中已被取代。 使用 iOS 8 和更新版本中的警示查看來查看 [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) 配方。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

IOS 7 中的分段控制項是透明的，而且支援色調色彩。 色調色彩用於文字和框線色彩。 選取區段時，會在背景和文字之間交換色彩，並使用色彩來反白顯示選取的區段，如下所示：

 ![範例 UISegmentedControl](ios7-ui-images/segmentedcontrol.png)

此外， `UISegmentedControlStyle` 已在 iOS 7 中淘汰。

### <a name="picker-views"></a>選擇器視圖

選擇器視圖的 API 大多保持不變;不過，iOS 7 設計指導方針現在狀態選擇器視圖應以內嵌方式呈現，而非從畫面底部以動畫顯示的輸入視圖，或透過推送至流覽控制器的堆疊的新控制器（如同先前的 iOS 版本）。 這可在系統行事曆應用程式中看到：

 ![這可在系統行事曆應用程式中看到](ios7-ui-images/inlinepicker.png)

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

當屬性設定為 true 時，搜尋列現在會顯示在導覽列中 `UISearchDisplayController.DisplaysSearchBarInNavigationBar` 。 當設定為 false 時-預設值-在顯示搜尋控制器時隱藏巡覽列。

下列螢幕擷取畫面顯示中的搜尋列 `UISearchDisplayController` ：

 ![範例 UISearchDisplayController](ios7-ui-images/searchbar.png)

### <a name="uitableview"></a>UITableView

這些 Api `UITableView` 主要是不變的; 不過，樣式已大幅變更，以符合新的使用者介面設計。 內部視圖階層也有點不同。 這項變更不會影響大部分的應用程式，但這是值得注意的事項。

#### <a name="grouped-table-style"></a>群組資料表樣式

已變更的分組樣式已更新，內容現在會延伸至螢幕邊緣，如下所示：

 ![範例分組的資料表樣式](ios7-ui-images/table1.png)

#### <a name="separatorinset"></a>SeparatorInset

您現在可以藉由設定屬性來縮排資料列分隔符號 `UITableVIewCell.SeparatorInset` 。 例如，下列程式碼會用來將左邊緣的儲存格縮排：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

這會產生具有縮排資料格的表格視圖，如下所示：

 ![範例 Ios uitableview 範例 SeparatorInset](ios7-ui-images/separatorinset.png)

#### <a name="table-button-styles"></a>資料表按鈕樣式

資料表視圖中使用的各種按鈕都有變更。 下列螢幕擷取畫面顯示在編輯模式中的資料表視圖：

 ![此螢幕擷取畫面顯示在編輯模式中的表格視圖](ios7-ui-images/table2.png)

### <a name="additional-control-changes"></a>其他控制項變更

其他 UIKit 控制項也有變更，包括滑杆、交換器和 steppers。 這些變更只是視覺效果。 如需詳細資訊，請參閱 Apple 的 [iOS 7 UI 轉換指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>一般消費者介面變更

除了 UIKit 中的變更之外，iOS 7 還引進了 UI 的各種視覺變更，包括：

- 全螢幕內容
- 橫條圖外觀
- 色調色彩

<a name="fullscreen"></a>

### <a name="full-screen-content"></a>全螢幕內容

iOS 7 的設計目的是讓應用程式充分利用整個畫面。 視圖控制器現在會以狀態列和導覽列重迭（如果有的話），而不是顯示在狀態和導覽列下方。

當您準備 iOS 7 的應用程式時，您可以使用 *Interface Builder* 或 *Xamarin iOS 設計*工具，以視覺化方式調整子檢視。 您也可以使用其中一個新的 Api 來協助以程式設計方式處理全螢幕內容。 以下介紹這些 Api。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide` 並 `BottomLayoutGuide` 做為參考，以供視圖的開頭或結尾，讓內容不會被半透明的橫條重迭 `UIKit` ，如下列範例所示：

 [![未以透明 UIKit 橫條重迭的範例內容](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

這些 Api 可以用來計算螢幕頂端或底部的視圖位移，並據以調整內容位置：

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

我們可以使用上面所計算的值 `ImageView` ，從畫面頂端設定我們的位移，如此就可以看到整個影像：

 [![範例 ImageViews 從畫面頂端位移](ios7-ui-images/good2.png)](ios7-ui-images/good2.png#lightbox)

如需實用範例，請參閱 [ImageViewer](/samples/xamarin/ios-samples/ios7-ui-updates/) 。

在將 View 加入階層之後，會以動態方式產生位移值，因此嘗試讀取 `TopLayoutGuide` 和中的 `BottomLayoutGuide` 值 `ViewDidLoad` 將會傳回0。 在載入視圖之後計算值-例如，在中 `ViewDidLayoutSubviews` 。

> [!IMPORTANT]
> `TopLayoutGuide` 和在 `BottomLayoutGuide` iOS 11 中已被取代，以促進新的安全區域配置。 Apple 已指出使用安全區域與 iOS 11 之前的 iOS 版本相容。 如需詳細資訊，請參閱 [更新 iOS 的應用程式 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) 指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 會指定應將視圖的邊緣延伸至全螢幕（不論是否有橫條半透明度）。 在 iOS 7 中，導覽列和工具列會顯示在控制器的視圖上方，與舊版的 iOS 版本不同，它們沒有佔用相同的空間。 IOS 7 相片應用程式說明預設 `UIViewController.EdgesForExtendedLayout` 值： `UIRectEdge.All` 。 這項設定會以內容填滿所有四個邊緣的內容，並建立重迭和全螢幕效果：

 [![範例 EdgesForExtendedLayout](ios7-ui-images/photos.png)](ios7-ui-images/photos.png#lightbox)

點擊影像會移除橫條，並顯示全螢幕的影像：

 [![已移除橫條的 EdgesForExtendedLayout](ios7-ui-images/photos2.png)](ios7-ui-images/photos2.png#lightbox)

因為全螢幕內容是預設值，所以針對 iOS 6 設定的應用程式將會裁剪一部分的視圖，如下列螢幕擷取畫面所示：

 [![針對 iOS 6 設定的應用程式將會裁剪部分的視圖，如下列螢幕擷取畫面所示](ios7-ui-images/clipped.png)](ios7-ui-images/clipped.png#lightbox)

修改 `UIViewController.EdgesForExtendedLayout` 此行為的屬性會進行調整。 我們可以將 view 指定為不填滿任何邊緣，因此我們的觀點將避免在每個方向)  (導覽或工具列所佔用的空間中顯示內容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我們的應用程式中，我們會看到重新置放的視圖，因此可以看到整個影像：

 [![顯示整個影像的範例](ios7-ui-images/good.png)](ios7-ui-images/good.png#lightbox)

請注意，雖然 `TopLayoutGuide/BottomLayoutGuide` 和 api 的效果 `EdgesForExtendedLayout` 很類似，但其目的是要填滿不同的目標。 `EdgesForExtendedLayout`從預設值變更設定，可能會修正針對 iOS 6 而設計之應用程式中的裁剪視圖，但良好的 iOS 7 設計應接受全螢幕的美觀，並提供全螢幕的觀賞體驗， `TopLayoutGuide` 並依賴並 `BottomLayoutGuide` 適當地將要操作的內容定位至方便的使用者位置。

如需實用範例，請參閱 [ImageViewer](/samples/xamarin/ios-samples/ios7-ui-updates/) 。

### <a name="status-and-navigation-bars"></a>狀態和導覽列

狀態列和導覽列會以透明呈現。 狀態列是透明的，而工具列和導覽列是半透明且模糊的，可在使用者介面中傳達深度的感覺。 下列螢幕擷取畫面顯示此模糊和透明度，其中收集視圖的藍色背景色彩會顯示狀態和導覽列，讓它們具有淺藍色外觀：

 ![範例狀態和導覽列模糊](ios7-ui-images/transparent-navbar.png)

#### <a name="status-bar-styles"></a>狀態列樣式

除了模糊和透明度，狀態列的前景可以是淺或深色 (深色是預設) 。 您可以從 view 控制器設定狀態列樣式。 視圖控制器也可以設定是否隱藏或顯示狀態列。

例如，下列程式碼會覆寫 `PreferredStatusBarStyle` view 控制器的方法，使狀態列顯示淺前景：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

這會導致狀態列出現，如下所示：

 ![範例狀態列](ios7-ui-images/light-status-bar.png)

若要從 view controller 的程式碼隱藏狀態列，請覆寫 `PrefersStatusBarHidden` ，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

這會隱藏狀態列：

 ![狀態列隱藏的狀態列](ios7-ui-images/status-bar-hidden.png)

### <a name="tint-color"></a>色調色彩

按鈕現在會顯示為不區分 chrome 的文字。 您可以使用上的新屬性來控制文字色彩 `TintColor` `UIView` 。 設定 `TintColor` 會將色彩套用至設定它的視圖的整個 view 階層。 若要在 `TintColor` 整個應用程式中套用，請在上進行設定 `Window` 。 您也可以透過方法來偵測色調色彩變更的時間 `UIView.TintColorDidChange` 。

例如，下列螢幕擷取畫面顯示將流覽控制器的視圖上的色調色彩變更為紫色的效果：

 ![導覽控制器視圖上的紫色色調色彩](ios7-ui-images/tint-color.png)

當設定為時，色調色彩也可以套用至影像 `RenderingMode` `UIImageRenderingMode.AlwaysTemplate` 。

> [!IMPORTANT]
> 無法使用設定色調色彩 `UIAppearance` 。

### <a name="dynamic-type"></a>動態類型

在 iOS 7 中，使用者可以指定系統設定中的文字大小。 使用動態類型時，不論大小為何，字型都會動態調整以看起來良好。 `UIFont.PreferredFontForTextStyle` 應該用來取得針對使用者控制的大小進行優化的字型。

## <a name="summary"></a>摘要

本文涵蓋 iOS 7 中的使用者介面元素變更。 它會檢查 UIKit 中對視圖和控制項所做的幾項變更，同時醒目提示視覺效果變更以及相關 Api 的變更。 最後，它引進了新的 Api，可使用全螢幕內容、新的色調色彩支援和動態類型。

## <a name="related-links"></a>相關連結

- [ImageViewer (範例) ](/samples/xamarin/ios-samples/ios7-ui-updates)