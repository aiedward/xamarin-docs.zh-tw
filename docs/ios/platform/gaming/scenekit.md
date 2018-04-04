---
title: SceneKit
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 7c00a3f6aed442eec402f34a5cea4b1895bb3685
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="scenekit"></a>SceneKit

場景套件是 3D 場景的圖形，可簡化使用 3D 圖形 API。 它引進 OS X 10.8，和 iOS 8 現在有了。 使用場景套件建立沈浸式 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 常見的場景圖形概念上建置，場景套件抽象化 OpenGL 和 OpenGL ES，因此很容易就能加入 3D 內容至應用程式的複雜性。 不過，如果您是 OpenGL 專家，場景套件會有很棒支援直接與 OpenGL 以及繫結。 也包含許多功能，可補充 3D 圖形，例如物理，並充分整合數個其他 Apple 架構，例如核心動畫、 Core 映像和套件精靈。

場景套件是非常容易使用。 它是負責呈現的宣告式 API。 您只要設定場景，將屬性加入至它，並場景套件處理場景的轉譯。

若要使用場景套件您建立場景圖形使用`SCNScene`類別。 場景中包含的執行個體所表示的節點階層`SCNNode`，定義在 3D 空間中的位置。 每個節點具有屬性，例如幾何、 光線和材質會影響其外觀，如下圖所示：

![](scenekit-images/image7.png "SceneKit 階層") 

## <a name="create-a-scene"></a>建立場景

若要讓螢幕上顯示的場景，您將它加入至`SCNView`交給檢視的場景屬性。 此外，如果您進行任何變更至場景，`SCNView`更新其本身，以顯示變更。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

從透過 3d 模型化工具，匯出的檔案或以程式設計方式幾何基本圖形，可以填入場景。 例如，以下是如何建立球體，並將它加入至場景：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>加入 Light

此時球體不會顯示任何項目，所以沒有光線場景中。 附加`SCNLight`節點的執行個體建立場景套件中的燈號。 有幾種類型的範圍從各種不同的方向性的光源到周遭的明暗度的燈號。 例如下列程式碼會建立側邊球體想使用全方向 light:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

想使用全方向光源產生導致甚至的光線，有點像都手電筒擴散反映。 建立環境光線很類似，不過它沒有方向為同樣固然所有方向。 它的思考情緒:) 光源

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

與就地燈，球體現在會顯示在場景中。

![](scenekit-images/image8.png "球體會顯示在場景時亮起")
 
## <a name="adding-a-camera"></a>新增數位相機

將相機 (SCNCamera) 加入至場景變更的觀點。 將相機的模式很類似。 建立相機、 將它附加至節點和將節點新增至場景。

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

您可以看到從上面的程式碼，您可以使用建構函式建立物件的場景套件或建立的 factory 方法。 前者允許使用 C# 初始設定式語法，但是要使用哪一個是主要的喜好設定。

就地相機，與整個球體是使用者可以看見：

![](scenekit-images/image9.png "整個球體會顯示給使用者")
 
您可以加入場景以及其他的燈號。 以下是它的外觀與更多的幾個想使用全方向燈號：

![](scenekit-images/image10.png "具有更多的幾個想使用全方向燈號球體")
 
此外，藉由設定`sceneView.AllowsCameraControl = true`，使用者可以變更與觸控筆勢的觀點。

### <a name="materials"></a>資料

資料會建立與 SCNMaterial 類別。 例如若要新增到球體的介面上的映像，將映像材料*擴散*內容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

此圖層到節點上的映像，如下所示：

![](scenekit-images/image11.png "分層球體到映像")
 
材質可以將其他類型的光源太回應。 比方說，物件可以建立發亮，並且已經設定要顯示反射反映，導致亮的位置，在介面中，如下所示的反射內容：

![](scenekit-images/image12.png "建立發亮使用反射反映，導致亮的位置，在介面上的物件")
 
材質時相當有彈性，可讓您達到更與非常少的程式碼。 例如，而不是將映像設定為擴散的內容，將它設定為反射內容改為。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

現在猴子會顯示成以視覺方式放置內球形，獨立的觀點。

### <a name="animation"></a>動畫

場景套件被為了搭配動畫。 您可以建立隱含或明確的動畫，並可能甚至會造成核心動畫層樹狀結構中的場景。 場景套件時建立隱含的動畫，提供它自己的轉換類別`SCNTransaction`。

旋轉的球面範例如下：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

您可以動畫顯示更多旋轉雖然。 場景套件的許多屬性是展示動畫。 例如，下列程式碼繪製材料`Shininess`增加反射反映。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

場景套件是使用非常直接。 它提供豐富的其他功能，包括條件約束、 物理、 宣告式的動作、 3D 文字、 欄位支援、 精靈套件整合以及 Core 映像整合命名少數的深度。