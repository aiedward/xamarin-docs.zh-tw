---
title: Xamarin 中的 ARKit 2
description: 本檔描述 iOS 12 中 ARKit 的更新。 其重點放在使用參考物件和影像進行偵測，包括適用于環境紋理的程式碼，並討論 ARKit 程式設計的常見問題。
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/22/2018
ms.openlocfilehash: 11c106483a98e4cd1412a6edb185d5da42da61ea
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032047"
---
# <a name="arkit-2-in-xamarinios"></a>Xamarin 中的 ARKit 2

ARKit 自過去一年的 iOS 11 開始已經大幅成熟。 首先，最重要的是，您現在可以偵測垂直和水準平面，以大幅改善室內增強型現實體驗的實用性。 此外，還有一些新功能：

- 辨識參照影像和物件作為真實世界與數位影像之間的連接
- 模擬真實世界光源的新光源模式
- 共用和保存 AR 環境的能力
- 儲存 AR 內容時慣用的新檔案格式

## <a name="recognizing-reference-objects"></a>識別參考物件

ARKit 2 中的一個展示功能是辨識參照影像和物件的能力。 您可以從一般影像檔案載入參考影像（[稍後會討論](#more-tracking-configurations)），但必須使用以開發人員為主的[`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)來掃描參考物件。

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>範例應用程式：掃描和偵測3D 物件

「[掃描和偵測3D 物件](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)」範例是[Apple 專案](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc)的埠，示範：

- 使用[`NSNotification`](xref:Foundation.NSNotification)物件的應用程式狀態管理
- 自訂視覺效果
- 複雜手勢
- 物件掃描
- 儲存[`ARReferenceObject`](xref:ARKit.ARReferenceObject)

掃描參考物件是需要大量電池和處理器，而且舊的裝置通常無法達到穩定追蹤的問題。

### <a name="state-management-using-nsnotification-objects"></a>使用 NSNotification 物件的狀態管理

此應用程式會使用在下列狀態之間轉換的狀態機器：

- `AppState.StartARSession`
- `AppState.NotReady`
- `AppState.Scanning`
- `AppState.Testing`

此外，當 `AppState.Scanning`時，還會使用內嵌的狀態集和轉換：

- `Scan.ScanState.Ready`
- `Scan.ScanState.DefineBoundingBox`
- `Scan.ScanState.Scanning`
- `Scan.ScanState.AdjustingOrigin`

應用程式會使用回應式架構，將狀態轉換通知張貼至[`NSNotificationCenter`](xref:Foundation.NSNotificationCenter)並訂閱這些通知。 安裝程式看起來就像此程式碼片段來自 `ViewController.cs`：

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

一般通知處理常式會更新 UI，而且可能會修改應用程式狀態，例如在掃描物件時更新的這個處理常式：

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

最後，`Enter{State}` 方法會將模型和 UX 修改為適用于新的狀態：

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>自訂視覺效果

應用程式會顯示包含在已偵測到之水準平面的周框方塊內之物件的低層級「點雲端」。

此雲端可供開發人員在[`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints)屬性中使用。 有效率地將點雲端視覺化可能會是一種棘手的問題。 逐一查看點，然後針對每個點建立並放置新的 SceneKit 節點，將會終止畫面播放速率。 或者，如果以非同步方式完成，則會有延遲。 此範例會使用三個部分的策略來維護效能：

- 使用 unsafe 程式碼來就地釘選資料，並將資料解讀為位元組的原始緩衝區。
- 將該原始緩衝區轉換成[`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) ，並建立 "template" [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement)物件。
- 使用[`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))快速將原始資料和範本「一起裝訂」

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

結果如下所示：

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>複雜手勢

使用者可以縮放、旋轉和拖曳環繞目標物件的周框方塊。 相關聯的手勢辨識器中有兩個有趣的專案。

首先，所有手勢辨識器都會在通過閾值之後啟動;例如，手指已拖曳太多圖元，或旋轉超過某個角度。 這項技術是累積移動直到超過閾值為止，然後以累加方式套用：

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

相對於手勢的第二個有趣的事，就是將周框方塊與偵測到的真實世界平面相對應的移動方式。 此方面會在[Xamarin blog 文章](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/)中討論。

## <a name="other-new-features-in-arkit-2"></a>ARKit 2 中的其他新功能

### <a name="more-tracking-configurations"></a>其他追蹤設定

現在，您可以使用下列任何一項做為混合現實體驗的基礎：

- 僅限裝置加速計（[`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration)、iOS 11）
- 臉部（[`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration)、iOS 11）
- 參考影像（[`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration)、iOS 12）
- 掃描3D 物件（[`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)、iOS 12）
- Visual 慣性 odometry （[`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)，在 iOS 12 中改進）

[這篇 blog 文章F#和範例](https://github.com/lobrien/FSharp_Face_AR)中所討論的 `AROrientationTrackingConfiguration`，是最受限制的，並提供不佳的混合現實體驗，因為它只會將數位物件與裝置的運動相關，而不會嘗試將裝置與螢幕系結到真正的成為.

此 `ARImageTrackingConfiguration` 可讓您辨識真實世界的2D 影像（畫作、標誌等等），並使用它們來錨定數位影像：

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

此設定有兩個有趣的層面：

- 它很有效率，而且可以與可能大量的參考影像搭配使用
- 數位影像會錨定到影像，即使該影像會在真實世界中移動（例如，如果已辨識書的封面，它也會追蹤從貨位拉出的書、配置給下等）。

`ARObjectScanningConfiguration`先前[已討論過](#recognizing-reference-objects), 而且是以開發人員為中心的設定, 用於掃描3d 物件。 這是高度處理器和耗用電池，不應用於終端使用者應用程式。 [掃描和偵測3D 物件](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)範例示範如何使用此設定。

最後的追蹤設定（`ARWorldTrackingConfiguration`）是大部分混合現實體驗的主力。 此設定會使用「視覺慣性 odometry」，將真實世界的「功能點」與數位影像產生關聯。 數位幾何或 sprite 會錨定相對於真實世界水準和垂直平面，或相對於偵測到的 `ARReferenceObject` 實例。 在此設定中，世界原點是相機在空間中的原始位置，其中的 Z 軸對齊重心，而數位物件則相對於真實世界中的物件保持原狀。

### <a name="environmental-texturing"></a>環境紋理

ARKit 2 支援「環境紋理」，其使用已捕捉的影像來估計光源，甚至將反射高光套用到光亮物件。 環境立方體貼圖是以動態方式建立的，一旦攝影機一查看所有方向，就可以產生更棒是的實際體驗：

![環境紋理示範影像](images/arkit_env_texturing.png)

若要使用環境紋理：

- 您的[`SCNMaterial`](xref:SceneKit.SCNMaterial)物件必須使用[`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) ，並為[`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness)和[`Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents)指派介於0到1之間的值。
- 您的追蹤設定必須將[`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing) = [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) ：

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

雖然上述程式碼片段中所顯示的完美反射材質在範例中很有趣，但環境紋理可能更適合用於以免，它會觸發「uncanny 谷」的回應（紋理只是根據相機的估計值已記錄）。

### <a name="shared-and-persistent-ar-experiences"></a>共用和持續性 AR 體驗

ARKit 2 的另一個主要新增功能是[`ARWorldMap`](xref:ARKit.ARWorldMap)類別，可讓您共用或儲存世界追蹤資料。 您可以使用[`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync)或[`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError}))取得目前的世界地圖：

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

若要共用或還原世界地圖：

1. 從檔案載入資料，
2. 將它將取消封存到 `ARWorldMap` 物件中，
3. 使用它做為[`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap)屬性的值：

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

`ARWorldMap` 只包含非可見的世界追蹤資料和[`ARAnchor`](xref:ARKit.ARAnchor)物件，而_不_包含數位資產。 若要共用幾何或影像，您必須自行開發適用于您使用案例的策略（可能是只儲存/傳輸幾何的位置和方向，並將其套用至靜態 `SCNGeometry` 或儲存/傳輸）序列化的物件）。 `ARWorldMap` 的優點是，一旦放置相對於共用 `ARAnchor`的資產，就會在裝置或會話之間一致地出現。

### <a name="universal-scene-description-file-format"></a>通用場景描述檔案格式

ARKit 2 的最後頭條新聞功能是 Apple 採用 Pixar 的[通用場景描述](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html)檔案格式。 此格式會取代 Collada 的 DAE 格式，做為共用和儲存 ARKit 資產的慣用格式。 IOS 12 和 Mojave 內建對視覺化資產的支援。 USDZ 副檔名是未壓縮和未加密的 zip 封存，其中包含美元的檔案。 Pixar[提供用來處理美元檔案的工具](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit)，但還沒有太多協力廠商支援。

## <a name="arkit-programming-tips"></a>ARKit 程式設計秘訣

### <a name="manual-resource-management"></a>手動管理資源

在 ARKit 中，請務必手動管理資源。 這不只會允許高畫面播放速率，而是_必須_避免混淆的「螢幕凍結」。 ARKit 架構與提供新的相機框架（[`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame)的延遲有關。 在目前的[`ARFrame`](xref:ARKit.ARFrame)已經呼叫 `Dispose()` 之後，ARKit 將不會提供新的框架！ 這會導致影片「凍結」，即使應用程式的其餘部分仍有回應。 解決方法是一律使用 `using` 區塊來存取 `ARSession.CurrentFrame`，或在其上手動呼叫 `Dispose()`。

衍生自 `NSObject` 的所有物件都會 `IDisposable`，而且 `NSObject` 會實作為[處置模式](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern)，因此您通常應該遵循[這個模式，以在衍生類別上執行 `Dispose`](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。

### <a name="manipulating-transform-matrices"></a>操作轉換矩陣

在任何3D 應用程式中，您將會處理4x4 轉換矩陣，簡潔地描述如何透過3D 空間來移動、旋轉和傾斜物件。 在 SceneKit 中，這些是[`SCNMatrix4`](xref:SceneKit.SCNMatrix4)物件。  

[`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform)屬性會傳回的 `SCNMatrix4` 轉換矩陣， [`SCNNode`](xref:SceneKit.SCNNode) _這是由_資料列主要 `simdfloat4x4` 型別所支援。 例如：

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

如您所見，位置是在底部資料列的前三個元素中編碼。

在 Xamarin 中，用來操作轉換矩陣的一般類型是 `NVector4`，依照慣例，會以資料行主要的方式來轉譯。 也就是說，M14、M24、M34、not M41、M42、Imb-m43 中應該要有轉譯/位置元件：

![資料列-主要與資料行-主要](images/arkit_row_vs_column.png)

與選擇的矩陣轉譯一致，對於適當的行為非常重要。 因為3D 轉換矩陣是4x4，所以一致性錯誤不會產生任何類型的編譯時間或甚至執行時間例外狀況，只是作業會非預期地採取動作。 如果您的 SceneKit/ARKit 物件似乎停滯、飛出或抖動，則不正確的轉換矩陣是很好的可能性。 解決方法很簡單： [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*)將會執行專案的就地調換。

## <a name="related-links"></a>相關連結

- [範例應用程式–掃描和偵測3D 物件](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [ARKit 2 的新功能（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/602/)
- [瞭解 ARKit 追蹤和偵測（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Xamarin 中的 ARKit 簡介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
