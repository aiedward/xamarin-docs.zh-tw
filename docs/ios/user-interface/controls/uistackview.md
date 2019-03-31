---
title: 在 Xamarin.iOS 中的堆疊檢視
description: 這篇文章說明如何使用新 UIStackView 控制項中的 Xamarin.iOS 應用程式，來管理一組子檢視中的水平或垂直排列的堆疊。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 6d5be72a9329675a65b0d6873d13894b314b50e7
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677842"
---
# <a name="stack-views-in-xamarinios"></a>在 Xamarin.iOS 中的堆疊檢視

_這篇文章說明如何使用新 UIStackView 控制項中的 Xamarin.iOS 應用程式，來管理一組子檢視中的水平或垂直排列的堆疊。_

> [!IMPORTANT]
> 請注意，雖然 StackView 都支援在 iOS 設計工具中，您可能會遇到可用性有關的錯誤，使用穩定通道時。 切換 Beta 和 Alpha 通道應該有助於解決此問題。 我們已經決定要呈現此逐步解說中使用 Xcode，直到為穩定通道中實作必要的修正程式。

堆疊檢視控制項 (`UIStackView`) 利用自動版面配置和大小類別來管理子檢視的堆疊水平或垂直方向和螢幕大小的 iOS 裝置會以動態方式回應。

根據開發人員定義的屬性，例如軸、 發佈、 對齊及間距管理附加至堆疊檢視的所有子檢視的配置：

