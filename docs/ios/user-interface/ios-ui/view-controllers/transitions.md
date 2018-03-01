---
title: "檢視控制器轉換"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: f85867f08b21a525937e1c938c7c8fd224554abc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="view-controller-transitions"></a>檢視控制器轉換

UIKit 加入支援自訂動畫呈現檢視控制器時，就會發生的轉換。 這項支援隨附內建的控制站，以及直接繼承自任何自訂控制器`UIViewController`。 此外，`UICollectionViewController`利用控制器轉換自訂，以利用集合檢視的版面配置中動畫的轉換。

## <a name="custom-transitions"></a>自訂轉換

在 iOS 7 的檢視控制站之間的動畫的轉換是可完全自訂的。 `UIViewController` 現在包含`TransitioningDelegate`轉換發生時，提供自訂動畫類別系統屬性。

若要使用的自訂轉換`PresentViewController`:

1.  設定`ModalPresentationStyle`至`UIModalPresentationStyle.Custom`呈現控制站上。
2.  實作`UIViewControllerTransitioningDelegate`建立動畫的類別，這是執行個體`UIViewControllerAnimatedTransitioning`。
3.  設定`TransitioningDelegate`的執行個體的內容`UIViewControllerTransitioningDelegate`，也會呈現在控制器上。
4.  呈現檢視控制器。


例如，下列程式碼提供的型別檢視控制器`ControllerTwo`-`UIViewController`子類別：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

執行應用程式並點選按鈕會導致預設動畫的第二個控制站的檢視中建立動畫的來源下方，如下所示：

 ![](transitions-images/no-custom-transition.png "執行應用程式並點選按鈕會導致第二個控制站檢視以動畫方式顯示在從下方的預設動畫")

不過，設定`ModalPresentationStyle`和`TransitioningDelegate`導致自訂動畫的轉換：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

`TransitioningDelegate`會負責建立的執行個體`UIViewControllerAnimatedTransitioning`子類別-呼叫`CustomAnimator`在下面範例：

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning PresentingController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

當轉換發生時系統會建立的執行個體`IUIViewControllerContextTransitioning`，它傳遞給動畫的方法。 `IUIViewControllerContextTransitioning` 包含`ContainerView`動畫的發生位置，以及起始轉換檢視控制器 」 和 「 正在轉換為檢視控制器。

`UIViewControllerAnimatedTransitioning`類別處理實際的動畫。 必須實作兩種方法：

1.  `TransitionDuration` -以秒為單位傳回動畫持續時間。
1.  `AnimateTransition` – 執行實際的動畫。


例如，下列類別會實作`UIViewControllerAnimatedTransitioning`以動畫方式顯示控制站的檢視的框架：

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

現在，當點選按鈕，動畫中實作`UIViewControllerAnimatedTransitioning`類別使用：

 ![](transitions-images/custom-transition.png "作用中執行縮放的範例")

## <a name="collection-view-transitions"></a>集合檢視轉換

集合檢視具有內建支援來建立動畫的轉換：

-  **導覽控制站**– 動畫顯示兩個轉換`UICollectionViewController`執行個體可以選擇性地處理自動當`UINavigationController`管理它們。
-  **轉換配置**– 新`UICollectionViewTransitionLayout`類別允許互動式版面配置之間轉換。


### <a name="navigation-controller-transitions"></a>瀏覽控制器轉換

當用於導覽控制站中`UICollectionViewController`包含支援動畫控制站之間的轉換。 這項支援是內建，而需要只有幾個簡單的步驟來實作：

1.  設定`UseLayoutToLayoutNavigationTransitions`至`false`上`UICollectionViewController`。
1.  執行個體加入`UICollectionViewController`導覽控制站的堆疊的根。
1.  建立第二個`UICollectionViewController`並設定其`UseLayoutToLayoutNavigtionTransitions`屬性`true`。
1.  推入第二個`UICollectionViewController`至導覽控制站的堆疊。


下列程式碼加入`UICollectionViewController`名為的子類別`ImagesCollectionViewController`導覽控制站的堆疊根目錄與`UseLayoutToLayoutNavigationTransitions`屬性設定為`false`:

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
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

選取項目時，另一個執行個體`ImagesController`建立時，只有這次使用不同的版面配置類別。 此控制站，`UseLayoutToLayoutNavigtionTransitions`設`true`，如下所示：

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
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

`UseLayoutToLayoutNavigationTransitions`之前加入巡覽堆疊中的控制站，必須設定屬性。 設定這個屬性，一般的水平滑動轉換取代為兩個控制站，在配置之間的動畫轉換如下所示：

![](transitions-images/nav2.png "在配置的兩個控制器之間執行動畫的轉換")

### <a name="transition-layout"></a>轉換版面配置

除了版面配置轉換支援導覽控制站內，呼叫新的版面配置`UICollectionViewTransitionLayout`現已提供。 此配置類別可讓互動式控制在版面配置轉換過程中，藉由使用`TransitionProgress`設定從程式碼。 `UICollectionViewTransitionLayout` 不同於和不能取代為`SetCollectionViewLayout`從 iOS 6，導致發生動畫版面配置轉換的方法。 該方法未提供內建支援來控制轉換動畫的進度。

 `UICollectionViewTransitionLayout` 例如，允許的筆勢辨識器設定來控制配置，以回應使用者互動，藉由管理原始的版面配置，以及轉換至預定的版面配置之間切換。

若要實作中使用筆勢辨識器的互動式轉換步驟`UICollectionViewTransitionLayout`如下：

1.  建立筆勢辨識器。
1.  呼叫`StartInteractiveTransition`方法`UICollectionView`，將其傳遞目標版面配置和完成處理常式。
1.  設定`TransitionProgress`屬性`UICollectionViewTransitionLayout`從傳回的執行個體`StartInteractiveTransition`方法。
1.  使版面配置。
1.  呼叫`FinishInteractiveTransition`方法`UICollectionView`來完成轉換或`CancelInteractiveTransition`方法來取消它。  `FinishInteractiveTransition` 使動畫完成轉換至目標版面配置，而`CancelInteractiveTransition`結果中傳回的原始配置的動畫。
1.  處理完成處理常式中的轉換完成`StartInteractiveTransition`方法。
1.  新增到集合檢視的筆勢辨識器。


下列程式碼會實作捏合筆勢辨識器中執行互動式的版面配置的轉換：

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

當使用者 pinches 集合檢視中，`TransitionProgress`設定相對於標尺的縮小。 在此實作中，如果使用者在之前的轉換是 50%已完成，結束縮小轉換已取消。 否則，就會完成轉換。




## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
