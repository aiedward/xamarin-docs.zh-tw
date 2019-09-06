---
title: 在 Xamarin 中 SceneKit
description: 本檔描述 SceneKit，這是一種3D 場景圖形 API，藉由抽象化 OpenGL 的複雜性來簡化3D 圖形的使用。
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: d6e6ff02fef3d2919e9716dc8a456aabd9533820
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292793"
---
# <a name="scenekit-in-xamarinios"></a>在 Xamarin 中 SceneKit

SceneKit 是可簡化使用3D 圖形的3D 場景圖形 API。 它最初是在 OS X 10.8 中引進，現在已進入 iOS 8。 有了 SceneKit，建立沉浸式3D 視覺效果和休閒的3D 遊戲並不需要 OpenGL 的專業知識。 以常見的場景圖形概念為基礎，SceneKit 會抽象化 OpenGL 和 OpenGL ES 的複雜性，讓您很容易就能將3D 內容新增至應用程式。 不過，如果您是 OpenGL 專家，SceneKit 也有絕佳的支援直接與 OpenGL 結合。 它也包含許多輔助3D 圖形的功能，例如物理，並與數個其他 Apple 架構（例如核心動畫、核心影像和 Sprite 套件）緊密整合。

SceneKit 非常容易使用。 它是宣告式 API，負責轉譯。 您只需要設定場景、在其中加入屬性，然後 SceneKit 就會處理場景的呈現。

若要使用 SceneKit，您可以使用`SCNScene`類別來建立場景圖形。 場景包含節點的階層（由實例表示），定義`SCNNode`3d 空間中的位置。 每個節點都有影響其外觀的屬性（例如 geometry、光源和材質），如下圖所示：

![](scenekit-images/image7.png "SceneKit 階層")

## <a name="create-a-scene"></a>建立場景

若要讓場景顯示在螢幕上，您可以將它`SCNView`指派給視圖的場景屬性，將它新增至。 此外，如果您對場景進行任何變更， `SCNView`將會自行更新以顯示變更。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

您可以從透過3d 模型化工具匯出的檔案，或以程式設計方式從幾何基本專案填入場景。 例如，這就是建立球體並將它新增至場景的方式：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>新增光線

此時，球體不會顯示任何專案，因為場景中沒有任何光線。 將`SCNLight`實例附加至節點會在 SceneKit 中建立燈。 有數種類型的光源，範圍從各種形式的方向光源到環境光源。 例如，下列程式碼會在球體的側邊建立全方向光源：

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

全方向光源會產生一個擴散反映，產生偶數光源，類似光源。 建立環境光源的方式很類似，雖然它沒有任何方向，因為它在所有方向上都是相同的。 想像一下，像是調式光源:)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

備妥燈之後，球現在會顯示在場景中。

![](scenekit-images/image8.png "當點亮時，會在場景中顯示球體")

## <a name="adding-a-camera"></a>新增相機

將相機（SCNCamera）新增至場景會變更觀點。 新增相機的模式很類似。 建立相機，將其附加至節點，並將節點加入場景。

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

如您在上述程式碼中所見，您可以使用函式或建立 factory 方法來建立 SceneKit 物件。 前者允許使用C#初始化運算式語法，但要使用哪一個是主要的喜好設定。

備妥相機之後，使用者就可以看見整個球體：

![](scenekit-images/image9.png "使用者可以看到整個球體")

您也可以將其他光源新增到場景中。 以下是幾個全方向燈的樣子：

![](scenekit-images/image10.png "具有一些全方向燈的球體")

此外，藉由`sceneView.AllowsCameraControl = true`設定，使用者可以使用觸控手勢來變更視圖的點。

### <a name="materials"></a>涉及

材質是使用 SCNMaterial 類別所建立。 例如，若要將影像加入球體的表面上，請將影像設定為材質的*擴散*內容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

這會將影像分層至節點，如下所示：

![](scenekit-images/image11.png "將影像分層到球體上")

也可以設定材質來回應其他類型的光源。 例如，您可以將物件設為光亮，並將其反射內容設定為顯示反射反映，並在表面上產生明亮的位置，如下所示：

![](scenekit-images/image12.png "物件透過反射反映而變得發亮，導致表面上的明亮點")

材質非常有彈性，可讓您使用非常少的程式碼來達到許多目標。 例如，不是將影像設定為擴散內容，而是改為將它設定為反射內容。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

現在，猴子似乎會在球體中以視覺化方式呈現，與觀點無關。

### <a name="animation"></a>動畫

SceneKit 的設計目的是要與動畫搭配運作。 您可以建立隱含或明確的動畫，甚至可以從核心動畫層樹狀結構呈現場景。 建立隱含動畫時，SceneKit 會提供自己的轉換類別`SCNTransaction`。

以下是旋轉球體的範例：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

不過，您可以建立更多動畫，而不是旋轉。 SceneKit 的許多屬性都是 animatable。 例如，下列程式碼會`Shininess`以動畫呈現材質，以增加反射反射。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit 的使用非常簡單。 它提供許多額外的功能，包括條件約束、物理、宣告式動作、3D 文字、欄位支援深度、Sprite 套件整合和核心影像整合等等。
