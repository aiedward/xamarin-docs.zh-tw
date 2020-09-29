---
title: Xamarin 中的堆疊視圖
description: 本文涵蓋如何在 Xamarin iOS 應用程式中使用新的 UIStackView 控制項，以水準或垂直排列的堆疊來管理一組子檢視。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: a926ae3aa91b7c1dbe13ba78c28f4df63228e151
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436493"
---
# <a name="stack-views-in-xamarinios"></a>Xamarin 中的堆疊視圖

_本文涵蓋如何在 Xamarin iOS 應用程式中使用新的 UIStackView 控制項，以水準或垂直排列的堆疊來管理一組子檢視。_

> [!IMPORTANT]
> 請注意，在 iOS 設計工具中支援 StackView 時，您可能會在使用穩定通道時遇到可用性錯誤。 切換 Beta 或 Alpha 通道應該可減輕此問題。 我們決定在穩定通道中執行所需的修正程式之前，先使用 Xcode 提出此逐步解說。

Stack View 控制項 (`UIStackView`) 利用自動設定和大小類別的功能，以水準或垂直方式管理子檢視堆疊，其會動態回應 iOS 裝置的方向和螢幕大小。

所有附加至堆疊視圖的子檢視配置都是由其根據開發人員定義的屬性（例如軸、分佈、對齊和間距）進行管理：

