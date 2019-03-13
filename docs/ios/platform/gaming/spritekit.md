---
title: 在 Xamarin.iOS 中的 SpriteKit
description: 本文件說明 SpriteKit，Apple 的 2D 圖形的架構與 SceneKit 整合、 併入物理學與動畫，包括支援光源和陰影，等等。 SpriteKit 可用來建立 2D 遊戲。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121642"
---
# <a name="spritekit-in-xamarinios"></a>在 Xamarin.iOS 中的 SpriteKit

SpriteKit，Apple 的 2D 圖形架構有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合 SceneKit、 著色器的支援、 光源、 shadows、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新的物理功能讓很容易在遊戲中加入實際的效果。

## <a name="physics-bodies"></a>物理內文

SpriteKit 包含 2D，rigid body 物理 API。 每個 sprite 具有相關聯的物理主體 (`SKPhysicsBody`) 物理世界中定義的物理條件屬性，例如大型和分歧，以及主體的幾何。

## <a name="creating-a-physics-body-from-a-texture"></a>從紋理建立物理主體
SpriteKit 現在支援衍生從其材質的 sprite 物理主體。 這可讓您輕鬆地實作看起來更自然的衝突。

例如，請注意，在下列衝突 banana 和 monkey 衝突幾乎在每個映像的介面的方式：
 
![](spritekit-images/image13.png "Banana 和 monkey 衝突幾乎在每個映像的介面")

SpriteKit 建立這類的物理主體可讓使用一行程式碼。 只要呼叫`SKPhysicsBody.Create`使用紋理和大小： sprite。PhysicsBody = SKPhysicsBody.Create （原件。紋理，sprite。大小）;

## <a name="alpha-threshold"></a>Alpha 臨界值

除了只需要設定`PhysicsBody`屬性直接為衍生自材質的幾何，應用程式可以設定和控制如何衍生之幾何的 alpha 臨界值。 

Alpha 的臨界值會定義要包含在產生的物理本文中必須像素的最小 alpha 值。 比方說，下列程式碼會產生稍微不同的物理主體：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

調整 alpha 的臨界值，就像這樣的效果會調整先前的衝突，使得 monkey 落碰撞的 banana 時：

![](spritekit-images/image14.png "Monkey 落碰撞的 banana 時")
 
## <a name="physics-fields"></a>物理欄位

SpriteKit 增添另一個絕佳的工具是新的物理欄位支援。 這些可讓您新增項目，例如旋風欄位星形重力欄位和 spring 欄位等等。

建立使用 SKFieldNode 類別，它會加入至場景，就像任何其他物理欄位`SKNode`。 上有各種不同的 factory 方法`SKFieldNode`建立不同的物理條件欄位。 您可以呼叫來建立 spring 欄位`SKFieldNode.CreateSpringField()`，藉由呼叫星形重力欄位`SKFieldNode.CreateRadialGravityField()`，依此類推。

`SKFieldNode` 也有控制欄位長度、 欄位區域中，等欄位強制的衰減欄位屬性的屬性。

## <a name="spring-field"></a>Spring 欄位

例如，下列程式碼會建立 spring 欄位，並將它新增至場景：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

您可以新增 sprite 並設定其`PhysicsBody`屬性的物理欄位將會影響 sprite，，和時使用者觸控螢幕，執行下列程式碼：

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

這會導致以類似 [欄位] 節點周圍 spring oscillate bananas:

![](spritekit-images/image15.png "Bananas oscillate 像 [欄位] 節點周圍的 spring")
 
## <a name="radial-gravity-field"></a>放射狀重力欄位

加入另一個欄位的方法類似。 比方說，下列程式碼會建立星形重力欄位：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

這會導致不同的強制欄位，其中 bananas 提取 radially 關於欄位：

![](spritekit-images/image16.png "欄位周圍 radially 提取 bananas")
