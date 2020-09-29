---
title: 在 Xamarin 中 ARKit 簡介
description: 本檔說明使用 ARKit 的 iOS 11 增強型事實。 它會討論如何將3D 模型新增至應用程式、設定 view、執行會話委派、將3D 模型放在世界中，以及暫停增強的現實會話。
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 6803ecf2303ff2c91265f3ac8352a7aa15e74d40
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436205"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>在 Xamarin 中 ARKit 簡介

_IOS 11 的增強現實_

ARKit 可提供各式各樣的增強現實應用程式和遊戲。 本節包含下列主題：

- [使用 ARKit 消費者入門](#gettingstarted)
- [使用 ARKit 搭配 UrhoSharp](urhosharp.md)

<a name="gettingstarted"></a>

## <a name="getting-started-with-arkit"></a>使用 ARKit 消費者入門

若要開始增強的現實，下列指示將逐步解說簡單的應用程式：定位3D 模型，並讓 ARKit 將模型與追蹤功能保持在原處。

![以相機影像浮動的 Jet 3D 模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. 新增3D 模型

資產應使用 **SceneKitAsset** 組建動作加入至專案。

![SceneKit 專案中的資產](images/scene-assets.png)

### <a name="2-configure-the-view"></a>2. 設定 view

在 view 控制器的 `ViewDidLoad` 方法中，載入場景資產，並 `Scene` 在視圖上設定屬性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. 選擇性地執行會話委派

雖然簡單的案例不需要這樣做，但是在實際的應用程式中，將 ARKit 會話的狀態 (和實際的應用程式中，提供意見反應給使用者) 會很有説明。 使用下列程式碼建立簡單委派：

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

在方法中指派委派 `ViewDidLoad` ：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. 將3D 模型放在世界中

在中 `ViewWillAppear` ，下列程式碼會建立 ARKit 會話，並將3d 模型的位置設定為相對於裝置相機的空間：

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

每次執行或繼續執行應用程式時，3D 模型都會放置在相機前方。 一旦模型定位之後，移動攝影機並監看 ARKit 會讓模型保持定位。

### <a name="5-pause-the-augmented-reality-session"></a>5. 暫停增強的現實會話

在方法中 (看不到視圖控制器時，暫停 ARKit 會話是很好的作法 `ViewWillDisappear` ：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>摘要

上述程式碼會產生簡單的 ARKit 應用程式。 更複雜的範例會預期裝載增強型現實會話的 view 控制器會執行 `IARSCNViewDelegate` ，並執行其他方法。

ARKit 提供許多更複雜的功能，例如面追蹤和使用者互動。 如需結合 ARKit 追蹤與 UrhoSharp 的範例，請參閱 [UrhoSharp 示範](urhosharp.md) 。

## <a name="related-links"></a>相關連結

- [ (Apple) 的擴充現實 ](https://developer.apple.com/arkit/)
- [使用 ARKit 搭配 UrhoSharp](urhosharp.md)
- [簡易 ARKit (Jet) 範例](/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit 將物件放 (範例) ](/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [介紹 ARKit-增強的 iOS (WWDC)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/602/)