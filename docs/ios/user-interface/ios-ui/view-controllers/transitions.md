---
title: 在 Xamarin 中查看控制器轉換
description: 本檔說明如何在 Xamarin iOS 應用程式中自訂視圖控制器之間的動畫轉換。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: e973221e013132c8172a4b1de0250a085f0ea1df
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436932"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin 中查看控制器轉換

UIKit 新增了自訂呈現視圖控制器時所發生之動畫轉換的支援。 內建控制器以及直接繼承自的任何自訂控制器都包含這項支援 `UIViewController` 。 此外， `UICollectionViewController` 會利用控制器轉換自訂，在集合視圖配置中利用動畫轉換。

## <a name="custom-transitions"></a>自訂轉換

您可以完全自訂 iOS 7 中視圖控制器之間的動畫轉換。 `UIViewController` 現在包含一個 `TransitioningDelegate` 屬性，這個屬性會在轉換發生時，提供自訂的 animator 類別給系統。

若要使用自訂轉換與 `PresentViewController` ：

1. 在要  `ModalPresentationStyle`  `UIModalPresentationStyle.Custom` 顯示的控制器上，將設定為。
2. 執行  `UIViewControllerTransitioningDelegate` 以建立 animator 類別，也就是的實例  `UIViewControllerAnimatedTransitioning` 。
3. 將  `TransitioningDelegate` 屬性（property）設定為的實例  `UIViewControllerTransitioningDelegate` ，也在要呈現的控制器上。
4. 顯示 view 控制器。

例如，下列程式碼會顯示型別 `ControllerTwo` 為子類別的視圖控制器 `UIViewController` ：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

執行應用程式並按下按鈕，會導致第二個控制器的視圖的預設動畫從底部建立動畫，如下所示：

 ![執行應用程式並按下按鈕，會導致第二個控制器視圖的預設動畫從底部建立動畫](transitions-images/no-custom-transition.png)

但是，設定 `ModalPresentationStyle` 和會 `TransitioningDelegate` 產生轉換的自訂動畫：

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

`TransitioningDelegate`負責建立子類別的實例 `UIViewControllerAnimatedTransitioning` - `CustomAnimator` 在下列範例中呼叫：

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

當轉換發生時，系統會建立的實例 `IUIViewControllerContextTransitioning` ，並將它傳遞至 animator 的方法。 `IUIViewControllerContextTransitioning` 包含 `ContainerView` 動畫發生的位置，以及起始轉換的 view 控制器以及正在轉換的視圖控制器。

`UIViewControllerAnimatedTransitioning`類別會處理實際的動畫。 必須實作為兩種方法：

1. `TransitionDuration` –傳回動畫的持續時間（以秒為單位）。
1. `AnimateTransition` –執行實際的動畫。

例如，下列類別可讓您 `UIViewControllerAnimatedTransitioning` 以動畫顯示控制器視圖的框架：

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

現在，當您按下按鈕時，就會使用在類別中實作為的動畫 `UIViewControllerAnimatedTransitioning` ：

 ![放大效果正在執行的範例](transitions-images/custom-transition.png)

## <a name="collection-view-transitions"></a>集合視圖轉換

集合視圖具有建立動畫轉換的內建支援：

- **流覽控制器** –兩個實例之間的動畫轉換可以選擇性地在進行  `UICollectionViewController` 管理時自動處理  `UINavigationController` 。
- **轉換** 配置-新  `UICollectionViewTransitionLayout` 類別可讓您在版面配置之間進行互動式轉換。

### <a name="navigation-controller-transitions"></a>流覽控制器轉換

在流覽控制器中使用時，會 `UICollectionViewController` 包含控制器之間動畫轉換的支援。 這項支援是內建的，而且只需要幾個簡單的步驟就能執行：

1. `UseLayoutToLayoutNavigationTransitions` `false` 在上設定為 `UICollectionViewController` 。
1. 將的實例加入  `UICollectionViewController` 至流覽控制器的堆疊根目錄。
1. 建立第二個  `UICollectionViewController` ，並將其  `UseLayoutToLayoutNavigtionTransitions` 屬性設定為  `true` 。
1. 將第二個推送  `UICollectionViewController` 至流覽控制器的堆疊。

下列程式碼會將名為的子 `UICollectionViewController` 類別新增 `ImagesCollectionViewController` 至流覽控制器之堆疊的根目錄，並將 `UseLayoutToLayoutNavigationTransitions` 屬性設定為 `false` ：

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

當選取專案時，就會建立的第二個實例 `ImagesController` ，但只有這次使用不同的版面配置類別。 在此控制器中， `UseLayoutToLayoutNavigtionTransitions` 會設定為 `true` ，如下所示：

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

`UseLayoutToLayoutNavigationTransitions`屬性必須先設定，才能將控制器加入至導覽堆疊。 使用這個屬性集時，會使用兩個控制器的版面配置之間的動畫轉換來取代一般水準滑動轉換，如下所示：

![兩個控制器的版面配置之間的動畫轉換](transitions-images/nav2.png)

### <a name="transition-layout"></a>轉換版面配置

除了流覽控制器內的版面配置轉換支援外，現在也提供名為的新版面配置 `UICollectionViewTransitionLayout` 。 這個版面配置類別可讓您透過程式碼設定，在版面配置轉換過程中進行互動式控制 `TransitionProgress` 。 `UICollectionViewTransitionLayout` 與不同的是，而不是 `SetCollectionViewLayout` 從 iOS 6 取代會造成動畫版面配置轉換的方法。 該方法未提供內建的支援，以控制動畫轉換的進度。

 `UICollectionViewTransitionLayout` 例如，您可以藉由管理原始配置以及要轉換的預定版面配置，來設定手勢辨識器以控制配置之間的轉換，以回應使用者互動。

使用在筆勢辨識器內執行互動式轉換的步驟如下 `UICollectionViewTransitionLayout` ：

1. 建立手勢辨識器。
1. 呼叫的  `StartInteractiveTransition` 方法  `UICollectionView` ，並將目標配置和完成處理常式傳遞給它。
1. 設定 `TransitionProgress`  `UICollectionViewTransitionLayout` 從方法傳回之實例的屬性  `StartInteractiveTransition` 。
1. 使版面配置失效。
1. 呼叫的 `FinishInteractiveTransition` 方法  `UICollectionView` ，以完成轉換或  `CancelInteractiveTransition` 取消它的方法。  `FinishInteractiveTransition` 讓動畫完成其轉換至目標配置，而 `CancelInteractiveTransition` 將動畫轉換成原始版面配置。
1. 在方法的完成處理常式中處理轉換完成  `StartInteractiveTransition` 。
1. 將手勢辨識器新增至集合視圖。

下列程式碼會在縮小的手勢辨識器內實行互動式版面配置轉換：

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

當使用者 pinches 收集視圖 `TransitionProgress` 時，會設定相對於縮小規模的相對比例。 在這種情況下，如果使用者在轉換50% 完成之前結束了縮小，轉換就會取消。 否則，轉換會完成。

## <a name="related-links"></a>相關連結

- [IOS 7 (範例) 簡介 ](/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景處理](~/ios/app-fundamentals/backgrounding/index.md)