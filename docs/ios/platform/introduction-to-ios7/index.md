---
title: IOS 7 簡介
description: 本文涵蓋在 iOS 7，包括檢視控制器轉換 UIView 動畫 UIKit Dynamics 及文字套件的增強功能中引進的主要新 Api。 其中也涵蓋部分使用者介面，以及新的增強式多工作業功能的變更。
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: db2ce779962947e2121ff03280544a080e193e2e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118041"
---
# <a name="introduction-to-ios-7"></a>IOS 7 簡介

_本文涵蓋在 iOS 7，包括檢視控制器轉換 UIView 動畫 UIKit Dynamics 及文字套件的增強功能中引進的主要新 Api。其中也涵蓋部分使用者介面，以及新的增強式多工作業功能的變更。_

iOS 7 是 iOS 的重要更新。 它導入了全新的使用者介面設計，會將焦點放在內容，而不是應用程式的 chrome。 視覺變更，以及 iOS 7 會加入在眾多新的 Api 來建立更豐富的互動和體驗。 此文件意見調查新技術所導入的 iOS 7，並做為起點供您進一步探索。

## <a name="uiview-animation-enhancements"></a>UIView 動畫增強功能

iOS 7 擴大 UIKit，讓應用程式執行之前，您必須直接在核心動畫架構的卸除的動作中的動畫支援。 例如，`UIView`現在可以執行 spring 動畫，以及主要畫面格動畫，讓先前`CAKeyframeAnimation`套用至`CALayer`。

### <a name="spring-animations"></a>動畫

 `UIView` 現在支援具有 spring 效果的動畫屬性變更。 若要新增這種情況，請呼叫`AnimateNotify`或`AnimateNotifyAsync`方法，如下所述，在值中傳遞 spring 禁止比例和初始 spring 速度：

-  `springWithDampingRatio` – 值介於 0 和 1，其中振動會增加為較小的值。
-  `initialSpringVelocity` – 每秒的動畫總距離的百分比初始 spring 速度。


映像檢視的中心變更時，下列程式碼會產生 spring 效果：

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

此 spring 效果會導致映像檢視出現來回傳送完成它的動畫，以新中心位置，如下所示：

 ![](images/spring-animation.png "此 spring 效果，可讓映像檢視出現來回傳送完成它的新中心位置的動畫")

### <a name="keyframe-animations"></a>主要畫面格動畫

`UIView`類別現在包含`AnimateWithKeyframes`方法上建立主要畫面格動畫`UIView`。 這個方法很類似於其他`UIView`動畫的方法，不同處是額外`NSAction`傳遞做為參數，以包含主要畫面格。 內`NSAction`，主要畫面格會新增呼叫`UIView.AddKeyframeWithRelativeStartTime`。

例如，下列程式碼片段會建立主要畫面格動畫以動畫顯示檢視的中心 」，來旋轉檢視：

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

前兩個參數`AddKeyframeWithRelativeStartTime`方法指定的開始時間和持續時間的主要畫面格，分別為整體的動畫長度的百分比。 上述結果映像檢視以動畫顯示到其新中心移轉的第一個第二個範例後面接著透過下一步 的第二個旋轉 90 度。 由於動畫指定`UIViewKeyframeAnimationOptions.Autoreverse`的選項，這兩個主要畫面格建立動畫以及反向。 最後，最終的值會設定為完成處理常式中的初始狀態。

以下螢幕擷取畫面說明透過主要畫面格動畫結合：

 ![](images/keyframes.png "此螢幕擷取畫面說明透過主要畫面格合併的動畫")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是一組新的 UIKit 中的 Api，可讓應用程式，以根據物理動畫的互動。 UIKit Dynamics 封裝才能達到這個目的 2D physics engine。

在本質上宣告式的 API。 您宣告的物理互動藉由呼叫物件的行為方式*行為*-express 的物理學概念，例如重力、 衝突、 springs 等等。然後您將行為附加至另一個物件，稱為*動態動畫*，其可封裝一個檢視。 動態動畫會套用所宣告的物理條件行為的在乎*動態項目*-項目實作`IUIDynamicItem`，例如`UIView`。

有幾種不同基本行為可以用來觸發複雜的互動，包括：

-  `UIAttachmentBehavior` – 附加兩個動態項目，使它們一起移動，或將動態項目附加至附件點。
-  `UICollisionBehavior` – 可讓參與衝突的動態項目。
-  `UIDynamicItemBehavior` -指定一組一般要套用至動態項目，例如彈性、 密度和衝突的屬性。
-  `UIGravityBehavior` -適用於動態項目，導致加速重力方向的項目的重心。
-  `UIPushBehavior` – 會強制套用動態項目。
-  `UISnapBehavior` – 可讓具有 spring 效果的位置貼齊的動態項目。


