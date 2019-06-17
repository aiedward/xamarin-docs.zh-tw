---
title: 在 Xamarin.iOS 中 CoreML 簡介
description: 本文件說明 CoreML，可讓在 iOS 上的機器學習服務。 本文件討論如何開始使用 CoreML，以及如何使用它來搭配視覺架構。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3a00a7256cace9cbcff3478d866646d48cfdc50b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61385041"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>在 Xamarin.iOS 中 CoreML 簡介

CoreML 帶來 ios 的機器學習服務 – 應用程式可以利用定型的機器學習服務模型，來執行各種工作，從影像辨識到解決問題。

本簡介涵蓋下列資訊：

- [Getting Started with CoreML](#coreml)
- [CoreML 使用視覺架構](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Getting Started with CoreML

下列步驟說明如何將 CoreML 新增至 iOS 專案。 請參閱[Mars Habitat Pricer 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)如需實用範例。

![Mars Habitat 價格預測範例螢幕擷取畫面](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1.CoreML 模型新增至專案

新增 CoreML 模型 (副檔名 **.mlmodel**延伸模組) 來**資源**專案的目錄。 

在模型檔案的內容中，其**建置動作**設為**CoreMLModel**。 這表示它將會編譯成 **.mlmodelc**時建置的應用程式檔案。

### <a name="2-load-the-model"></a>2.載入模型

載入模型使用`MLModel.Create`靜態方法：

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3.設定參數

模型參數和輸出會使用傳遞的容器類別可實作`IMLFeatureProvider`。

功能提供者類別的行為類似字串的字典和`MLFeatureValue`s，其中每個功能值可以是簡單的字串、 數字、 陣列或資料或包含影像的像素緩衝區。

單一值的功能提供者的程式碼如下所示：

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

使用像這樣的類別，可以在某種程度的了解 CoreML 提供輸入的參數。 功能名稱 (例如`myParam`程式碼範例中) 必須符合模型的預期。

### <a name="4-run-the-model"></a>4.執行模型

使用模型需要的功能提供者，要具現化和參數設定，然後，`GetPrediction`方法呼叫：

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5.擷取結果

預測結果`outFeatures`也是的執行個體`IMLFeatureProvider`; 輸出值可以使用存取`GetFeatureValue`每個輸出參數的名稱 (例如`theResult`)，如這個範例所示：

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>CoreML 使用視覺架構

CoreML 也可搭配視覺架構映像，例如形狀辨識、 物件識別碼，以及其他工作上執行作業。

下列步驟說明 CoreML 與視覺中使用的方式一起[CoreMLVision 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)。 此範例會結合[矩形辨識](~/ios/platform/introduction-to-ios11/vision.md#rectangles)從之願景 framework _MNINSTClassifier_ CoreML 模型，以識別一張相片的手寫數字。

![影像辨識的數字 3](coreml-images/vision3.png) ![影像辨識的數字 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1.建立願景 CoreML 模型

CoreML 模型_MNISTClassifier_就會載入，然後包裝於`VNCoreMLModel`提供模型的視覺工作。 此程式碼也會建立兩個視覺要求： 第一次針對尋找矩形在映像，然後再針對處理 CoreML 模型的矩形：

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

類別仍必須實作`HandleRectangles`和`HandleClassification`願景的要求，步驟 3 和 4 中所示的方法。

### <a name="2-start-the-vision-processing"></a>2.啟動願景處理

下列程式碼會開始處理要求。 在  **CoreMLVision**範例中，使用者選取映像之後，執行此程式碼：

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

這個處理常式會傳遞`ciImage`願景 framework`VNDetectRectanglesRequest`步驟 1 中建立。

### <a name="3-handle-the-results-of-vision-processing"></a>3.處理願景處理的結果

矩形偵測完成後，它會執行`HandleRectangles`方法，將影像裁剪成擷取的第一個矩形，將矩形影像轉換為灰階，並將它傳遞至 CoreML 模型進行分類。

`request`傳遞至這個方法的參數會包含要求的詳細資料的願景，並使用`GetResults<VNRectangleObservation>()`方法，它會傳回一份找到的映像中的矩形。 第一個矩形`observations[0]`是擷取和傳遞給 CoreML 模型：

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

`ClassificationRequest`已在步驟 1 使用初始化`HandleClassification`下一個步驟中所定義的方法。

### <a name="4-handle-the-coreml"></a>4.處理 CoreML

`request`傳遞至這個方法的參數包含要求的詳細資料 CoreML，以及使用`GetResults<VNClassificationObservation>()`方法，它會傳回一份依信心的可能結果 (最高信賴度第一次):

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

有三個重試的 CoreML 範例：

* [Mars Habitat 價格預測範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)具有簡單的數值輸入和輸出。

* [願景和 CoreML 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)接受映像參數，並使用視覺架構找出在圖中，傳遞至 CoreML 模型可辨識的單一數字的方形區域。

* 最後， [CoreML 影像辨識範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)用以 CoreML 識別相片中的功能。 依預設它會使用較小**SqueezeNet**模型 (5 MB)，但是寫入的讓您可以下載並納入較大**VGG16**模型 (553 MB)。 如需詳細資訊，請參閱 <<c0> [ 範例的讀我檔案](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。

## <a name="related-links"></a>相關連結

- [機器學習服務 (Apple)](https://developer.apple.com/machine-learning/)
- [CoreML 範例 (Mars Habitat) （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML 與視覺 （數字辨識） （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [CoreML 影像辨識 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML 與 Azure 自訂視覺 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [簡介 CoreML (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/703/)
