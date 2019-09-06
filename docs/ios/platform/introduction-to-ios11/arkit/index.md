---
title: Xamarin 中的 ARKit 簡介
description: 本檔說明 iOS 11 與 ARKit 的增強現實。 它討論如何將3D 模型新增至應用程式、設定視圖、執行會話委派、將3D 模型放在世界中，以及暫停增強的現實會話。
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/30/2017
ms.openlocfilehash: 9e6b5c763d620bf5ef17e4fdb613d4bed9c00279
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290907"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Xamarin 中的 ARKit 簡介

_IOS 11 的增強現實_

ARKit 可提供各種增強的現實應用程式和遊戲。 本章節涵蓋下列主題：

- [使用 ARKit 消費者入門](#gettingstarted)
- [搭配使用 ARKit 與 UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>使用 ARKit 消費者入門

若要開始使用增強的現實，下列指示會逐步解說一個簡單的應用程式：定位3D 模型，並讓 ARKit 保持模型與追蹤功能的位置。

![以相機影像浮動的 Jet 3D 模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1.加入3D 模型

資產應該使用 [ **SceneKitAsset** ] [建立] 動作新增至專案。

![SceneKit 專案中的資產](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2.設定視圖

在 view 控制器的`ViewDidLoad`方法中，載入場景資產，並在此視圖上`Scene`設定屬性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3.選擇性地執行會話委派

雖然在簡單的情況下並不需要，但執行會話委派有助於偵測 ARKit 會話的狀態（在實際的應用程式中，提供意見反應給使用者）。 使用下列程式碼建立簡單的委派：

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

在`ViewDidLoad`方法的中，指派委派：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4.將3D 模型放在世界中

在`ViewWillAppear`中，下列程式碼會建立 ARKit 會話，並將3d 模型的位置設定為相對於裝置相機的空間：

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

每次應用程式執行或繼續時，3D 模型就會放在相機前方。 模型定位之後，移動相機並監看 ARKit，讓模型保持定位。

### <a name="5-pause-the-augmented-reality-session"></a>5.暫停增強的現實會話

當看不到視圖控制器時（在`ViewWillDisappear`方法中），是暫停 ARKit 會話的最佳做法：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>總結

上述程式碼會產生簡單的 ARKit 應用程式。 更複雜的範例會預期裝載增強型現實會話的 view controller 會`IARSCNViewDelegate`執行，並執行其他方法。

ARKit 提供許多更複雜的功能，例如 surface 追蹤和使用者互動。 如需結合 ARKit 追蹤與 UrhoSharp 的範例，請參閱[UrhoSharp 示範](urhosharp.md)。


## <a name="related-links"></a>相關連結

- [增強的現實（Apple）](https://developer.apple.com/arkit/)
- [搭配使用 ARKit 與 UrhoSharp](urhosharp.md)
- [Simple ARKit （Jet）範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit 放置物件（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [適用于 iOS 的 ARKit 增強現實簡介（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/602/)
