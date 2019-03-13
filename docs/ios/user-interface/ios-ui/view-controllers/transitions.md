---
title: 在 Xamarin.iOS 中的檢視控制器轉換
description: 本文件說明如何自訂動畫在 Xamarin.iOS 應用程式中的檢視控制器之間的轉換。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 6711d3af06619aa54a2d735cb83862ed64abacec
ms.sourcegitcommit: 06f88979db160fb8dd1c9ee0d5000d8749107489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2018
ms.locfileid: "53806895"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin.iOS 中的檢視控制器轉換

UIKit 新增支援自訂動畫呈現檢視控制器時，就會發生的轉換。 這項支援隨附內建的控制站，以及直接繼承自任何自訂控制器`UIViewController`。 此外，`UICollectionViewController`會充分利用集合檢視版面配置中的動畫的轉換的控制站轉換自訂。

## <a name="custom-transitions"></a>自訂轉換

在 iOS 7 中的檢視控制器之間的動畫的轉換可完全自訂。 `UIViewController` 現在包含`TransitioningDelegate`轉換發生時提供給系統的自訂動畫類別的屬性。

若要使用的自訂轉換`PresentViewController`:

1.  設定`ModalPresentationStyle`至`UIModalPresentationStyle.Custom`顯示控制站上。
2.  實作`UIViewControllerTransitioningDelegate`來建立動畫類別，這是執行個體`UIViewControllerAnimatedTransitioning`。
3.  設定`TransitioningDelegate`的執行個體的內容`UIViewControllerTransitioningDelegate`，也會顯示在控制器上。
4.  呈現的檢視控制器。


例如，下列程式碼顯示檢視控制器的型別`ControllerTwo`-`UIViewController`子類別：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

執行應用程式和點選的按鈕會導致預設的動畫來以動畫顯示在底部，從第二個控制器的檢視，如下所示：

 ![](transitions-images/no-custom-transition.png "執行應用程式，並點選的按鈕會導致以動畫顯示在底部的第二個控制器檢視的預設動畫")

不過，設定`ModalPresentationStyle`和`TransitioningDelegate`轉換的自訂動畫會產生：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

`TransitioningDelegate`負責建立的執行個體`UIViewControllerAnimatedTransitioning`子類別-呼叫`CustomAnimator`在下列範例中：

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

進行轉換時，系統會建立的執行個體`IUIViewControllerContextTransitioning`，它傳遞給動畫的方法。 `IUIViewControllerContextTransitioning` 包含`ContainerView`動畫的發生位置，以及起始轉換的檢視控制器和檢視控制器轉換成。

`UIViewControllerAnimatedTransitioning`類別處理實際的動畫。 必須實作兩種方法：

1.  `TransitionDuration` -傳回動畫的持續時間 （秒）。
1.  `AnimateTransition` – 會執行實際的動畫。


例如，下列類別會實作`UIViewControllerAnimatedTransitioning`以動畫顯示控制器的檢視框架：

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

現在，當點選按鈕時，動畫中實作`UIViewControllerAnimatedTransitioning`類別使用：

 ![](transitions-images/custom-transition.png "作用中執行的縮放的範例")

## <a name="collection-view-transitions"></a>集合檢視轉換

集合檢視有內建支援來建立動畫的轉換：

-  **瀏覽控制器**– 以動畫顯示兩個之間的轉換`UICollectionViewController`執行個體可以選擇性地處理自動當`UINavigationController`管理它們。
-  **轉換的版面配置**– 新`UICollectionViewTransitionLayout`類別可讓互動式版面配置之間轉換。


### <a name="navigation-controller-transitions"></a>瀏覽控制器轉換

瀏覽控制器中使用時`UICollectionViewController`包含控制站之間的動畫轉換的支援。 這項支援是內建功能，而且需要只有幾個簡單步驟來實作：

1.  設定`UseLayoutToLayoutNavigationTransitions`要`false`上`UICollectionViewController`。
1.  新增的執行個體`UICollectionViewController`的瀏覽控制器的堆疊根目錄。
1.  建立第二個`UICollectionViewController`並將其`UseLayoutToLayoutNavigtionTransitions`屬性設`true`。
1.  推播第二個`UICollectionViewController`至瀏覽控制器的堆疊。


下列程式碼加入`UICollectionViewController`名為的子類別`ImagesCollectionViewController`瀏覽控制器的堆疊根目錄與`UseLayoutToLayoutNavigationTransitions`屬性設定為`false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

選取項目時，另一個執行個體`ImagesController`建立時，只有這次使用不同的版面配置的類別。 此控制站，如`UseLayoutToLayoutNavigtionTransitions`設為`true`，如下所示：

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };
            
    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

`UseLayoutToLayoutNavigationTransitions`之前將控制器新增至導覽堆疊上，必須設定屬性。 設定這個屬性，一般的水平滑動轉換取代為兩個控制站，在配置之間的動畫轉換如下所示：

![](transitions-images/nav2.png "未配置的兩個控制器之間的動畫的轉換")

### <a name="transition-layout"></a>轉換的版面配置

瀏覽控制器內的版面配置轉換支援，除了呼叫新的版面配置`UICollectionViewTransitionLayout`現已推出。 版面配置，此類別允許互動式控制在版面配置的轉換過程中，藉由使用`TransitionProgress`設定從程式碼。 `UICollectionViewTransitionLayout` 不同於-而不會為取代-`SetCollectionViewLayout`從 iOS 6，導致發生動畫的版面配置轉換的方法。 該方法未提供內建支援，來控制動畫的轉換的進度。

 `UICollectionViewTransitionLayout` 例如，允許設定來控制配置，以回應使用者互動，藉由管理的原始版面配置，以及轉換成預期的版面配置之間轉換的筆勢辨識器。

若要實作中使用筆勢辨識器的互動式轉換步驟`UICollectionViewTransitionLayout`如下所示：

1.  建立筆勢辨識器。
1.  呼叫`StartInteractiveTransition`方法的`UICollectionView`，將它傳遞目標版面配置和完成處理常式。
1.  設定`TransitionProgress`的屬性`UICollectionViewTransitionLayout`從傳回的執行個體`StartInteractiveTransition`方法。
1.  使配置失效。
1.  呼叫`FinishInteractiveTransition`方法`UICollectionView`來完成轉換或`CancelInteractiveTransition`方法來取消它。  `FinishInteractiveTransition` 使動畫完成其轉換為目標的版面配置，而`CancelInteractiveTransition`結果中傳回的原始配置的動畫。
1.  處理轉換完成的完成處理常式中`StartInteractiveTransition`方法。
1.  加入此集合檢視中的筆勢辨識器。


下列程式碼會實作內捏合筆勢辨識器的互動式的版面配置轉換：

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {   
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

當使用者 pinches 集合檢視中，`TransitionProgress`設定相對的捏合縮放比例。 在此實作中，如果使用者已結束縮小的轉換是 50%已完成，就會取消轉換。 否則，轉換已完成。




## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