[![堆疊視圖版面配置圖表](uistackview-images/stacked01.png)](uistackview-images/stacked01.png#lightbox)

`UIStackView`在 Xamarin ios 應用程式中使用時，開發人員可以在 Ios 設計工具的分鏡腳本內定義子檢視，或在 c # 程式碼中新增和移除子檢視。

這份檔是由兩個部分所組成：快速入門可協助您執行第一個堆疊視圖，以及更多有關其運作方式的技術詳細資料。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView 影片**

## <a name="uistackview-quickstart"></a>UIStackView 快速入門

作為控制項的快速簡介 `UIStackView` ，我們將建立一個簡單的介面，讓使用者輸入從1到5的評等。 我們將使用兩個堆疊視圖：一個用來在裝置的螢幕上垂直排列介面，另一個則會在螢幕上水準排列1-5 分級圖示。

### <a name="define-the-ui"></a>定義 UI

啟動新的 Xamarin 專案，並在 Xcode 的 Interface Builder 中編輯 **主要** 的分鏡腳本檔案。 首先，將單一 **垂直堆疊視圖** 拖曳至 **View Controller**：

[![拖曳視圖控制器上的單一垂直堆疊視圖](uistackview-images/quick01.png)](uistackview-images/quick01.png#lightbox)

在 [ **屬性偵測器**] 中，設定下列選項：

[![設定堆疊視圖選項](uistackview-images/quick02.png)](uistackview-images/quick02.png#lightbox)

其中：

- **軸** –決定堆疊視圖是以 **水準** 或 **垂直**方式排列子檢視。
- **對齊** –控制子檢視在堆疊視圖內的對齊方式。
- **分佈** –控制子檢視在堆疊視圖內的大小。
- **間距** -控制堆疊視圖中每個子視圖之間的最短空間。
- **基準相對** -如果選取此選項，每個子視圖的垂直間距將衍生自其基準。
- **相對的版面配置邊界** ：使子檢視相對於標準版面配置邊界。

使用堆疊視圖時，您可以將**對齊方式**視為子視圖的**X**和**Y**位置，以及做為**高度**和**寬度**的**分佈**。

> [!IMPORTANT]
> `UIStackView` 是設計成非轉譯容器視圖，因此不會像的其他子類別一樣繪製到畫布上 `UIView` 。 因此設定屬性（例如 `BackgroundColor` 或覆寫） `DrawRect` 不會有視覺化效果。

藉由新增標籤、>imageview、兩個按鈕和水準堆疊視圖，讓它類似于下列內容，繼續配置應用程式介面：

[![配置堆疊視圖 UI](uistackview-images/quick03.png)](uistackview-images/quick03.png#lightbox)

使用下列選項設定水準堆疊視圖：

[![設定水準堆疊視圖選項](uistackview-images/quick04.png)](uistackview-images/quick04.png#lightbox)

因為我們不想要在將評量中的每個「點」新增到水準堆疊視圖時進行延展，所以我們已將 **對齊方式** 設定為 [ **置** 中]，並將 [ **分佈** ] 設定為 [ **平均填滿**]。

最後，連接下列 **輸出** 和 **動作**：

[![堆疊視圖的輸出和動作](uistackview-images/quick05.png)](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>從程式碼填入 UIStackView

返回 Visual Studio for Mac 並編輯 **ViewController.cs** 檔案，並新增下列程式碼：

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

讓我們詳細看看這段程式碼的幾個部分。 首先，我們會使用 `if` 語句來檢查不超過五個 "星星" 或小於零。

若要加入新的「星星」，我們會載入其影像，並將其 **內容模式** 設定為 **適當**調整：

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這會讓「星形」圖示在新增至堆疊視圖時變得不失真。

接下來，我們會將新的「星星」圖示新增至堆疊視圖的子檢視集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

您將會注意到，我們已將加入 `UIImageView` 至的 `UIStackView` `ArrangedSubviews` 屬性，而不是加入至 `SubView` 。 您要讓堆疊視圖控制其版面配置的任何視圖都必須加入至 `ArrangedSubviews` 屬性。

若要從堆疊視圖中移除子視圖，請先取得要移除的子視圖：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

接著，我們需要從 `ArrangedSubviews` 集合和超級視圖中移除它：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

從集合中移除子視圖 `ArrangedSubviews` 會將它移出堆疊視圖的控制項，但不會將它從畫面中移除。

### <a name="testing-the-ui"></a>測試 UI

使用所有必要的 UI 元素和程式碼之後，您現在可以執行和測試介面。 當 UI 顯示時，垂直堆疊視圖中的所有元素都會平均地從上到下的間距。

當使用者按下 [ **增加評** 等] 按鈕時，會將另一個「星號」新增至螢幕 (最多5個) ：

[![範例應用程式執行](uistackview-images/intro01.png)](uistackview-images/intro01.png#lightbox)

「星星」會自動置中，並平均分散在水準堆疊視圖中。 當使用者按下 [ **降低評** 等] 按鈕時，會移除「星號」 (直到無) 為止。

## <a name="stack-view-details"></a>堆疊視圖詳細資料

現在我們已經大致瞭解 `UIStackView` 控制項的功能及其運作方式，讓我們進一步瞭解其功能和詳細資料。

### <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

如上面所見，當子類別加入至堆疊視圖時，其配置完全是由該堆疊視圖使用自動版面配置和大小類別來定位和調整排列檢視的大小。

堆疊視圖會將集合中的第一個和最後一個子視圖 _釘_ 選到垂直堆疊視圖的 **上** 邊緣和 **下** 邊緣，或是水準堆疊視圖的 **左邊** 緣和 **右** 邊緣。 如果您將 `LayoutMarginsRelativeArrangement` 屬性設定為 `true` ，則視圖會將子檢視釘選到相關邊界，而不是邊緣。

`IntrinsicContentSize`當您沿著定義的 `Axis` (（) 除外）計運算元檢視大小時，堆疊視圖會使用子視圖的屬性 `FillEqually Distribution` 。 `FillEqually Distribution`會調整所有子檢視的大小，使其大小相同，因此在中填入堆疊視圖 `Axis` 。

除了之外 `Fill Alignment` ，堆疊視圖會使用子視圖的 `IntrinsicContentSize` 屬性來計算視圖的大小，使其與指定的垂直垂直 `Axis` 。 若為 `Fill Alignment` ，則會將所有的子檢視調整大小，使其填滿垂直于指定的堆疊視圖 `Axis` 。

### <a name="positioning-and-sizing-the-stack-view"></a>定位和調整堆疊視圖的大小

雖然堆疊視圖對於任何子視圖 (的版面配置具有完全控制權，但根據 `Axis` 和) 等屬性 `Distribution` ，您仍然需要使用自動設定和大小類別，將堆疊 view (`UIStackView`) 放置在父視圖內。

一般而言，這表示要釘選堆疊視圖的兩個邊來展開和收縮，進而定義其位置。 如果沒有任何額外的條件約束，堆疊視圖會自動調整大小以符合其所有的子檢視，如下所示：

- 的大小 `Axis` 會是所有子視圖大小加上每個子視圖之間已定義之任何空間的總和。
- 如果 `LayoutMarginsRelativeArrangement` 屬性為 `true` ，堆疊視圖大小也會包含邊界的空間。
- 垂直的大小 `Axis` 會設定為集合中最大的子視圖。

此外，您可以指定堆疊視圖的 **高度** 和 **寬度**條件約束。 在此情況下，子檢視會配置 (大小) 以填滿由和屬性決定的堆疊視圖所指定的空間 `Distribution` `Alignment` 。

如果 `BaselineRelativeArrangement` 屬性為 `true` ，則會根據第一個或最後一個子視圖的基準配置子檢視，而不是使用**頂端**、**下**或**置**中的 -  **Y**位置。 這些會在堆疊視圖的內容上計算，如下所示：

- 垂直堆疊視圖會傳回第一個基準的第一個子視圖，最後一個是最後一個。 如果其中一個子檢視本身是堆疊視圖，則會使用其第一個或最後一個基準。
- 水準堆疊視圖會針對第一個和最後一個基準使用其最高的子視圖。 如果最高的視圖也是堆疊視圖，它會使用最高的子視圖作為基準。

> [!IMPORTANT]
> 基準對齊無法在延伸或壓縮的子視圖大小上運作，因為基準將會計算到錯誤的位置。 針對基準對齊，請確定子視圖的 **高度** 符合內建內容視圖的 **高度**。

### <a name="common-stack-view-uses"></a>一般堆疊視圖使用

有數種版面配置類型可搭配 Stack View 控制項運作。 根據 Apple，以下是幾個較常見的用途：

- **定義沿著軸的大小** –藉由將兩個邊緣沿著堆疊視圖 `Axis` 和連續的其中一個邊緣釘選來設定位置，堆疊視圖將沿著軸成長，以符合其子檢視所定義的空間。
- **定義子視圖的位置** –藉由釘選到堆疊視圖的相鄰邊緣至其父視圖，堆疊視圖會在這兩個維度中成長，以符合其包含子檢視。
- **定義堆疊的大小和位置** –藉由將堆疊視圖的四個邊緣釘選到父視圖，堆疊視圖會根據堆疊視圖內定義的空間來排列子檢視。
- **定義垂直軸的大小** –藉由將兩個邊緣垂直釘選到堆疊視圖 `Axis` ，以及沿著軸的其中一個邊緣來設定位置，堆疊視圖將會隨著軸垂直成長，以符合其子檢視所定義的空間。

### <a name="managing-the-appearance"></a>管理外觀

`UIStackView`是設計成非轉譯的容器視圖，因此不會像的其他子類別一樣繪製到畫布上 `UIView` 。 設定屬性（例如 `BackgroundColor` 或覆寫） `DrawRect` 不會有視覺化效果。

有幾個屬性會控制堆疊視圖如何排列其子檢視的集合：

- **軸** –決定堆疊視圖是以 **水準** 或 **垂直**方式排列子檢視。
- **對齊** –控制子檢視在堆疊視圖內的對齊方式。
- **分佈** –控制子檢視在堆疊視圖內的大小。
- **間距** -控制堆疊視圖中每個子視圖之間的最短空間。
- **基準相對** -如果為 `true` ，則每個子視圖的垂直間距將衍生自其基準。
- **相對的版面配置邊界** ：使子檢視相對於標準版面配置邊界。

通常您會使用堆疊視圖來排列少量的子檢視。 藉由將一或多個堆疊 (視圖嵌套在彼此的 [UIStackView 快速入門](#uistackview-quickstart)) 中，即可建立更複雜的使用者介面。

您可以藉由將額外的條件約束新增至子檢視 (（例如控制高度或寬度) ），進一步微調 Ui 外觀。 不過，請小心不要將衝突的條件約束加入堆疊視圖本身所引進的條件約束。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>維護排列的視圖和子視圖一致性

堆疊視圖會 `ArrangedSubviews` 使用下列規則，確保其屬性一律是其屬性的子集 `Subviews` ：

- 如果子視圖加入至 `ArrangedSubviews` 集合，則會自動將它加入至 `Subviews` 集合 (，除非它已經是該集合) 的一部分。
- 如果從集合移除子視圖 `Subviews` (從顯示) 移除，它也會從集合中移除 `ArrangedSubviews` 。
- 從集合中移除子視圖 `ArrangedSubviews` 並不會將它從 `Subviews` 集合中移除。 因此它不會再由堆疊視圖配置，但仍會顯示在螢幕上。

`ArrangedSubviews`集合一律是集合的子集 `Subview` ，但是每個集合中個別子檢視的順序會由下列各項分開和控制：

- 集合內的子檢視順序 `ArrangedSubviews` 決定其在堆疊內的顯示順序。
- 集合內的子檢視順序決定了 `Subview` 它們的迭置順序 (或在視野內的分層) 回正面。

### <a name="dynamically-changing-content"></a>動態變更內容

每當加入、移除或隱藏子視圖時，堆疊視圖都會自動調整子檢視的版面配置。 如果堆疊視圖的任何屬性調整 (例如其) ，也會調整版面配置 `Axis` 。

版面配置變更可透過將其放在動畫區塊內進行動畫，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊視圖的屬性都可以使用分鏡腳本內的大小類別來指定。 這些屬性將會自動以動畫顯示，回應大小或方向變更。

## <a name="summary"></a>摘要

本文涵蓋了 `UIStackView` ios 9 的新控制項 (，) 在 Xamarin ios 應用程式中，以水準或垂直排列的堆疊來管理一組子檢視。
它一開始會有一個簡單的範例，說明如何使用堆疊查看來建立 UI，並已完成更詳細的查看堆疊視圖及其屬性和功能。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [UIStackView (影片) 簡介 ](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)