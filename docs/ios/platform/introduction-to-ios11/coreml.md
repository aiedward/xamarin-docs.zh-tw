---
title: 在 Xamarin 中 CoreML 簡介
description: 本檔說明 CoreML，可在 iOS 上啟用機器學習。 本檔討論如何開始使用 CoreML，以及如何將它與視覺架構搭配使用。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 875ae9c4712c974c663854f7790c51111eea4807
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434508"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>在 Xamarin 中 CoreML 簡介

CoreML 將機器學習帶到 iOS –應用程式可利用定型的機器學習模型來執行各種工作，從解決問題到影像辨識。

本簡介涵蓋下列各項：

- [使用 CoreML 消費者入門](#coreml)
- [搭配使用 CoreML 與視覺架構](#coremlvision)

<a name="coreml"></a>

## <a name="getting-started-with-coreml"></a>使用 CoreML 消費者入門

這些步驟說明如何將 CoreML 新增至 iOS 專案。 如需實用範例，請參閱 [Mars Habitat Pricer 範例](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) 。

![Mars Habitat 價格預測器範例螢幕擷取畫面](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. 將 CoreML 模型新增至專案

將 CoreML 模型 (**mlmodel**) 副檔名的檔案新增至專案的 **Resources** 目錄。 

在模型檔案的屬性中，其 **組建動作** 會設定為 **CoreMLModel**。 這表示當建立應用程式時，它會編譯成**mlmodelc 檔案。**

### <a name="2-load-the-model"></a>2. 載入模型

使用靜態方法載入模型 `MLModel.Create` ：

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. 設定參數

模型參數會使用實作為的容器類別傳入和傳出 `IMLFeatureProvider` 。

功能提供者類別的行為就像是字串和的字典 `MLFeatureValue` ，其中每個功能值都可以是簡單字串或數位、陣列或資料，或是包含影像的圖元緩衝區。

單一值功能提供者的程式碼如下所示：

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

使用像這樣的類別，就能以 CoreML 瞭解的方式來提供輸入參數。 在程式碼範例) 的功能 (的名稱 `myParam` 必須符合模型所預期的名稱。

### <a name="4-run-the-model"></a>4. 執行模型

使用模型需要具現化功能提供者並設定參數，然後 `GetPrediction` 呼叫方法：

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. 將結果解壓縮

預測結果 `outFeatures` 也是的實例 `IMLFeatureProvider` ; 您可以使用 `GetFeatureValue` 每個輸出參數的名稱來存取輸出值 (例如 `theResult`) ，如下列範例所示：

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision"></a>

## <a name="using-coreml-with-the-vision-framework"></a>搭配使用 CoreML 與視覺架構

CoreML 也可以與視覺架構搭配使用，以在影像上執行作業，例如圖形辨識、物件識別和其他工作。

下列步驟說明如何在 [CoreMLVision 範例](/samples/xamarin/ios-samples/ios11-coremlvision)中搭配使用 CoreML 和願景。 此範例結合視覺效果中的 [矩形](~/ios/platform/introduction-to-ios11/vision.md#rectangles) 辨識與 _MNINSTClassifier_ CoreML 模型，以識別相片中的手寫數位。

![數位3的影像辨識](coreml-images/vision3.png) ![數位5的影像辨識](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. 建立視覺 CoreML 模型

CoreML 模型 _MNISTClassifier_ 會載入，然後包裝在中， `VNCoreMLModel` 讓模型可供視覺工作使用。 這段程式碼也會建立兩個視覺要求：先尋找影像中的矩形，然後使用 CoreML 模型處理矩形：

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

類別仍需要 `HandleRectangles` `HandleClassification` 針對視覺要求執行和方法，如下列步驟3和4所示。

### <a name="2-start-the-vision-processing"></a>2. 開始視覺處理

下列程式碼會開始處理要求。 在 **CoreMLVision** 範例中，此程式碼會在使用者選取影像之後執行：

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

此處理程式會將移 `ciImage` 至 `VNDetectRectanglesRequest` 在步驟1中建立的視覺架構。

### <a name="3-handle-the-results-of-vision-processing"></a>3. 處理視覺處理的結果

矩形偵測完成後，它會執行 `HandleRectangles` 方法，該方法會裁剪影像以解壓縮第一個矩形、將矩形影像轉換成灰階，並將它傳遞至 CoreML 模型進行分類。

`request`傳遞給這個方法的參數包含視覺要求的詳細資料，並使用 `GetResults<VNRectangleObservation>()` 方法，它會傳回在影像中找到的矩形清單。 第一個矩形 `observations[0]` 會解壓縮並傳遞至 CoreML 模型：

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

`ClassificationRequest`在步驟1中已初始化，以使用 `HandleClassification` 下一個步驟中定義的方法。

### <a name="4-handle-the-coreml"></a>4. 處理 CoreML

`request`傳遞給這個方法的參數包含 CoreML 要求的詳細資料，並使用 `GetResults<VNClassificationObservation>()` 方法，它會傳回一份可能的結果清單，並以信賴度為優先 (最高的信賴度) ：

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>範例

您可以嘗試三個 CoreML 範例：

- [Mars Habitat 價格預測器範例](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)具有簡單的數值輸入和輸出。

- [願景 & CoreML 範例](/samples/xamarin/ios-samples/ios11-coremlvision)會接受影像參數，並使用視覺架構來識別影像中的正方形區域，這些區域會傳遞至可辨識單一數位的 CoreML 模型。

- 最後， [CoreML 影像辨識範例](/samples/xamarin/ios-samples/ios11-coremlimagerecognition) 會使用 CoreML 來識別相片中的功能。 根據預設，它會使用較小的 **SqueezeNet** 模型 (5mb) ，但它已寫入，因此您可以下載較大的 **VGG16** 模型 (553MB) 。 如需詳細資訊，請參閱 [範例的讀我檔案](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。

## <a name="related-links"></a>相關連結

- [Machine Learning (Apple) ](https://developer.apple.com/machine-learning/)
- [CoreML 範例 (Mars Habitat)  (範例) ](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [CoreML 和視覺 (數位辨識)  (範例) ](/samples/xamarin/ios-samples/ios11-coremlvision)
- [CoreML 影像辨識 (範例) ](/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [CoreML 與 Azure 自訂視覺 (範例) ](/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [介紹 CoreML (WWDC)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/703/)