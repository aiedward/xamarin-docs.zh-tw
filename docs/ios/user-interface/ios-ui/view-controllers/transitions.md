---
title: 在 Xamarin 中查看控制器轉換
description: 本檔說明如何在 Xamarin iOS 應用程式中自訂視圖控制器之間的動畫轉換。
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: bbba1cbb907e3744677ff6bbe39b97fb62616e33
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768160"
---
# <a name="view-controller-transitions-in-xamarinios"></a>在 Xamarin 中查看控制器轉換

UIKit 加入了自訂在呈現視圖控制器時所發生動畫轉換的支援。 這項支援包含在內建控制器中，以及直接從`UIViewController`繼承的任何自訂控制器。 此外， `UICollectionViewController`會利用控制器轉換自訂，以在集合視圖配置中運用動畫轉換。

## <a name="custom-transitions"></a>自訂轉換

IOS 7 中的視圖控制器之間的動畫轉換可完全自訂。 `UIViewController`現在包含一個`TransitioningDelegate`屬性，可在轉換發生時，提供自訂的 animator 類別給系統。

若要搭配使用自訂`PresentViewController`轉換：

1. 在要呈現`UIModalPresentationStyle.Custom`的控制器上，將設為。`ModalPresentationStyle`
2. 執行`UIViewControllerTransitioningDelegate`以建立 animator 類別，這是的`UIViewControllerAnimatedTransitioning`實例。
3. 將屬性設定為的`UIViewControllerTransitioningDelegate`實例，也會在要呈現的控制器上。 `TransitioningDelegate`
4. 呈現視圖控制器。

例如，下列程式碼會顯示型`ControllerTwo`別為的視圖控制器-a 子`UIViewController`類別：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

執行應用程式並點擊按鈕，會使第二個控制器的預設動畫從底部以動畫顯示，如下所示：

 ![](transitions-images/no-custom-transition.png "執行應用程式並點擊按鈕，會使第二個控制器的預設動畫從底部以動畫顯示")

不過，設定`ModalPresentationStyle`和`TransitioningDelegate`會導致轉換的自訂動畫：

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

會負責建立子`UIViewControllerAnimatedTransitioning`類別的實例-在下列範例中`CustomAnimator`呼叫： `TransitioningDelegate`

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

轉換發生時，系統會建立的實例`IUIViewControllerContextTransitioning`，並將它傳遞至 animator 的方法。 `IUIViewControllerContextTransitioning`包含發生動畫的位置，以及起始轉換的視圖控制器以及要轉換成的視圖控制器。`ContainerView`

`UIViewControllerAnimatedTransitioning`類別會處理實際的動畫。 必須實作為兩種方法：

1. `TransitionDuration`–傳回動畫的持續時間（以秒為單位）。
1. `AnimateTransition`–執行實際的動畫。

例如，下列類別會實行`UIViewControllerAnimatedTransitioning`以動畫顯示控制器視圖的框架：

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

現在，當您按下按鈕時，會使用在`UIViewControllerAnimatedTransitioning`類別中實作為的動畫：

 ![](transitions-images/custom-transition.png "放大效果的執行範例")

## <a name="collection-view-transitions"></a>集合視圖轉換

集合視圖具有建立動畫轉換的內建支援：

- **導覽控制器**–兩個`UICollectionViewController`實例之間的動畫轉換可以在`UINavigationController`管理它們時，選擇性地自動處理。
- **轉換**配置–新`UICollectionViewTransitionLayout`的類別允許在版面配置之間進行互動式轉換。

### <a name="navigation-controller-transitions"></a>流覽控制器轉換

在流覽控制器中使用時， `UICollectionViewController`包含在控制器之間進行動畫轉換的支援。 這項支援是內建的功能，而且只需要幾個簡單的步驟就能執行：

1. 將`UseLayoutToLayoutNavigationTransitions`設定`false`為上`UICollectionViewController`的。
1. 將的實例`UICollectionViewController`加入至流覽控制器堆疊的根目錄。
1. 建立第二`UICollectionViewController`個，並`UseLayoutToLayoutNavigtionTransitions`將其`true`屬性設定為。
1. 將第二`UICollectionViewController`個推送至流覽控制器的堆疊。

下列程式碼會將`UICollectionViewController`名`ImagesCollectionViewController`為的子類別新增至流覽控制器`UseLayoutToLayoutNavigationTransitions`的堆疊的根目錄，並將屬性`false`設定為：

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

選取專案時， `ImagesController`會建立的第二個實例，只有這次使用不同的版面配置類別。 針對此控制器， `UseLayoutToLayoutNavigtionTransitions`會設定為`true`，如下所示：

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

必須先設定屬性，才能將控制器加入至導覽堆疊。 `UseLayoutToLayoutNavigationTransitions` 設定此屬性時，會以兩個控制器配置之間的動畫轉換來取代一般水準滑動轉換，如下所示：

![](transitions-images/nav2.png "兩個控制器的版面配置之間的動畫轉換")

### <a name="transition-layout"></a>轉換版面配置

除了導覽控制器內的版面配置轉換支援外，現在也提供`UICollectionViewTransitionLayout`名為的新配置。 這個版面配置類別允許在版面配置轉換程式期間使用互動式控制項， `TransitionProgress`方法是允許從程式碼設定。 `UICollectionViewTransitionLayout`不同于-，而不是取代- `SetCollectionViewLayout` iOS 6 中導致動畫版面配置轉換發生的方法。 該方法並未提供內建支援，以控制動畫轉換的進度。

 `UICollectionViewTransitionLayout`例如，可讓手勢辨識器進行設定，以控制回應使用者互動的配置之間的轉換，方法是管理原始版面配置以及要轉換的目標配置。

使用`UICollectionViewTransitionLayout`在筆勢辨識器中執行互動式轉換的步驟如下所示：

1. 建立手勢辨識器。
1. 呼叫的`UICollectionView`方法，並將目標配置和完成處理常式傳遞給它。 `StartInteractiveTransition`
1. 設定從`StartInteractiveTransition`方法傳回之`UICollectionViewTransitionLayout`實例的屬性。`TransitionProgress`
1. 使版面配置失效。
1. 呼叫的`UICollectionView` `CancelInteractiveTransition`方法，以完成轉換或方法來取消它。 `FinishInteractiveTransition`  `FinishInteractiveTransition`讓動畫完成其轉換至目標版面配置，而`CancelInteractiveTransition`導致動畫回到原始的版面配置。
1. 在`StartInteractiveTransition`方法的完成處理常式中處理轉換完成。
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

當使用者 pinches [集合] 視圖`TransitionProgress`時，會相對於縮小的小數值來設定。 在此實行中，如果使用者在轉換 50% 完成之前結束縮小，轉換就會取消。 否則，轉換就會完成。

## <a name="related-links"></a>相關連結

- [IOS 7 簡介（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
