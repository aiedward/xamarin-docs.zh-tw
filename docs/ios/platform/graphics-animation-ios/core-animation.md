---
title: "核心動畫"
description: "這篇文章會檢查核心動畫 framework 中，顯示如何啟用高效能、 流暢動畫，以及如何 UIKit，較低層級動畫控制項的直接使用它。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f0cb4e00abffead854c2590bde6df45c200ff0bb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="core-animation"></a>核心動畫

_這篇文章會檢查核心動畫 framework 中，顯示如何啟用高效能、 流暢動畫，以及如何 UIKit，較低層級動畫控制項的直接使用它。_

iOS 包含[*核心動畫*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)提供動畫支援您的應用程式中的檢視。
所有超級平滑捲動資料表等不同的檢視之間撥動 iOS 中動畫的以及它們進行，因為它們依賴核心動畫內部執行。

核心動畫和核心圖形架構可一起運作來建立美觀、 展示動畫 2D 圖形。 事實上核心動畫甚至可以轉換 2D 圖形 3D 空間中，建立令人讚嘆的影片體驗。 不過，若要建立，則為 true 的 3D 圖形，您必須使用的項目，例如 OpenGL ES 或遊戲回合 MonoGame，例如應用程式開發介面雖然 3D 超出本文的範圍。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>核心動畫

iOS 會使用核心動畫架構建立動畫效果，例如檢視表之間轉換、 滑動功能表和捲動等等的效果。 有兩種方式可處理動畫：

