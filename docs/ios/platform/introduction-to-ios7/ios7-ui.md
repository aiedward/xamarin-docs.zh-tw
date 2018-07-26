---
title: iOS 7 使用者介面概觀
description: iOS 7 引進了大量的使用者介面的變更。 這篇文章強調一些更大規模的變更，在控制項的視覺外觀和中支援的新設計的 Api。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7e7725418ed104bd9be014b80d20fd62de144ca5
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242286"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 使用者介面概觀

_iOS 7 引進了大量的使用者介面的變更。這篇文章強調一些更大規模的變更，在控制項的視覺外觀和中支援的新設計的 Api。_

iOS 7 會著重在 chrome 的內容。 在 iOS 7 中的使用者介面項目不校正 chrome 藉由移除多餘的框線、 狀態列等導覽列，減少的內容檢視所使用的螢幕空間的屬性。 在 iOS 7 中，內容可使用整個螢幕。

iOS 7 中引進了數個其他變更： 色彩用來區別使用者介面項目，就不需等按鈕框線屬性。 許多項目，例如導覽列和狀態列，現在會採用其下的區域的模糊和半透明或透明的與內容的檢視。 透過套用模糊效果的橫條圖，傳達使用者介面中的深度的滿足感，轉譯這些內容的檢視。

本文章涵蓋數個使用者介面項目，在 iOS 7，以及各種 Api 與新的使用者介面設計中的變更。

## <a name="view-and-control-changes"></a>檢視和控制的變更

UIKit 中檢視的所有符合的 iOS 7 新的外觀與風格。 本章節強調一些變更，這些檢視，以及相關的 Api 來支援新的 UI 已變更。

### <a name="uibutton"></a>標記的 UIButton

建立從按鈕`UIButton`類別已無框線，以根據預設，任何背景，如下所示：

 ![](ios7-ui-images/button.png "範例標記的 UIButton")

`UIButtonType.RoundedRect`樣式已被取代。 如果用於 iOS 7`UIButtonType.RoundedRect`將會導致`UIButtonType.System`正在使用中，這會產生預設的按鈕樣式不含背景或顯示邊緣，如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

類似於`UIButton`，列按鈕還有無框線，將預設為新`UIBarButtonItemStyle.Plain`樣式如下所示：

 ![](ios7-ui-images/barbuttonplain.png "範例 UIBarButtonItem")

此外，`UIBarButtonItemStyle.Bordered`樣式已被取代。 設定`UIBarButtonItemStyle.Bordered`在 iOS 7 會在產生`UIBarButtonItemStyle.Plain`樣式所使用。

`UIBarButtonItemStyle.Done`樣式未被取代。 不過，它也會建立一個 [無框線] 按鈕只使用粗體文字樣式所示：

 ![](ios7-ui-images/barbuttondone.png "完成的樣式中的範例 UIBarButtonItem")

### <a name="uialertview"></a>UIAlertView

新的 iOS 7 外觀與風格的樣式變更，除了警示檢視 中不再支援透過子檢視。 即使`UIAlertView`繼承自`UIView`，則呼叫`AddSubview`上`UIAlertView`沒有任何作用。 例如，請參考下列程式碼：

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

使用被忽略，子檢視中，如下所示，這會產生標準的警示檢視:

 ![](ios7-ui-images/alert.png "範例 UIAlertView")
 
 注意： UIAlertView iOS 8 中已被取代。 檢視[警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)上使用警示檢視，在 iOS 8 及更新版本的配方。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

在 iOS 7 中的分段的控制項而言是透明的而且支援濃淡的色彩。 濃淡色彩用於文字和框線色彩。 選取的區段時，色彩會換成背景與文字之間濃淡色彩來反白顯示選取的區段，如下所示：

 ![](ios7-ui-images/segmentedcontrol.png "範例 UISegmentedControl")

此外， `UISegmentedControlStyle` iOS 7 中已被取代。

### <a name="picker-views"></a>檢視選擇器

選擇器檢視的 API 是維持不變;不過，iOS 7 的設計指導方針現在的狀態檢視選擇器應該會看到內嵌，而不是因為從以動畫顯示輸入的檢視下方的螢幕，或透過新的控制器推入至瀏覽控制器的堆疊，如同先前的 iOS 版本。 系統行事曆應用程式中，可以看到這個：

 ![](ios7-ui-images/inlinepicker.png "您可以在系統行事曆應用程式")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

在搜尋列現在會顯示在瀏覽列時`UISearchDisplayController.DisplaysSearchBarInNavigationBar`屬性設定為 true。 當設定為 false-預設值-出現的搜尋控制站時，會隱藏巡覽列。

