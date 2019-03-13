---
title: 在 Xamarin.iOS 中的 SceneKit
description: 本文件說明 SceneKit 簡化 3D 圖形處理，藉由抽象化複雜的 OpenGL 的 3D 場景圖形 API。
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 0944978b34c8e164acd6e829db177bf4fd72dea9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109857"
---
# <a name="scenekit-in-xamarinios"></a>在 Xamarin.iOS 中的 SceneKit

SceneKit 是 3D 場景圖形 API，可簡化的 3D 圖形。 它最初是在 OS X 10.8，現在已有 iOS 8。 使用 SceneKit 建立擬真 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 在一般的場景圖形概念建置，SceneKit 複雜性抽象化的 OpenGL 和 OpenGL ES，因此很容易就能新增 3D 內容的應用程式。 不過，如果您是 OpenGL 專家，SceneKit 有繫結，直接與 OpenGL 也有絕佳的支援。 也包含許多功能可補充 3D 圖形，例如物理條件，並非常適合整合數個其他 Apple 的架構，例如 Core Animation、 Core 映像和 Sprite Kit。

SceneKit 是非常容易使用。 它是負責呈現的宣告式 API。 您只要設定場景，將屬性加入至它，並 SceneKit 處理場景的呈現。

您建立使用 SceneKit 的場景圖形 using`SCNScene`類別。 場景中包含的執行個體所表示的節點階層`SCNNode`，在 3D 空間中定義的位置。 每個節點有屬性，例如幾何、 光源和會影響它的外觀的資料，如下圖所示：

![](scenekit-images/image7.png "SceneKit 階層") 

## <a name="create-a-scene"></a>建立場景

若要讓場景，會出現在畫面上，您將它新增至`SCNView`將它指派給該檢視的場景屬性。 此外，如果您進行任何變更至場景，`SCNView`本身顯示的變更會更新。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

從 3d 模型化工具，透過匯出的檔案，或以程式設計方式從幾何基本圖形，可以填入場景。 比方說，這是如何建立球體，並將它新增至場景：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>新增光線

此時球體不會顯示任何項目因為場景中沒有任何輕。 附加`SCNLight`到節點的執行個體中 SceneKit 建立號誌。 有數種類型的範圍是從各種形式的定向光源到環境光源的燈號。 例如下列程式碼會建立全方向的光線，球體旁邊：

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

全方向光源會產生導致偶數的光線，有點像是如何手電筒擴散反映。 建立環境光線很類似，但它有沒有方向與它同樣大展身手往所有方向。 將它想像成情緒:) 光源

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

使用就地號誌，球體現在會顯示在場景中。

![](scenekit-images/image8.png "球體是顯示在場景時亮起")
 
## <a name="adding-a-camera"></a>新增相機

將相機 (SCNCamera) 加入至場景變更觀點來看。 將觀景窗的模式很類似。 建立數位相機、 將它附加至節點並將節點新增至場景。

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

您可以看到從 SceneKit 物件可以使用建構函式來建立上述程式碼或建立的 factory 方法。 先前的功能可讓使用C#初始設定式語法，但要使用哪一個是主要的喜好設定。

就地相機，整個球體是使用者可以看見：

![](scenekit-images/image9.png "整個球體是使用者可以看見")
 
您可以加入場景以及其他的燈號。 以下是看起來像是具有少數的更多 omnidirectional 燈號：

![](scenekit-images/image10.png "使用一些更多的 omnidirectional 燈號球體")
 
此外，藉由設定`sceneView.AllowsCameraControl = true`，使用者可以變更與觸控筆勢的觀點。

### <a name="materials"></a>資料

資料會以 SCNMaterial 類別建立的。 例如若要將影像拖曳至球體的介面，設定影像的資料*擴散*內容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

此圖層到節點上的映像，如下所示：

![](scenekit-images/image11.png "分層到球體上的映像")
 
材質可以設定為回應其他類型的太光源。 比方說，物件可以成為閃亮，並已設定為顯示反射的反映，如下所示，在表面上，往的位置所產生的反射內容：

![](scenekit-images/image12.png "進行閃亮使用反射的反映，導致較亮的位置，在介面上的物件")
 
內容有極具彈性，可讓您達到更少許的程式碼。 比方說，而不是擴散的內容中設定的映像，將它設定為反映的內容改為。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Monkey 隨即顯示以視覺方式坐在球體，獨立的觀點來看。

### <a name="animation"></a>動畫

SceneKit 可適用於動畫。 您可以建立隱含或明確的動畫，並甚至可以轉譯場景來自 Core 動畫層樹狀結構。 SceneKit 時建立隱含的動畫，提供它自己的轉換類別`SCNTransaction`。

旋轉的球面範例如下：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

您可以以動畫顯示更多旋轉不過。 SceneKit 的許多屬性都可顯示動畫。 例如，下列程式碼以動畫顯示的材料`Shininess`增加反射的反映。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit 是非常容易使用。 它提供豐富的額外功能，包括條件約束、 物理、 宣告式的動作、 3D 文字、 深度的欄位支援、 Sprite Kit 整合 Core 映像整合等等。