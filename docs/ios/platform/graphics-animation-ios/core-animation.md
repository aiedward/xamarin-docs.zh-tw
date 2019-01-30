---
title: 在 Xamarin.iOS 中的核心動畫
description: 這篇文章審視 Core 動畫架構中，顯示如何讓高效能、 在 UIKit，流暢的動畫，以及如何將它直接用於較低層級動畫控制項。
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a40d0911b7dabc900a4c6e50c692e4f091f22be9
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233619"
---
# <a name="core-animation-in-xamarinios"></a>在 Xamarin.iOS 中的核心動畫

_這篇文章審視 Core 動畫架構中，顯示如何讓高效能、 在 UIKit，流暢的動畫，以及如何將它直接用於較低層級動畫控制項。_

包含 iOS [ *Core Animation* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)提供動畫支援您的應用程式中的檢視。
所有的超級 smooth 動畫在 iOS 中捲動資料表等撥動不同檢視之間，以及它們執行，因為它們依賴 Core 動畫在內部執行。

Core 動畫及 Core Graphics 架構如何共同運作，以建立漂亮的 2D 圖形以動畫顯示。 事實上 Core 動畫甚至可以轉換 2D 圖形，在 3D 空間中，建立令人讚嘆、 場景的體驗。 不過，若要建立真正的 3D 圖形，您想要使用的項目，例如 OpenGL ES 或遊戲輪到您的 API，例如 MonoGame，雖然 3D 已超出本文的範圍。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Core 動畫

iOS 會使用的 Core 動畫架構，來建立動畫效果，例如檢視之間轉換、 滑動功能表和捲動效果等等。 有兩種方式可處理動畫：

