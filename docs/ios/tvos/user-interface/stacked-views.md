---
title: 使用 Xamarin 在 tvOS 堆疊檢視
description: 本文件說明如何使用 Xamarin 建置的應用程式中的堆疊的檢視從事 tvOS。 它提供堆疊檢視的高階概觀，並討論自動版面配置，定位和調整大小，堆疊的檢視、 一般用途，分鏡腳本，與整合和更多功能。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f51ed3d6dbbfc8a7e430c2949485838a7471e545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161300"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>使用 Xamarin 在 tvOS 堆疊檢視

堆疊檢視控制項 (`UIStackView`) 利用自動版面配置和大小類別來管理子檢視的堆疊，以水平或垂直方式動態回應內容發生變更及 Apple TV 裝置螢幕大小。

根據開發人員定義的屬性，例如軸、 發佈、 對齊及間距管理附加至堆疊檢視的所有子檢視的配置：

[![](stacked-views-images/stacked01.png "子檢視版面配置圖表")](stacked-views-images/stacked01.png#lightbox)

使用時`UIStackView`Xamarin.tvOS 應用程式中，開發人員可以定義將子檢視，請在 iOS 設計工具，或透過新增和移除子檢視中的分鏡腳本內C#程式碼。

## <a name="about-stacked-view-controls"></a>關於堆疊的檢視控制項 

`UIStackView`旨在做為非轉譯容器檢視，因此，它不會繪製到類似的其他子畫布`UIView`。 設定屬性，例如`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

有數個屬性可控制如何堆疊檢視會排列它的子檢視集合：

- **座標軸**– 決定是否堆疊檢視可可以排列子檢視**水平**或是**垂直**。
- **對齊**– 控制項將子檢視堆疊檢視中的對齊方式。
- **發佈**– 控制如何將子檢視的大小調整堆疊檢視中。
- **間距**– 控制在堆疊檢視中的每一個子檢視之間最小的空間。
- **基準線的相對**– 如果`true`，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 相對於標準版面配置邊界會將子檢視。

通常您會使用堆疊檢視，來排列一小部分的子檢視。 巢狀內彼此的一或多個堆疊檢視，可以建立更複雜的使用者介面。

新增將子檢視 （例如若要控制的高度或寬度） 的其他條件約束，可以進一步微調 Ui 的外觀。 不過，應該小心不必包含衝突的條件約束所引進的堆疊檢視本身。

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

子檢視新增至 堆疊檢視時由使用自動版面配置和大小類別來定位和調整大小的排列的檢視該堆疊檢視完全控制其配置。

堆疊檢視會_釘選_其集合中的第一個和最後一個子檢視**頂端**並**底部**邊緣的垂直堆疊檢視或**保留**並**右**邊緣的水平堆疊檢視。 如果您設定`LayoutMarginsRelativeArrangement`屬性設`true`，然後檢視釘選的子檢視，而非 edge 相關的邊界。

堆疊檢視會使用子檢視的`IntrinsicContentSize`屬性一起定義的子檢視大小在計算時會`Axis`(除了`FillEqually Distribution`)。 `FillEqually Distribution`調整大小的所有子檢視，使它們是相同的大小，因此填入 堆疊檢視，沿著`Axis`。

除了`Fill Alignment`，堆疊檢視會使用子檢視的`IntrinsicContentSize`屬性來計算垂直檢視的大小指定`Axis`。 針對`Fill Alignment`，所有的子檢視會調整大小，讓它們填滿垂直堆疊檢視給定`Axis`。

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>定位和調整大小堆疊檢視

堆疊檢視具有完整控制權的任何子檢視版面配置時 (例如根據屬性`Axis`並`Distribution`)，您仍然需要放置堆疊檢視 (`UIStackView`) 及其使用自動版面配置和大小類別的父檢視內。

一般而言，這表示釘選的堆疊檢視來進行擴大和縮減，進而定義其位置的至少兩個邊緣。 而不需要任何額外的條件約束，堆疊檢視會自動調整大小以符合所有其子，如下所示：

* 大小，沿著其`Axis`會是所有的子檢視大小加上任何已定義每一個子檢視之間的空間的總和。
* 如果`LayoutMarginsRelativeArrangement`屬性是`true`，堆疊檢視大小也會包含邊界的空間。
* 垂直大小`Axis`會設定為集合中最大的子檢視。

此外，您可以指定條件約束，堆疊檢視**高度**並**寬度**。 在此情況下，將子檢視會以配置 （大小） 以填滿由 堆疊檢視所指定的空間`Distribution`和`Alignment`屬性。

如果`BaselineRelativeArrangement`屬性是`true`，將子檢視會以配置根據第一個或最後一個子檢視的基準，而不是使用**頂端**，**底部**或 **Center*-  **Y**位置。 這些計算堆疊檢視的內容，如下所示：

* 垂直的堆疊檢視就會傳回第一個子檢視，第一個基準，最後的最後一個。 如果任一這些子檢視本身都是堆疊檢視，則會使用其第一個或最後一個基準。
* 水平堆疊檢視會使用其最高的子檢視的第一個和最後一個基準。 如果 [高] 檢視也堆疊檢視，它會使用它的最高子檢視作為基準。

> [!IMPORTANT]
> 基準線對齊不適用於自動縮放或壓縮子檢視的大小，基準會計算錯誤的位置。 基準線對齊，請確定子檢視的**高度**符合內建內容檢視**高度**。




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>常見的堆疊檢視用法

有數個適用於堆疊檢視控制項的版面配置類型。 根據 Apple，以下是幾個較常見的用法：

- **定義大小沿著座標軸**– 釘選沿著堆疊檢視的兩邊`Axis`以及其中一個設定的位置，堆疊檢視將會成長以符合其子檢視所定義的空間軸相鄰的邊緣。
*  **定義子檢視的位置**– 釘選到相鄰的邊緣，它的父檢視的堆疊檢視，堆疊檢視將會成長以符合其包含子檢視這兩個維度中。
- **定義的大小和位置堆疊的**– 釘選的父檢視的 [堆疊] 檢視的所有四個邊，堆疊檢視排列堆疊檢視中定義的空間將子檢視。
*  **定義大小與垂直軸**– 釘選到 堆疊檢視中的兩個邊緣與`Axis`以及其中一個設定的位置，堆疊檢視將會成長以符合所定義的空間軸與垂直軸邊緣其子檢視。

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>堆疊檢視和分鏡腳本

使用堆疊檢視 Xamarin.tvOS 應用程式中的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 設計您要新增到 堆疊檢視中的個別項目的版面配置： 

    [![](stacked-views-images/layout01.png "項目配置範例")](stacked-views-images/layout01.png#lightbox)
1. 任何必要的條件約束加入元素，以確保其正確縮放。 項目新增至 堆疊檢視之後，這個步驟很重要。
1. 進行所需的複本 （在此情況下四） 數目： 

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 拖曳**堆疊檢視**從**工具箱**並將它放在檢視上： 

    [![](stacked-views-images/layout03.png "堆疊檢視")](stacked-views-images/layout03.png#lightbox)
1. 選取 堆疊檢視 中**小工具 索引標籤**的**Properties Pad**選取**填滿**如**對齊**，**填滿同樣**for**發佈**，然後輸入`25`for**間距**: 

    [![](stacked-views-images/layout04.png "小工具 索引標籤")](stacked-views-images/layout04.png#lightbox)
1. 堆疊檢視，供您想要它，以及新增條件約束，以保存在所需的位置在螢幕上的位置。
1. 選取個別的項目，並將它們拖曳至 堆疊檢視： 

    [![](stacked-views-images/layout05.png "在 堆疊檢視個別的項目")](stacked-views-images/layout05.png#lightbox)
1. 將會調整版面配置和項目會安排在堆疊檢視中，根據您上面所設定的屬性。
1. 指派**名稱**中**小工具 索引標籤**的**屬性總管**來使用您的 UI 控制項，在C#的程式碼。
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**，按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 設計您要新增到 堆疊檢視中的個別項目的版面配置： 

    [![](stacked-views-images/layout01.png "範例項目配置")](stacked-views-images/layout01.png#lightbox)
1. 任何必要的條件約束加入元素，以確保其正確縮放。 項目新增至 堆疊檢視之後，這個步驟很重要。
1. 進行所需的複本 （在此情況下四） 數目： 

    [![](stacked-views-images/layout02.png "所需的複本數目")](stacked-views-images/layout02.png#lightbox)
1. 拖曳**堆疊檢視**從**工具箱**並將它放在檢視上： 

    [![](stacked-views-images/layout03-vs.png "堆疊檢視")](stacked-views-images/layout03-vs.png#lightbox)
1. 選取 堆疊檢視 中**小工具 索引標籤**的**屬性總管**選取**填滿**如**對齊**，**填滿同樣**for**發佈**，然後輸入`25`for**間距**: 

    [![](stacked-views-images/layout04-vs.png "小工具 索引標籤")](stacked-views-images/layout04-vs.png#lightbox)
1. 堆疊檢視，供您想要它，以及新增條件約束，以保存在所需的位置在螢幕上的位置。
1. 選取個別的項目，並將它們拖曳至 堆疊檢視： 

    [![](stacked-views-images/layout05-vs.png "在 堆疊檢視個別的項目")](stacked-views-images/layout05-vs.png#lightbox)
1. 將會調整版面配置和項目會安排在堆疊檢視中，根據您上面所設定的屬性。
1. 指派**名稱**中**小工具 索引標籤**的**屬性總管**來使用您的 UI 控制項，在C#的程式碼。
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`UI 項目 (例如`UIButton`) 在 iOS 設計工具建立事件處理常式時，它絕不會呼叫因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用預設值`Action Type`tvOS 的使用者介面項目建立動作時。

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

在我們的範例中，我們已公開的輸出和 區段控制項動作和每個 「 player 卡片 」 的輸出。 程式碼中，我們可以隱藏和顯示目前的區段為基礎的播放程式。 例如：

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

執行應用程式時，四個項目將會平均散發在堆疊檢視：

[![](stacked-views-images/layout06.png "執行應用程式時，四個項目會平均散發在堆疊檢視")](stacked-views-images/layout06.png#lightbox)

如果播放程式數目會減少，未使用的檢視會隱藏，並堆疊檢視調整配置以符合：

[![](stacked-views-images/layout07.png "如果播放程式數目會減少，未使用的檢視會隱藏，並堆疊檢視調整配置以符合")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>填入程式碼的堆疊檢視

除了完全在 iOS 設計工具中定義的內容和版面配置堆疊檢視，您可以建立和移除它以動態方式從C#程式碼。

請使用以下範例用來處理 「 星星 」 檢閱 (1 至 5) 中的 堆疊檢視：

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

讓我們看看這段程式碼的幾項詳細資料中。 首先，我們使用`if`陳述式來檢查不超過五個 「 星星 」 或小於零。

若要加入新的"star"中，我們載入其映像，並設定其**內容模式**要**擴展層面符合**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這可防止 「 星型 」 圖示加入至 堆疊檢視時要扭曲。

接下來，我們將新的 「 星型 」 圖示新增至的子檢視的堆疊檢視的集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

您會注意到，我們已新增`UIImageView`要`UIStackView`的`ArrangedSubviews`屬性，而不`SubView`。 任何您想要控制其配置堆疊檢視的檢視必須新增至`ArrangedSubviews`屬性。

若要從堆疊檢視中移除子檢視，請先我們取得移除子檢視：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然後我們要移除兩個`ArrangedSubviews`集合和超級檢視：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只從移除子檢視`ArrangedSubviews`集合會從堆疊檢視的控制項，但不會從螢幕移除。

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>以動態方式變更內容

加入、 移除或隱藏子檢視時，堆疊檢視會自動調整將子檢視的配置。 如果調整堆疊檢視的任何屬性時，也會調整版面配置 (例如其`Axis`)。

版面配置的變更可以動畫顯示放動畫在區塊內，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊檢視的屬性可以使用分鏡腳本內的大小類別來指定。 這些屬性將會自動顯示動畫是回應大小或方向的變更。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與 Xamarin.tvOS 應用程式內使用堆疊的檢視。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
