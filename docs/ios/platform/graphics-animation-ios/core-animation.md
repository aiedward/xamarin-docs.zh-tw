---
title: Xamarin 中的核心動畫
description: 本文將探討核心動畫架構，其中顯示如何在 UIKit 中啟用高效能、流暢的動畫，以及如何直接將其用於較低層級的動畫控制項。
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 60bab56440fc7227e14d31875a8b6108cd1a86f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032486"
---
# <a name="core-animation-in-xamarinios"></a>Xamarin 中的核心動畫

_本文將探討核心動畫架構，其中顯示如何在 UIKit 中啟用高效能、流暢的動畫，以及如何直接將其用於較低層級的動畫控制項。_

iOS 包含[*核心動畫*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)，可為應用程式中的 views 提供動畫支援。
IOS 中所有超平滑的動畫（例如，在不同的視圖之間滾動資料表和輕量）都會執行，因為它們會在內部依賴核心動畫。

核心動畫與核心圖形架構可以共同合作，建立美觀的動畫2D 圖形。 事實上，核心動畫甚至可以轉換3D 空間中的2D 圖形，建立令人驚奇的 cinematic 體驗。 不過，若要建立真正的3D 圖形，您必須使用像是 OpenGL ES 的東西，或讓遊戲轉型為 API （例如 MonoGame），雖然3D 已超出本文的範圍。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Core Animation

iOS 使用核心動畫架構來建立動畫效果，例如在視圖之間轉換、滑動功能表和滾動效果等。 有兩種方式可以使用動畫：

