---
title: "IOS 7 簡介"
description: "本文涵蓋的 iOS 7，包括檢視控制器轉換 UIView 動畫 UIKit Dynamics 和文字套件的增強功能中導入的主要新 Api。 其中也涵蓋某些使用者介面和新的增強式多工作業功能的變更。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a7bebc2b73ecb564028a92340c726bd5c1f1c54b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-7"></a>IOS 7 簡介

_本文涵蓋的 iOS 7，包括檢視控制器轉換 UIView 動畫 UIKit Dynamics 和文字套件的增強功能中導入的主要新 Api。其中也涵蓋某些使用者介面和新的增強式多工作業功能的變更。_

iOS 7 是 iOS 的重大更新。 它導入了全新的使用者介面設計，將焦點放在內容，而不是應用程式的色彩。 視覺變更，連同 iOS 7 將加入的新 Api 來建立更豐富的互動和體驗。 此文件意見調查新的技術所導入的 iOS 7，而且可以當做進一步瀏覽的起點。

## <a name="uiview-animation-enhancements"></a>UIView 動畫增強功能

iOS 7 擴大動畫中的支援 UIKit，允許應用程式執行之前，您必須卸除，直接將核心動畫架構的動作。 例如，`UIView`現在可以執行彈性動畫，以及主要畫面格動畫，讓先前`CAKeyframeAnimation`套用至`CALayer`。

### <a name="spring-animations"></a>彈簧動畫

 `UIView` 現在支援 spring 效果的動畫屬性變更。 若要加入這種情況，呼叫`AnimateNotify`或`AnimateNotifyAsync`方法，傳入值 spring 禁止比例和初始 spring 速度，如下所述：

-  `springWithDampingRatio` – 值介於 0 和 1，振動其中較小的值會增加。
-  `initialSpringVelocity` – 初始 spring 速度為每秒總動畫距離的百分比。


變更映像檢視的中心時，下列程式碼會產生 spring 效果：

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

此 spring 效果，可讓影像檢視，跳動完成它的動畫，新的中心位置，如下所示：

 ![](images/spring-animation.png "此 spring 效果，可讓您可以完成它到新的中心位置的動畫顯示的映像檢視")

### <a name="keyframe-animations"></a>主要畫面格動畫

`UIView`類別現在包含`AnimateWithKeyframes`方法上建立主要畫面格動畫`UIView`。 這個方法是類似於其他`UIView`動畫方法，除了額外`NSAction`傳遞為參數，以包含主要畫面格。 內`NSAction`，藉由呼叫加入主要畫面格`UIView.AddKeyframeWithRelativeStartTime`。

比方說，下列程式碼片段會建立主要畫面格動畫來建立檢視的中心 」 並旋轉檢視：

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

前兩個參數`AddKeyframeWithRelativeStartTime`方法的開始時間和持續時間主要畫面格，分別指定以整體的動畫長度的百分比表示。 上述結果中映像檢視動畫顯示到其新中心透過第一個第二個範例後面跟著 下一步的第二個透過旋轉 90 度。 由於動畫指定`UIViewKeyframeAnimationOptions.Autoreverse`的選項，這兩個主要畫面格動畫以及反向。 最後，最終的值會設定為初始狀態中完成處理常式。

以下螢幕擷取畫面說明透過主要畫面格動畫結合：

 ![](images/keyframes.png "這個螢幕擷取畫面說明合併到主要畫面格動畫")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是一組新 api UIKit 中可讓應用程式建立動畫的物理為基礎的互動。 UIKit 動態封裝來進行這項作業的 2D 物理引擎。

API 是宣告式的本質。 您宣告物理互動藉由建立物件，呼叫的行為方式*行為*-express 物理概念，例如重力、 衝突、 springs 等等。然後您將 behavior(s) 附加至另一個物件，稱為*動態動畫*，以封裝檢視。 動態動畫會套用所宣告的物理行為的在乎*動態項目*-項目可實作`IUIDynamicItem`，例如`UIView`。

有數個不同基本的行為，可以用來觸發複雜的互動，包括：

-  `UIAttachmentBehavior` – 附加兩個動態項目，使其在一起，移動或將動態項目附加至附件點。
-  `UICollisionBehavior` – 可讓要參與 衝突的動態項目。
-  `UIDynamicItemBehavior` – 指定一組要套用至動態項目，例如彈性、 密度和人事的屬性。
-  `UIGravityBehavior` -將重力套動態項目，導致加速重力方向的項目。
-  `UIPushBehavior` – 會強制套用至動態項目。
-  `UISnapBehavior` – 允許貼齊 spring 效果的位置將動態項目。


