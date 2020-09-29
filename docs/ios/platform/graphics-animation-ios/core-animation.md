---
title: Xamarin 中的核心動畫
description: 本文將探討核心動畫架構，示範如何在 UIKit 中啟用高效能、流暢的動畫，以及如何直接用於較低層級的動畫控制項。
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d25d48421ad9b05925c1fa373ddba600ad3bac2e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431226"
---
# <a name="core-animation-in-xamarinios"></a>Xamarin 中的核心動畫

_本文將探討核心動畫架構，示範如何在 UIKit 中啟用高效能、流暢的動畫，以及如何直接用於較低層級的動畫控制項。_

iOS 包含 [*核心動畫*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) ，以提供應用程式中的視圖動畫支援。
IOS 中的所有超平滑動畫（例如，在不同的視圖之間滾動）和輕量都會執行，因為它們會在內部依賴核心動畫。

核心動畫與核心圖形架構可以搭配使用，以建立美觀的動畫2D 圖形。 事實上，核心動畫甚至可以轉換3D 空間中的2D 圖形，創造令人驚奇的 cinematic 體驗。 不過，若要建立真正的3D 圖形，您需要使用像是 OpenGL ES 的專案，或將遊戲轉換成 MonoGame 之類的 API，雖然3D 已超出本文的範圍。

<a name="Using_Core_Animation"></a>

## <a name="core-animation"></a>Core Animation

iOS 使用核心動畫架構來建立動畫效果，例如在視圖之間轉換、滑動功能表和滾動效果等等。 使用動畫的方式有兩種：

