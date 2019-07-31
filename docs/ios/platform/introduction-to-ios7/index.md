---
title: iOS 7 簡介
description: 本文涵蓋 iOS 7 中引進的主要新 Api, 包括視圖控制器轉換、UIView 動畫的增強功能、UIKit Dynamics 和文字套件。 其中也涵蓋使用者介面的一些變更, 以及新的 enchanced 多工功能。
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 067d97e6a36dae6c11f056241c08c21899e96c08
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649338"
---
# <a name="introduction-to-ios-7"></a>iOS 7 簡介

_本文涵蓋 iOS 7 中引進的主要新 Api, 包括視圖控制器轉換、UIView 動畫的增強功能、UIKit Dynamics 和文字套件。其中也涵蓋使用者介面的一些變更, 以及新的 enchanced 多工功能。_

iOS 7 是 iOS 的重大更新。 它引進全新的使用者介面設計, 將焦點放在內容, 而不是應用程式 chrome。 除了視覺效果變更之外, iOS 7 還新增了新 Api 的眾多, 以建立更豐富的互動和體驗。 本檔會調查 iOS 7 引進的新技術, 並做為進一步探索的起點。

## <a name="uiview-animation-enhancements"></a>UIView 動畫增強功能

iOS 7 增強了 UIKit 中的動畫支援, 讓應用程式能夠執行先前需要直接拖放至核心動畫架構的專案。 例如, `UIView`現在可以執行春季動畫, 以及`CAKeyframeAnimation`先前套用至的`CALayer`主要畫面格動畫。

### <a name="spring-animations"></a>彈簧動畫

 `UIView`現在支援以彈簧效果建立屬性變更的動畫。 若要新增此項, 請`AnimateNotify`呼叫`AnimateNotifyAsync`或方法, 傳入彈簧阻尼比例和初始彈簧速度的值, 如下所述:

-  `springWithDampingRatio`–介於0和1之間的值, 其中的震盪會增加較小的值。
-  `initialSpringVelocity`–初始彈簧速度, 以每秒動畫距離總計的百分比表示。


當影像視圖的中心變更時, 下列程式碼會產生彈簧效果:

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

這個彈簧效果會使影像視圖在將其動畫完成至新的中心位置時顯示為跳動, 如下所示:

 ![](images/spring-animation.png "這個彈簧效果會使影像視圖在將其動畫完成至新的中心位置時出現跳動")

### <a name="keyframe-animations"></a>主要畫面格動畫

類別現在包含在上`AnimateWithKeyframes` `UIView`建立主要畫面格動畫的方法。 `UIView` 這個方法類似于其他`UIView`的動畫方法, 不同之處在于會以參數的形式傳遞額外`NSAction`的, 以包含主要畫面格。 在中`UIView.AddKeyframeWithRelativeStartTime`, 會藉由呼叫來新增主要畫面格。 `NSAction`

例如, 下列程式碼片段會建立主要畫面格動畫, 以動畫顯示視圖的中心, 以及旋轉視圖:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

`AddKeyframeWithRelativeStartTime`方法的前兩個參數分別指定主要畫面格的開始時間和持續時間, 以整體動畫長度的百分比表示。 上述範例會導致影像視圖在第一秒以動畫方式繪製到其新中心, 然後在下一秒旋轉90度。 因為動畫會指定`UIViewKeyframeAnimationOptions.Autoreverse`為選項, 所以這兩個主要畫面也會反向動畫。 最後, 最後的值會設定為完成處理常式中的初始狀態。

下列螢幕擷取畫面說明透過主要畫面格結合的動畫:

 ![](images/keyframes.png "此螢幕擷取畫面說明透過主要畫面格結合的動畫")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是 UIKit 中的一組新 Api, 可讓應用程式根據物理建立動畫互動。 UIKit Dynamics 會封裝2D 物理引擎以實現這種情況。

API 本質上是宣告式。 您可以藉由建立稱為「*行為*」的物件來宣告物理互動的表現方式, 以表達物理概念, 例如引力、碰撞、彈簧等等。然後將行為附加至另一個物件, 稱為*動態 animator*, 其會封裝一個視圖。 動態 animator 的目的是將宣告的物理行為套用至*動態專案*-會執行`IUIDynamicItem`的專案`UIView`, 例如。

有幾個不同的基本行為可用於觸發複雜的互動, 包括:

-  `UIAttachmentBehavior`–附加兩個動態專案, 使其一起移動, 或將動態專案附加至附加點。
-  `UICollisionBehavior`-允許動態專案參與衝突。
-  `UIDynamicItemBehavior`–指定一組要套用至動態專案的一般屬性, 例如彈性、密度和摩擦。
-  `UIGravityBehavior`-將重心套用至動態專案, 使專案以 gravitational 方向加速。
-  `UIPushBehavior`–將 force 套用至動態專案。
-  `UISnapBehavior`–允許動態專案貼齊至具有彈簧效果的位置。


