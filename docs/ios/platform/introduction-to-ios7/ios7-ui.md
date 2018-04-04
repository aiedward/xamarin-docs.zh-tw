---
title: iOS 7 使用者介面概觀
description: iOS 7 導入的使用者介面的變更。 本文章重點說明一些較大的變更，在控制項的視覺外觀和 Api，可支援新的設計中。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3f5ea8abd41e718f9ac947c5acb290dffe400ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 使用者介面概觀

_iOS 7 導入的使用者介面的變更。本文章重點說明一些較大的變更，在控制項的視覺外觀和 Api，可支援新的設計中。_

iOS 7 著重在 chrome 的內容。 在 iOS 7 中的使用者介面項目不校正 chrome 藉由移除多餘的框線、 狀態列和巡覽列，減少使用的內容檢視的螢幕空間等屬性。 在 iOS 7 中，被設計為使用整個螢幕的內容。

iOS 7 中引進了數個其他變更： 色彩用來區別使用者介面項目，就不需等按鈕的框線屬性。 許多項目，例如導覽列和狀態列，現在是花在其下的區域的模糊和半透明或透明的與內容的檢視。 透過套用模糊效果的橫條，傳達知道哪些使用者介面中的深度會轉譯這些內容的檢視。

本文涵蓋數個使用者介面項目，在 iOS 7 以及各種應用程式開發介面與新的使用者介面設計中的變更。

## <a name="view-and-control-changes"></a>檢視和控制變更

UIKit 中的檢視表的所有符合的 iOS 7 新的外觀及操作。 本章節強調一些變更，這些檢視，以及相關以支援新的 UI 已變更的 Api。

### <a name="uibutton"></a>UIButton

建立從按鈕`UIButton`類別現在無框線，以根據預設，沒有背景如下所示：

 ![](ios7-ui-images/button.png "範例 UIButton")

`UIButtonType.RoundedRect`樣式已被取代。 如果使用中 iOS 7`UIButtonType.RoundedRect`會導致`UIButtonType.System`正在使用中，這會產生不含背景或顯示邊緣、 預設按鈕樣式如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

類似於`UIButton`，列按鈕還有無框線，將預設為新`UIBarButtonItemStyle.Plain`樣式如下所示：

 ![](ios7-ui-images/barbuttonplain.png "範例 UIBarButtonItem")

此外，`UIBarButtonItemStyle.Bordered`樣式已被取代。 設定`UIBarButtonItemStyle.Bordered`在 iOS 7 會導致`UIBarButtonItemStyle.Plain`所使用的樣式。

`UIBarButtonItemStyle.Done`樣式未被取代。 不過，它也會建立無框線 按鈕，僅以粗體文字樣式所示：

 ![](ios7-ui-images/barbuttondone.png "範例 UIBarButtonItem 完成樣式")

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 外觀與風格的樣式變更，警示檢視不再支援透過子檢視的自訂。 即使`UIAlertView`繼承自`UIView`，則呼叫`AddSubview`上`UIAlertView`沒有任何作用。 例如，請參考下列程式碼：

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

正在略過子檢視與中，如下所示，這會產生標準警示檢視：

 ![](ios7-ui-images/alert.png "範例 UIAlertView")
 
 注意： UIAlertView iOS 8 中已被取代。 檢視[警示控制器](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)配方 iOS 8 和更新版本使用警示檢視。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

在 iOS 7 中的分割的控制項而言是透明的而且支援濃淡的色彩。 濃淡色彩用於文字和框線色彩。 選取的區段時，色彩會交換背景與文字之間，使用濃淡的色彩用於反白顯示所選取的區段，如下所示：

 ![](ios7-ui-images/segmentedcontrol.png "範例 UISegmentedControl")

此外， `UISegmentedControlStyle` iOS 7 中已被取代。

### <a name="picker-views"></a>檢視選擇器

選擇器檢視 API 是大部分相同。但是，iOS 7 的設計指導方針現在的狀態檢視選擇器應該會以內嵌方式呈現，而不是為輸入的檢視顯示動畫從底部螢幕或新的控制器透過推入至導覽控制站的堆疊，如同先前的 iOS 版本。 系統行事曆應用程式中，可以看到這個：

 ![](ios7-ui-images/inlinepicker.png "系統行事曆應用程式中可以看到這")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

[搜尋] 列會顯示在瀏覽列時`UISearchDisplayController.DisplaysSearchBarInNavigationBar`屬性設定為 true。 當設定為 false-預設值-隱藏巡覽列顯示搜尋控制站時。

