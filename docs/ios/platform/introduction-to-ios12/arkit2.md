---
title: 在 Xamarin.iOS 中 ARKit 2
description: 本文件說明 ARKit iOS 12 中的更新。 它著重在使用參考的物件和影像偵測、 包含程式碼設定材質的環境，並討論 ARKit 程式設計中常見的問題。
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 559ef9cc9a3e5ace7a4023e81363825c6861f6d4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399176"
---
# <a name="arkit-2-in-xamarinios"></a>在 Xamarin.iOS 中 ARKit 2

ARKit iOS 11 中的最後一年中引進以來已經大幅的發展。 首先，您現在可以偵測垂直和水平平面，進而大幅改善的室內擴增的實境體驗的可行性。 此外，有新功能：

* 為連接點之間的真實世界和數位圖像辨識參照映像和物件
* 新的光源模式，以模擬真實光源
* 共用和保存 AR 環境的能力
* 新的檔案格式來儲存 AR 內容慣用

## <a name="recognizing-reference-objects"></a>體認到參考的物件

ARKit 2 中的一個展示功能是能夠辨識參照映像和物件。 參照映像可以載入從一般的映像檔案 ([稍後討論](#more-tracking-configurations))，但參考的物件必須掃描，使用 開發人員為焦點[ `ARObjectScanningConfiguration` ](xref:ARKit.ARObjectScanningConfiguration)。

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>範例應用程式：掃描和偵測 3D 物件

[掃描和偵測 3D 物件](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/)範例是連接埠[Apple 專案](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc)示範：

* 使用的應用程式狀態管理[ `NSNotification` ](xref:Foundation.NSNotification)物件
* 自訂視覺效果
* 複雜的筆勢
* 掃描的物件
* 儲存 [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

掃描的參考物件是使用電池及處理器密集和較舊的裝置通常會有無法達到穩定的追蹤。

### <a name="state-management-using-nsnotification-objects"></a>使用 NSNotification 物件的狀態管理

此應用程式會使用下列的狀態之間轉換的狀態機器：

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

此外會使用內嵌的組狀態，並轉換在`AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

應用程式使用文章狀態轉換通知的回應式架構[ `NSNotificationCenter` ](xref:Foundation.NSNotificationCenter) ，訂閱這些通知。 安裝程式看起來像此程式碼片段從`ViewController.cs`:

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

典型的通知處理常式會更新 UI，並可能修改應用程式狀態，例如當物件被掃描更新這個處理常式：

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

最後，`Enter{State}`方法會修改模型和 UX 視新的狀態：

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

低層級 「 點雲端 」 物件的週框方塊內包含投射到偵測到的水平平面上的應用程式顯示。

此點雲端可供開發人員[ `ARFrame.RawFeaturePoints` ](xref:ARKit.ARFrame.RawFeaturePoints)屬性。 有效率地視覺化點雲端可以是棘手的問題。 逐一查看的點，然後建立並且放入新的 SceneKit 節點，每個資料點會終止畫面播放速率。 或者，如果以非同步方式完成，會有延遲情形。 此範例會維護效能的三段式策略：

* 使用不安全的程式碼以 pin 碼中的資料放置和解譯原始緩衝區的位元組的資料。
* 轉換成該原始緩衝區[ `SCNGeometrySource` ](xref:SceneKit.SCNGeometrySource)並建立 「 範本 」 [ `SCNGeometryElement` ](xref:SceneKit.SCNGeometryElement)物件。
* 快速 「 聯結在一起 」 的未經處理資料和範本中使用 [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

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

結果看起來像這樣：

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>複雜的筆勢

使用者可以縮放、 旋轉和拖曳括住的目標物件的週框方塊。 有兩個有趣的作業，在關聯的筆勢辨識器。

首先，所有筆勢辨識器啟用只有在已通過臨界值;比方說，為已拖曳這麼多的像素為單位，或旋轉超過一些角度。 技巧是將會累積移動，直到已超過，臨界值，然後將它套用到以累加方式：

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

與筆勢關聯性來完成第二個有趣的事是週框方塊移動相關偵測到真實世界的平面的方式。 此層面會討論[此 Xamarin 部落格文章](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/)。

## <a name="other-new-features-in-arkit-2"></a>ARKit 2 中的其他新功能

### <a name="more-tracking-configurations"></a>詳細的追蹤設定

現在，您可以使用下列任一項為基礎的混合實境體驗：

* 裝置加速計 ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration)，iOS 11)
* 面對 ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration)，iOS 11)
* 參考映像 ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration)，iOS 12)
* 掃描 3D 物件 ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)，iOS 12)
* Visual 慣性 odometry ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)、 改善 iOS 12 功能)

`AROrientationTrackingConfiguration`所述[此部落格文章和F#範例](https://github.com/lobrien/FSharp_Face_AR)，是最小提供混合實境體驗不佳，因為它只會數位物件相對於裝置的動作，但不會嘗試將裝置繫結和畫面到真實世界中。

`ARImageTrackingConfiguration`可讓您辨識 （繪畫、 標誌等） 的真實世界 2D 影像，並使用這些來錨點數位圖像：

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

有兩個有趣的地方，此設定：

* 它會有效率，並可與大量的參照映像
* 數位圖像錨定至映像，即使該映像將真實世界中 （比方說，如果辨認書本的封面，則它將會追蹤本書提取現成配置相應減少，依此類推。）。

`ARObjectScanningConfiguration`討論[先前](#recognizing-reference-objects)而進行掃描 3D 物件是以開發人員中心組態。 它是高處理器和大量的電池，不應該用於使用者應用程式。 此範例[掃描和偵測 3D 物件](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/)示範如何使用這項設定。

最終的追蹤設定， `ARWorldTrackingConfiguration` ，是大部分的混合實境體驗的主力。 此組態會使用 「 visual 慣性 odometry 」，指數位圖像的真實世界的 「 功能點 」。 相對於真實世界的水平和垂直飛機錨定或相對於偵測到數位的幾何或 sprite`ARReferenceObject`執行個體。 在此組態中，世界原點是與 z 軸重力，對齊的空間中的相機的原始位置同時數位物件 「 就地 」 相對於真實世界中的物件。

### <a name="environmental-texturing"></a>材質環境

ARKit 2 支援 「 環境材質 」 用來估計光源和甚至是適用於 shiny 物件的 反射反白顯示的 擷取的影像。 環境立方體貼圖會以動態方式建立，而且一旦觀景窗有過往往所有方向，可以產生棒逼真的體驗：

![環境材質的示範映像](images/arkit_env_texturing.png)

若要使用環境的點陣圖：

* 您[ `SCNMaterial` ](xref:SceneKit.SCNMaterial)物件必須使用[ `SCNLightingModel.PhysicallyBased` ](xref:SceneKit.SCNLightingModel.PhysicallyBased)並指派為 0 到 1 的範圍內的值[ `Metalness.Contents` ](xref:SceneKit.SCNMaterial.Metalness)和[ `Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents)和
* 追蹤組態必須設定[ `EnvironmentTexturing` ](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [AREnvironmentTexturing.Automatic'](xref:ARKit.AREnvironmentTexturing.Automatic) :

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

雖然上面的程式碼片段所示的完美反射材質取樣的樂趣，環境點陣圖會可能更好搭配本例，以免它觸發 （紋理是只根據哪些相機是估計值 」 好 valley"回應記錄）。


### <a name="shared-and-persistent-ar-experiences"></a>共用和持續性的 AR 體驗

是另一個主要 ARKit 2 新增[ `ARWorldMap` ](xref:ARKit.ARWorldMap)類別，可讓您共用或儲存全球追蹤資料。 取得與目前的世界地圖[ `ARSession.GetCurrentWorldMapAsync` ](xref:ARKit.ARSession.GetCurrentWorldMapAsync)或是[ `GetCurrentWorldMap(Action<ARWorldMap,NSError>` ](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

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

若要共用，或還原世界地圖：

1. 從檔案載入資料
2. 將到`ARWorldMap`物件，
3. 做為值使用該[ `ARWorldTrackingConfiguration.InitialWorldMap` ](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap)屬性：

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

`ARWorldMap`只包含不可見的世界追蹤資料並[ `ARAnchor` ](xref:ARKit.ARAnchor)物件，它會_不_包含數位資產。 若要共用幾何或影像，您必須開發您自己的策略適用於您的使用案例 (或許是由儲存/傳輸只的位置和方向的幾何，並將它套用至靜態`SCNGeometry`或可能是透過儲存/傳輸序列化的物件）。 優點`ARWorldMap`是資產，一次放置相對於共用`ARAnchor`，會以一致的方式顯示裝置或工作階段之間。

### <a name="universal-scene-description-file-format"></a>通用的場景描述檔案格式

ARKit 2 的最後一個標題功能是 Apple 的 Pixar 的採用[通用的場景描述](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html)檔案格式。 此格式會取代的 Collada DAE 格式做為慣用的格式，以共用及儲存 ARKit 資產。 以視覺化方式呈現資產的支援內建 iOS 12 和 Mojave。 USDZ 檔案延伸模組是包含 USD 檔案未壓縮和未加密的 zip 封存。 Pixar[提供的工具使用美元檔案](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit)但尚未有許多協力廠商的支援。

## <a name="arkit-programming-tips"></a>ARKit 程式設計提示

### <a name="manual-resource-management"></a>手動資源管理

在 ARKit，就必須以手動方式管理資源。 不只這允許高的畫面播放速率，實際上就_必要_以避免混淆 」 畫面凍結 」。 ARKit framework 會提供新的相機框架的相關延遲 ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame)。 到目前[ `ARFrame` ](xref:ARKit.ARFrame)已`Dispose()`在它上面呼叫，ARKit 就不會提供新的框架 ！ 這會導致 「 凍結 」 即使應用程式的其餘部分是回應式的影片。 解決方法是一律存取`ARSession.CurrentFrame`具有`using`封鎖或手動呼叫`Dispose()`在其上。

所有物件都衍生自`NSObject`都`IDisposable`並`NSObject`實作[處置模式](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern)，因此您通常應該遵循[實作此模式`Dispose`上都衍生類別](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。

### <a name="manipulating-transform-matrices"></a>操作轉換矩陣

在任何 3D 應用程式，你會處理簡潔地說明如何移動、 旋轉及傾斜物件，可透過 3D 空間的 4x4 轉換矩陣。 這些是在 SceneKit [ `SCNMatrix4` ](xref:SceneKit.SCNMatrix4)物件。  

[ `SCNNode.Transform` ](xref:SceneKit.SCNNode.Transform)屬性會傳回`SCNMatrix4`轉換矩陣[ `SCNNode` ](xref:SceneKit.SCNNode) _為後盾_資料列為主`simdfloat4x4`型別。 因此，例如：

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

如您所見，底部資料列的前三個項目被編碼的位置。

在 Xamarin，來操作轉換矩陣的一般型別是`NVector4`，其依慣例會解譯的資料行做為主要方式。 也就是說，M14 M24、 M34、 未 M41、 M42、 M43 中預期的轉譯/位置部分：

![以列為主要與資料行做為主要](images/arkit_row_vs_column.png)

取得矩陣解譯選擇一致的結果是適當的行為很重要。 由於 3D 轉換矩陣是 4 x 4，一致性錯誤並不會產生任何種類的編譯時期或甚至是執行階段例外狀況，它只是作業將會非預期地運作。 如果您的 SceneKit / ARKit 物件似乎會停滯，飛，或抖動，不正確的轉換矩陣是很好的可能性。 解決方法很簡單： [ `NMatrix4.Transpose` ](xref:OpenTK.NMatrix4.Transpose*)會執行就地調換，項目。

## <a name="related-links"></a>相關連結

- [範例應用程式 – 掃描和偵測 3D 物件](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [什麼是 ARKit 2 (WWDC 2018) 的新功能](https://developer.apple.com/videos/play/wwdc2018/602/)
- [了解 ARKit 追蹤和偵測 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [在 Xamarin.iOS 中 ARKit 簡介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