雖然有許多基本類型，加入檢視，使用 UIKit Dynamics 物理為基礎的互動的一般程序是一致行為：

1.  建立動態的動畫。
1.  建立 behavior(s)。
1.  將行為加入至動態動畫。


### <a name="dynamics-example"></a>Dynamics 範例

讓我們看看範例新增重力和衝突界限`UIView`。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

加入影像檢視重力遵循上述的步驟 3。

我們將會使用`ViewDidLoad`此範例中的方法。 首先，新增`UIImageView`執行個體，如下所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

這會建立在螢幕頂端置中檢視，映像。 若要讓 「 回復 」 的映像與重力，建立的執行個體`UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`執行個體的參考`UIView`或`UICollectionViewLayout`，其中包含每個附加 behavior(s) 建立動畫的項目。

接下來，建立`UIGravityBehavior`執行個體。 您可以將實作的一或多個物件傳遞`IUIDynamicItem`、 like `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

傳遞陣列的行為是`IUIDynamicItem`，在此情況下包含單一`UIImageView`我們會建立動畫的執行個體。

最後，將行為加入至動態動畫：

```csharp
dynAnimator.AddBehavior (gravity);
```

這會導致與重力，做為下面詳述的向下建立動畫的影像：

![](images/gravity2.png "啟動映像位置") 
![](images/gravity3.png "結束的映像位置")

因為沒有任何限制螢幕的界限，映像檢視只會底部。 若要限制檢視，以便與螢幕的邊緣衝突的映像，我們可以加入`UICollisionBehavior`。 我們會在下一節。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我們一開始會藉由建立`UICollisionBehavior`並將其加入動態的動畫，我們所做一樣`UIGravityBehavior`。

修改程式碼，包括`UICollisionBehavior`:

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

`UICollisionBehavior`具有名`TranslatesReferenceBoundsIntoBoundry`。 將此設定為`true`造成參考檢視的範圍，以做為衝突界限。

現在，當映像以動畫方式顯示向下拖曳以重力，它撞稍微畫面底部之前那里貼齊侷限。

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我們可以進一步控制下降的映像檢視其他行為的行為。 例如，我們可以加入`UIDynamicItemBehavior`增加彈性，讓它與螢幕的底部發生衝突時，來回傳送多個影像檢視。

加入`UIDynamicItemBehavior`遵循相同步驟，就如同其他行為。 第一次建立行為：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然後，將行為加入至動態動畫：

 `dynAnimator.AddBehavior (dynBehavior);`

發生這種行為，與影像檢視退多個與界限它發生衝突時。

## <a name="general-user-interface-changes"></a>一般使用者介面變更

除了新的 UIKit Api 例如 UIKit Dynamics、 控制站轉換，以及增強的 UIView 動畫上面所述，iOS 7 導入了許多視覺效果變更的 ui，與不同的檢視和控制項相關的應用程式開發介面變更。 如需詳細資訊，請參閱[iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文字套件

文字套件是新的 API 可提供配置和轉譯功能強大的文字。 它會建置於核心文字架構的低層級之上，但更容易使用的核心文字以外。

如需詳細資訊，請參閱我們[TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多工

iOS 7 變更何時及如何執行背景工作。 工作完成 iOS 7 中的不再會保留應用程式執行中狀態時在背景中執行工作和應用程式會決定非連續的方式處理的背景。 iOS 7 也會加入三個新的應用程式開發介面來更新應用程式，以在背景中新的內容：

-  背景擷取 – 更新內容，在背景中以固定間隔的允許應用程式。
-  遠端通知-可讓應用程式接收推播通知時，更新內容。 通知可以是無訊息或可以在鎖定畫面上顯示橫幅。
-  背景傳送服務 – 允許上傳與下載的資料，例如大型檔案，而不固定的時間限制。


如需有關新的多工能力的詳細資訊，請參閱在 Xamarin 的 iOS 章節[Backgrounding 指南](~/ios/app-fundamentals/backgrounding/index.md)。

## <a name="summary"></a>總結

本文涵蓋數個主要的新增部分至 iOS。 首先，它會顯示如何將自訂的轉換加入至檢視控制器。 然後，它會顯示如何使用在集合檢視中，同時從中導覽控制站，以及以互動方式檢視集合之間的轉換。 接下來，它導入了對 UIView 動畫，顯示應用程式之前，您必須直接針對核心動畫進行程式設計的物件所使用的 UIKit 的數個增強功能。 最後，新 UIKit Dynamics API，其帶來 UIKit 物理引擎，引進與文字套件架構中目前可用的 rich text 格式支援。

## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
