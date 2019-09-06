---
title: 在 Xamarin 中 SpriteKit
description: 本檔描述 SpriteKit、Apple 的2D 圖形架構，其與 SceneKit 整合、併入物理和動畫，包括對光源和陰影的支援等等。 SpriteKit 可以用來建立2D 遊戲。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: dfda8b1ec3e7cfbdec3fe313d305d78422487f08
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289320"
---
# <a name="spritekit-in-xamarinios"></a>在 Xamarin 中 SpriteKit

SpriteKit 是 Apple 的2D 圖形架構，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 其中包括與 SceneKit、著色器支援、光源、陰影、條件約束、一般地圖產生和物理增強功能的整合。 特別的是，新的物理功能讓您很容易就能在遊戲中加入實際的效果。

## <a name="physics-bodies"></a>物理主體

SpriteKit 包括2D 的固定主體物理 API。 每個 sprite 都有相關聯的`SKPhysicsBody`物理主體（），以定義物理屬性（例如大量和摩擦），以及實體在物理世界中的幾何。

## <a name="creating-a-physics-body-from-a-texture"></a>從材質建立物理主體
SpriteKit 現在支援從其材質衍生 sprite 的物理主體。 這可讓您輕鬆地執行看起來更自然的衝突。

例如，在下列衝突中，請注意，香蕉和猴子在每個影像的表面上幾乎會互相衝突：
 
![](spritekit-images/image13.png "香蕉和猴子幾乎會在每個影像的表面上衝突")

SpriteKit 可讓您使用一行程式碼來建立這種物理主體。 只要使用`SKPhysicsBody.Create`材質和大小來呼叫： sprite。PhysicsBody = SKPhysicsBody。 Create （sprite。材質、sprite。大小）;

## <a name="alpha-threshold"></a>Alpha 閾值

除了直接將`PhysicsBody`屬性設定為衍生自材質的幾何之外，應用程式也可以設定和 Alpha 臨界值，以控制幾何的衍生方式。 

Alpha 臨界值會定義圖元必須包含在產生的物理主體中的最小 Alpha 值。 例如，下列程式碼會產生稍微不同的物理主體：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

調整 Alpha 臨界值的效果，就像這樣會微調先前的衝突，如此一來，當與香蕉發生衝突時，猴子就會落在一起：

![](spritekit-images/image14.png "當與香蕉發生衝突時，猴子會落在一起")
 
## <a name="physics-fields"></a>物理欄位

SpriteKit 的另一個絕佳的新增物理欄位支援。 這些功能可讓您將 vortex 欄位、星形引力欄位和彈簧欄位等專案加入至簡單的名稱。

物理欄位是使用 SKFieldNode 類別建立的，它會加入場景中，就像任何其他`SKNode`情況一樣。 上`SKFieldNode`有各種不同的 factory 方法可建立不同的物理欄位。 您可以藉由`SKFieldNode.CreateSpringField()`呼叫來建立彈簧欄位， `SKFieldNode.CreateRadialGravityField()`方法是呼叫，依此類推。

`SKFieldNode`也有控制欄位屬性的屬性，例如欄位強度、欄位區域，以及欄位強制的衰減。

## <a name="spring-field"></a>彈簧欄位

例如，下列程式碼會建立一個彈簧欄位，並將它新增至場景：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

接著，您可以新增 sprite 並設定`PhysicsBody`其屬性，讓物理欄位會影響 sprite，如同下列程式碼會在使用者觸及畫面時執行：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

這會導致香蕉 oscillate，如同欄位節點的彈簧：

![](spritekit-images/image15.png "香蕉 oscillate 類似于欄位節點的彈簧")
 
## <a name="radial-gravity-field"></a>星形引力欄位

新增不同的欄位很類似。 例如，下列程式碼會建立星形引力欄位：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

這會產生不同的 [強制] 欄位，其中香蕉會提取有關欄位的 radially：

![](spritekit-images/image16.png "香蕉會在欄位周圍提取 radially")
