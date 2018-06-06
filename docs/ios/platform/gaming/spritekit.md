---
title: 在 Xamarin.iOS SpriteKit
description: 本文件說明 SpriteKit，Apple 的 2D 圖形 framework SceneKit 與整合、 併入了物理和動畫，包括支援光源和陰影，等等。 SpriteKit 可以用來建立 2D 遊戲。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: b74b5a722aab240b55ed96bea2a33b162d7817eb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786765"
---
# <a name="spritekit-in-xamarinios"></a>在 Xamarin.iOS SpriteKit

SpriteKit、 2D 圖形架構，向 Apple 有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合 SceneKit、 著色器支援、 光源、 陰影、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新物理功能使遊戲中加入實際的效果很容易。

## <a name="physics-bodies"></a>物理內文

SpriteKit 包括 2D，固定主體物理應用程式開發介面。 每個精靈具有相關聯的物理主體 (`SKPhysicsBody`) 物理世界中定義的物理屬性，例如大型和人事，以及主體的幾何。

## <a name="creating-a-physics-body-from-a-texture"></a>從紋理建立物理主體
SpriteKit 現在支援從其紋理衍生物理主體的精靈。 這使得容易實作看起來更自然的衝突。

例如，請注意，在下列衝突香蕉和猴子幾乎會在每個影像表面衝突的方式：
 
![](spritekit-images/image13.png "香蕉和猴子衝突幾乎會在每個映像的介面")

SpriteKit 建立這類物理主體可讓使用一行程式碼。 只需呼叫`SKPhysicsBody.Create`使用紋理和大小： 小精靈。PhysicsBody = SKPhysicsBody.Create （精靈。紋理，精靈。大小）。

## <a name="alpha-threshold"></a>Alpha 閾值

除了只需要設定`PhysicsBody`直接到衍生自紋理幾何的屬性，應用程式可以設定和控制如何衍生之幾何的 alpha 閾值。 

Alpha 閾值定義像素必須產生物理本文中包含的最小 alpha 值。 例如，下列程式碼會產生稍有不同的物理主體：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

調整 alpha 閾值，就像這樣的效果調整先前的衝突，使得猴子落正在與香蕉時：

![](spritekit-images/image14.png "當正在與香蕉猴子落")
 
## <a name="physics-fields"></a>物理欄位

另一個絕佳 SpriteKit 是新物理欄位支援。 這些選項可讓您新增項目頂點處欄位，例如星形重力欄位和要命名在短短幾 spring 欄位。

建立使用 SKFieldNode 類別，它會加入至場景，如同任何其他物理欄位`SKNode`。 上有各種不同的 factory 方法`SKFieldNode`建立不同的物理欄位。 您可以藉由呼叫建立 spring 欄位`SKFieldNode.CreateSpringField()`，藉由呼叫星形重力欄位`SKFieldNode.CreateRadialGravityField()`，依此類推。

`SKFieldNode` 也有屬性來控制欄位的屬性，例如欄位長度、 欄位區域與欄位強制的衰減。

## <a name="spring-field"></a>Spring 欄位

例如，下列程式碼建立 spring 欄位，並將它加入至場景：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

您可以新增小並設定其`PhysicsBody`屬性以便物理欄位將會影響小，如下列程式碼使用者碰觸螢幕：

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

這會導致以類似 [欄位] 節點周圍彈簧 oscillate 香蕉：

![](spritekit-images/image15.png "欄位節點周圍彈簧像 oscillate 香蕉")
 
## <a name="radial-gravity-field"></a>放射狀重力欄位

加入另一個欄位的方法類似。 例如，下列程式碼會建立星形重力欄位：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

這會導致不同的強制欄位，其中香蕉會提取有關之欄位的 radially:

![](spritekit-images/image16.png "香蕉是欄位周圍 radially 提取")
