---
title: Xamarin 中的核心 ML 2
description: 本檔說明在 iOS 12 中提供的核心 ML 更新。 特別是，它會查看與新批次預測 API 相關聯的效能改進。
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/15/2018
ms.openlocfilehash: 6245873385caa23e37d5499daa822fa0b699ac1e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032027"
---
# <a name="core-ml-2-in-xamarinios"></a>Xamarin 中的核心 ML 2

核心 ML 是 iOS、macOS、tvOS 和 watchOS 上提供的機器學習服務技術。 它可讓應用程式根據機器學習模型進行預測。

在 iOS 12 中，核心 ML 包含批次處理 API。 此 API 可讓核心 ML 更有效率，並在使用模型來做出一系列預測的案例中提供效能改進。

## <a name="sample-app-marshabitatcoremltimer"></a>範例應用程式： MarsHabitatCoreMLTimer

若要示範使用 Core ML 的批次預測，請參閱[MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)範例應用程式。 這個範例會使用已定型的核心 ML 模型來預測根據各種輸入，在 Mars 上建立 habitat 的成本：日光面板數目、greenhouses 數目和英畝數目。

本檔中的程式碼片段來自此範例。

## <a name="generate-sample-data"></a>產生範例資料

在 `ViewController`中，範例應用程式的 `ViewDidLoad` 方法會呼叫 `LoadMLModel`，以載入包含的核心 ML 模型：

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

然後，範例應用程式會建立 100000 `MarsHabitatPricerInput` 物件，以作為連續核心 ML 預測的輸入。 每個產生的範例都會針對日光面板數目、greenhouses 數目和英畝數設定隨機值：

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

點擊任一應用程式的三個按鈕，會執行兩個預測序列：一個使用 `for` 迴圈，另一個使用 iOS 12 中引進的新批次 `GetPredictions` 方法：

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for 迴圈

[測試輕鬆自在管理] 的 [`for` 迴圈] 版本會逐一查看指定的輸入數目，並針對每個呼叫[`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) ，並捨棄結果。 方法會花太多時間來進行預測：

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions （新的 batch API）

測試的批次版本會從輸入陣列建立 `MLArrayBatchProvider` 物件（因為這是 `GetPredictions` 方法所需的輸入參數），會建立[`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
防止預測計算限制為 CPU 的物件，並使用 `GetPredictions` API 來提取預測，然後再次捨棄結果：

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>結果

在模擬器和裝置上，`GetPredictions` 完成的速度比迴圈式核心 ML 預測更快。

## <a name="related-links"></a>相關連結

- [範例應用程式-MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [Core ML 的新功能，第1部分（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Core ML 的新功能，第2部分（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Xamarin 中的核心 ML 簡介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [核心 ML （Apple）](https://developer.apple.com/documentation/coreml?language=objc)
- [使用核心 ML 模型](https://developer.apple.com/machine-learning/build-run-models/)
