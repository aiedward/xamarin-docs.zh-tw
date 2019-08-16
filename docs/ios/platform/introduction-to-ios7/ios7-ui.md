---
title: iOS 7 使用者介面概觀
description: iOS 7 引進了使用者介面變更的眾多。 這篇文章強調一些較大的變更, 包括控制項的視覺外觀, 以及支援新設計的 Api。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e943b10623507278c073e73e5b755bcb5954bdaa
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528327"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 使用者介面概觀

_iOS 7 引進了使用者介面變更的眾多。這篇文章強調一些較大的變更, 包括控制項的視覺外觀, 以及支援新設計的 Api。_

iOS 7 著重于 chrome 上的內容。 IOS 7 中的使用者介面元素會藉由移除多餘框線、狀態列和導覽列等屬性, 減少內容流覽所使用的螢幕空間量, 而取消強調 chrome。 在 iOS 7 中, 內容是設計用來使用整個畫面。

iOS 7 引進了其他幾項變更: 色彩是用來區別使用者介面專案, 而不是像是按鈕框線的屬性。 許多專案 (例如導覽列和狀態列) 現在都是模糊、半透明或透明, 且內容視圖會將區域放在其下方。 這些內容視圖會透過模糊的橫條轉譯, 傳達使用者介面的深度。

本文涵蓋 iOS 7 中使用者介面元素的幾項變更, 以及與新使用者介面設計相關的各種 Api。

## <a name="view-and-control-changes"></a>查看和控制變更

UIKit 中的所有觀點都符合 iOS 7 的新外觀與風格。 本節將重點說明這些視圖的一些變更, 以及已變更為支援新 UI 的相關 Api。

### <a name="uibutton"></a>UIButton

從`UIButton`類別建立的按鈕現在是無邊框的, 預設為沒有背景, 如下所示:

 ![](ios7-ui-images/button.png "範例 UIButton")

`UIButtonType.RoundedRect`樣式已被取代。 如果用於 iOS 7, `UIButtonType.RoundedRect`會`UIButtonType.System`導致使用, 這會產生沒有背景或可見邊緣的預設按鈕樣式, 如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

類似于`UIButton`, 橫條按鈕也是無邊框的, 預設為`UIBarButtonItemStyle.Plain`新樣式, 如下所示:

 ![](ios7-ui-images/barbuttonplain.png "範例 UIBarButtonItem")

此外, `UIBarButtonItemStyle.Bordered`樣式已被取代。 IOS `UIBarButtonItemStyle.Bordered` 7 中的`UIBarButtonItemStyle.Plain`設定會導致使用樣式。

`UIBarButtonItemStyle.Done`樣式尚未被取代。 不過, 它也會建立無邊框的按鈕, 只會使用粗體文字樣式, 如下所示:

 ![](ios7-ui-images/barbuttondone.png "完成樣式中的範例 UIBarButtonItem")

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 外觀與風格變更以外, 警示視圖也不再支援透過子視圖進行自訂。 即使繼承自`UIView`, 在上`AddSubview` `UIAlertView`呼叫也不會有任何作用。 `UIAlertView` 例如，請參考下列程式碼：

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

這會產生標準的警示視圖, 並忽略子視圖, 如下所示:

 ![](ios7-ui-images/alert.png "範例 UIAlertView")
 
 注意:UIAlertView 在 iOS 8 中已被取代。 使用 iOS 8 和更新版本中的警示視圖, 查看[警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)配方。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

IOS 7 中的分段控制項是透明的, 而且支援色調色彩。 色調色彩會用於文字和框線色彩。 選取區段時, 色彩會在背景與文字之間交換, 並使用色調色彩來反白顯示選取的區段, 如下所示:

 ![](ios7-ui-images/segmentedcontrol.png "範例 UISegmentedControl")

此外, `UISegmentedControlStyle`在 iOS 7 中已被取代。

### <a name="picker-views"></a>選擇器視圖

選擇器視圖的 API 大多不變;不過, iOS 7 設計指導方針現在狀態選擇器視圖應該以內嵌方式呈現, 而不是從畫面底部以動畫顯示的輸入視圖, 或透過推送至流覽控制器堆疊的新控制器, 如同先前的 iOS 版本。 這可以在系統行事曆應用程式中看到:

 ![](ios7-ui-images/inlinepicker.png "這可以在系統行事曆應用程式中看到")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

當`UISearchDisplayController.DisplaysSearchBarInNavigationBar`屬性設定為 true 時, 搜尋列現在會顯示在導覽列中。 設定為 false 時-預設值-顯示搜尋控制器時, 會隱藏導覽列。