雖然有許多基本類型, 但使用 UIKit Dynamics 將以物理為基礎的互動新增至視圖的一般程式, 在行為上是一致的:

1.  建立動態 animator。
1.  建立行為。
1.  將行為新增到動態 animator。


### <a name="dynamics-example"></a>Dynamics 範例

讓我們來看一個範例, 將重心和碰撞界限新增至`UIView`。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

將重心加入影像視圖中, 會遵循上面所述的3個步驟。

我們將在此範例`ViewDidLoad`的方法中工作。 首先, 新增`UIImageView`實例, 如下所示:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

這會建立以畫面上邊緣為中心的影像視圖。 若要讓影像「落」在引力的範圍內`UIDynamicAnimator`, 請建立的實例:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

會接受參考`UIView`或的`UICollectionViewLayout`實例, 其中包含將根據附加行為動畫的專案。 `UIDynamicAnimator`

接下來, 建立`UIGravityBehavior`實例。 您可以傳遞一個或多個執行的`IUIDynamicItem`物件, `UIView`例如:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

此行為會傳遞給的陣列`IUIDynamicItem`, 在此案例中, 會包含`UIImageView`我們正在製作動畫的單一實例。

最後, 將行為新增至動態 animator:

```csharp
dynAnimator.AddBehavior (gravity);
```

這會導致影像以引力向下動畫, 如下所示:

![](images/gravity2.png "開始映射位置") 
 ![ ](images/gravity3.png "結束映射位置")

由於不會限制畫面的界限, 因此影像視圖只會落在底部。 為了限制視圖, 使影像與螢幕邊緣衝突, 我們可以新增`UICollisionBehavior`。 我們將在下一節中討論這一點。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我們會先建立`UICollisionBehavior` , 並將它加入至動態 animator, 就像我們針對所`UIGravityBehavior`做的一樣。

修改程式碼以包含`UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`具有名`TranslatesReferenceBoundsIntoBoundry`為的屬性。 將此設定`true`為, 會將參考視圖的界限當做衝突界限使用。

現在, 當影像以引力向下動畫時, 它會稍微偏離畫面底部, 然後才會進行其他工作。

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我們可以進一步控制具有其他行為之影像視圖的行為。 例如, 我們可以加入`UIDynamicItemBehavior`來增加彈性, 使影像視圖在與畫面底部發生衝突時跳動。

新增會`UIDynamicItemBehavior`遵循與其他行為相同的步驟。 首先建立行為:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然後, 將行為新增至動態 animator:

 `dynAnimator.AddBehavior (dynBehavior);`

當此行為發生時, 影像視圖會在與界限衝突時, 使其更多。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

除了上述的新 UIKit Api (如 UIKit Dynamics、Controller 轉換和增強的 UIView 動畫) 之外, iOS 7 也引進了 UI 的各種視覺變更, 以及各種視圖和控制項的相關 API 變更。 如需詳細資訊, 請參閱[iOS 7 使用者介面總覽](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文字套件

文字套件是新的 API, 可提供強大的文字版面配置和呈現功能。 它建置於低層級核心文字架構之上, 但比核心文字更容易使用。

如需詳細資訊, 請參閱我們的[TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多工

iOS 7 會變更背景工作的執行時間和方式。 當工作在背景中執行時, iOS 7 中的工作完成不會再讓應用程式保持喚醒狀態, 而且應用程式會以非連續的方式喚醒背景處理。 iOS 7 也新增了三個新的 Api, 可讓您在背景中以新內容更新應用程式:

-  背景提取–可讓應用程式定期更新背景中的內容。
-  遠端通知-允許應用程式在收到推播通知時更新內容。 通知可以是「無訊息」, 或可以在鎖定畫面上顯示橫幅。
-  背景傳送服務–允許上傳和下載資料 (例如大型檔案), 而不會有固定時間限制。


如需新多工功能的詳細資訊, 請參閱 Xamarin[背景處理指南](~/ios/app-fundamentals/backgrounding/index.md)的 iOS 小節。

## <a name="summary"></a>總結

這篇文章涵蓋 iOS 的幾個重要新增功能。 首先, 它會示範如何將自訂轉換新增至視圖控制器。 然後, 它會示範如何使用集合視圖中的轉換, 從流覽控制器內, 以及在集合視圖之間互動。 接下來, 它引進了數個 UIView 動畫的增強功能, 其中顯示應用程式如何使用 UIKit 來進行先前需要直接針對核心動畫進行程式設計的專案。 最後, 新的 UIKit Dynamics API (將物理引擎帶入 UIKit) 引進了文字套件架構中現在提供的豐富文字支援。

## <a name="related-links"></a>相關連結

- [IOS 7 簡介 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
