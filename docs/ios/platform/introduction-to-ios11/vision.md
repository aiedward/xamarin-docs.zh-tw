---
title: Xamarin 中的視覺架構
description: 本檔說明如何在 Xamarin 中使用 iOS 11 視覺架構。 具體而言，它會討論矩形偵測和臉部偵測。
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: 89a21eec369691e5c6e1ec8ce2430d679b6b309d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572139"
---
# <a name="vision-framework-in-xamarinios"></a>Xamarin 中的視覺架構

視覺架構會在 iOS 11 新增一些新的影像處理功能，包括：

- [矩形偵測](#rectangles)
- [臉部偵測](#faces)
- Machine Learning 影像分析（在[CoreML](~/ios/platform/introduction-to-ios11/coreml.md)中討論）
- 條碼偵測
- 影像對齊分析
- 文字偵測
- 水準偵測
- & 追蹤的物件偵測

![已偵測到三個矩形的相片](vision-images/found-rectangles-tiny.png) ![偵測到具有兩個臉部的相片](vision-images/xamarin-home-faces-tiny.png)

下面將更詳細地討論矩形偵測和臉部偵測。

<a name="rectangles"></a>

## <a name="rectangle-detection"></a>矩形偵測

[VisionRects 範例會示範](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)如何處理影像，並在其上繪製偵測到的矩形。

### <a name="1-initialize-the-vision-request"></a>1. 將願景要求初始化

在中 `ViewDidLoad` ，建立 `VNDetectRectanglesRequest` 參考 `HandleRectangles` 將在每個要求結束時呼叫之方法的：

`MaximumObservations`也應該設定屬性，否則它會預設為1，而且只會傳回單一結果。

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. 開始視覺處理

下列程式碼會開始處理要求。 在**VisionRects**範例中，此程式碼會在使用者選取影像之後執行：

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此處理程式會將傳遞 `ciImage` 至 `VNDetectRectanglesRequest` 在步驟1中建立的願景架構。

### <a name="3-handle-the-results-of-vision-processing"></a>3. 處理視覺處理的結果

完成矩形偵測之後，架構就會執行 `HandleRectangles` 方法，其摘要如下所示：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. 顯示結果

`OverlayRectangles` **VisionRectangles**範例中的方法有三個功能：

- 呈現來源影像，
- 繪製矩形以指出每個偵測到的位置，以及
- 使用 CoreGraphics 為每個矩形加入文字標籤。

針對確切的 CoreGraphics 方法，查看[範例的來源](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)。

![已偵測到三個矩形的相片](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. 進一步處理

矩形偵測通常只是一連串作業的第一個步驟，例如使用[此 CoreMLVision 範例](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision)，其中矩形會傳遞至 CoreML 模型以剖析手寫數位。

<a name="faces"></a>

## <a name="face-detection"></a>臉部偵測

[VisionFaces 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)的運作方式與使用不同視覺要求類別的**VisionRectangles**範例相同。

### <a name="1-initialize-the-vision-request"></a>1. 將願景要求初始化

在中 `ViewDidLoad` ，建立 `VNDetectFaceRectanglesRequest` 參考 `HandleRectangles` 將在每個要求結束時呼叫之方法的。

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. 開始視覺處理

下列程式碼會開始處理要求。 在**VisionFaces**範例中，此程式碼會在使用者選取影像之後執行：

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

此處理程式會將傳遞 `ciImage` 至 `VNDetectFaceRectanglesRequest` 在步驟1中建立的願景架構。

### <a name="3-handle-the-results-of-vision-processing"></a>3. 處理視覺處理的結果

一旦臉部偵測完成，處理常式就會執行 `HandleRectangles` 執行錯誤處理的方法，並顯示所偵測到臉部的界限，並呼叫 `OverlayRectangles` 來繪製原始圖片上的周框矩形：

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. 顯示結果

`OverlayRectangles` **VisionFaces**範例中的方法有三個功能：

- 呈現來源影像，
- 針對每個偵測到的臉部繪製一個矩形，以及
- 使用 CoreGraphics 為每個臉部新增文字標籤。

針對確切的 CoreGraphics 方法，查看[範例的來源](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)。

![偵測到具有兩個臉部的相片](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. 進一步處理

視覺架構包含偵測臉部功能的額外功能，例如眼睛和嘴。 使用 `VNDetectFaceLandmarksRequest` 類型，這會傳回 `VNFaceObservation` 上述步驟3中的結果，但包含其他 `VNFaceLandmark` 資料。

## <a name="related-links"></a>相關連結

- [視覺矩形（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)
- [視覺臉部（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)
- [核心影像的進展-篩選、金屬、願景等等（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/510/)
