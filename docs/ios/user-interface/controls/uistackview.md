---
title: "堆疊檢視"
description: "本文說明如何使用新 UIStackView 控制項 Xamarin.iOS 應用程式中的，來管理在 subviews 一組水平或垂直排列的堆疊。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: b8d3fc920610b357520dfebddb79492c44d4781c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="stack-view"></a>堆疊檢視

_本文說明如何使用新 UIStackView 控制項 Xamarin.iOS 應用程式中的，來管理在 subviews 一組水平或垂直排列的堆疊。_

> [!IMPORTANT]
> 請注意，雖然 StackView 支援 iOS 設計工具中，您可能會遇到可用性 bug 時使用穩定的通道。 切換 Beta 或 Alpha 通道應該可以解決此問題。 我們已決定在呈現之前所需的修正程式實作於穩定的通道使用 Xcode 本逐步解說。

堆疊檢視控制項 (`UIStackView`) 會利用自動配置和大小類別來管理一堆 subviews，水平或垂直方向，iOS 裝置的方向和螢幕大小會動態地回應。

它會根據開發人員定義的屬性，例如軸、 發佈、 對齊和間距管理附加至堆疊檢視所有 subviews 的版面配置：

[![](uistackview-images/stacked01.png "堆疊配置圖檢視")](uistackview-images/stacked01.png#lightbox)

當使用`UIStackView`是 Xamarin.iOS 應用程式中，開發人員可以定義 subviews 分鏡腳本設計工具中，在 iOS 中或透過加入和移除 subviews C# 程式碼內。

這份文件是由兩個部分所組成： 快速入門可協助您檢視您的第一個堆疊，請實作，然後一些有關其運作方式的詳細技術資訊。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView、 藉由[Xamarin 大學](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>UIStackView 快速入門

為的快速簡介`UIStackView`控制項，我們會建立一個簡單的介面，讓使用者輸入 1 到 5 的分級。 我們將使用兩個堆疊檢視： 其中一個來排列垂直上裝置的螢幕，另一種水平排列的 1-5 的分級圖示，在畫面上的介面。

### <a name="define-the-ui"></a>用於定義 UI

啟動新的 Xamarin.iOS 專案和編輯**Main.storyboard** Xcode 的介面產生器中的檔案。 首先，將單一**垂直堆疊檢視**上**檢視控制器**:

[![](uistackview-images/quick01.png "拖曳檢視控制器上的單一垂直堆疊檢視")](uistackview-images/quick01.png#lightbox)

在**屬性偵測器**，設定下列選項：

[![](uistackview-images/quick02.png "設定堆疊檢視選項")](uistackview-images/quick02.png#lightbox)

其中：

- **軸**– 判斷堆疊檢視是否是將排列 subviews**水平**或**垂直**。
- **對齊**– 控制 subviews 堆疊檢視中的對齊方式。
- **發佈**– 控制堆疊檢視內 subviews 已調整的大小。
- **間距**– 控制每個堆疊檢視中的子檢視之間最小的空間。
- **基準相對**– 如果選取此選項，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 放置 subviews 相對於標準配置邊界。

當使用堆疊檢視，您可以將**對齊**為**X**和**Y**子檢視的位置和**發佈**為**高度**和**寬度**。

> [!IMPORTANT]
> `UIStackView` 設計成非轉譯容器檢視，因此，不繪製在畫布上的其他子類別類似`UIView`。 因此這類設定屬性`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

繼續配置應用程式的介面加入標籤、 ImageView、 兩個按鈕和水平堆疊檢視，讓它類似下列：

[![](uistackview-images/quick03.png "堆疊檢視 UI 的版面配置")](uistackview-images/quick03.png#lightbox)

設定水平堆疊檢視下列選項：

[![](uistackview-images/quick04.png "設定水平堆疊檢視選項")](uistackview-images/quick04.png#lightbox)

因為我們不要加以延展分級代表每個 「 點 」 的圖示，當它加入水平堆疊檢視中，我們設定**對齊**至**Center**和**發佈**至**平均填滿**。

最後，下列連接**插座**和**動作**:

[![](uistackview-images/quick05.png "堆疊檢視插座和動作")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>填入 UIStackView 從程式碼

返回 Visual Studio，針對 Mac 和編輯**ViewController.cs**檔案，然後加入下列程式碼：

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

### <a name="testing-the-ui"></a>測試 UI

包含所有必要的 UI 項目與位置中的程式碼，您現在可以執行，並測試該介面。 顯示 UI 時，所有的垂直堆疊檢視中的項目會等間距從上到下。

當使用者點選**增加分級** 按鈕，另一個 「 星星 」 加入至畫面 （最多為 5）：

[![](uistackview-images/intro01.png "執行範例應用程式")](uistackview-images/intro01.png#lightbox)

「 星星 」 將自動置中，平均分散到水平堆疊檢視中。 當使用者點選**減少分級**按鈕，「 星星 」 移除 （直到無會保留）。

## <a name="stack-view-details"></a>堆疊檢視詳細資料

既然我們已經有什麼的一般概念`UIStackView`控制項以及運作方式、 讓我們更深入的探討一些其功能的詳細資料。

### <a name="auto-layout-and-size-classes"></a>自動配置和大小類別

如我們所見上方，當子檢視加入到堆疊檢視其版面配置完全由該堆疊檢視，使用自動配置和大小類別來定位和調整大小排列的檢視。

堆疊檢視將_pin_其集合中的第一個和最後一個子檢視**頂端**和**底部**邊緣的垂直堆疊檢視或**剩餘**和**右邊**邊緣的水平堆疊檢視。 如果您設定`LayoutMarginsRelativeArrangement`屬性`true`，然後檢視釘選的相關的邊界，而不是在邊緣 subviews。

堆疊檢視會使用子檢視`IntrinsicContentSize`屬性時計算沿著定義 subviews 大小`Axis`(除了`FillEqually Distribution`)。 `FillEqually Distribution`調整所有 subviews，使其大小相同，因此填滿沿著 堆疊檢視`Axis`。

除了`Fill Alignment`，堆疊檢視會使用子檢視`IntrinsicContentSize`屬性計算檢視表的大小與給定`Axis`。 如`Fill Alignment`，所有 subviews 調整都大小，讓它們填滿垂直堆疊檢視給定`Axis`。

### <a name="positioning-and-sizing-the-stack-view"></a>定位和調整大小堆疊檢視

堆疊檢視具有完整控制權的任何子檢視版面配置時 (例如根據屬性`Axis`和`Distribution`)，您仍需位置堆疊檢視 (`UIStackView`) 及其使用自動配置和大小類別的父檢視內。

一般而言，這表示釘選在至少兩個邊緣堆疊檢視，以展開和收縮，進而定義它的位置。 沒有任何額外的條件約束，堆疊檢視將會自動調整大小以符合所有其 subviews，如下所示：

 - 沿著大小其`Axis`將所有的子檢視大小加上任何已定義每個子檢視之間的空間的總和。
 - 如果`LayoutMarginsRelativeArrangement`屬性是`true`，堆疊檢視大小也會包含邊界的空間。
 - 若要垂直大小`Axis`會設定為集合中最大的子檢視。

此外，您可以指定條件約束堆疊檢視**高度**和**寬度**。 在此情況下，subviews 配置 （大小） 以填滿由 堆疊檢視所指定的空間`Distribution`和`Alignment`屬性。

如果`BaselineRelativeArrangement`屬性是`true`，subviews 配置根據第一個或最後一個子檢視的基準，而不是使用**頂端**，**底部**或 **Center*-  **Y**位置。 這些計算堆疊檢視的內容，如下所示：

 - 垂直的堆疊檢視會傳回第一個子檢視，第一個基準，最後的最後一個。 如果其中一個這些 subviews 本身堆疊檢視，然後將使用其第一個或最後的基準。
 - 水平堆疊檢視會使用其最高的子檢視的第一個和最後一個基準。 如果最高的檢視，也是堆疊檢視，則會使用它的最高子檢視做為基準。

> [!IMPORTANT]
> 基準線對齊方式不適用於延展或壓縮的子檢視的大小，會計算基準，以在錯誤的位置。 基準線對齊方式，請確定子檢視**高度**符合內建函式的內容檢視的**高度**。

### <a name="common-stack-view-uses"></a>檢視常用堆疊

有數個適用於 堆疊檢視控制項的版面配置類型。 根據 Apple，以下是一些較常見的用法：

- **定義大小沿著軸**– 釘選沿著堆疊檢視的這兩個邊緣`Axis`以及其中一個設定的位置堆疊檢視將會成長以符合其 subviews 所定義的空間軸相鄰的邊緣。
 -  **定義子檢視的位置**– 釘選至相鄰的邊緣，它的父檢視的堆疊檢視，堆疊檢視會成長以容納它的包含 subviews 這兩個維度。
- **定義的大小和位置堆疊的**– 釘選的父檢視 堆疊檢視的所有四個邊緣，堆疊檢視排列 subviews 堆疊檢視中定義的空間。
 -  **定義大小與垂直軸**– 釘選到 堆疊檢視的這兩個邊緣垂直`Axis`以及其中一個設定的位置堆疊檢視將會成長以符合所定義的空間軸與垂直軸邊緣其subviews。

### <a name="managing-the-appearance"></a>管理外觀

`UIStackView`設計成非轉譯容器檢視，因此，不繪製在畫布上的其他子類別類似`UIView`。 設定屬性，例如`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

有幾個控制堆疊檢視將會如何排列 subviews 其集合的屬性：

- **軸**– 判斷堆疊檢視是否是將排列 subviews**水平**或**垂直**。
- **對齊**– 控制 subviews 堆疊檢視中的對齊方式。
- **發佈**– 控制堆疊檢視內 subviews 已調整的大小。
- **間距**– 控制每個堆疊檢視中的子檢視之間最小的空間。
- **基準相對**– 如果`true`，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 放置 subviews 相對於標準配置邊界。

通常您會使用堆疊檢視來排列 subviews 數少。 藉由巢狀彼此間的一個或多個堆疊檢視可以建立更複雜的使用者介面 (如同[UIStackView 快速入門](#UIStackView-Quickstart)上方)。

您可以進一步微調 Ui 外觀，方法 （例如若要控制項的高度或寬度） subviews 中加入額外的條件約束。 不過，應該小心不包含衝突的條件約束與所導入堆疊檢視本身。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>檢視和子檢視一致性維護排列

堆疊檢視可確保其`ArrangedSubviews`屬性一律會是子集其`Subviews`屬性使用下列規則：

 - 如果子檢視加入至`ArrangedSubviews`集合，它會自動加入至`Subviews`集合 （除非已該集合的一部分）。
 - 如果從移除子檢視`Subviews`集合 （從顯示中移除），它也會移除從`ArrangedSubviews`集合。
 - 移除從子檢視`ArrangedSubviews`集合不會移除從`Subviews`集合。 因此它不會再進行配置堆疊檢視，但仍會顯示在畫面上。

`ArrangedSubviews`集合一律都是子集`Subview`集合，但是每個集合內的個別 subviews 的順序是個別，由下列控制：

 - 內 subviews 順序`ArrangedSubviews`集合判斷其堆疊內的顯示順序。
 - 內 subviews 順序`Subview`集合決定回到最上層檢視中的疊置順序 （或圖層）。

### <a name="dynamically-changing-content"></a>動態內容

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

## <a name="summary"></a>總結

這篇文章已涵蓋新`UIStackView`管理 subviews Xamarin.iOS 應用程式中任一個水平或垂直排列堆疊中的一組控制 （適用於 iOS 9)。
它以簡單的範例，以建立 UI，使用堆疊檢視的開始，並已完成，但更深入的探討堆疊檢視其屬性和功能。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [介紹 UIStackView （影片）](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
