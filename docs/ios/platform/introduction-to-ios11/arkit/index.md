---
title: 在 Xamarin.iOS 中 ARKit 簡介
description: 本文件說明在 iOS 11 中使用 ARKit 擴增的實境。 它討論如何將 3D 模型新增至應用程式、 設定檢視、 實作工作階段代理人，在世界中，定位 3D 模型和暫停擴增的實境工作階段。
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 348d2f2090105ed693da7be5a44c82ef18bd2a89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176698"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>在 Xamarin.iOS 中 ARKit 簡介

_針對 iOS 11 擴增的實境_

ARKit 可讓各種擴增的實境應用程式和遊戲。 本章節涵蓋下列主題：

- [Getting Started with ARKit](#gettingstarted)
- [ARKit 使用 UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Getting Started with ARKit

若要開始使用擴增實境，下列指示會逐步簡單的應用程式： 定位 3D 模型，並讓 ARKit 就地保留模型和追蹤功能。

![Jet 浮動在攝影映像的 3D 模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1.將 3D 模型

資產應該新增至專案**SceneKitAsset**建置動作。

![在專案中的 SceneKit 資產](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2.設定檢視

在檢視控制器`ViewDidLoad`方法，載入場景資產，並設定`Scene`檢視上的屬性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3.選擇性地實作工作階段代理人

雖然不需要簡單的情況下，實作工作階段代理人可能有助於偵錯 ARKit 工作階段 （而且在實際的應用程式，提供回饋給使用者） 的狀態。 建立簡單的委派，使用下列程式碼：

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

指派中的委派中`ViewDidLoad`方法：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4.全球定位 3D 模型

在  `ViewWillAppear`，下列程式碼會建立 ARKit 工作階段，並設定相對於裝置的相機的空間中的 3D 模型的位置：

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

每次執行或繼續執行，應用程式時的 3D 模型會位於前方相機。 一旦位於模型，移動觀景窗，並觀看 ARKit 會保留定位模型。

### <a name="5-pause-the-augmented-reality-session"></a>5.暫停擴增的實境工作階段

暫停 ARKit 工作階段，當檢視控制器不是可見的建議 (在`ViewWillDisappear`方法：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>總結

上述程式碼會產生一個簡單的 ARKit 應用程式。 更複雜的範例需要裝載擴增的實境工作階段來實作檢視控制器`IARSCNViewDelegate`，並實作額外的方法。

ARKit 提供許多更複雜的功能，例如介面的追蹤和使用者互動。 請參閱[UrhoSharp 示範](urhosharp.md)結合 ARKit 使用 UrhoSharp 追蹤範例。


## <a name="related-links"></a>相關連結

- [擴增的實境 (Apple)](https://developer.apple.com/arkit/)
- [ARKit 使用 UrhoSharp](urhosharp.md)
- [簡單 ARKit (Jet) 範例](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit 放置物件 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [簡介 ARKit-擴增實境適用於 iOS (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/602/)
