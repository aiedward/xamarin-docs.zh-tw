---
title: iOS 7 簡介
description: 本文涵蓋 iOS 7 中引進的主要新 Api，包括視圖控制器轉換、UIView 動畫的增強功能、UIKit Dynamics 和文字套件。 其中也涵蓋了一些對使用者介面的變更，以及新的 enchanced 多工功能。
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9f27fa550bb21f4261288e8a9a3d809a0ccc7de2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436509"
---
# <a name="introduction-to-ios-7"></a>iOS 7 簡介

_本文涵蓋 iOS 7 中引進的主要新 Api，包括視圖控制器轉換、UIView 動畫的增強功能、UIKit Dynamics 和文字套件。其中也涵蓋了一些對使用者介面的變更，以及新的 enchanced 多工功能。_

iOS 7 是 iOS 的重大更新。 它引進了全新的使用者介面設計，可將焦點放在內容而非應用程式 chrome。 除了視覺效果的變更，iOS 7 還新增了新 Api 的眾多，以建立更豐富的互動和體驗。 本檔將調查 iOS 7 所引進的新技術，並作為進一步探索的起點。

## <a name="uiview-animation-enhancements"></a>UIView 動畫增強功能

iOS 7 增強了 UIKit 中的動畫支援，讓應用程式能夠執行先前需要直接放入核心動畫架構的作業。 例如， `UIView` 現在可以執行彈簧動畫以及主要畫面格動畫，其先前已套用 `CAKeyframeAnimation` 至 `CALayer` 。

### <a name="spring-animations"></a>彈簧動畫

 `UIView` 現在支援以彈簧效果建立屬性變更的動畫。 若要加入這項功能，請呼叫 `AnimateNotify` 或 `AnimateNotifyAsync` 方法，並傳入彈簧阻尼比例和初始彈簧速度的值，如下所述：

- `springWithDampingRatio` –介於0和1之間的值，其中震盪會增加較小的值。
- `initialSpringVelocity` –初始的彈簧速度，以每秒動畫距離總計的百分比表示。

下列程式碼會在影像視圖的中心變更時產生彈簧效果：

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

此彈簧效果會讓影像視圖在完成其動畫至新的中心位置時出現彈跳，如下所示：

 ![此彈簧效果會導致影像視圖在完成其動畫至新的中心位置時出現彈跳](images/spring-animation.png)

### <a name="keyframe-animations"></a>主要畫面格動畫

`UIView`類別現在包含在上建立主要畫面格 `AnimateWithKeyframes` 動畫的方法 `UIView` 。 這個方法類似于其他 `UIView` 動畫方法，不同之處在于會以參數形式傳遞額外的動畫方法來包含主要畫面格 `NSAction` 。 在中 `NSAction` ，會藉由呼叫來新增主要畫面格 `UIView.AddKeyframeWithRelativeStartTime` 。

例如，下列程式碼片段會建立主要畫面格動畫，以建立視圖的動畫，以及旋轉視圖：

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

方法的前兩個參數 `AddKeyframeWithRelativeStartTime` 分別指定主要畫面格的開始時間和持續時間，以整體動畫長度的百分比表示。 上述範例會導致影像視圖以動畫顯示第一秒的新中心，然後在下一秒旋轉90度。 由於動畫會指定 `UIViewKeyframeAnimationOptions.Autoreverse` 為選項，因此這兩個主要畫面格也會以相反的方式進行動畫。 最後，最終的值會在完成處理常式中設定為初始狀態。

下列螢幕擷取畫面說明透過主要畫面格組合的動畫：

 ![此螢幕擷取畫面說明透過主要畫面格組合的動畫](images/keyframes.png)

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是 UIKit 中的一組新 Api，可讓應用程式根據物理建立動畫互動。 UIKit Dynamics 會封裝2D 物理引擎以進行這種情況。

API 本質上是宣告式。 您可以藉由建立稱為 *行為* 的物件（例如重力、衝突、彈簧等）來宣告物理互動的行為。然後，您可以將行為 (s) 附加至另一個物件，稱為 *動態 animator*，它會封裝一個視圖。 動態 animator 會將已宣告的物理行為套用至 *動態專案* -可執行檔專案 `IUIDynamicItem` ，例如 `UIView` 。

有幾個不同的基本行為可觸發複雜的互動，包括：

- `UIAttachmentBehavior` –附加兩個動態專案，使其一起移動，或將動態專案附加至附加點。
- `UICollisionBehavior` –允許動態專案參與衝突。
- `UIDynamicItemBehavior` –指定一組要套用至動態專案的一般屬性，例如彈性、密度和摩擦。
- `UIGravityBehavior` -將重力套用至動態專案，使專案在 gravitational 方向加速。
- `UIPushBehavior` –適用于動態專案。
- `UISnapBehavior` –允許動態專案貼齊具有彈簧效果的位置。

