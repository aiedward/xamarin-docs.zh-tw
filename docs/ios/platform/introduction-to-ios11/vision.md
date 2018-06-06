---
title: Xamarin.iOS 中的目標 Framework
description: 本文件說明如何使用 iOS 11 Xamarin.iOS 中的目標架構。 具體來說，它討論矩形偵測和面對偵測。
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2016
ms.openlocfilehash: c44c4b3ab12c1ba448f1befb6f831f5ad9119f18
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787415"
---
# <a name="vision-framework-in-xamarinios"></a>Xamarin.iOS 中的目標 Framework

目標 framework 中加入一些新的映像處理功能 ios 11 中，包括：

- [矩形偵測](#rectangles)
- [正面偵測](#faces)
- 機器學習影像分析 (討論[CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- 條碼偵測
- 影像的對齊方式分析
- 文字偵測
- 水平偵測
- 物件的偵測和追蹤

![偵測到的三個矩形會拍攝相片](vision-images/found-rectangles-tiny.png) ![相片與偵測到的兩面](vision-images/xamarin-home-faces-tiny.png)

以下更詳細討論矩形偵測和臉偵測。

<a name="rectangles" />

## <a name="rectangle-detection"></a>矩形偵測

[VisionRects 範例](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)示範如何處理映像，並在其上繪製偵測到的矩形。

### <a name="1-initialize-the-vision-request"></a>1.初始化願景要求

在`ViewDidLoad`，建立`VNDetectRectanglesRequest`參考`HandleRectangles`在每個要求結束時呼叫的方法：

`MaximumObservations`屬性也應該設定，否則它會預設為 1，並將傳回單一結果。

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2.開始願景處理

下列程式碼會開始處理要求。 在**VisionRects**範例使用者選取映像之後，執行此程式碼：

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

這個處理常式會將傳遞`ciImage`願景 framework`VNDetectRectanglesRequest`步驟 1 中建立。

### <a name="3-handle-the-results-of-vision-processing"></a>3.處理願景處理的結果

矩形偵測完成後，framework 就會執行`HandleRectangles`方法，如下所示的摘要：

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

### <a name="4-display-the-results"></a>4.顯示結果

`OverlayRectangles`方法中的**VisionRectangles**範例有三個函式：

- 呈現來源映像
- 繪製矩形，表示每個偵測到程式碼，並
- 加入使用 CoreGraphics 每個矩形的文字標籤。

檢視[範例的來源](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)確切 CoreGraphics 方法。

![偵測到的三個矩形會拍攝相片](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5.進一步處理

矩形偵測通常只是第一個步驟鏈結中的作業，例如與[CoreMLVision 本例](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision)，其中矩形會傳遞至 CoreML 模型剖析手寫的數字。


<a name="faces" />

## <a name="face-detection"></a>正面偵測

[VisionFaces 範例](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)以類似的方式運作**VisionRectangles**取樣，請使用不同的願景要求類別。

### <a name="1-initialize-the-vision-request"></a>1.初始化願景要求

在`ViewDidLoad`，建立`VNDetectFaceRectanglesRequest`參考`HandleRectangles`在每個要求結束時呼叫的方法。

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2.開始願景處理

下列程式碼會開始處理要求。 在**VisionFaces**使用者選取映像之後，執行此程式碼的範例：

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

這個處理常式會將傳遞`ciImage`願景 framework`VNDetectFaceRectanglesRequest`步驟 1 中建立。

### <a name="3-handle-the-results-of-vision-processing"></a>3.處理願景處理的結果

處理常式的臉偵測完成後，在執行`HandleRectangles`方法會執行錯誤處理，並顯示的偵測到的字體，然後呼叫界限`OverlayRectangles`原始圖片上繪製週框矩形：

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

### <a name="4-display-the-results"></a>4.顯示結果

`OverlayRectangles`方法中的**VisionFaces**範例有三個函式：

- 呈現來源映像
- 繪製矩形，偵測到，每一面和
- 加入使用 CoreGraphics 每一面的文字標籤。

檢視[範例的來源](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)確切 CoreGraphics 方法。

![相片與偵測到的兩面](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5.進一步處理

目標 framework 包括額外的功能，可偵測臉部特徵，例如眼睛和說話。 使用`VNDetectFaceLandmarksRequest`型別，會傳回`VNFaceObservation`結果，如上述，步驟 3 所示，但具有額外`VNFaceLandmark`資料。


## <a name="related-links"></a>相關連結

- [願景矩形 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [願景字體 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [進展 Core 映像篩選器、 金屬、 願景和更 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/510/)
