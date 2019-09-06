---
title: Xamarin 中的堆疊視圖
description: 本文涵蓋在 Xamarin iOS 應用程式中使用新的 UIStackView 控制項，以水準或垂直排列堆疊管理一組子檢視。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: a3adbf18e18e4f2a42ed3c89162455040a9fe699
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282692"
---
# <a name="stack-views-in-xamarinios"></a>Xamarin 中的堆疊視圖

_本文涵蓋在 Xamarin iOS 應用程式中使用新的 UIStackView 控制項，以水準或垂直排列堆疊管理一組子檢視。_

> [!IMPORTANT]
> 請注意，雖然 iOS 設計工具支援 System.windows.forms.toolstrip.stackview，但在使用穩定通道時，您可能會遇到可用性錯誤。 切換搶鮮版（Beta）或 Alpha 聲道應該可減輕這個問題。 我們決定使用 Xcode 來呈現此逐步解說，直到在穩定通道中執行所需的修正程式為止。

Stack 視圖控制項（`UIStackView`）會利用自動設定和大小類別的功能來管理子檢視堆疊（不論是水準或垂直），這會動態回應 iOS 裝置的方向和螢幕大小。

附加至堆疊視圖的所有子檢視的配置，都是由它以開發人員定義的屬性（例如軸、分佈、對齊和間距）為基礎來管理：