下列的螢幕擷取畫面顯示在 [搜尋] 列`UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "範例 UISearchDisplayController")

### <a name="uitableview"></a>UITableView

周邊的 Api`UITableView`主要是不變; 不過，樣式已變更為符合新的使用者介面設計的大幅。 也稍有不同的內部檢視階層。 這項變更不會影響大部分的應用程式，但要注意的某些項目。

#### <a name="grouped-table-style"></a>群組的資料表樣式

變更的群組的樣式已更新，現在擴充到的如下所示的畫面邊緣的內容：

 ![](ios7-ui-images/table1.png "範例群組的資料表樣式")

#### <a name="separatorinset"></a>SeparatorInset

資料列分隔符號現在會藉由設定縮排`UITableVIewCell.SeparatorInset`屬性。 例如，下列程式碼會用於縮排距離左邊緣的資料格：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

這會產生縮排的資料格的 [資料表] 檢視中，如下所示：

 ![](ios7-ui-images/separatorinset.png "範例 UITableView SeparatorInset")

#### <a name="table-button-styles"></a>表格按鈕樣式

所有已變更的資料表檢視中使用不同的按鈕。 下列螢幕擷取畫面顯示處於編輯模式的資料表檢視：

 ![](ios7-ui-images/table2.png "此螢幕擷取畫面顯示處於編輯模式的資料表檢視")

### <a name="additional-control-changes"></a>其他的控制項變更

其他 UIKit 控制項也已變更，包括滑桿、 交換器和 steppers。 這些變更是純粹視覺項目。 如需詳細資訊，請參閱 Apple [iOS 7 UI 轉換指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

UIKit 中的變更，除了 iOS 7 引進了各種不同的視覺變更 ui，包括：

-  全螢幕的內容
-  橫條圖的外觀
-  濃淡色彩

<a name="fullscreen" />

### <a name="full-screen-content"></a>全螢幕的內容

iOS 7 的設計可以讓應用程式利用整個螢幕。 檢視控制器現在會顯示狀態列 並瀏覽列-重疊，如果有的話-而非顯示狀態和巡覽列下方。

當您準備您的應用程式，適用於 iOS 7 時，您可以調整以視覺化方式使用的子檢視*Interface Builder*或*Xamarin iOS 設計工具*。 您也可以使用其中一個新的 Api，以協助以程式設計方式處理全螢幕的內容。 這些 Api 導入下面。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide` 並`BottomLayoutGuide`做為檢視應開始或結束，其中的參考，使內容不重疊的半透明`UIKit`列中的，如下列範例所示：

 [![](ios7-ui-images/clipped.png "不重疊的半透明 UIKit 列的範例內容")](ios7-ui-images/clipped.png#lightbox)

這些 Api 可以用來計算檢視的位移，從頂端或底部的畫面上，並據以調整內容的位置：

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

我們可以使用值來設定上面計算出我們`ImageView`的位移，從畫面上，因此整個影像是可見的頂端：

 [![](ios7-ui-images/good2.png "從畫面頂端的 範例 ImageViews 加上位移")](ios7-ui-images/good2.png#lightbox)

請參閱[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)如需實用範例。

檢視已加入至階層，因此嘗試讀取之後動態產生的位移值`TopLayoutGuide`並`BottomLayoutGuide`中的值`ViewDidLoad`會傳回 0。 檢視載入-例如，在計算值`ViewDidLayoutSubviews`。

> [!IMPORTANT]
> `TopLayoutGuide` 和`BottomLayoutGuide`iOS 11 改用新的安全區域版面配置中已被取代。 Apple 表明，使用安全的區域是與 iOS 版本早於 iOS 11 相容。 如需詳細資訊，請參閱 <<c0> [ 更新您的應用程式，適用於 iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 會指定檢視的邊緣應該延伸到全螢幕，不論列半透明。 在 iOS 7 中，瀏覽列和工具列會顯示之上控制器的檢視表-不同於在先前的 iOS 版本，其中未佔用相同的空間。 IOS 7 相片應用程式示範預設`UIViewController.EdgesForExtendedLayout`值， `UIRectEdge.All`。 這項設定會填入內容，檢視中的所有四個邊建立的重疊和全螢幕的效果：

 [![](ios7-ui-images/photos.png "範例 EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

點選 映像移除橫條圖，並示範影像全螢幕：

 [![](ios7-ui-images/photos2.png "與移除的橫條 EdgesForExtendedLayout")](ios7-ui-images/photos2.png#lightbox)

全螢幕的內容是預設值，因為設定為 iOS 6 應用程式將會有一部分裁剪，如以下螢幕擷取畫面所示的檢視：

 [![](ios7-ui-images/clipped.png "設定適用於 iOS 6 的應用程式將會裁剪，如以下螢幕擷取畫面所示的檢視部分")](ios7-ui-images/clipped.png#lightbox)

修改`UIViewController.EdgesForExtendedLayout`屬性調整此行為。 我們可以指定的檢視不會填滿任何的邊緣，讓我們檢視可避免在瀏覽或 （在每一個方向） 的工具列所佔用的空間中顯示內容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

在我們的應用程式中，我們會看到檢視再次重新調整位置，讓整個影像會顯示：

 [![](ios7-ui-images/good.png "與整個影像顯示的範例")](ios7-ui-images/good.png#lightbox)

請注意，雖然的效果`TopLayoutGuide/BottomLayoutGuide`和`EdgesForExtendedLayout`Api 很類似，它們一定會以填滿不同的目標。 變更`EdgesForExtendedLayout`設定的預設值可能會修正 ios 6，設計應用程式中的裁剪的檢視，但良好的 iOS 7 設計應該接受全螢幕美觀，並提供全螢幕檢視體驗，同時依賴`TopLayoutGuide`並`BottomLayoutGuide`正確，將預定插入到舒適位置，以讓使用者可操作的內容。

請參閱[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)如需實用範例。

### <a name="status-and-navigation-bars"></a>狀態和導覽

狀態列和導覽列會轉譯與透明度。 狀態列是深度的透明的工具列和導覽是深度的半透明與套用模糊效果傳達使用者介面中的感覺。 下列螢幕擷取畫面顯示此模糊和透明度，其中藍色的背景色彩的集合檢視會顯示透過 狀態 和 瀏覽列，讓他們的淺藍色的外觀：

 ![](ios7-ui-images/transparent-navbar.png "範例狀態和導覽列模糊處理")

#### <a name="status-bar-styles"></a>狀態列樣式

模糊和透明度，以及 light 或暗色調 （深色沒有預設值），可以是狀態列的前景。 狀態列樣式可以從檢視控制器設定。 檢視控制器時，也可以設定是否在隱藏或顯示 [狀態] 列。

例如，下列程式碼覆寫`PreferredStatusBarStyle`讓狀態列顯示淺色前景的檢視控制器方法：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

這會導致出現的 [狀態] 列，如下所示：

 ![](ios7-ui-images/light-status-bar.png "範例 [狀態] 列")

若要隱藏 [狀態] 列，從檢視控制器的程式碼，請覆寫`PrefersStatusBarHidden`，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

這可能會隱藏 [狀態] 列：

 ![](ios7-ui-images/status-bar-hidden.png "隱藏狀態列")

### <a name="tint-color"></a>濃淡色彩

按鈕現在會顯示為 無色彩的文字。 您可以使用新控制文字色彩`TintColor`屬性上的`UIView`。 設定`TintColor`將色彩套用至整個檢視階層檢視，以設定它。 若要套用`TintColor`整個應用程式，將它設定在`Window`。 您也可以偵測何時濃淡色彩變更透過`UIView.TintColorDidChange`方法。

例如，下列螢幕擷取畫面顯示變更 巡覽控制器的檢視上的濃淡色彩的影響為紫色：

 ![](ios7-ui-images/tint-color.png "紫色濃淡的色彩，在 導覽控制器檢視")

濃淡色彩可以套用至映像以及何時`RenderingMode`設為`UIImageRenderingMode.AlwaysTemplate`。

> [!IMPORTANT]
> 濃淡色彩無法使用設定`UIAppearance`。


### <a name="dynamic-type"></a>動態類型

在 iOS 7 中，使用者可以指定系統設定 中的文字大小。 具有動態類型，字型會動態調整，以呈現最佳效果，而不論大小為何。 `UIFont.PreferredFontForTextStyle` 應該用來取得字型，最適合的使用者控制的大小。

## <a name="summary"></a>總結

本文涵蓋在 iOS 7 中的使用者介面項目變更。 它會檢查數個檢視和 UIKit，反白顯示視覺化變更的控制項所做的變更，以及對相關的 Api。 最後，它引入了新的 Api，以搭配完整的畫面內容、 新濃淡色彩支援，以及動態型別。

## <a name="related-links"></a>相關連結

- [ImageViewer （範例）](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