下列螢幕擷取畫面顯示中`UISearchDisplayController`的搜尋列:

 ![](ios7-ui-images/searchbar.png "範例 UISearchDisplayController")

### <a name="uitableview"></a>UITableView

中的 api `UITableView`主要不變; 不過, 樣式已大幅變更, 以符合新的使用者介面設計。 內部視圖階層也會有些不同。 這項變更不會影響大部分的應用程式, 但它是值得注意的事項。

#### <a name="grouped-table-style"></a>群組資料表樣式

已變更的群組樣式已更新, 內容現在會延伸到畫面的邊緣, 如下所示:

 ![](ios7-ui-images/table1.png "範例群組資料表樣式")

#### <a name="separatorinset"></a>SeparatorInset

現在可以藉由設定`UITableVIewCell.SeparatorInset`屬性來縮排資料列分隔符號。 例如, 下列程式碼會用來縮排左邊緣的資料格:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

這會在具有縮排資料格的資料表視圖中產生, 如下所示:

 ![](ios7-ui-images/separatorinset.png "範例 UITableView SeparatorInset")

#### <a name="table-button-styles"></a>資料表按鈕樣式

資料表視圖中使用的各種按鈕都已變更。 下列螢幕擷取畫面以編輯模式呈現資料表視圖:

 ![](ios7-ui-images/table2.png "這個螢幕擷取畫面會以編輯模式呈現資料表視圖")

### <a name="additional-control-changes"></a>其他控制項變更

