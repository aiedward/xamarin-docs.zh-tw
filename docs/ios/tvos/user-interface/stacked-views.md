---
title: "使用堆疊檢視"
description: "本文涵蓋設計和 Xamarin.tvOS 應用程式內使用堆疊檢視。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: efc0da0b0f0a800069efb00a402361a8b0cd7f1d
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-stacked-view"></a>使用堆疊檢視

_本文涵蓋設計和 Xamarin.tvOS 應用程式內使用堆疊檢視。_


堆疊檢視控制項 (`UIStackView`) 會利用自動配置和大小類別來管理一堆 subviews，水平或垂直，與內容的變更和 Apple TV 裝置的螢幕大小會動態地回應。

它會根據開發人員定義的屬性，例如軸、 發佈、 對齊和間距管理附加至堆疊檢視所有 subviews 的版面配置：

[![](stacked-views-images/stacked01.png "子檢視配置圖表")](stacked-views-images/stacked01.png#lightbox)

當使用`UIStackView`是 Xamarin.tvOS 應用程式中，開發人員可以定義 subviews 分鏡腳本設計工具中，在 iOS 中或透過加入和移除 subviews C# 程式碼內。

## <a name="about-stacked-view-controls"></a>關於堆疊的檢視控制項 

`UIStackView`設計成非轉譯容器檢視，因此，不繪製在畫布上的其他子類別類似`UIView`。 設定屬性，例如`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

有幾個控制堆疊檢視將會如何排列 subviews 其集合的屬性：

- **軸**– 判斷堆疊檢視是否是將排列 subviews**水平**或**垂直**。
- **對齊**– 控制 subviews 堆疊檢視中的對齊方式。
- **發佈**– 控制堆疊檢視內 subviews 已調整的大小。
- **間距**– 控制每個堆疊檢視中的子檢視之間最小的空間。
- **基準相對**– 如果`true`，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 放置 subviews 相對於標準配置邊界。

通常您會使用堆疊檢視來排列 subviews 數少。 巢狀方式置於彼此間的一個或多個堆疊檢視，可以建立更複雜的使用者介面。

您可以進一步微調 Ui 外觀，方法 （例如若要控制項的高度或寬度） subviews 中加入額外的條件約束。 不過，應該小心不包含衝突的條件約束與所導入堆疊檢視本身。

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>自動配置和大小類別

子檢視加入至 堆疊檢視時由使用自動配置和大小類別來定位和調整大小排列的檢視該堆疊檢視完全控制其配置。

堆疊檢視將_pin_其集合中的第一個和最後一個子檢視**頂端**和**底部**邊緣的垂直堆疊檢視或**剩餘**和**右邊**邊緣的水平堆疊檢視。 如果您設定`LayoutMarginsRelativeArrangement`屬性`true`，然後檢視釘選的相關的邊界，而不是在邊緣 subviews。

堆疊檢視會使用子檢視`IntrinsicContentSize`屬性時計算沿著定義 subviews 大小`Axis`(除了`FillEqually Distribution`)。 `FillEqually Distribution`調整所有 subviews，使其大小相同，因此填滿沿著 堆疊檢視`Axis`。

除了`Fill Alignment`，堆疊檢視會使用子檢視`IntrinsicContentSize`屬性計算檢視表的大小與給定`Axis`。 如`Fill Alignment`，所有 subviews 調整都大小，讓它們填滿垂直堆疊檢視給定`Axis`。

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>定位和調整大小堆疊檢視

堆疊檢視具有完整控制權的任何子檢視版面配置時 (例如根據屬性`Axis`和`Distribution`)，您仍需位置堆疊檢視 (`UIStackView`) 及其使用自動配置和大小類別的父檢視內。

一般而言，這表示釘選在至少兩個邊緣堆疊檢視，以展開和收縮，進而定義它的位置。 沒有任何額外的條件約束，堆疊檢視將會自動調整大小以符合所有其 subviews，如下所示：

* 沿著大小其`Axis`將所有的子檢視大小加上任何已定義每個子檢視之間的空間的總和。
* 如果`LayoutMarginsRelativeArrangement`屬性是`true`，堆疊檢視大小也會包含邊界的空間。
* 若要垂直大小`Axis`會設定為集合中最大的子檢視。

此外，您可以指定條件約束堆疊檢視**高度**和**寬度**。 在此情況下，subviews 配置 （大小） 以填滿由 堆疊檢視所指定的空間`Distribution`和`Alignment`屬性。

如果`BaselineRelativeArrangement`屬性是`true`，subviews 配置根據第一個或最後一個子檢視的基準，而不是使用**頂端**，**底部**或 **Center*-  **Y**位置。 這些計算堆疊檢視的內容，如下所示：

* 垂直的堆疊檢視會傳回第一個子檢視，第一個基準，最後的最後一個。 如果其中一個這些 subviews 本身堆疊檢視，然後將使用其第一個或最後的基準。
* 水平堆疊檢視會使用其最高的子檢視的第一個和最後一個基準。 如果最高的檢視，也是堆疊檢視，則會使用它的最高子檢視做為基準。

> [!IMPORTANT]
> 基準線對齊方式不適用於延展或壓縮的子檢視的大小，會計算基準，以在錯誤的位置。 基準線對齊方式，請確定子檢視**高度**符合內建函式的內容檢視的**高度**。




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>檢視常用堆疊

有數個適用於 堆疊檢視控制項的版面配置類型。 根據 Apple，以下是一些較常見的用法：

- **定義大小沿著軸**– 釘選沿著堆疊檢視的這兩個邊緣`Axis`以及其中一個設定的位置堆疊檢視將會成長以符合其 subviews 所定義的空間軸相鄰的邊緣。
*  **定義子檢視的位置**– 釘選至相鄰的邊緣，它的父檢視的堆疊檢視，堆疊檢視會成長以容納它的包含 subviews 這兩個維度。
- **定義的大小和位置堆疊的**– 釘選的父檢視 堆疊檢視的所有四個邊緣，堆疊檢視排列 subviews 堆疊檢視中定義的空間。
*  **定義大小與垂直軸**– 釘選到 堆疊檢視的這兩個邊緣垂直`Axis`以及其中一個設定的位置堆疊檢視將會成長以符合所定義的空間軸與垂直軸邊緣其subviews。

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>堆疊檢視和分鏡腳本

使用 Xamarin.tvOS 應用程式中的 堆疊檢視最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 設計您的個別項目所要新增到 堆疊檢視的配置： 

    [![](stacked-views-images/layout01.png "項目配置範例")](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束加入至元素，以確保它們正確縮放。 項目新增到 堆疊檢視後，這個步驟很重要。
1. 進行所需的複本 （在此情況下四） 數目： 

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 拖曳**堆疊檢視**從**工具箱**並將它放在檢視上： 

    [![](stacked-views-images/layout03.png "堆疊檢視")](stacked-views-images/layout03.png#lightbox)
1. 選取 堆疊檢視 中**Widget 索引標籤**的**屬性板**選取**填滿**如**對齊**，**填滿同樣**如**發佈**輸入`25`如**間距**: 

    [![](stacked-views-images/layout04.png "[小工具] 索引標籤")](stacked-views-images/layout04.png#lightbox)
1. 將 堆疊檢視，其中它並將條件約束，以將它保存在必要位置在螢幕上。
1. 選取的個別項目，並將它們拖曳至 堆疊檢視： 

    [![](stacked-views-images/layout05.png "堆疊檢視中的個別項目")](stacked-views-images/layout05.png#lightbox)
1. 將會調整版面配置和項目會排列在根據您設定上述屬性 堆疊檢視。
1. 指派**名稱**中**Widget 索引標籤**的**屬性總管**使用 C# 程式碼將 UI 控制項。
1. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**方案總管 中**，按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 設計您的個別項目所要新增到 堆疊檢視的配置： 

    [![](stacked-views-images/layout01.png "範例項目配置")](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束加入至元素，以確保它們正確縮放。 項目新增到 堆疊檢視後，這個步驟很重要。
1. 進行所需的複本 （在此情況下四） 數目： 

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 拖曳**堆疊檢視**從**工具箱**並將它放在檢視上： 

    [![](stacked-views-images/layout03-vs.png "堆疊檢視")](stacked-views-images/layout03-vs.png#lightbox)
1. 選取 堆疊檢視 中**Widget 索引標籤**的**屬性總管**選取**填滿**如**對齊**，**填滿同樣**如**發佈**輸入`25`如**間距**: 

    [![](stacked-views-images/layout04-vs.png "[小工具] 索引標籤")](stacked-views-images/layout04-vs.png#lightbox)
1. 將 堆疊檢視，其中它並將條件約束，以將它保存在必要位置在螢幕上。
1. 選取的個別項目，並將它們拖曳至 堆疊檢視： 

    [![](stacked-views-images/layout05-vs.png "堆疊檢視中的個別項目")](stacked-views-images/layout05-vs.png#lightbox)
1. 將會調整版面配置和項目會排列在根據您設定上述屬性 堆疊檢視。
1. 指派**名稱**中**Widget 索引標籤**的**屬性總管**使用 C# 程式碼將 UI 控制項。
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`UI 項目 (例如`UIButton`) 上的 ios 設計工具建立事件處理常式時，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 您應該一律使用預設`Action Type`tvos 使用者介面項目建立動作時。

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

在我們的範例，我們已公開插座和區段控制項動作和每個 「 player 卡 」 的輸出。 程式碼中，我們會隱藏和顯示播放程式根據目前的區段。 例如: 

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

執行應用程式時，四個項目會平均分散我們堆疊檢視中：

[![](stacked-views-images/layout06.png "執行應用程式時，四個項目會平均散發我們堆疊檢視中")](stacked-views-images/layout06.png#lightbox)

就會減少的播放程式數目，如果未使用的檢視會隱藏起來，並堆疊檢視調整配置以符合：

[![](stacked-views-images/layout07.png "就會減少的播放程式數目，如果未使用的檢視會隱藏和堆疊檢視調整以符合的版面配置")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>填入程式碼的堆疊檢視

除了完全 iOS 設計工具中定義的內容和 [堆疊檢視] 的版面配置，您可以建立並從 C# 程式碼以動態方式將它移除。

取得用來處理 「 星星 」 檢閱 (1 到 5) 中的 堆疊檢視下列範例：

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

讓我們看看一些這段程式碼片段中詳細資料。 首先，我們使用`if`陳述式來檢查不超過五個 「 星星 」 或少於零時。

若要加入新的 「 星星 」 中，我們載入其映像，並設定其**內容模式**至**標尺的外觀符合**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這可防止 「 星型 」 圖示加入至 堆疊檢視時被扭曲。

接下來，我們會將新的 「 星型 」 圖示加入 subviews 堆疊檢視的集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

您會注意到我們新增`UIImageView`至`UIStackView`的`ArrangedSubviews`屬性並無法供`SubView`。 任何您想要控制其配置的堆疊檢視的檢視必須新增至`ArrangedSubviews`屬性。

若要從堆疊檢視中移除子檢視，請先我們取得移除子檢視：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然後我們需要將它移除，同時從`ArrangedSubviews`集合和超級檢視：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只從移除子檢視`ArrangedSubviews`集合會將它從堆疊檢視的控制項，但不會移除它的螢幕。

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>動態內容

堆疊檢視加入、 移除或隱藏子檢視時，會自動調整 subviews 版面的配置。 如果調整堆疊檢視的任何屬性時，也會調整版面配置 (例如其`Axis`)。

版面配置的變更可以動畫顯示放置動畫在區塊內，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊檢視的屬性可以使用分鏡腳本大小類別來指定。 這些屬性將會自動建立動畫是回應大小或方向的變更。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和 Xamarin.tvOS 應用程式內使用堆疊檢視。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