- [透過 UIKit](#Using_UIKit_Animation)，其中包含檢視為基礎的動畫，以及動畫控制站之間的轉換。
- [透過核心動畫](#Using_Core_Animation)，哪些圖層，以進行更精細的控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 動畫

UIKit 提供數個功能，讓您輕鬆將動畫加入至應用程式。 雖然它會在內部使用核心動畫，但是它抽象化它使得您只能檢視和控制器。

本章節將討論 UIKit 動畫功能，包括：

-  控制站之間的轉換
-  檢視之間的轉換
-  檢視屬性動畫


### <a name="view-controller-transitions"></a>檢視控制器轉換

 `UIViewController` 透過檢視控制器之間轉換時，提供內建支援`PresentViewController`方法。 當使用`PresentViewController`，轉換至第二個控制站可以選擇性地動畫顯示。

例如，假設應用程式具有兩個控制器，其中碰觸中第一個控制器按鈕呼叫`PresentViewController`顯示第二個控制站。 若要控制顯示第二個控制器使用何種轉換動畫，直接將其[ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/)屬性如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在此情況下`PartialCurl`使用動畫，但其他可用，包括：

-  `CoverVertical` – 投影片上從畫面底部
-  `CrossDissolve` – 舊的檢視淡出 （& s) 的新檢視淡入
-  `FlipHorizontal` 的由右至左水平翻轉。 Dismissal 上轉換翻轉左到右。


若要以動畫顯示轉換，將傳遞`true`做為第二個引數`PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

下列螢幕擷取畫面顯示轉換外觀`PartialCurl`案例：

 ![](core-animation-images/06-view-transitions.png "這個螢幕擷取畫面顯示 PartialCurl 轉換")

### <a name="view-transitions"></a>檢視轉換

除了控制站之間的轉換，UIKit 也支援動畫來交換另一個檢視的檢視之間的轉換。

例如，假設您有此控制器`UIImageView`點選映像上的顯示每秒的位置、 `UIImageView`。 要繪製影像的檢視表的超級檢視表轉換至第二個影像檢視很簡單，呼叫`UIView.Transition`，將其傳遞`toView`和`fromView`如下所示：

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 也會採用`duration`參數，可控制多少時間執行的動畫，以及[ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/)來指定像是動畫的 easing 函式來使用。 此外，您可以指定將在動畫完成時呼叫的完成處理常式。

螢幕擷取畫面顯示下列影像動畫的切換檢視時`TransitionFlipFromTop`用：

 ![](core-animation-images/07-animated-transition.png "這個螢幕擷取畫面顯示動畫的 TransitionFlipFromTop 使用時，在影像檢視之間轉換")

### <a name="view-property-animations"></a>檢視屬性動畫

UIKit 支援建立各種不同屬性的動畫上`UIView`免費，類別包括：

-  Frame
-  界限
-  置中
-  Alpha
-  Transform
-  色彩


這些動畫會隱含地發生藉由指定中的屬性變更`NSAction`委派傳遞給靜態`UIView.Animate`方法。 例如，下列程式碼繪製的中心點`UIImageView`:

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

這會導致映像建立動畫來回橫跨頂端的畫面中，如下所示：

 ![](core-animation-images/08-animate-center.png "映像建立動畫來回螢幕的頂端做為輸出")

如同`Transition`方法，`Animate`允許設定，以及 easing 函式的持續時間。 此範例也使用`UIViewAnimationOptions.Autoreverse`選項，使以動畫方式顯示的值回第一個動畫。 不過，此程式碼也設定`Center`回其初始值完成處理常式中。 經過一段時間，動畫會插入屬性的值，而屬性的實際模型值一律是已設定的最終值。 在此範例中，值會是超級檢視表靠近右邊的點。 如果沒有設定`Center`初始的點，這是因為在動畫完成的其中`Autoreverse`設定，影像會貼齊回右側動畫完成之後、 如下所示：

 ![](core-animation-images/09-animation-complete.png "如果沒有設定初始點的中央，影像會貼齊回右側，即在動畫完成之後")

## <a name="using-core-animation"></a>使用核心動畫

 `UIView` 動畫允許許多功能，並且盡可能由於簡化實作中使用。 如先前所述，UIView 動畫會使用核心動畫架構。 不過，某些作業不能透過`UIView`動畫，例如建立其他屬性無法檢視時，請繪製動畫，或插入非線性的路徑。 在這種情況下，您需要進行更細微的控制，可以直接也使用核心動畫。

### <a name="layers"></a>圖層

當使用核心動畫，動畫會透過*層*，類型`CALayer`。 圖層與概念上類似的檢視，有是圖層的階層中，更像沒有檢視階層架構。 實際上，圖層將檢視中，使用 [新增使用者互動的支援] 檢視。 您可以存取透過檢視表的任何檢視的圖層`Layer`屬性。 事實上，在中使用的內容`Draw`方法`UIView`實際上已建立從圖層。 就內部而言，備份的圖層`UIView`已設定為檢視本身，也就是呼叫的項目其委派`Draw`。 因此，當繪製`UIView`，您實際上繪製到其圖層。

圖層動畫可以隱含或明確。 隱含的動畫是宣告式。 您只宣告應該變更的圖層屬性和動畫就會運作。 明確的動畫另一方面建立透過動畫類別加入至圖層。 明確的動畫允許加法控制動畫的建立方式。 下列各節深入更深入的隱含和明確的動畫。

### <a name="implicit-animations"></a>隱含的動畫

圖層屬性顯示的一個方式是透過隱含的動畫。 `UIView` 動畫建立隱含的動畫。 不過，您可以建立隱含的動畫，直接針對的圖層。

例如，下列程式碼會設定圖層的`Contents`從映像，設定框線寬度和色彩，並將圖層加入做為檢視的圖層的副層：

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

若要加入圖層的隱含動畫，只要換行中的屬性變更`CATransaction`。 這可讓您建立屬性不是展示動畫以檢視動畫，例如動畫`BorderWidth`和`BorderColor`如下所示：

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

此程式碼也會繪製的圖層`Position`，這是從左上角 superlayer 座標為單位的圖層的錨點的位置。 錨點圖層是圖層的座標系統內的正規化時間點。

下圖顯示的位置和錨點：

 ![](core-animation-images/10-postion-anchorpt.png "此圖將顯示的位置和錨點")

執行範例時， `Position`，`BorderWidth`和`BorderColor`動畫中的下列螢幕擷取畫面所示：

 ![](core-animation-images/11-implicit-animation.png "執行範例時，位置、 框線寬度和框線色彩動畫所示")

### <a name="explicit-animations"></a>明確的動畫

除了隱含的動畫，核心動畫包含各種類別繼承自`CAAnimation`，可讓您將接著會明確地加入至圖層的動畫封裝。 這些行為允許動畫，例如修改動畫的起始值，分組動畫，指定允許非線性路徑的主要畫面格進行更精細控制權。

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

此程式碼變更`Position`的圖層所建立然後用來定義主要畫面格動畫的路徑。 請注意，圖層的`Position`設定的最後一個值為`Position`動畫。 若未這麼做，圖層會突然回到其`Position`動畫之前因為動畫只變更簡報而非實際模型值。 動畫的最後一個值來設定模型值，圖層會保留在動畫的結束。

下列螢幕擷取畫面顯示圖層包含映像建立動畫透過指定的路徑：

 ![](core-animation-images/12-explicit-animation.png "這個螢幕擷取畫面顯示含有指定的路徑透過映像建立動畫的圖層")
 
## <a name="summary"></a>總結

本文章中我們探討了透過所提供的動畫功能*核心動畫*架構。 我們會檢查核心動畫，其中顯示如何開啟電源 UIKit 中的動畫，以及如何直接針對較低層級動畫控制項使用。

## <a name="related-links"></a>相關連結

- [核心動畫範例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