- 透過[UIKit](#Using_UIKit_Animation)，其中包括以視圖為基礎的動畫，以及在控制器之間的動畫轉換。
- 透過[核心動畫](#Using_Core_Animation)，這是直接層級，可讓您進行更精細的控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 動畫

UIKit 提供數個功能，可讓您輕鬆地將動畫新增至應用程式。 雖然它會在內部使用核心動畫，但它會將其抽象化，讓您只使用 views 和控制器。

本節討論 UIKit 動畫功能，包括：

- 控制器之間的轉換
- Views 之間的轉換
- View 屬性動畫

### <a name="view-controller-transitions"></a>檢視控制器轉換

 `UIViewController` 提供內建支援，可透過 `PresentViewController` 方法在視圖控制器之間轉換。 使用 `PresentViewController`時，可以選擇性地將轉換至第二個控制器的動畫。

例如，假設有兩個控制器的應用程式，其中觸及第一個控制器中的按鈕會呼叫 `PresentViewController` 以顯示第二個控制器。 若要控制用來顯示第二個控制器的轉換動畫，只要設定其[`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle)屬性，如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在此情況下，會使用 `PartialCurl` 動畫，雖然有其他幾個可用的，包括：

- `CoverVertical` –從畫面底部滑片
- `CrossDissolve` –舊的視圖會淡出 & 新視圖淡入
- `FlipHorizontal`-水準由右至左翻轉。 在關閉上，轉換會從左至右翻轉。

若要建立轉換的動畫，請將 `true` 傳遞為 `PresentViewController`的第二個引數：

```csharp
PresentViewController (vc2, true, null);
```

下列螢幕擷取畫面顯示 `PartialCurl` 案例中轉換的樣子：

 ![](core-animation-images/06-view-transitions.png "This screenshot shows the PartialCurl transition")

### <a name="view-transitions"></a>視圖轉換

除了控制器之間的轉換之外，UIKit 也支援在 views 之間製作動畫轉換，以交換另一個視圖。

例如，假設您有一個具有 `UIImageView`的控制器，其中的點擊影像應該會顯示第二個 `UIImageView`。 若要以動畫顯示影像視圖的 superview 頂端以轉換至第二個影像視圖，就像呼叫 `UIView.Transition`一樣簡單，將 `toView` 和 `fromView` 傳遞給它，如下所示：

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 也會採用 `duration` 參數來控制動畫的執行時間長度，以及[`options`](xref:UIKit.UIViewAnimationOptions)來指定要使用的動畫和緩動函式等專案。 此外，您可以指定在動畫完成時將呼叫的完成處理常式。

下列螢幕擷取畫面顯示使用 `TransitionFlipFromTop` 時，影像視圖之間的動畫轉換：

 ![](core-animation-images/07-animated-transition.png "This screenshot shows the animated transition between the image views when TransitionFlipFromTop is used")

### <a name="view-property-animations"></a>View 屬性動畫

UIKit 支援在 `UIView` 類別上以自由動畫顯示各種屬性，包括：

- Frame
- 超出
- 置中
- Alpha
- 資料轉換
- 色彩

這些動畫會藉由在傳遞至靜態 `UIView.Animate` 方法的 `NSAction` 委派中指定屬性變更，以隱含的方式發生。 例如，下列程式碼會以動畫繪製 `UIImageView`的中心點：

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

這會導致影像在畫面頂端來回動畫，如下所示：

 ![](core-animation-images/08-animate-center.png "An image animating back and forth across the top of the screen as the output")

如同 `Transition` 方法，`Animate` 允許設定持續時間，以及緩時函數。 這個範例也使用了 [`UIViewAnimationOptions.Autoreverse`] 選項，這會導致動畫以動畫顯示從值回到第一個。 不過，此程式碼也會將 `Center` 設定回完成處理常式中的初始值。 當動畫在一段時間內插入屬性值時，屬性的實際模型值一律是已設定的最終值。 在此範例中，值是接近 superview 頂端右邊的點。 若不將 `Center` 設定為初始點（這是因為設定 `Autoreverse` 而完成動畫的位置），影像會在動畫完成後貼回右側，如下所示：

 ![](core-animation-images/09-animation-complete.png "Without setting the Center to the initial point, the image would snap back to the right side after the animation completes")

## <a name="using-core-animation"></a>使用核心動畫

 `UIView` 動畫可提供許多功能，而且應該在可能的情況下使用，因為這是容易執行的工作。 如先前所述，UIView 動畫會使用核心動畫架構。 不過，您無法使用 `UIView` 動畫來完成某些動作，例如建立無法以視圖動畫顯示的其他屬性動畫，或是沿著非線性路徑進行插上。 在您需要更精細控制的情況下，也可以直接使用核心動畫。

### <a name="layers"></a>分層

使用核心動畫時，動畫會透過 `CALayer`類型的*層*級進行。 圖層在概念上類似于「圖層」（layer）階層，與「視圖」階層很類似。 實際上，圖層會並排顯示，而此視圖會新增使用者互動的支援。 您可以透過視圖的 [`Layer`] 屬性來存取任何視圖的圖層。 事實上，`UIView` 的 `Draw` 方法中使用的內容實際上是從圖層建立。 就內部而言，支援 `UIView` 的層級會將其委派設定為 view 本身，這就是呼叫 `Draw`。 因此，當繪製到 `UIView`時，您實際上是繪製到其圖層。

圖層動畫可以是隱含或明確的。 隱含動畫是宣告式。 您只需要宣告應該變更的圖層屬性，動畫就能正常運作。 另一方面，明確的動畫是透過新增至圖層的動畫類別來建立。 明確的動畫可讓您對動畫的建立方式進行額外的控制。 下列各節將深入探討隱含和明確的動畫。

### <a name="implicit-animations"></a>隱含動畫

以動畫顯示圖層屬性的其中一種方式是透過隱含動畫。 `UIView` 動畫會建立隱含的動畫。 不過，您也可以直接針對圖層建立隱含動畫。

例如，下列程式碼會從影像設定圖層的 `Contents`、設定框線寬度和色彩，並將圖層新增為視圖圖層的子層級：

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

若要加入圖層的隱含動畫，請直接將屬性變更包裝在 `CATransaction`中。 這可讓您將不會使用 view 動畫 animatable 的屬性進行動畫處理，例如 `BorderWidth` 和 `BorderColor`，如下所示：

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

這段程式碼也會以動畫呈現圖層的 `Position`，也就是圖層錨點的位置，從 superlayer 座標的左上方測量。 圖層的錨點是圖層座標系統內的正規化點。

下圖顯示位置和錨點：

 ![](core-animation-images/10-postion-anchorpt.png "This figure shows the position and anchor point")

執行此範例時，`Position`、`BorderWidth` 和 `BorderColor` 動畫，如下列螢幕擷取畫面所示：

 ![](core-animation-images/11-implicit-animation.png "When the example is run, the Position, BorderWidth and BorderColor animate as shown")

### <a name="explicit-animations"></a>明確動畫

除了隱含動畫以外，Core 動畫還包含各種繼承自 `CAAnimation` 的類別，可讓您封裝動畫，然後明確新增至圖層。 這些可讓您更精細地控制動畫，例如修改動畫的開始值、將動畫分組，以及指定主要畫面格允許非線性路徑。

下列程式碼會使用稍早所示圖層的 `CAKeyframeAnimation`，顯示明確動畫的範例（在 [隱含動畫] 區段中）：

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

這段程式碼會藉由建立用來定義主要畫面格動畫的路徑，來變更圖層的 `Position`。 請注意，圖層的 `Position` 會設定為動畫中 `Position` 的最終值。 如果沒有這麼做，圖層會突然在動畫前返回其 `Position`，因為動畫只會變更呈現值，而不是實際的模型值。 藉由將模型值設定為動畫中的最後一個值，圖層就會留在動畫的結尾。

下列螢幕擷取畫面顯示包含影像的圖層，並透過指定的路徑製作動畫：

 ![](core-animation-images/12-explicit-animation.png "This screenshot shows the layer containing the image animating through the specified path")

## <a name="summary"></a>總結

在本文中，我們探討了透過*核心動畫*架構所提供的動畫功能。 我們已檢查核心動畫，同時顯示它如何在 UIKit 中提供動畫，以及如何直接將其用於較低層的動畫控制。

## <a name="related-links"></a>相關連結

- [核心動畫範例](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