雖然有許多基本功能，但使用 UIKit Dynamics 將物理互動新增至視圖的一般程式，在行為上是一致的：

1. 建立動態 animator。
1. 建立行為 (s) 。
1. 將行為新增至動態 animator。

### <a name="dynamics-example"></a>Dynamics 範例

讓我們看看將重力和碰撞界限新增至的範例 `UIView` 。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

將重力新增至影像視圖，會遵循上述的3個步驟。

我們將在 `ViewDidLoad` 此範例中使用方法。 首先，新增實例，如下 `UIImageView` 所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

這會建立以畫面上邊緣為中心的影像視圖。 若要讓影像「落在引力」，請建立的實例 `UIDynamicAnimator` ：

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`會採用參考 `UIView` 或的實例 `UICollectionViewLayout` ，其中包含將依據附加行為以動畫顯示的專案 (s) 。

接著，建立 `UIGravityBehavior` 實例。 您可以傳遞一個或多個執行的物件 `IUIDynamicItem` ，例如 `UIView` ：

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

此行為會傳遞給的陣列 `IUIDynamicItem` ，在此案例中，會包含 `UIImageView` 我們正在製作動畫的單一實例。

最後，將行為新增至動態 animator：

```csharp
dynAnimator.AddBehavior (gravity);
```

這會導致影像以重力向下動畫，如下所示：

![起始影像位置 ](images/gravity2.png)
 ![ 的結束映射位置](images/gravity3.png)

因為沒有任何條件約束畫面的界限，所以影像視圖只會落在底部。 若要限制視圖，使影像與螢幕邊緣衝突，我們可以加入 `UICollisionBehavior` 。 我們將在下一節討論這一點。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我們首先會建立， `UICollisionBehavior` 並將它新增至動態 animator，就像我們為所做的一樣 `UIGravityBehavior` 。

修改程式碼，以包含 `UICollisionBehavior` ：

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

`UICollisionBehavior`具有名為的屬性 `TranslatesReferenceBoundsIntoBoundry` 。 將此設定為， `true` 會將參考視圖的界限當做碰撞界限使用。

現在，當影像以引力向下動畫時，它會在畫面底部稍微向下彈跳，然後才會在該處進行其他工作。

<!--, as shown below:

 ![Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there](images/bounce.png)-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我們可以進一步控制具有其他行為的下降影像視圖行為。 例如，我們可以加入， `UIDynamicItemBehavior` 以增加彈性，使影像視圖在與螢幕底部發生衝突時，更多地彈跳。

加入會 `UIDynamicItemBehavior` 遵循與其他行為相同的步驟。 先建立行為：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然後，將行為新增至動態 animator：

 `dynAnimator.AddBehavior (dynBehavior);`

有了這種行為之後，影像視圖會在與界限相衝突時，更多地彈跳。

## <a name="general-user-interface-changes"></a>一般消費者介面變更

除了上述的新 UIKit Api （例如 UIKit Dynamics、控制器轉換和增強的 UIView 動畫）之外，iOS 7 還引進了 UI 的各種視覺變更，以及各種不同視圖和控制項的相關 API 變更。 如需詳細資訊，請參閱 [iOS 7 消費者介面總覽](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文字套件

文字套件是新的 API，可提供強大的文字版面配置和轉譯功能。 它是以低層級的核心文字架構為基礎，但比核心文字更容易使用。

如需詳細資訊，請參閱我們的 [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多工

iOS 7 變更執行背景工作的時間和方式。 當工作在背景中執行時，iOS 7 中的工作完成不再讓應用程式保持喚醒狀態，而應用程式會以非連續的方式喚醒背景處理。 iOS 7 也新增了三個新的 Api，以在背景中以新內容來更新應用程式：

- 背景提取–可讓應用程式以固定間隔定期更新背景中的內容。
- 遠端通知-可讓應用程式在收到推播通知時更新內容。 通知可以是無訊息的，也可以在鎖定畫面上顯示橫幅。
- 背景傳送服務-允許上傳及下載資料，例如大型檔案，但不含固定的時間限制。

如需新多工功能的詳細資訊，請參閱 Xamarin [背景處理指南](~/ios/app-fundamentals/backgrounding/index.md)的 iOS 章節。

## <a name="summary"></a>摘要

本文涵蓋許多 iOS 的重大新功能。 首先，它會說明如何將自訂轉換新增至 View controller。 然後，它會示範如何在集合視圖中使用轉換，包括從流覽控制器，以及在集合視圖之間互動。 接下來，它引進了幾項 UIView 動畫的增強功能，顯示應用程式如何針對先前需要直接針對核心動畫進行程式設計的專案，使用 UIKit。 最後，新的 UIKit Dynamics API （將物理引擎帶到 UIKit）隨附于文字套件架構中現在提供的 rich text 支援。

## <a name="related-links"></a>相關連結

- [IOS 7 (範例) 簡介 ](/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景處理](~/ios/app-fundamentals/backgrounding/index.md)