- [透過 UIKit](#Using_UIKit_Animation)，其中包括檢視為基礎的動畫，以及控制站之間的動畫的轉換。
- [透過 Core Animation](#Using_Core_Animation)，哪些圖層，直接以更精細的控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 動畫

UIKit 提供數個功能，輕鬆地將動畫加入至應用程式。 雖然它在內部使用核心動畫，但是它抽走它，您只能使用檢視和控制器的工作。

本章節將討論 UIKit 動畫功能，包括：

-  控制站之間的轉換
-  檢視之間的轉換
-  檢視屬性的動畫


### <a name="view-controller-transitions"></a>檢視控制器轉換

 `UIViewController` 透過檢視控制器之間轉換時，提供內建支援`PresentViewController`方法。 當使用`PresentViewController`，可以選擇性地使用動畫轉換至第二個控制站。

例如，假設應用程式具有兩個控制器，其中碰觸的第一個控制器中的按鈕呼叫`PresentViewController`来顯示的第二個控制站。 若要控制哪些過場動畫用來顯示的第二個控制站，請設定其[ `ModalTransitionStyle` ](xref:UIKit.UIModalTransitionStyle)屬性，如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在此情況下`PartialCurl`使用動畫，但其他許多選項可供選擇，包括：

-  `CoverVertical` – 投影片上從畫面底部
-  `CrossDissolve` – 舊的檢視淡出，與新的檢視會淡入
-  `FlipHorizontal` -從右至左的水平翻轉。 在 dismissal 轉換翻轉左到右。


若要以動畫顯示轉換，請傳遞`true`做為第二個引數`PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

下列螢幕擷取畫面顯示轉換的外觀`PartialCurl`案例：

 ![](core-animation-images/06-view-transitions.png "此螢幕擷取畫面顯示 PartialCurl 轉換")

### <a name="view-transitions"></a>檢視轉換

除了控制站之間的轉換，UIKit 也支援動畫交換另一個檢視的檢視之間的轉換。

例如，假設您有此控制器會執行`UIImageView`，其中點選映像應該會顯示第二個`UIImageView`。 若要建立映像的動畫轉換至第二個映像檢視的檢視表的超級檢視表很簡單，只要呼叫`UIView.Transition`，將其傳遞`toView`和`fromView`，如下所示：

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 也會採用`duration`參數，可控制動畫執行多久，以及[ `options` ](xref:UIKit.UIViewAnimationOptions)指定動畫等的項目，使用和 easing 函式。 此外，您可以指定動畫完成時所呼叫的完成處理常式。

以下顯示的螢幕擷取畫面之間的映像的動畫的轉換檢視時`TransitionFlipFromTop`用：

 ![](core-animation-images/07-animated-transition.png "此螢幕擷取畫面顯示使用 TransitionFlipFromTop 時在映像檢視之間的動畫的轉換")

### <a name="view-property-animations"></a>檢視屬性的動畫

UIKit 支援建立各種不同屬性的動畫上`UIView`類別供免費使用，包括：

-  Frame
-  繫結
-  置中
-  Alpha
-  資料轉換
-  色彩


這些動畫可以藉由指定中的屬性變更以隱含方式會發生`NSAction`委派傳遞給靜態`UIView.Animate`方法。 例如，下列程式碼建立動畫的中心點`UIImageView`:

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

這會導致映像以動畫顯示來回頂端的畫面中，如下所示：

 ![](core-animation-images/08-animate-center.png "映像以動畫顯示來回螢幕頂端做為輸出")

如同`Transition`方法，`Animate`允許設定，以及加/減速函式的持續時間。 此範例也使用`UIViewAnimationOptions.Autoreverse`選項時，這會導致以動畫顯示的值，回到第一個動畫。 不過，程式碼也會設定`Center`回其初始的值，在完成處理常式。 動畫一段時間的變化屬性值，而實際的模型屬性的值一律是最終的值已設定。 在此範例中，值會是超級檢視表的右側附近的點。 如果沒有設定`Center`初始的點，這是因為動畫的結束位置`Autoreverse`正在設定映像會貼齊回右側動畫完成之後，如下所示：

 ![](core-animation-images/09-animation-complete.png "而不需要設定中央到初始的點，將映像會貼齊回右側動畫完成之後")

## <a name="using-core-animation"></a>使用核心動畫

 `UIView` 動畫允許大量功能，並且應該盡可能使用因為實作簡單。 如先前所述，UIView 動畫會使用核心動畫架構。 不過，某些項目不能透過`UIView`動畫，例如以動畫顯示檢視，無法以動畫顯示的其他屬性或非線性的路徑是否插入而定。 在此情況下，您需要更細微的控制，可以直接也使用核心動畫。

### <a name="layers"></a>圖層

當使用 Core 動畫，動畫會透過*層級*，類型`CALayer`。 沒有層階層，更像是檢視階層，圖層在概念上類似的檢視。 實際上，圖層會將檢視中的，使用 [新增支援使用者互動] 檢視。 您可以透過檢視表的任何檢視的圖層`Layer`屬性。 事實上，在中使用的內容`Draw`方法的`UIView`實際上會建立從圖層。 就內部而言，備份的圖層`UIView`已設為檢視本身，也就是所謂的其委派`Draw`。 因此，當繪製`UIView`，實際上要繪製到其圖層。

圖層的動畫可以是隱含或明確。 隱含的動畫是宣告式。 您只要宣告應該變更的圖層屬性，動畫就會運作。 明確的動畫的另一方面被建立為透過動畫類別加入至圖層上的數字。 明確的動畫可加入控制動畫的建立方式。 下列各節探討更深入的隱含和明確的動畫。

### <a name="implicit-animations"></a>隱含的動畫

以動畫顯示的圖層屬性的一個方式是透過隱含的動畫。 `UIView` 動畫建立隱含的動畫。 不過，您可以建立隱含的動畫，直接針對一個圖層。

例如，下列程式碼會將設定圖層的`Contents`從映像，設定框線寬度和色彩，並將圖層加入檢視的圖層的副層為：

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

若要加入圖層的隱含動畫，只要換行中的屬性變更`CATransaction`。 這可讓您建立不是檢視的動畫，動畫這類的屬性動畫`BorderWidth`和`BorderColor`，如下所示：

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

此程式碼也以動畫顯示的圖層的`Position`，這是從左上方的 superlayer 的座標為單位的圖層的錨點的位置。 錨點圖層是標準化的點圖層的座標系統中。

下圖顯示的位置和錨點：

 ![](core-animation-images/10-postion-anchorpt.png "此圖顯示的位置和錨點")

執行範例時， `Position`，`BorderWidth`和`BorderColor`以動畫顯示，如下列螢幕擷取畫面所示：

 ![](core-animation-images/11-implicit-animation.png "執行範例時，位置、 框線寬度和框線色彩以動畫顯示所示")

### <a name="explicit-animations"></a>明確的動畫

除了隱含的動畫、 Core Animation 包含各種不同的類別繼承自`CAAnimation`，可讓您封裝將會明確新增至圖層的動畫。 這些行為允許更精密的控制，透過動畫，例如修改動畫的起始值、 分組動畫以及指定主要畫面格，以允許非線性的路徑。

下列程式碼示範使用明確的動畫`CAKeyframeAnimation`稍早所示 （隱含的動畫 區段中） 的圖層：

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

此程式碼變更`Position`所建立的路徑，然後用來定義主要畫面格動畫的圖層。 請注意，圖層的`Position`設定的最後一個值為`Position`動畫。 如果沒有這麼做，圖層會突然回到其`Position`system.windows.media.animation.repeatbehavior> 的動畫，因為動畫只能變更展示值而非實際模型值。 藉由設定的最終值的模型值，從動畫，圖層會保留在動畫結束時。

下列螢幕擷取畫面顯示圖層包含映像以動畫顯示到指定的路徑：

 ![](core-animation-images/12-explicit-animation.png "此螢幕擷取畫面顯示層級，包含指定的路徑透過映像建立動畫")
 
## <a name="summary"></a>總結

在本文中我們探討了透過提供的動畫功能*Core Animation*架構。 Core 動畫，同時也提供在 UIKit，動畫的方式，以及如何使用它直接為較低層級動畫控制項顯示，我們檢查。

## <a name="related-links"></a>相關連結

- [Core 動畫範例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
