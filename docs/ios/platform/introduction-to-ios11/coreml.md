---
title: 在 Xamarin.iOS CoreML 簡介
description: 本文件說明 CoreML，可讓在 iOS 上的機器學習。 本文將討論如何開始使用 CoreML 以及如何使用目標 framework。
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: b893fe5e56cc2d43a71870ffbbd20f0b8c6cfd18
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787490"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>在 Xamarin.iOS CoreML 簡介

_機器學習 iOS 11 上的行動裝置應用程式_

CoreML 帶來 ios 的機器學習 – 應用程式可以利用定型的機器學習模型，以執行各種工作，從映像辨識來解決問題。

本簡介涵蓋下列資訊：

- [開始使用 CoreML](#coreml)
- [使用目標 framework CoreML](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>開始使用 CoreML

下列步驟說明如何將 CoreML 加入 iOS 的專案。 請參閱[Mars 展示區 Pricer 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)的實際範例。

![Mars 展示區預測工具範例螢幕擷取畫面](coreml-images/marspricer-heading.png)

### <a name="1-add-the-model-to-the-project"></a>1.將模型加入至專案

將編譯過的模型 (與目錄 **.modelc**延伸模組) 以**資源**專案的目錄。 目錄的內容都應該具備的建置動作**BundleResource**:

![[資源] 資料夾應該包含編譯的模型](coreml-images/resources-modelc.png)

[範例](https://developer.xamarin.com/samples/monotouch/ios11/)使用模型在 Xcode 9 中編譯，或手動使用下列終端機的命令：

```csharp
xcrun coremlcompiler compile {model.mlmodel} {outputFolder}
```

> [!NOTE]
> **.model**檔案_必須_編譯為 **.modelc**可供 CoreML 之前

### <a name="2-load-the-model"></a>2.載入模型

之前使用模型，其使用載入`MLModel.FromUrl`靜態方法：

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.FromUrl(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3.設定參數

模型的參數傳遞入和移出使用容器類別可實作`IMLFeatureProvider`。

功能提供者類別的行為就如同字典的字串和`MLFeatureValue`s，其中每個功能值可以是簡單的字串或數字、 陣列或資料或包含影像像素緩衝區。

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

使用像這樣的類別，可以了解 CoreML 的方式提供輸入的參數。 功能的名稱 (例如`myParam`程式碼範例中) 必須符合模型的預期。

### <a name="4-run-the-model"></a>4.執行模型

使用模型要求的功能提供者具現化和參數設定，然後，`GetPrediction`方法呼叫：

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5.擷取結果

預測結果`outFeatures`也是的執行個體`IMLFeatureProvider`; 輸出值可以使用存取`GetFeatureValue`與每個輸出參數的名稱 (例如`theResult`)，如這個範例所示：

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>使用目標 Framework CoreML

CoreML 映像，例如圖形辨識、 物件識別和其他工作上執行作業也願景 framework 搭配使用。

下列步驟說明 CoreML 和願景中使用的方式一起[CoreMLVision 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)。 範例會結合[矩形辨識](~/ios/platform/introduction-to-ios11/vision.md#rectangles)之目標 framework 從_MNINSTClassifier_ CoreML 模型，以識別一張相片的手寫數字。

![影像辨識的數字 3](coreml-images/vision3.png) ![影像辨識的數字 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1.建立 Vision CoreML 模型

CoreML 模型_MNISTClassifier_載入與然後包裝於`VNCoreMLModel`提供模型的願景工作。 此程式碼也會建立兩個願景要求： 第一次針對尋找矩形在映像，然後再針對處理 CoreML 模型的矩形：

```csharp
// Load the ML model
var assetPath = NSBundle.MainBundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
var mlModel = MLModel.FromUrl(assetPath, out NSError mlErr);
var vModel = VNCoreMLModel.FromMLModel(mlModel, out NSError vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(vModel, HandleClassification);
```

類別仍然需要實作`HandleRectangles`和`HandleClassification`願景的要求，步驟 3 和 4 底下所示的方法。

### <a name="2-start-the-vision-processing"></a>2.開始願景處理

下列程式碼會開始處理要求。 在**CoreMLVision**範例使用者選取映像之後，執行此程式碼：

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

這個處理常式會將傳遞`ciImage`願景 framework`VNDetectRectanglesRequest`步驟 1 中建立。

### <a name="3-handle-the-results-of-vision-processing"></a>3.處理願景處理的結果

矩形偵測完成後，它會執行`HandleRectangles`方法，這個方法會裁剪影像以擷取第一個矩形，將矩形影像轉換成 [灰階]，並將其傳遞至 CoreML 模型的分類。

`request`傳遞至此方法的參數包含要求詳細資料的願景，並使用`GetResults<VNRectangleObservation>()`方法，它會傳回一份映像中找到的矩形。 第一個矩形`observations[0]`擷取，並傳遞給 CoreML 模型：

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] { ClassificationRequest }, out NSError err);
  });
}
```

`ClassificationRequest`初始化在步驟 1 使用`HandleClassification`下一個步驟中定義的方法。

### <a name="4-handle-the-coreml"></a>4.控制代碼 CoreML

`request`傳遞至此方法的參數包含 CoreML 要求的詳細資訊和使用`GetResults<VNClassificationObservation>()`方法，它會傳回依信心的可能結果的清單 (最高信心第一個):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```



## <a name="samples"></a>範例

有三個 CoreML 範例，再試一次：

* [Mars 展示區預測工具範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)包含簡單的數值輸入和輸出。

* [願景 CoreML 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)接受影像參數，而用來識別正方形區域在影像中，也會傳遞至單一數字會辨識 CoreML 模型的目標 framework。

* 最後， [CoreML 影像辨識 」 範例](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)使用 CoreML 識別相片中的功能。 根據預設使用較小**SqueezeNet**模型 (5 MB)，但是會被寫入，讓您可以下載並納入較大**VGG16**模型 (553 MB)。 如需詳細資訊，請參閱[範例的讀我檔案](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md)。


## <a name="related-links"></a>相關連結

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [CoreML 範例 （Mars 展示區） （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML 和願景 （數字辨識） （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [CoreML 影像辨識 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML 視力 Azure 自訂 （範例）](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [簡介 CoreML (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/703/)