- 透過[UIKit](#Using_UIKit_Animation)，包含以視圖為基礎的動畫以及控制器之間的動畫轉換。
- 透過[核心動畫](#Using_Core_Animation)（直接分層），允許更精細的控制。

<a name="Using_UIKit_Animation"></a>

## <a name="using-uikit-animation"></a>使用 UIKit 動畫

UIKit 提供數個功能，可讓您輕鬆地將動畫新增至應用程式。 雖然它會在內部使用核心動畫，但它會將其抽象化，讓您只使用視圖和控制器。

本節討論 UIKit 動畫功能，包括：

- 控制器之間的轉換
- 視圖之間的轉換
- 視圖屬性動畫

### <a name="view-controller-transitions"></a>檢視控制器轉換

 `UIViewController` 提供內建支援，可透過方法在 view controller 之間轉換 `PresentViewController` 。 使用時 `PresentViewController` ，可以選擇性地以動畫的形式轉換至第二個控制器。

例如，假設有一個具有兩個控制器的應用程式，在其中觸及第一個控制器呼叫中的按鈕， `PresentViewController` 以顯示第二個控制器。 若要控制要使用哪一個轉換動畫來顯示第二個控制器，只要設定其 [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) 屬性，如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在此情況下 `PartialCurl` ，會使用動畫，雖然有其他數個可供使用，包括：

- `CoverVertical` –從畫面底部滑下
- `CrossDissolve` –舊的視圖淡出 & 新的視圖淡入
- `FlipHorizontal` -水準由右至左翻轉。 在關閉上，從左至右的轉換反向。

若要以動畫顯示轉換，請以 `true` 第二個引數形式傳遞至 `PresentViewController` ：

```csharp
PresentViewController (vc2, true, null);
```

下列螢幕擷取畫面顯示此案例的轉換看起來像這樣 `PartialCurl` ：

 ![此螢幕擷取畫面顯示 PartialCurl 轉換](core-animation-images/06-view-transitions.png)

### <a name="view-transitions"></a>視圖轉換

除了控制器之間的轉換外，UIKit 也支援以動畫顯示視圖之間的轉換，以交換另一個視圖。

例如，假設您有一個具有的控制器 `UIImageView` ，在此情況下，您應該在其中點擊影像 `UIImageView` 。 若要以動畫顯示影像視圖的超級檢視表，以轉換成第二個影像視圖，就像呼叫一樣簡單 `UIView.Transition` ，並且將它傳遞給， `toView` `fromView` 如下所示：

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 也會使用 `duration` 參數來控制動畫執行的時間長度，以及指定要 [`options`](xref:UIKit.UIViewAnimationOptions) 使用的動畫以及緩和函式等專案。 此外，您可以指定當動畫完成時將呼叫的完成處理常式。

下列螢幕擷取畫面顯示使用時，影像視圖之間的動畫轉換 `TransitionFlipFromTop` ：

 ![此螢幕擷取畫面顯示使用 TransitionFlipFromTop 時，影像視圖之間的動畫轉換](core-animation-images/07-animated-transition.png)

### <a name="view-property-animations"></a>視圖屬性動畫

UIKit 支援在類別上以無動畫的形式建立各種屬性 `UIView` ，包括：

- Frame
- Bounds
- Center
- Alpha
- 轉換
- Color

這些動畫會以隱含方式在 `NSAction` 傳遞給靜態方法的委派中指定屬性變更時發生 `UIView.Animate` 。 例如，下列程式碼會將的中心點動畫 `UIImageView` ：

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

 ![在螢幕頂端以動畫顯示的影像作為輸出](core-animation-images/08-animate-center.png)

與方法相同 `Transition` ，可 `Animate` 讓您設定持續期間以及緩和函數。 這個範例也使用了 `UIViewAnimationOptions.Autoreverse` 選項，這會導致動畫將值的動畫動畫傳回初始的值。 不過，程式碼也會將 `Center` 完成處理常式中的值設回其初始值。 當動畫在一段時間內插上了屬性值時，屬性的實際模型值一律為已設定的最後一個值。 在此範例中，值是接近超級檢視表右邊的點。 若未將設定 `Center` 為初始點（也就是動畫因設定而完成的位置）， `Autoreverse` 則影像會在動畫完成後再貼回右邊，如下所示：

 ![如果未將中心設定為初始點，影像會在動畫完成後再貼回右邊](core-animation-images/09-animation-complete.png)

## <a name="using-core-animation"></a>使用核心動畫

 `UIView` 動畫可提供許多功能，而且應該在可能的情況下使用，因為這是容易執行的程式。 如先前所述，UIView 動畫使用核心動畫架構。 不過，某些專案無法使用動畫完成 `UIView` ，例如建立無法使用視圖動畫動畫的其他屬性，或是沿著非線性路徑插上的動畫。 在需要更細微控制的情況下，您也可以直接使用核心動畫。

### <a name="layers"></a>圖層

使用核心動畫時，動畫會透過類型為的 *圖層*進行 `CALayer` 。 圖層在概念上類似于圖層階層，這就像是一個 view 階層。 實際上，圖層會顯示新增使用者互動支援的視圖。 您可以透過視圖的屬性來存取任何視圖的圖層 `Layer` 。 事實上，在的方法中使用的內容 `Draw` `UIView` 實際上是從圖層建立。 就內部而言，支援的層級 `UIView` 會將其委派設定為 view 本身，也就是所謂的呼叫 `Draw` 。 因此，當繪圖至時 `UIView` ，您實際上是繪製到它的圖層。

圖層動畫可以是隱含或明確。 隱含動畫是宣告式。 您只需宣告應該變更的圖層屬性，動畫就能正常運作。 另一方面，明確的動畫會透過新增至圖層的動畫類別來建立。 明確動畫可讓您進一步控制動畫的建立方式。 下列各節深入探討隱含和明確動畫的深度。

### <a name="implicit-animations"></a>隱含動畫

建立圖層屬性動畫的其中一種方式是透過隱含動畫。 `UIView` 動畫會建立隱含動畫。 不過，您也可以直接對圖層建立隱含動畫。

例如，下列程式碼會設定影像的圖層 `Contents` 、設定框線寬度和色彩，以及將圖層新增為視圖圖層的子層：

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

若要加入圖層的隱含動畫，只要將屬性變更包裝在中即可 `CATransaction` 。 這可讓您將不會使用視圖動畫 animatable 的屬性建立動畫，例如 `BorderWidth` 和， `BorderColor` 如下所示：

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

這段程式碼也會以動畫呈現圖層 `Position` ，也就是圖層錨點的位置，測量自 superlayer 座標的左上角。 圖層的錨點是圖層座標系統內的正規化點。

下圖顯示位置和錨點：

 ![下圖顯示位置和錨點](core-animation-images/10-postion-anchorpt.png)

當範例執行時，和會 `Position` 建立 `BorderWidth` `BorderColor` 動畫，如下列螢幕擷取畫面所示：

 ![執行範例時，位置、BorderWidth 和邊框出動畫顯示如下](core-animation-images/11-implicit-animation.png)

### <a name="explicit-animations"></a>明確動畫

除了隱含動畫之外，核心動畫還包含各種繼承自的類別， `CAAnimation` 可讓您封裝接著明確新增至圖層的動畫。 這些可讓您更精細地控制動畫，例如修改動畫的開始值、將動畫分組，以及指定主要畫面格以允許非線性路徑。

下列程式碼示範使用的圖層的明確動畫範例，如 `CAKeyframeAnimation` 先前在隱含動畫區段中 () ：

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

這段程式碼 `Position` 會藉由建立用來定義主要畫面格動畫的路徑，來變更圖層的。 請注意，圖層 `Position` 會從動畫設定為的最終值 `Position` 。 如果沒有這樣做，圖層會突然回到 `Position` 動畫的前面，因為動畫只會變更展示值，而不會變更實際的模型值。 藉由將模型值設定為動畫的最後一個值，圖層會在動畫的結尾處維持在原處。

下列螢幕擷取畫面顯示包含影像的圖層，並透過指定的路徑進行動畫：

 ![此螢幕擷取畫面顯示包含影像的圖層，並透過指定的路徑進行動畫](core-animation-images/12-explicit-animation.png)

## <a name="summary"></a>摘要

在本文中，我們探討了透過 *核心動畫* 架構所提供的動畫功能。 我們已檢查核心動畫，顯示如何在 UIKit 中使用動畫，以及如何直接針對較低層級的動畫控制項來使用它。

## <a name="related-links"></a>相關連結

- [核心動畫範例](/samples/xamarin/ios-samples/graphicsandanimation)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)