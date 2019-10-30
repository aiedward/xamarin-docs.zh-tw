---
title: Stack Views in Xamarin.iOS
description: This article covers using the new UIStackView control in a Xamarin.iOS app to manage a set of subviews in either a horizontally or vertically arranged stack.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b4a8507d4d1497964f6b60307622ca3e1dc4cd90
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021799"
---
# <a name="stack-views-in-xamarinios"></a>Stack Views in Xamarin.iOS

_This article covers using the new UIStackView control in a Xamarin.iOS app to manage a set of subviews in either a horizontally or vertically arranged stack._

> [!IMPORTANT]
> Please note that while StackView is supported in the iOS Designer, you may encounter usability bugs when using the Stable channel. Switching the Beta or Alpha channels should alleviate this issue. We have decided to present this walkthrough using Xcode until the fixes required are implemented in the Stable channel.

The Stack View control (`UIStackView`) leverages the power of Auto Layout and Size Classes to manage a stack of subviews, either horizontally or vertically, which dynamically responds to the orientation and screen size of the iOS device.

The layout of all subviews attached to a Stack View are managed by it based on developer defined properties such as axis, distribution, alignment and spacing:

[![](uistackview-images/stacked01.png "Stack View layout diagram")](uistackview-images/stacked01.png#lightbox)

When using a `UIStackView` in a Xamarin.iOS app, the developer can either define the subviews either inside a Storyboard in the iOS Designer, or by adding and removing subviews in C# code.

This document consists of two parts: a quick start to help you implement your first stack view, and then some more technical details about how it works.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView video**

## <a name="uistackview-quickstart"></a>UIStackView Quickstart

As a quick introduction to the `UIStackView` control, we are going to create a simple interface that allow the user to enter a rating from 1 to 5. We'll be using two Stack Views: one to arrange the interface vertically on the device's screen and one to arrange the 1-5 rating icons horizontally across the screen.

### <a name="define-the-ui"></a>Define the UI

Start a new Xamarin.iOS project and edit the **Main.storyboard** file in Xcode's Interface Builder. First, drag a single **Vertical Stack View** on the **View Controller**:

[![](uistackview-images/quick01.png "Drag a single Vertical Stack View on the View Controller")](uistackview-images/quick01.png#lightbox)

In the **Attribute Inspector**, set the following options:

[![](uistackview-images/quick02.png "Set the Stack View options")](uistackview-images/quick02.png#lightbox)

其中：

- **軸**–決定堆疊視圖是以**水準**或**垂直**方式排列子檢視。
- **對齊**–控制子檢視在堆疊視圖中的對齊方式。
- **散發**–控制如何在堆疊視圖內調整子檢視大小。
- **間距**–控制堆疊視圖中每個子視圖之間的最小空間。
- **Baseline Relative** – If checked, the vertical spacing of each subview will be derived from it's baseline.
- **相對於版面配置邊界**：將子檢視相對於標準版面配置邊界。

When working with a Stack View, you can think of the **Alignment** as the **X** and **Y** location of the subview and the **Distribution** as the **Height** and **Width**.

> [!IMPORTANT]
> `UIStackView` is designed as a non-rendering container view and as such, it is not drawn to the canvas like other subclasses of `UIView`. So setting properties such as `BackgroundColor` or overriding `DrawRect` will have no visual effect.

Continue to layout the app's interface by adding a Label, ImageView, two Buttons and a Horizontal Stack View so that it resembles the following:

[![](uistackview-images/quick03.png "Laying out the Stack View UI")](uistackview-images/quick03.png#lightbox)

Configure the Horizontal Stack View with the following options:

[![](uistackview-images/quick04.png "Configure the Horizontal Stack View options")](uistackview-images/quick04.png#lightbox)

Because we don't want the icon that represents each "point" in the rating to be stretched when it's added to the Horizontal Stack View, we've set the **Alignment** to **Center** and the **Distribution** to **Fill Equally**.

Finally, wire up the following **Outlets** and **Actions**:

[![](uistackview-images/quick05.png "The Stack View Outlets and Actions")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Populate a UIStackView from Code

Return to Visual Studio for Mac and edit the **ViewController.cs** file and add the following code:

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

Let's take a look at a few pieces of this code in detail. First, we use an `if` statements to check that there isn't more than five "stars" or less than zero.

To add a new "star" we load its image and set its **Content Mode** to **Scale Aspect Fit**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

This keeps the "star" icon from being distorted when it is added to the Stack View.

Next, we add the new "star" icon to the Stack View's collection of subviews:

```csharp
RatingView.AddArrangedSubview(icon);
```

您會注意到，我們已將 `UIImageView` 新增至 `UIStackView`的 `ArrangedSubviews` 屬性，而不是 `SubView`。 您想要讓堆疊視圖控制其版面配置的任何視圖，都必須加入至 `ArrangedSubviews` 屬性。

若要從堆疊視圖中移除子視圖，請先取得子視圖以移除：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然後，我們需要從 `ArrangedSubviews` 集合和超級視圖中移除它：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只從 `ArrangedSubviews` 集合中移除子視圖，會將它移出堆疊視圖的控制項，但不會將它從畫面中移除。

### <a name="testing-the-ui"></a>測試 UI

當所有必要的 UI 元素和程式碼都備妥之後，您現在可以執行並測試介面。 當 UI 顯示時，垂直堆疊視圖中的所有專案都會從上到下的間距。

當使用者按下 [**增加評**等] 按鈕時，會在畫面中新增另一個「星星」（最多5個）：

[![](uistackview-images/intro01.png "The sample app run")](uistackview-images/intro01.png#lightbox)

「星星」將會自動置中，並平均分散在水準堆疊視圖中。 當使用者按下 [**降低評**等] 按鈕時，就會移除「星星」（直到不剩下任何內容）。

## <a name="stack-view-details"></a>堆疊視圖詳細資料

現在，我們已大致瞭解 `UIStackView` 控制項及其運作方式，接下來讓我們深入瞭解其部分功能和詳細資料。

### <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

如先前所見，當子視圖加入至堆疊視圖時，其配置完全是由該堆疊視圖使用自動版面配置和大小類別來定位和調整排列的視圖大小。

堆疊視圖會將其集合中的第一個和最後一個子視圖_釘_選到垂直堆疊視圖的**上**和**下**邊緣，或水準堆疊視圖的**左邊**和**右邊**邊緣。 如果您將 [`LayoutMarginsRelativeArrangement`] 屬性設定為 [`true`]，則此視圖會將子檢視釘選到相關的邊界，而不是邊緣。

堆疊視圖會在以定義的 `Axis` 計運算元檢視大小時，使用子視圖的 `IntrinsicContentSize` 屬性（`FillEqually Distribution`除外）。 `FillEqually Distribution` 會將所有子檢視的大小調整成相同的大小，因此沿著 `Axis`填滿堆疊視圖。

除了 `Fill Alignment`以外，堆疊視圖會使用子視圖的 `IntrinsicContentSize` 屬性來計算與給定 `Axis`垂直的視圖大小。 針對 `Fill Alignment`，所有子檢視都會調整大小，使其填滿垂直于指定 `Axis`的堆疊視圖。

### <a name="positioning-and-sizing-the-stack-view"></a>定位和調整堆疊視圖的大小

雖然堆疊視圖對於任何子視圖的版面配置（根據 `Axis` 和 `Distribution`等屬性）有完全控制權，但您仍然需要使用自動設定和大小類別，將堆疊視圖（`UIStackView`）放在其父視圖內。

一般來說，這表示至少要釘選堆疊視圖的兩個邊緣，以擴充和合約，進而定義其位置。 如果沒有任何額外的條件約束，則會自動調整堆疊視圖的大小，以符合其所有的子檢視，如下所示：

- 沿著其 `Axis` 的大小會是所有子視圖大小的總和，加上每個子視圖之間已定義的任何空間。
- 如果 `LayoutMarginsRelativeArrangement` 屬性是 `true`，則堆疊 Views 大小也會包含邊界的空間。
- 垂直于 `Axis` 的大小會設定為集合中最大的子視圖。

此外，您可以指定堆疊視圖的**高度**和**寬度**的條件約束。 在此情況下，子檢視會配置（調整大小），以填滿堆疊視圖所指定的空間，如 `Distribution` 和 `Alignment` 屬性所決定。

如果 `BaselineRelativeArrangement` 屬性是 `true`，將會根據第一個或最後一個子視圖的基準來配置子檢視，而不是使用**上**、**下**或**置**中- **Y**位置。 這些是在堆疊視圖的內容上計算，如下所示：

- 垂直堆疊視圖會傳回第一個基準的第一個子視圖，最後一個是最後一個。 如果其中一個子檢視本身是堆疊視圖，則會使用其第一個或最後一個基準。
- 水準堆疊視圖會針對第一個和最後一個基準使用其最高的子視圖。 如果最高的視圖也是堆疊視圖，它會使用其最高的子視圖作為基準。

> [!IMPORTANT]
> 基準對齊不適用於延伸或壓縮的子視圖大小，因為基準會計算到錯誤的位置。 針對 [基準對齊]，請確定子視圖的**高度**符合內建內容視圖的**高度**。

### <a name="common-stack-view-uses"></a>一般堆疊視圖使用

有數種版面配置類型可搭配堆疊視圖控制項運作。 根據 Apple，以下是幾個較常見的用法：

- **定義沿著軸的大小**–藉由將兩個邊緣沿著堆疊視圖的 `Axis`，以及其中一個相鄰邊緣來設定位置，堆疊視圖會沿著軸成長，以符合其子檢視所定義的空間。
- **定義子視圖的位置**–藉由釘選到堆疊視圖的相鄰邊緣與其父視圖，堆疊視圖會同時在這兩個維度中成長，以符合其包含的子檢視。
- **定義堆疊的大小和位置**–藉由將堆疊視圖的全部四個邊緣釘選到父視圖，堆疊視圖會根據堆疊視圖內定義的空間來排列子檢視。
- **定義垂直軸的大小**–藉由將兩個邊緣固定于堆疊視圖的 `Axis`，以及軸上的其中一個邊緣來設定位置，堆疊視圖會以垂直方式成長到軸，以符合其子檢視所定義的空間。

### <a name="managing-the-appearance"></a>管理外觀

`UIStackView` 是設計為非轉譯的容器視圖，因此不會繪製到畫布上，如 `UIView`的其他子類別。 設定屬性（例如 `BackgroundColor` 或覆寫 `DrawRect`）將不會有任何視覺效果。

有數個屬性可以控制堆疊視圖如何排列其子檢視的集合：

- **軸**–決定堆疊視圖是以**水準**或**垂直**方式排列子檢視。
- **對齊**–控制子檢視在堆疊視圖中的對齊方式。
- **散發**–控制如何在堆疊視圖內調整子檢視大小。
- **間距**–控制堆疊視圖中每個子視圖之間的最小空間。
- **基準相對**–如果 `true`，則每個子視圖的垂直間距會衍生自其基準。
- **相對於版面配置邊界**：將子檢視相對於標準版面配置邊界。

通常您會使用堆疊視圖來排列少量的子檢視。 更複雜的使用者介面可以藉由將一或多個堆疊視圖彼此嵌套來建立（就像我們在上面的[UIStackView 快速入門](#uistackview-quickstart)中所做的一樣）。

您可以藉由在子檢視中新增額外的條件約束（例如，控制高度或寬度），進一步微調 Ui 外觀。 不過，請小心不要將衝突的條件約束納入堆疊視圖本身所引進的限制式。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>維護排列的視圖和子視圖的一致性

堆疊視圖會使用下列規則，確保其 `ArrangedSubviews` 屬性一律是其 `Subviews` 屬性的子集：

- 如果子視圖已加入至 `ArrangedSubviews` 集合，則會自動將它加入至 `Subviews` 集合（除非它已經是該集合的一部分）。
- If a subview is removed from the `Subviews` collection (removed from display), it is also removed from the `ArrangedSubviews` collection.
- Removing a subview from the `ArrangedSubviews` collection does not remove it from the `Subviews` collection. So it will no longer be laid out by the Stack View, but will still be visible on screen.

The `ArrangedSubviews` collection is always a subset of the `Subview` collection, however the order of the individual subviews within each collection is separate and controlled by the following:

- The order of the subviews within the `ArrangedSubviews` collection determine their display order within the stack.
- The order of the subviews within the `Subview` collection determines their Z-Order (or layering) within the view back to front.

### <a name="dynamically-changing-content"></a>Dynamically Changing Content

A Stack View will automatically adjust the layout of the subviews whenever a subview is added, removed or hidden. The layout will also be adjusted if any property of the Stack View is adjusted (such as its `Axis`).

Layout changes can be animated by placing them within an Animation Block, for example:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Many of the Stack View's properties can be specified using Size Classes within a Storyboard. These properties will be automatically animated is response to size or orientation changes.

## <a name="summary"></a>總結

This article has covered the new `UIStackView` control (for iOS 9) to manage a set of subviews in either a horizontally or vertically arranged stack in a Xamarin.iOS app.
It began with a simple example of using Stack Views to create a UI, and finished with a more detailed look at Stack Views and their properties and features.

## <a name="related-links"></a>相關連結

- [iOS 9 Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 for Developers](https://developer.apple.com/ios/pre-release/)
- [What's New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView Reference](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introducing UIStackView (video)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