雖然有許多基本項目，加入檢視，使用 UIKit Dynamics 物理式互動的一般程序行為之間是一致：

1.  建立動態的動畫。
1.  建立行為。
1.  將行為新增至動態動畫中。


### <a name="dynamics-example"></a>Dynamics 範例

讓我們看看一個範例，將重力和衝突的界限，以加入`UIView`。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

加入影像檢視重力遵循上述的 3 個步驟。

我們將會使用`ViewDidLoad`此範例中的方法。 首先，新增`UIImageView`執行個體，如下所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

這會建立在螢幕上邊緣置中的映像檢視。 使用重力製作映像 」 fall 」，建立的執行個體`UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`執行個體的參考`UIView`或`UICollectionViewLayout`，其中包含每個附加行為建立動畫的項目。

接下來，建立`UIGravityBehavior`執行個體。 您可以將實作的一或多個物件傳遞`IUIDynamicItem`，例如`UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

行為會傳遞陣列`IUIDynamicItem`，在此情況下包含單一`UIImageView`我們會以動畫顯示的執行個體。

最後，加入動態動畫的行為：

```csharp
dynAnimator.AddBehavior (gravity);
```

這會導致與重力，做為下面詳述的向下以動畫顯示的映像：

![](images/gravity2.png "開始的映像位置") 
![](images/gravity3.png "結束的映像位置")

因為沒有任何限制螢幕的界限，只會下降底部決定映像檢視。 若要限制檢視，以便在螢幕的邊緣與衝突的映像，我們可以新增`UICollisionBehavior`。 我們將討論這一節中。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我們一開始先建立`UICollisionBehavior`並將它加入動態的動畫，就像我們`UIGravityBehavior`。

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

`UICollisionBehavior`屬性，稱為`TranslatesReferenceBoundsIntoBoundry`。 這個設定設為`true`造成參考檢視的範圍來做為衝突的界限。

現在，當映像以動畫顯示向下與重力，它撞稍微螢幕的底部那里休息決定之前。

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我們可以進一步控制下降的映像檢視與其他行為的行為。 比方說，我們可以新增`UIDynamicItemBehavior`增加彈性，造成來回傳送多個當它撞畫面底部的 [映像] 檢視。

新增`UIDynamicItemBehavior`遵循相同的步驟，如同其他行為。 第一次建立行為：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然後，加入動態動畫的行為：

 `dynAnimator.AddBehavior (dynBehavior);`

就地這項行為，映像檢視不斷彈跳直到多個與界限衝突，所以當。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

新的 UIKit Api UIKit Dynamics、 控制器轉換等增強的 UIView 動畫上面所述，除了 iOS 7 導入了各種不同的視覺變更 UI，以及各種檢視和控制項相關的 API 變更。 如需詳細資訊，請參閱[iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文字套件

文字套件是新的 API，提供功能強大的文字版面配置和轉譯功能。 它建置在核心文字架構的低層級之上，但更容易就能使用比核心文字。

如需詳細資訊，請參閱我們[TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多工

iOS 7 變更何時及如何執行背景工作。 工作完成 iOS 7 中的不會再保留應用程式甦醒狀態時在背景中執行工作和應用程式事項的背景處理非連續的方式。 iOS 7 也會加入以新的內容，在背景中更新應用程式的三個新的 Api:

-  背景擷取-允許應用程式，以更新內容，在背景中以固定間隔。
-  遠端通知-可讓應用程式，以接收推播通知時，更新內容。 通知可以是無訊息或可以在鎖定畫面上顯示橫幅。
-  背景傳送服務 – 允許上傳和下載的資料，例如大型檔案，沒有固定的時間限制。


如需進一步瞭解新的多工功能的詳細資訊，請參閱 Xamarin iOS 區段[背景指南](~/ios/app-fundamentals/backgrounding/index.md)。

## <a name="summary"></a>總結

本文涵蓋幾項主要的新功能，為 iOS。 首先，它會顯示如何將檢視控制器的自訂轉換。 然後，它會示範如何使用在集合檢視中，同時從儲存格內的瀏覽控制器，以及以互動方式集合檢視之間的轉換。 接下來，它引入了對 UIView 動畫，顯示應用程式之前，您必須直接針對核心動畫進行程式設計的項目所使用的 UIKit 的數個增強功能。 最後，新的 UIKit Dynamics API，其帶入 UIKit 物理引擎，引進與 rich text 格式支援現已納入文字套件架構。

## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
