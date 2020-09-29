---
title: Xamarin 中的核心 ML 2
description: 本檔說明可作為 iOS 12 一部分的核心 ML 更新。 尤其是，它會查看與新的 batch 預測 API 相關聯的效能改進。
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/15/2018
ms.openlocfilehash: 13eecdfe3ded3a0fd68594527f6c5bc8ca3a6c66
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434247"
---
# <a name="core-ml-2-in-xamarinios"></a>Xamarin 中的核心 ML 2

Core ML 是一種可在 iOS、macOS、tvOS 和 watchOS 上使用的機器學習技術。 它可讓應用程式根據機器學習模型來進行預測。

在 iOS 12 中，Core ML 包含批次處理 API。 此 API 可讓核心 ML 更有效率，並在使用模型來建立預測順序的案例中提供效能改進。

## <a name="sample-app-marshabitatcoremltimer"></a>範例應用程式： MarsHabitatCoreMLTimer

若要使用 Core ML 示範批次預測，請參閱 [MarsHabitatCoreMLTimer](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer) 範例應用程式。 此範例使用已定型的核心 ML 模型，以根據各種輸入來預測在 Mars 上建立 habitat 的成本：陽曆數、greenhouses 數目以及英畝數目。

本檔中的程式碼片段來自此範例。

## <a name="generate-sample-data"></a>產生範例資料

在中 `ViewController` ，範例應用程式的 `ViewDidLoad` 方法會呼叫 `LoadMLModel` ，這會載入內含的核心 ML 模型：

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

然後，範例應用程式會建立 100000 `MarsHabitatPricerInput` 物件，作為順序核心 ML 預測的輸入使用。 每個產生的範例都有一個隨機值，為日光板數目、greenhouses 數目和英畝數目設定隨機值：

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

只要使用應用程式的三個按鈕，就會執行兩個預測順序：一個使用 `for` 迴圈，另一個使用 `GetPredictions` iOS 12 中引進的新批次方法：

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

`for`測試輕鬆自在管理的迴圈版本會逐一查看指定的輸入數目， [`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) 並對每個輸入進行呼叫，並捨棄結果。 方法會乘以進行預測所花費的時間：

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

## <a name="getpredictions-new-batch-api"></a>GetPredictions (新的 batch API) 

測試的批次版本 `MLArrayBatchProvider` 會從輸入陣列 (建立物件，因為這是方法的必要輸入參數 `GetPredictions`) 、建立 [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
防止預測計算限制為 CPU 的物件，並使用 `GetPredictions` API 來提取預測，再次捨棄結果：

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

在模擬器和裝置上， `GetPredictions` 完成的速度比以迴圈為基礎的核心 ML 預測更快。

## <a name="related-links"></a>相關連結

- [範例應用程式-MarsHabitatCoreMLTimer](/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [Core ML 的新功能，第1部 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Core ML 的新功能，第2部分 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Xamarin 中的 Core ML 簡介](../introduction-to-ios11/coreml.md)
- [ (Apple) 的核心 ML ](https://developer.apple.com/documentation/coreml?language=objc)
- [使用核心 ML 模型](https://developer.apple.com/machine-learning/build-run-models/)