下列螢幕擷取畫面顯示 [搜尋] 列中的`UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "範例 UISearchDisplayController")

### <a name="uitableview"></a>UITableView

周圍 Api`UITableView`主要是不變; 不過，樣式已變更為符合新的使用者介面設計的大幅。 也稍有不同的內部檢視階層架構。 這項變更不會影響大部分的應用程式，但要注意的項目。

#### <a name="grouped-table-style"></a>群組的資料表樣式

已更新群組的樣式變更現在如下所示的畫面邊緣延伸內容：

 ![](ios7-ui-images/table1.png "範例分組的資料表樣式")

#### <a name="separatorinset"></a>SeparatorInset

資料列分隔符號現在會藉由設定縮排`UITableVIewCell.SeparatorInset`屬性。 例如，下列程式碼會用來縮排距離左邊緣的資料格：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

這會產生縮排的儲存格的表格檢視中如下所示：

 ![](ios7-ui-images/separatorinset.png "範例 UITableView SeparatorInset")

#### <a name="table-button-styles"></a>資料表按鈕樣式

所有已變更的資料表檢視中使用不同的按鈕。 下列螢幕擷取畫面會呈現在編輯模式下的表格檢視：

 ![](ios7-ui-images/table2.png "這個螢幕擷取畫面顯示在編輯模式下的表格檢視")

### <a name="additional-control-changes"></a>其他控制項變更

其他 UIKit 控制項也已變更，包括滑桿、 交換器和 stepper。 這些變更是單純的視覺化。 如需詳細資訊，請參閱 Apple 的[iOS 7 UI 轉換引導](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

除了 UIKit 變更，iOS 7 介紹各種不同的視覺效果變更的 ui，包括：

-  全螢幕的內容
-  列外觀
-  濃淡的色彩

<a name="fullscreen" />

### <a name="full-screen-content"></a>全螢幕的內容

iOS 7 被為了讓應用程式利用整個螢幕。 檢視控制器現在會出現重疊的狀態列和巡覽列-如果有的話-而非顯示狀態和巡覽列下方。

當您準備您的應用程式，適用於 iOS 7，您可以重新對齊 subviews 以視覺化方式使用*介面產生器*或*Xamarin iOS 設計工具*。 您也可以使用其中一個新的 Api，以協助以程式設計方式處理全螢幕的內容。 這些 Api 導入下面。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide` 和`BottomLayoutGuide`做為檢視應開始或結束，其中的參考，使內容不重疊的半透明`UIKit`列中的，如下列範例所示：

 [![](ios7-ui-images/clipped.png "不重疊的半透明的 UIKit 列的範例內容")](ios7-ui-images/clipped.png#lightbox)

這些 Api 可以用來計算的頂端或底部的畫面上，從檢視表的位移，並據以調整內容的位置：

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

我們可以使用設定上面計算出值我們`ImageView`的位移，從畫面上，因此整個影像是可見的頂端：

 [![](ios7-ui-images/good2.png "從畫面頂端的範例 ImageViews 位移")](ios7-ui-images/good2.png#lightbox)

請參閱[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)如需實用範例。

檢視已加入至階層中，因此嘗試讀取後，動態產生的位移值`TopLayoutGuide`和`BottomLayoutGuide`值`ViewDidLoad`會傳回 0。 檢視已載入後-例如，計算值`ViewDidLayoutSubviews`。

> [!IMPORTANT]
> `TopLayoutGuide` 和`BottomLayoutGuide`改用新的安全區域配置 iOS 11 中已被取代。 Apple 已經表明使用安全的區域是 iOS 版本早於 iOS 11 相容。 如需詳細資訊，請參閱[更新您的應用程式，適用於 iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

這個 API 會指定檢視的哪些邊緣應該延伸到全螢幕，不論列半透明。 IOS 7，在導覽列和工具列會顯示置於之上控制站的檢視-不像在先前的 iOS 版本中，其中未佔用相同的空間。 IOS 7 相片應用程式示範預設`UIViewController.EdgesForExtendedLayout`值`UIRectEdge.All`。 這項設定會填入內容，檢視中的所有四個邊建立的重疊和全螢幕的效果：

 [![](ios7-ui-images/photos.png "Sample EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

點選映像移除橫條圖，並顯示影像全螢幕：

 [![](ios7-ui-images/photos2.png "與移除的橫條 EdgesForExtendedLayout")](ios7-ui-images/photos2.png#lightbox)

全螢幕的內容是預設值，因為設定適用於 iOS 6 的應用程式都需要顯示在檢視裁剪，如以下螢幕擷取畫面所示：

 [![](ios7-ui-images/clipped.png "設定適用於 iOS 6 應用程式會有裁剪，如下列螢幕擷取畫面所示檢視的一部分")](ios7-ui-images/clipped.png#lightbox)

修改`UIViewController.EdgesForExtendedLayout`屬性調整為這個行為。 我們可以指定，檢視填滿任何邊緣，因此我們檢視將會避免在瀏覽或 （在每個方向） 的工具列所佔用的空間中顯示內容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我們的應用程式中，我們會看到檢視再次重新調整位置，讓整個影像會顯示：

 [![](ios7-ui-images/good.png "使用可見的整個映像的範例")](ios7-ui-images/good.png#lightbox)

請注意，雖然的效果`TopLayoutGuide/BottomLayoutGuide`和`EdgesForExtendedLayout`Api 非常類似，它們一定會以填滿不同的目標。 變更`EdgesForExtendedLayout`設定設成預設值可能會修正裁剪的檢視適用於 iOS 6、 設計的應用程式中，但是應該接受全螢幕美觀良好 iOS 7 設計，並提供全螢幕檢視經驗，同時依賴`TopLayoutGuide`和`BottomLayoutGuide`正確，將目的在於到舒適位置，以讓使用者可操作的內容。

請參閱[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)如需實用範例。

### <a name="status-and-navigation-bars"></a>狀態和導覽列

透明度呈現狀態列及導覽列。 狀態列皆為透明，而工具列和導覽列是半透明並套用模糊效果來傳遞使用者介面中的深度的感覺。 下列螢幕擷取畫面顯示此模糊和透明度，其中藍色背景的色彩集合檢視會顯示透過 狀態 和 瀏覽列中，提供它們的淺藍色的外觀：

 ![](ios7-ui-images/transparent-navbar.png "範例狀態和導覽列模糊")

#### <a name="status-bar-styles"></a>狀態列樣式

模糊和透明度，以及狀態列的前景可以輕或暗色調 （深色的預設值）。 從檢視控制器，可以設定狀態 列樣式。 檢視控制器時，也可以設定是否隱藏或顯示狀態列。

例如，下列程式碼覆寫`PreferredStatusBarStyle`檢視控制站，以便顯示淺色前景的狀態列的方法：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

這會造成狀態列會出現如下：

 ![](ios7-ui-images/light-status-bar.png "範例 [狀態] 列")

若要隱藏 [狀態] 列，從檢視控制站的程式碼，請覆寫`PrefersStatusBarHidden`，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

這會隱藏 [狀態] 列：

 ![](ios7-ui-images/status-bar-hidden.png "隱藏的狀態列")

### <a name="tint-color"></a>濃淡的色彩

按鈕現在會顯示為無色彩的文字。 可以使用新控制的文字色彩`TintColor`屬性`UIView`。 設定`TintColor`將色彩套用至整個檢視階層的檢視，將它設定。 若要套用`TintColor`整個應用程式上設定`Window`。 您也可以偵測何時濃淡的色彩變更透過`UIView.TintColorDidChange`方法。

例如，下列螢幕擷取畫面顯示變更檢視的導覽控制站上的濃淡色彩的效果為紫色：

 ![](ios7-ui-images/tint-color.png "紫色濃淡的色彩，在導覽控制站檢視")

濃淡色彩可以套用至映像，以及當`RenderingMode`設`UIImageRenderingMode.AlwaysTemplate`。

> [!IMPORTANT]
> 無法使用設定濃淡的色彩`UIAppearance`。


### <a name="dynamic-type"></a>動態類型

在 iOS 7 中，使用者可以指定系統設定中的文字大小。 使用動態類型，字型會以動態方式看來，不論大小調整。 `UIFont.PreferredFontForTextStyle` 應該用來取得使用者控制大小最適合的字型。

## <a name="summary"></a>總結

本文涵蓋的變更，在 iOS 7 中的使用者介面項目。 它會檢查幾個檢視和 UIKit，反白顯示的視覺變更中的控制項所做的變更，以及對相關的 Api。 最後，它導入了新的 Api 可使用全螢幕的內容、 新濃淡的色彩支援和動態類型。

## <a name="related-links"></a>相關連結

- [ImageViewer （範例）](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
