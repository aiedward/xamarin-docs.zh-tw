---
title: 在 Xamarin 中使用 tvOS 堆疊視圖
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 堆疊視圖。 其中提供堆疊視圖的高階總覽, 並討論自動版面配置、定位和調整堆疊視圖、常見用法、與分鏡腳本整合等等。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: be4d10eceb4ddf7da44409815f3e9c08117a06f7
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227223"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 堆疊視圖

Stack 視圖控制項 (`UIStackView`) 會利用自動設定和大小類別的功能, 來管理子檢視的堆疊 (不論是水準或垂直), 這會動態回應內容變更和 Apple TV 裝置的螢幕大小。

附加至堆疊視圖的所有子檢視的配置, 都是由它以開發人員定義的屬性 (例如軸、分佈、對齊和間距) 為基礎來管理:

[![](stacked-views-images/stacked01.png "子視圖版面配置圖表")](stacked-views-images/stacked01.png#lightbox)

`UIStackView`在 tvOS 應用程式中使用時, 開發人員可以在 iOS 設計工具中的分鏡腳本內定義子檢視, 或藉由在程式C#代碼中新增和移除子檢視。

## <a name="about-stacked-view-controls"></a>關於堆疊視圖控制項

是設計為非轉譯容器視圖, 因此不會繪製到畫布上, 如的`UIView`其他子類別。 `UIStackView` 設定屬性 (例如`BackgroundColor`或覆`DrawRect`寫) 不會有任何視覺效果。

有數個屬性可以控制堆疊視圖如何排列其子檢視的集合:

- **軸**–決定堆疊視圖是以**水準**或**垂直**方式排列子檢視。
- **對齊**–控制子檢視在堆疊視圖中的對齊方式。
- **散發**–控制如何在堆疊視圖內調整子檢視大小。
- **間距**–控制堆疊視圖中每個子視圖之間的最小空間。
- **基準相對**-如果`true`為, 則每個子視圖的垂直間距會從它的基準衍生。
- **相對於版面配置邊界**: 將子檢視相對於標準版面配置邊界。

通常您會使用堆疊視圖來排列少量的子檢視。 更複雜的使用者介面可以藉由在彼此之間嵌套一個或多個堆疊視圖來建立。

您可以藉由在子檢視中新增額外的條件約束 (例如, 控制高度或寬度), 進一步微調 Ui 外觀。 不過, 請小心不要將衝突的條件約束納入堆疊視圖本身所引進的限制式。

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

當子視圖加入至堆疊時, 其配置完全受到該堆疊視圖的控制, 使用自動版面配置和大小類別來定位和調整排列的視圖大小。

堆疊視圖會將其集合中的第一個和最後一個子視圖_釘_選到垂直堆疊視圖的**上**和**下**邊緣, 或水準堆疊視圖的**左邊**和**右邊**邊緣。 如果您將`LayoutMarginsRelativeArrangement`屬性設定為`true`, 則此視圖會將子檢視釘選到相關的邊界, 而不是邊緣。

堆疊視圖會在計算已定義`IntrinsicContentSize` `Axis`的子檢視大小 (除外`FillEqually Distribution`) 時, 使用子視圖的屬性。 會調整所有子檢視的大小, 使其具有相同的大小, 因此會`Axis`沿著來填滿堆疊視圖。 `FillEqually Distribution`

除了的例外狀況之外`Fill Alignment`, 堆疊視圖會使用`IntrinsicContentSize`子視圖的屬性來計算與指定`Axis`的垂直的視圖大小。 針對, 所有子檢視都會調整大小, 使其填滿垂直于指定`Axis`之的堆疊視圖。 `Fill Alignment`

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>定位和調整堆疊視圖的大小

雖然堆疊視圖對於任何子視圖的版面配置 (根據`Axis`和`Distribution`之類的屬性) 有完全控制權, 但您仍然需要使用自動設定和大小類別`UIStackView`, 在其父視圖內放置堆疊視圖 ()。

一般來說, 這表示至少要釘選堆疊視圖的兩個邊緣, 以擴充和合約, 進而定義其位置。 如果沒有任何額外的條件約束, 則會自動調整堆疊視圖的大小, 以符合其所有的子檢視, 如下所示:

- 連同`Axis`所有子視圖大小的總和, 以及每個子視圖之間已定義的任何空間, 都是大小。
- 如果屬性為`true`, 則堆疊 Views 大小也會包含邊界的空間。 `LayoutMarginsRelativeArrangement`
- 垂直于的`Axis`大小會設定為集合中最大的子視圖。

此外, 您可以指定堆疊視圖的**高度**和**寬度**的條件約束。 在此情況下, 子檢視會配置 (調整大小), 以填滿堆疊視圖所指定的空間 (由`Distribution`和`Alignment`屬性所決定)。

`true` - 如果屬性為, 則會根據第一個或最後一個子視圖的基準來配置子檢視, 而不是使用上、下或 * 置中 Y 位置。 `BaselineRelativeArrangement` 這些是在堆疊視圖的內容上計算, 如下所示:

- 垂直堆疊視圖會傳回第一個基準的第一個子視圖, 最後一個是最後一個。 如果其中一個子檢視本身是堆疊視圖, 則會使用其第一個或最後一個基準。
- 水準堆疊視圖會針對第一個和最後一個基準使用其最高的子視圖。 如果最高的視圖也是堆疊視圖, 它會使用其最高的子視圖作為基準。

> [!IMPORTANT]
> 基準對齊不適用於延伸或壓縮的子視圖大小, 因為基準會計算到錯誤的位置。 針對 [基準對齊], 請確定子視圖的**高度**符合內建內容視圖的**高度**。




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>一般堆疊視圖使用

有數種版面配置類型可搭配堆疊視圖控制項運作。 根據 Apple, 以下是幾個較常見的用法:

- **定義沿著軸的大小**–藉由將兩個邊緣沿著堆疊視圖`Axis`和其中一個相鄰邊緣釘選來設定位置, 堆疊視圖會沿著軸成長, 以符合其子檢視所定義的空間。
- **定義子視圖的位置**–藉由釘選到堆疊視圖的相鄰邊緣與其父視圖, 堆疊視圖會同時在這兩個維度中成長, 以符合其包含的子檢視。
- **定義堆疊的大小和位置**–藉由將堆疊視圖的全部四個邊緣釘選到父視圖, 堆疊視圖會根據堆疊視圖內定義的空間來排列子檢視。
- **定義垂直軸的大小**–藉由將邊緣垂直`Axis`放在堆疊視圖和軸上的其中一個邊緣來設定位置, 堆疊視圖會沿著軸垂直成長, 以符合其子檢視所定義的空間。

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>堆疊視圖和分鏡腳本

在 tvOS 應用程式中使用堆疊視圖的最簡單方式, 是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 設計您要加入至堆疊視圖之個別元素的版面配置:

    [![](stacked-views-images/layout01.png "元素版面配置範例")](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束新增至專案, 以確保它們能夠正確地進行調整。 將元素加入至堆疊視圖之後, 這個步驟很重要。
1. 建立所需的複本數目 (在此案例中為四個):

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 從 [**工具箱**] 拖曳 [**堆疊視圖**], 並將它放在視圖上:

    [![](stacked-views-images/layout03.png "堆疊視圖")](stacked-views-images/layout03.png#lightbox)
1. 選取 [堆疊] 視圖, 在**Properties Pad**的 [ **Widget]** 索引標籤中, 選取 [**填滿** **對齊**], 並`25`針對 [**分佈**] 按 [**填滿**], 並輸入**間距**:

    [![](stacked-views-images/layout04.png "[Widget] 索引標籤")](stacked-views-images/layout04.png#lightbox)
1. 將堆疊視圖放在您想要的畫面上, 並加入條件約束以將其保留在所需的位置。
1. 選取個別的專案, 並將其拖曳至 [堆疊] 視圖:

    [![](stacked-views-images/layout05.png "堆疊視圖中的個別元素")](stacked-views-images/layout05.png#lightbox)
1. 系統會調整配置, 並根據您在上面設定的屬性, 將元素排列在堆疊視圖中。
1. 在 [**屬性] 瀏覽器**的 [ **Widget]** 索引標籤中指派**名稱**, C#以在程式碼中使用您的 UI 控制項。
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 設計您要加入至堆疊視圖之個別元素的版面配置:

    [![](stacked-views-images/layout01.png "範例元素版面配置")](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束新增至專案, 以確保它們能夠正確地進行調整。 將元素加入至堆疊視圖之後, 這個步驟很重要。
1. 建立所需的複本數目 (在此案例中為四個):

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 從 [**工具箱**] 拖曳 [**堆疊視圖**], 並將它放在視圖上:

    [![](stacked-views-images/layout03-vs.png "堆疊視圖")](stacked-views-images/layout03-vs.png#lightbox)
1. 選取 [堆疊] 視圖, 在 [**屬性] Explorer**的 [ **Widget]** 索引標籤中選取 [**填入** **對齊方式**]、 `25` [針對**分佈** **平均填滿**], 然後輸入作為**間距**:

    [![](stacked-views-images/layout04-vs.png "[Widget] 索引標籤")](stacked-views-images/layout04-vs.png#lightbox)
1. 將堆疊視圖放在您想要的畫面上, 並加入條件約束以將其保留在所需的位置。
1. 選取個別的專案, 並將其拖曳至 [堆疊] 視圖:

    [![](stacked-views-images/layout05-vs.png "堆疊視圖中的個別元素")](stacked-views-images/layout05-vs.png#lightbox)
1. 系統會調整配置, 並根據您在上面設定的屬性, 將元素排列在堆疊視圖中。
1. 在 [**屬性] 瀏覽器**的 [ **Widget]** 索引標籤中指派**名稱**, C#以在程式碼中使用您的 UI 控制項。
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以在建立事件處理常式時`TouchUpInside` , 將動作 (例如`UIButton`) 指派給 iOS 設計工具中的 UI 專案 (例如), 但永遠不會呼叫它, 因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 建立 tvOS 使用者介面元素的`Action Type`動作時, 您應該一律使用預設值。

如需使用分鏡腳本的詳細資訊, 請參閱我們的[Hello, tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

在我們的範例中, 我們已公開區段控制項的輸出和動作, 以及每個「玩家卡片」的輸出。 在程式碼中, 我們會根據目前的區段來隱藏和顯示播放程式。 例如：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

當應用程式執行時, 四個元素會平均分散在堆疊視圖中:

[![](stacked-views-images/layout06.png "當應用程式執行時, 四個元素會平均分散在堆疊視圖中")](stacked-views-images/layout06.png#lightbox)

如果播放者數目減少, 則不會顯示未使用的視圖, 而且堆疊視圖會調整配置以符合:

[![](stacked-views-images/layout07.png "如果播放者數目減少, 則不會顯示未使用的視圖, 而且堆疊視圖會調整版面配置以符合")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>從程式碼填入堆疊視圖

除了在 iOS 設計工具中完全定義堆疊視圖的內容和配置之外, 您還可以從C#程式碼動態建立和移除它。

採用下列範例, 使用堆疊視圖來處理評論中的「星星」 (1 到 5):

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

讓我們詳細探討這段程式碼的幾個部分。 首先, 我們使用`if`語句來檢查是否有五個以上的「星星」或小於零。

若要加入新的「星星」, 我們會載入其影像, 並將其**內容模式**設定為**適合調整外觀**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這會讓「星星」圖示在新增至堆疊視圖時遭到扭曲。

接下來, 我們會將新的「星星」圖示新增至 Stack 視圖的子檢視集合:

```csharp
RatingView.AddArrangedSubview(icon);
```

您會注意到, 我們已`UIImageView`將新增`UIStackView`至`ArrangedSubviews`的屬性, 而不`SubView`是。 您想要讓堆疊視圖控制其版面配置的任何視圖, 都必須加入`ArrangedSubviews`至屬性。

若要從堆疊視圖中移除子視圖, 請先取得子視圖以移除:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然後, 我們需要從`ArrangedSubviews`集合和超級視圖中移除它:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只`ArrangedSubviews`從集合中移除子視圖, 會將它移出堆疊視圖的控制項, 但不會將它從畫面中移除。

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>動態變更內容

每當加入、移除或隱藏子視圖時, 堆疊視圖就會自動調整子檢視的版面配置。 如果堆疊視圖的任何屬性已調整 (例如其`Axis`), 則也會調整版面配置。

您可以將版面配置變更放在動畫區塊內來進行動畫處理, 例如:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊視圖的屬性都可以使用分鏡腳本中的大小類別來指定。 這些屬性會自動以動畫顯示大小或方向變更的回應。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式中設計和使用堆疊視圖。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