其他 UIKit 控制項也已變更, 包括滑杆、交換器和 steppers。 這些變更純粹是視覺效果。 如需詳細資訊, 請參閱 Apple 的[iOS 7 UI 轉換指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

除了 UIKit 中的變更之外, iOS 7 也引進了 UI 的各種視覺變更, 包括:

- 全螢幕內容
- 橫條外觀
- 色調色彩

<a name="fullscreen" />

### <a name="full-screen-content"></a>全螢幕內容

iOS 7 的設計可讓應用程式充分利用整個畫面。 視圖控制器現在會以狀態列和導覽列重迭 (如果有的話), 而不是顯示在狀態和導覽列下方。

當您準備 iOS 7 的應用程式時, 您可以使用*Interface Builder*或*Xamarin iOS 設計*工具, 以視覺化方式重新對齊子檢視。 您也可以使用其中一個新的 Api 協助以程式設計方式處理全螢幕內容。 以下引進這些 Api。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide`並`BottomLayoutGuide`做為參考, 其中的 views 應開始或結束, 讓內容不會被半透明`UIKit`的橫條重迭, 如下列範例所示:

 [![](ios7-ui-images/clipped.png "不是由半透明 UIKit 列重迭的範例內容")](ios7-ui-images/clipped.png#lightbox)

這些 Api 可用來計算螢幕頂端或底部的視圖位移, 並據以調整內容位置:

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

我們可以使用上面所計算的值, 從`ImageView`畫面頂端設定我們的位移, 讓整個影像看得到:

 [![](ios7-ui-images/good2.png "畫面頂端的 ImageViews 位移範例")](ios7-ui-images/good2.png#lightbox)

如需實用範例, 請參閱[ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) 。

在視圖加入階層之後, 會動態產生置換值, 因此嘗試讀取`TopLayoutGuide`和`BottomLayoutGuide`中的`ViewDidLoad`值將會傳回0。 在此視圖載入後計算值-例如, 中的`ViewDidLayoutSubviews`。

> [!IMPORTANT]
> `TopLayoutGuide`和`BottomLayoutGuide`在 iOS 11 中已被取代, 以取代新的安全區域配置。 Apple 已聲明使用安全區域與 iOS 11 之前的 iOS 版本相容。 如需詳細資訊, 請參閱[更新您的應用程式以取得 iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 會指定應將視圖的哪些邊緣延伸至全螢幕, 不論是否使用橫條半透明度。 在 iOS 7 中, 流覽列和工具列會顯示在控制器的視圖上方, 不同于先前的 iOS 版本, 它們並未佔用相同的空間。 IOS 7 相片應用程式說明預設`UIViewController.EdgesForExtendedLayout` `UIRectEdge.All`值。 此設定會以內容填滿此視圖中的四個邊緣, 並建立重迭和全螢幕效果:

 [![](ios7-ui-images/photos.png "範例 EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

點擊影像會移除橫條, 並以全螢幕顯示影像:

 [![](ios7-ui-images/photos2.png "已移除橫條的 EdgesForExtendedLayout")](ios7-ui-images/photos2.png#lightbox)

因為全螢幕內容是預設值, 所以針對 iOS 6 設定的應用程式將會裁剪部分的視圖, 如下列螢幕擷取畫面所示:

 [![](ios7-ui-images/clipped.png "針對 iOS 6 設定的應用程式將會裁剪部分的視圖, 如下列螢幕擷取畫面所示")](ios7-ui-images/clipped.png#lightbox)

修改此`UIViewController.EdgesForExtendedLayout`行為的屬性會調整。 我們可以指定不填滿任何邊緣的視圖, 因此我們的視圖會避免在導覽或工具列所佔用的空間中顯示內容 (每個方向):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我們的應用程式中, 我們會看到此視圖再次重新置放, 因此可以看見整個影像:

 [![](ios7-ui-images/good.png "顯示完整影像的範例")](ios7-ui-images/good.png#lightbox)

請注意, 雖然`TopLayoutGuide/BottomLayoutGuide`和`EdgesForExtendedLayout` api 的效果很相似, 但它們的目的是要填滿不同的目標。 從預設值變更`TopLayoutGuide` 設定,可能會修正針對iOS6設計之應用程式中的裁剪視圖,但良好的iOS7設計應接受全螢幕的美觀並提供全螢幕的觀賞體驗,並依賴和`EdgesForExtendedLayout` `BottomLayoutGuide`適當地將要操作的內容定位至使用者的舒適位置。

如需實用範例, 請參閱[ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) 。

### <a name="status-and-navigation-bars"></a>狀態和巡覽列

狀態列和導覽列會以透明度呈現。 狀態列是透明的, 而工具列和導覽列則是半透明且模糊的, 可在使用者介面中傳達深度感受。 下列螢幕擷取畫面顯示此模糊和透明度, 其中集合視圖的藍色背景色彩會顯示狀態和巡覽列, 讓它們具有淺藍色外觀:

 ![](ios7-ui-images/transparent-navbar.png "範例狀態和巡覽列模糊")

#### <a name="status-bar-styles"></a>狀態列樣式

除了模糊和透明度之外, 狀態列的前景也可以是淺色或深色 (暗為預設值)。 狀態列樣式可以從 view controller 進行設定。 View controller 也可以設定狀態列是否隱藏或顯示。

例如, 下列程式碼會覆寫`PreferredStatusBarStyle`視圖控制器的方法, 讓狀態列顯示淺前景:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

這會導致狀態列顯示如下:

 ![](ios7-ui-images/light-status-bar.png "範例狀態列")

若要從視圖控制器的程式碼隱藏狀態列, 請覆`PrefersStatusBarHidden`寫, 如下所示:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

這會隱藏狀態列:

 ![](ios7-ui-images/status-bar-hidden.png "隱藏的狀態列")

### <a name="tint-color"></a>色調色彩

按鈕現在會顯示為不區分 chrome 的文字。 您可以使用上`TintColor` `UIView`的新屬性來控制文字色彩。 設定會將色彩套用至設定它的視圖的整個視圖階層。`TintColor` 若要在應用程式中套用, 請`Window`在上設定。 `TintColor` 您也可以透過`UIView.TintColorDidChange`方法來偵測色彩色彩何時變更。

例如, 下列螢幕擷取畫面顯示在流覽控制器的視圖上, 將色調色彩變更為紫色的效果:

 ![](ios7-ui-images/tint-color.png "導覽控制器視圖上的紫色色調色彩")

當設定為`RenderingMode` `UIImageRenderingMode.AlwaysTemplate`時, 色調色彩也可以套用至影像。

> [!IMPORTANT]
> 無法使用`UIAppearance`來設定色調色彩。


### <a name="dynamic-type"></a>動態類型

在 iOS 7 中, 使用者可以在系統設定中指定文字大小。 使用動態類型時, 字型會以動態方式調整, 而不論大小為何都很好。 `UIFont.PreferredFontForTextStyle`應該用來取得針對使用者控制的大小優化的字型。

## <a name="summary"></a>總結

本文涵蓋 iOS 7 中使用者介面元素的變更。 它會檢查 UIKit 中對 views 和控制項所做的幾項變更, 同時強調顯示視覺效果的變更, 以及對相關 Api 的變更。 最後, 它引進了新的 Api, 以使用全螢幕內容、新的色調色彩支援和動態類型。

## <a name="related-links"></a>相關連結

- [ImageViewer (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