[![](uistackview-images/stacked01.png "堆疊檢視版面配置圖表")](uistackview-images/stacked01.png#lightbox)

當使用`UIStackView`在 Xamarin.iOS 應用程式中，開發人員可以定義將子檢視，請在 iOS 設計工具中，或透過新增和移除子檢視 C# 程式碼中的分鏡腳本內。

這份文件是由兩個部分所組成： 可讓您檢視您的第一個堆疊，請實作，然後一些詳細技術資訊及其運作方式的快速入門。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView、 藉由[Xamarin University](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>UIStackView 快速入門

為快速介紹`UIStackView`控制項中，我們將建立一個簡單的介面，允許使用者輸入 1 到 5 的分級。 我們將使用兩個堆疊檢視： 一個用來排列垂直上裝置的螢幕，另一種水平排列 1-5 分級圖示，在螢幕上的介面。

### <a name="define-the-ui"></a>用於定義 UI

啟動新的 Xamarin.iOS 專案，並編輯**Main.storyboard** Xcode 的 Interface Builder 中的檔案。 首先，將單一**垂直堆疊檢視**上**檢視控制器**:

[![](uistackview-images/quick01.png "拖曳 單一檢視控制器上垂直堆疊檢視")](uistackview-images/quick01.png#lightbox)

在 **屬性偵測器**，設定下列選項：

[![](uistackview-images/quick02.png "設定堆疊檢視選項")](uistackview-images/quick02.png#lightbox)

其中：

- **座標軸**– 決定是否堆疊檢視可可以排列子檢視**水平**或是**垂直**。
- **對齊**– 控制項將子檢視堆疊檢視中的對齊方式。
- **發佈**– 控制如何將子檢視的大小調整堆疊檢視中。
- **間距**– 控制在堆疊檢視中的每一個子檢視之間最小的空間。
- **基準相對**– 若選取此選項，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 相對於標準版面配置邊界會將子檢視。

當使用堆疊檢視，您可以將**對齊**做為**X**並**Y**子檢視的位置和**發佈**為**高度**並**寬度**。

> [!IMPORTANT]
> `UIStackView` 設計為非轉譯容器檢視，因此，它不會繪製到類似的其他子畫布`UIView`。 因此這類設定屬性`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

繼續配置應用程式的介面新增標籤、 ImageView、 兩個按鈕和水平堆疊檢視，使它類似下列：

[![](uistackview-images/quick03.png "堆疊檢視 UI 的版面配置")](uistackview-images/quick03.png#lightbox)

設定水平堆疊檢視，使用下列選項：

[![](uistackview-images/quick04.png "設定水平堆疊檢視選項")](uistackview-images/quick04.png#lightbox)

因為我們不要加以延展分級代表每個 「 點 」 的圖示新增時以水平堆疊檢視中，我們將**對齊**要**Center**而**發佈**要**填滿同樣**。

最後，會接到下列**插座**並**動作**:

[![](uistackview-images/quick05.png "堆疊檢視輸出和動作")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>填入 UIStackView，以從程式碼

返回 Visual Studio for Mac 和編輯**ViewController.cs**檔案，並新增下列程式碼：

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

### <a name="testing-the-ui"></a>測試 UI

與所有必要的 UI 項目和程式碼就緒之後，您現在可以執行並測試該介面。 UI 顯示時，所有的垂直堆疊檢視中的項目會等間距從上到下。

當使用者點選**增加分級** 按鈕，另一個 「 star"加入至畫面 （最多為 5）：

[![](uistackview-images/intro01.png "執行範例應用程式")](uistackview-images/intro01.png#lightbox)

將自動置中 「 星星 」，並將其平均分散在水平堆疊檢視中。 當使用者點選**減少分級**"star" 按鈕，會移除 （直到過了無會保留）。

## <a name="stack-view-details"></a>堆疊檢視詳細資料

既然我們已經有什麼的一般概念`UIStackView`控制項以及及其運作方式，讓我們看看一些詳細資料及其功能的更深入的探討。

### <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

如上面的我們所見，當子檢視加入到堆疊檢視其版面配置會完全受到該堆疊檢視，使用自動版面配置和大小類別來定位和調整大小排列的檢視。

堆疊檢視會_釘選_其集合中的第一個和最後一個子檢視**頂端**並**底部**邊緣的垂直堆疊檢視或**保留**並**右**邊緣的水平堆疊檢視。 如果您設定`LayoutMarginsRelativeArrangement`屬性設`true`，然後檢視釘選的子檢視，而非 edge 相關的邊界。

堆疊檢視會使用子檢視的`IntrinsicContentSize`屬性一起定義的子檢視大小在計算時會`Axis`(除了`FillEqually Distribution`)。 `FillEqually Distribution`調整大小的所有子檢視，使它們是相同的大小，因此填入 堆疊檢視，沿著`Axis`。

除了`Fill Alignment`，堆疊檢視會使用子檢視的`IntrinsicContentSize`屬性來計算垂直檢視的大小指定`Axis`。 針對`Fill Alignment`，所有的子檢視會調整大小，讓它們填滿垂直堆疊檢視給定`Axis`。

### <a name="positioning-and-sizing-the-stack-view"></a>定位和調整大小堆疊檢視

堆疊檢視具有完整控制權的任何子檢視版面配置時 (例如根據屬性`Axis`並`Distribution`)，您仍然需要放置堆疊檢視 (`UIStackView`) 及其使用自動版面配置和大小類別的父檢視內。

一般而言，這表示釘選的堆疊檢視來進行擴大和縮減，進而定義其位置的至少兩個邊緣。 而不需要任何額外的條件約束，堆疊檢視會自動調整大小以符合所有其子，如下所示：

 - 大小，沿著其`Axis`會是所有的子檢視大小加上任何已定義每一個子檢視之間的空間的總和。
 - 如果`LayoutMarginsRelativeArrangement`屬性是`true`，堆疊檢視大小也會包含邊界的空間。
 - 垂直大小`Axis`會設定為集合中最大的子檢視。

此外，您可以指定條件約束，堆疊檢視**高度**並**寬度**。 在此情況下，將子檢視會以配置 （大小） 以填滿由 堆疊檢視所指定的空間`Distribution`和`Alignment`屬性。

如果`BaselineRelativeArrangement`屬性是`true`，將子檢視會以配置根據第一個或最後一個子檢視的基準，而不是使用**頂端**，**底部**或**Center**-  **Y**位置。 這些計算堆疊檢視的內容，如下所示：

 - 垂直的堆疊檢視就會傳回第一個子檢視，第一個基準，最後的最後一個。 如果任一這些子檢視本身都是堆疊檢視，則會使用其第一個或最後一個基準。
 - 水平堆疊檢視會使用其最高的子檢視的第一個和最後一個基準。 如果 [高] 檢視也堆疊檢視，它會使用它的最高子檢視作為基準。

> [!IMPORTANT]
> 基準線對齊不適用於自動縮放或壓縮子檢視的大小，基準會計算錯誤的位置。 基準線對齊，請確定子檢視的**高度**符合內建內容檢視**高度**。

### <a name="common-stack-view-uses"></a>常見的堆疊檢視用法

有數個適用於堆疊檢視控制項的版面配置類型。 根據 Apple，以下是幾個較常見的用法：

- **定義大小沿著座標軸**– 釘選沿著堆疊檢視的兩邊`Axis`以及其中一個設定的位置，堆疊檢視將會成長以符合其子檢視所定義的空間軸相鄰的邊緣。
 -  **定義子檢視的位置**– 釘選到相鄰的邊緣，它的父檢視的堆疊檢視，堆疊檢視將會成長以符合其包含子檢視這兩個維度中。
- **定義的大小和位置堆疊的**– 釘選的父檢視的 [堆疊] 檢視的所有四個邊，堆疊檢視排列堆疊檢視中定義的空間將子檢視。
 -  **定義大小與垂直軸**– 釘選到 堆疊檢視中的兩個邊緣與`Axis`以及其中一個設定的位置，堆疊檢視將會成長以符合所定義的空間軸與垂直軸邊緣其子檢視。

### <a name="managing-the-appearance"></a>管理外觀

`UIStackView`旨在做為非轉譯容器檢視，因此，它不會繪製到類似的其他子畫布`UIView`。 設定屬性，例如`BackgroundColor`或覆寫`DrawRect`會有任何視覺效果。

有數個屬性可控制如何堆疊檢視會排列它的子檢視集合：

- **座標軸**– 決定是否堆疊檢視可可以排列子檢視**水平**或是**垂直**。
- **對齊**– 控制項將子檢視堆疊檢視中的對齊方式。
- **發佈**– 控制如何將子檢視的大小調整堆疊檢視中。
- **間距**– 控制在堆疊檢視中的每一個子檢視之間最小的空間。
- **基準線的相對**– 如果`true`，每一個子檢視的垂直間距會衍生自它的基準。
- **版面配置邊界相對**– 相對於標準版面配置邊界會將子檢視。

通常您會使用堆疊檢視，來排列一小部分的子檢視。 巢狀內彼此的一或多個堆疊檢視也可以建立更複雜的使用者介面 (如同我們在[UIStackView 快速入門](#uistackview-quickstart)上方)。

新增將子檢視 （例如若要控制的高度或寬度） 的其他條件約束，可以進一步微調 Ui 的外觀。 不過，應該小心不必包含衝突的條件約束所引進的堆疊檢視本身。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>維護排列檢視和子檢視一致性

堆疊檢視可確保其`ArrangedSubviews`屬性一律會是子集其`Subviews`屬性使用下列規則：

 - 如果子檢視加入至`ArrangedSubviews`集合，它會自動加入`Subviews`集合 （除非它已經是該集合的一部分）。
 - 如果子檢視中已經移除了`Subviews`（從顯示中移除） 的集合，它也會移除從`ArrangedSubviews`集合。
 - 移除從子檢視`ArrangedSubviews`集合並不會移除從`Subviews`集合。 因此它不會再進行配置堆疊檢視中，由，但仍會顯示在畫面上。

`ArrangedSubviews`集合一律都是子集`Subview`集合，不過個別的子檢視每個集合內的順序是獨立且由下列控制：

 - 將子檢視中的順序`ArrangedSubviews`集合判斷它們在堆疊內的顯示順序。
 - 將子檢視中的順序`Subview`集合判斷由後至前檢視內的疊置順序 （或分層）。

### <a name="dynamically-changing-content"></a>以動態方式變更內容

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

## <a name="summary"></a>總結

這篇文章已涵蓋新`UIStackView`控制 （適用於 iOS 9) 來管理一組的其中一個水平或垂直排列堆疊中的 Xamarin.iOS 應用程式中的子檢視。
它會建立 UI，使用堆疊檢視的簡單範例是從開始，並完成，堆疊檢視其屬性和功能更詳細探討。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [簡介 UIStackView （影片）](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
