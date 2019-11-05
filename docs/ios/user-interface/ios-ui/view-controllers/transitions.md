---
title: 在 Xamarin 中查看控制器轉換
description: 本檔說明如何在 Xamarin iOS 應用程式中自訂視圖控制器之間的動畫轉換。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: f0886ac9d47e7ab08a6f74365bcc25163b803e11
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002398"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin 中查看控制器轉換

UIKit 加入了自訂在呈現視圖控制器時所發生動畫轉換的支援。 這項支援包含在內建控制器中，以及直接繼承 `UIViewController`的任何自訂控制器。 此外，`UICollectionViewController` 會利用控制器轉換自訂，以在集合視圖配置中運用動畫轉換。

## <a name="custom-transitions"></a>自訂轉換

IOS 7 中的視圖控制器之間的動畫轉換可完全自訂。 `UIViewController` 現在包含 `TransitioningDelegate` 屬性，可在轉換發生時，提供自訂的 animator 類別給系統。

若要使用具有 `PresentViewController`的自訂轉換：

1. 將 `ModalPresentationStyle` 設定為要顯示的控制器上 `UIModalPresentationStyle.Custom`。
2. 執行 `UIViewControllerTransitioningDelegate` 來建立 animator 類別，這是 `UIViewControllerAnimatedTransitioning` 的實例。
3. 將 [`TransitioningDelegate`] 屬性設定為 `UIViewControllerTransitioningDelegate` 的實例，也就是要呈現的控制器上。
4. 呈現視圖控制器。

例如，下列程式碼會提供類型為 `ControllerTwo`-a `UIViewController` 子類別的視圖控制器：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

執行應用程式並點擊按鈕，會使第二個控制器的預設動畫從底部以動畫顯示，如下所示：

 ![](transitions-images/no-custom-transition.png "Running the app and tapping the button causes the default animation of the second controllers view to animate in from the bottom")

不過，設定 `ModalPresentationStyle` 和 `TransitioningDelegate` 會導致轉換的自訂動畫：

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

在下列範例中，`TransitioningDelegate` 會負責建立 `UIViewControllerAnimatedTransitioning` 子類別的實例，稱為 `CustomAnimator`：

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

轉換發生時，系統會建立 `IUIViewControllerContextTransitioning`的實例，並將它傳遞至 animator 的方法。 `IUIViewControllerContextTransitioning` 包含發生動畫的 `ContainerView`，以及起始轉換的視圖控制器以及要轉換成的視圖控制器。

`UIViewControllerAnimatedTransitioning` 類別會處理實際的動畫。 必須實作為兩種方法：

1. `TransitionDuration` –傳回動畫的持續時間（以秒為單位）。
1. `AnimateTransition` –執行實際的動畫。

例如，下列類別會執行以動畫顯示控制器視圖框架的 `UIViewControllerAnimatedTransitioning`：

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

現在，當您按下按鈕時，會使用在 `UIViewControllerAnimatedTransitioning` 類別中實作為動畫：

 ![](transitions-images/custom-transition.png "An example of the zoom in effect running")

## <a name="collection-view-transitions"></a>集合視圖轉換

集合視圖具有建立動畫轉換的內建支援：

- **導覽控制器**–兩個 `UICollectionViewController` 實例之間的動畫轉換可以在 `UINavigationController` 管理時，選擇性地自動處理。
- **轉換**配置–新的 `UICollectionViewTransitionLayout` 類別允許在版面配置之間進行互動式轉換。

### <a name="navigation-controller-transitions"></a>流覽控制器轉換

在流覽控制器中使用時，`UICollectionViewController` 包含在控制器之間進行動畫轉換的支援。 這項支援是內建的功能，而且只需要幾個簡單的步驟就能執行：

1. 將 `UICollectionViewController` 上的 `UseLayoutToLayoutNavigationTransitions` 設定為 `false`。
1. 將 `UICollectionViewController` 的實例新增至流覽控制器堆疊的根目錄。
1. 建立第二個 `UICollectionViewController`，並將其 `UseLayoutToLayoutNavigtionTransitions` 屬性設定為 [`true`]。
1. 將第二個 `UICollectionViewController` 推送至流覽控制器的堆疊。

下列程式碼會將名為 `ImagesCollectionViewController` 的 `UICollectionViewController` 子類別加入至流覽控制器的堆疊的根目錄，並將 `UseLayoutToLayoutNavigationTransitions` 屬性設定為 `false`：

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

選取專案時，會建立 `ImagesController` 的第二個實例，只有這次使用不同的版面配置類別。 針對此控制器，`UseLayoutToLayoutNavigtionTransitions` 設定為 `true`，如下所示：

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

您必須先設定 `UseLayoutToLayoutNavigationTransitions` 屬性，才能將控制器新增至導覽堆疊。 設定此屬性時，會以兩個控制器配置之間的動畫轉換來取代一般水準滑動轉換，如下所示：

![](transitions-images/nav2.png "An animated transition between the layouts of the two controllers")

### <a name="transition-layout"></a>轉換版面配置

除了導覽控制器內的版面配置轉換支援外，現在也提供稱為 `UICollectionViewTransitionLayout` 的新版面配置。 這個版面配置類別允許從程式碼設定 `TransitionProgress`，在版面配置轉換過程中進行互動式控制。 `UICollectionViewTransitionLayout` 不同于-，而不是從 iOS 6 取代 `SetCollectionViewLayout` 方法，這會導致產生動畫版面配置轉換。 該方法並未提供內建支援，以控制動畫轉換的進度。

 例如，`UICollectionViewTransitionLayout` 可讓手勢辨識器進行設定，藉由管理原始配置以及要轉換的目標版面配置，以控制要回應使用者互動的版面配置之間的轉換。

使用 `UICollectionViewTransitionLayout` 在手勢辨識器內執行互動式轉換的步驟如下所示：

1. 建立手勢辨識器。
1. 呼叫 `UICollectionView` 的 `StartInteractiveTransition` 方法，將目標配置和完成處理常式傳遞給它。
1. 設定從 `StartInteractiveTransition` 方法傳回的 `UICollectionViewTransitionLayout` 實例的 `TransitionProgress` 屬性。
1. 使版面配置失效。
1. 呼叫 `UICollectionView` 的 `FinishInteractiveTransition` 方法，以完成轉換或 `CancelInteractiveTransition` 方法將其取消。  `FinishInteractiveTransition` 會導致動畫完成其轉換至目標版面配置，而 `CancelInteractiveTransition` 會導致動畫回到原始的版面配置。
1. 在 `StartInteractiveTransition` 方法的完成處理常式中處理轉換完成。
1. 將手勢辨識器新增至集合視圖。

下列程式碼會在縮小手勢辨識器內實行互動式版面配置轉換：

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

當使用者 pinches [集合] 視圖時，會將 `TransitionProgress` 設定為相對於縮小的尺規。 在此實行中，如果使用者在轉換50% 完成之前結束縮小，轉換就會取消。 否則，轉換就會完成。

## <a name="related-links"></a>相關連結

- [IOS 7 簡介（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