[![](uistackview-images/stacked01.png "堆疊視圖版面配置圖表")](uistackview-images/stacked01.png#lightbox)

`UIStackView`在 Xamarin ios 應用程式中使用時，開發人員可以在 ios 設計工具中的分鏡腳本內定義子檢視，或是在程式C#代碼中新增和移除子檢視。

此檔包含兩個部分：快速入門可協助您執行第一個堆疊視圖，然後再提供更多有關其運作方式的技術詳細資料。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView 影片**

## <a name="uistackview-quickstart"></a>UIStackView 快速入門

作為`UIStackView`控制項的快速簡介，我們將建立一個簡單的介面，讓使用者可以輸入1到5的評等。 我們將使用兩個堆疊視圖：一個用來在裝置的螢幕上垂直排列介面，另一個用來在螢幕上水準排列1-5 評等圖示。

### <a name="define-the-ui"></a>定義 UI

啟動新的 Xamarin iOS 專案，並在 Xcode 的 Interface Builder 中編輯**主要**的分鏡腳本檔案。 首先，將單一**垂直堆疊視圖**拖曳到**View Controller**上：

[![](uistackview-images/quick01.png "將單一垂直堆疊視圖拖曳至視圖控制器")](uistackview-images/quick01.png#lightbox)

在 [**屬性偵測器**] 中，設定下列選項：

[![](uistackview-images/quick02.png "設定堆疊視圖選項")](uistackview-images/quick02.png#lightbox)

其中：

- **軸**–決定堆疊視圖是以**水準**或**垂直**方式排列子檢視。
- **對齊**–控制子檢視在堆疊視圖中的對齊方式。
- **散發**–控制如何在堆疊視圖內調整子檢視大小。
- **間距**–控制堆疊視圖中每個子視圖之間的最小空間。
- **基準相對**–如果核取，則每個子視圖的垂直間距將衍生自其基準。
- **相對於版面配置邊界**：將子檢視相對於標準版面配置邊界。

使用堆疊視圖時，您可以將**對齊**方式視為子視圖的**X**和**Y**位置，並將**分佈**視為**高度**和**寬度**。

> [!IMPORTANT]
> `UIStackView`是設計為非轉譯容器視圖，因此不會繪製到畫布上，如的`UIView`其他子類別。 因此，設定屬性（ `BackgroundColor`例如或`DrawRect`覆寫）將不會有任何視覺效果。

藉由新增標籤（ImageView）、兩個按鈕和水準堆疊視圖，繼續配置應用程式的介面，讓它看起來像下面這樣：

[![](uistackview-images/quick03.png "配置堆疊視圖 UI")](uistackview-images/quick03.png#lightbox)

使用下列選項設定水準堆疊視圖：

[![](uistackview-images/quick04.png "設定水準堆疊視圖選項")](uistackview-images/quick04.png#lightbox)

由於我們不想要在將分級中的每個「點」新增至水準堆疊視圖時，將其顯示為伸展的圖示，因此我們已將**對齊方式**設定為 [**中央**]，而 [**分佈**] 則會**平均填滿**。

最後，連接下列**輸出**和**動作**：

[![](uistackview-images/quick05.png "堆疊視圖的輸出和動作")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>從程式碼填入 UIStackView

返回 Visual Studio for Mac 並編輯**ViewController.cs**檔案，並新增下列程式碼：

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

讓我們詳細探討這段程式碼的幾個部分。 首先，我們使用`if`語句來檢查是否有五個以上的「星星」或小於零。

若要加入新的「星星」，我們會載入其影像，並將其**內容模式**設定為**適合調整外觀**：

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這會讓「星星」圖示在新增至堆疊視圖時遭到扭曲。

接下來，我們會將新的「星星」圖示新增至 Stack 視圖的子檢視集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

您會注意到，我們已`UIImageView`將新增`UIStackView`至`ArrangedSubviews`的屬性，而不`SubView`是。 您想要讓堆疊視圖控制其版面配置的任何視圖，都必須加入`ArrangedSubviews`至屬性。

若要從堆疊視圖中移除子視圖，請先取得子視圖以移除：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然後，我們需要從`ArrangedSubviews`集合和超級視圖中移除它：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只`ArrangedSubviews`從集合中移除子視圖，會將它移出堆疊視圖的控制項，但不會將它從畫面中移除。

### <a name="testing-the-ui"></a>測試 UI

當所有必要的 UI 元素和程式碼都備妥之後，您現在可以執行並測試介面。 當 UI 顯示時，垂直堆疊視圖中的所有專案都會從上到下的間距。

當使用者按下 [**增加評**等] 按鈕時，會在畫面中新增另一個「星星」（最多5個）：

[![](uistackview-images/intro01.png "執行範例應用程式")](uistackview-images/intro01.png#lightbox)

「星星」將會自動置中，並平均分散在水準堆疊視圖中。 當使用者按下 [**降低評**等] 按鈕時，就會移除「星星」（直到不剩下任何內容）。

## <a name="stack-view-details"></a>堆疊視圖詳細資料

現在，我們已大致瞭解`UIStackView`控制項的功能以及其運作方式，接下來讓我們深入探討其中一些功能和詳細資料。

### <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

如先前所見，當子視圖加入至堆疊視圖時，其配置完全是由該堆疊視圖使用自動版面配置和大小類別來定位和調整排列的視圖大小。

堆疊視圖會將其集合中的第一個和最後一個子視圖_釘_選到垂直堆疊視圖的**上**和**下**邊緣，或水準堆疊視圖的**左邊**和**右邊**邊緣。 如果您將`LayoutMarginsRelativeArrangement`屬性設定為`true`，則此視圖會將子檢視釘選到相關的邊界，而不是邊緣。

堆疊視圖會在計算已定義`IntrinsicContentSize` `Axis`的子檢視大小（除外`FillEqually Distribution`）時，使用子視圖的屬性。 會調整所有子檢視的大小，使其具有相同的大小，因此會`Axis`沿著來填滿堆疊視圖。 `FillEqually Distribution`

除了的例外狀況之外`Fill Alignment`，堆疊視圖會使用`IntrinsicContentSize`子視圖的屬性來計算與指定`Axis`的垂直的視圖大小。 針對，所有子檢視都會調整大小，使其填滿垂直于指定`Axis`之的堆疊視圖。 `Fill Alignment`

### <a name="positioning-and-sizing-the-stack-view"></a>定位和調整堆疊視圖的大小

雖然堆疊視圖對於任何子視圖的版面配置（根據`Axis`和`Distribution`之類的屬性）有完全控制權，但您仍然需要使用自動設定和大小類別`UIStackView`，在其父視圖內放置堆疊視圖（）。

一般來說，這表示至少要釘選堆疊視圖的兩個邊緣，以擴充和合約，進而定義其位置。 如果沒有任何額外的條件約束，則會自動調整堆疊視圖的大小，以符合其所有的子檢視，如下所示：

- 連同`Axis`所有子視圖大小的總和，以及每個子視圖之間已定義的任何空間，都是大小。
- 如果屬性為`true`，則堆疊 Views 大小也會包含邊界的空間。 `LayoutMarginsRelativeArrangement`
- 垂直于的`Axis`大小會設定為集合中最大的子視圖。

此外，您可以指定堆疊視圖的**高度**和**寬度**的條件約束。 在此情況下，子檢視會配置（調整大小），以填滿堆疊視圖所指定的空間（由`Distribution`和`Alignment`屬性所決定）。

`true` - 如果屬性為，則會根據第一個或最後一個子視圖的基準來配置子檢視，而不是使用頂端、底端或中央 Y 位置。 `BaselineRelativeArrangement` 這些是在堆疊視圖的內容上計算，如下所示：

- 垂直堆疊視圖會傳回第一個基準的第一個子視圖，最後一個是最後一個。 如果其中一個子檢視本身是堆疊視圖，則會使用其第一個或最後一個基準。
- 水準堆疊視圖會針對第一個和最後一個基準使用其最高的子視圖。 如果最高的視圖也是堆疊視圖，它會使用其最高的子視圖作為基準。

> [!IMPORTANT]
> 基準對齊不適用於延伸或壓縮的子視圖大小，因為基準會計算到錯誤的位置。 針對 [基準對齊]，請確定子視圖的**高度**符合內建內容視圖的**高度**。

### <a name="common-stack-view-uses"></a>一般堆疊視圖使用

有數種版面配置類型可搭配堆疊視圖控制項運作。 根據 Apple，以下是幾個較常見的用法：

- **定義沿著軸的大小**–藉由將兩個邊緣沿著堆疊視圖`Axis`和其中一個相鄰邊緣釘選來設定位置，堆疊視圖會沿著軸成長，以符合其子檢視所定義的空間。
- **定義子視圖的位置**–藉由釘選到堆疊視圖的相鄰邊緣與其父視圖，堆疊視圖會同時在這兩個維度中成長，以符合其包含的子檢視。
- **定義堆疊的大小和位置**–藉由將堆疊視圖的全部四個邊緣釘選到父視圖，堆疊視圖會根據堆疊視圖內定義的空間來排列子檢視。
- **定義垂直軸的大小**–藉由將邊緣垂直`Axis`放在堆疊視圖和軸上的其中一個邊緣來設定位置，堆疊視圖會沿著軸垂直成長，以符合其子檢視所定義的空間。

### <a name="managing-the-appearance"></a>管理外觀

是設計為非轉譯容器視圖，因此不會繪製到畫布上，如的`UIView`其他子類別。 `UIStackView` 設定屬性（例如`BackgroundColor`或覆`DrawRect`寫）不會有任何視覺效果。

有數個屬性可以控制堆疊視圖如何排列其子檢視的集合：

- **軸**–決定堆疊視圖是以**水準**或**垂直**方式排列子檢視。
- **對齊**–控制子檢視在堆疊視圖中的對齊方式。
- **散發**–控制如何在堆疊視圖內調整子檢視大小。
- **間距**–控制堆疊視圖中每個子視圖之間的最小空間。
- **基準相對**-如果`true`為，則每個子視圖的垂直間距會從它的基準衍生。
- **相對於版面配置邊界**：將子檢視相對於標準版面配置邊界。

通常您會使用堆疊視圖來排列少量的子檢視。 更複雜的使用者介面可以藉由將一或多個堆疊視圖彼此嵌套來建立（就像我們在上面的[UIStackView 快速入門](#uistackview-quickstart)中所做的一樣）。

您可以藉由在子檢視中新增額外的條件約束（例如，控制高度或寬度），進一步微調 Ui 外觀。 不過，請小心不要將衝突的條件約束納入堆疊視圖本身所引進的限制式。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>維護排列的視圖和子視圖的一致性

堆疊視圖會使用下列規則， `ArrangedSubviews`確保其屬性一律是其`Subviews`屬性的子集：

- 如果子視圖已加入至`ArrangedSubviews`集合，則會自動將它加入`Subviews`至集合（除非它已經是該集合的一部分）。
- 如果從`Subviews`集合中移除子視圖（從顯示中移除），它也會`ArrangedSubviews`從集合中移除。
- 從`ArrangedSubviews`集合中移除子視圖並不會將它`Subviews`從集合中移除。 如此一來，堆疊視圖就不會再配置它，但在螢幕上仍會顯示。

集合一律是`Subview`集合的子集，但是每個集合內個別子檢視的順序是由下列各項分隔並控制： `ArrangedSubviews`

- `ArrangedSubviews`集合內的子檢視順序會決定其在堆疊內的顯示順序。
- `Subview`集合內的子檢視順序會決定其在視圖內的迭置順序（或分層）。

### <a name="dynamically-changing-content"></a>動態變更內容

每當加入、移除或隱藏子視圖時，堆疊視圖就會自動調整子檢視的版面配置。 如果堆疊視圖的任何屬性已調整（例如其`Axis`），則也會調整版面配置。

您可以將版面配置變更放在動畫區塊內來進行動畫處理，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊視圖的屬性都可以使用分鏡腳本中的大小類別來指定。 這些屬性會自動以動畫顯示大小或方向變更的回應。

## <a name="summary"></a>總結

本文涵蓋了新`UIStackView`的控制項（適用于 ios 9），可管理 Xamarin iOS 應用程式中水準或垂直排列堆疊中的一組子檢視。
它從使用堆疊視圖建立 UI 的簡單範例開始，並已完成堆疊視圖及其屬性和功能的詳細探討。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [UIStackView 簡介（影片）](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
