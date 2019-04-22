---
title: 核心機器學習服務 2 在 Xamarin.iOS 中
description: 本文件說明 iOS 12 一部分的核心機器學習服務可用的更新。 特別是，它會查看新的批次預測 API 相關聯的效能改進。
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 50d59f0b6ff2133c5870d84a1d740547768116e0
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869724"
---
# <a name="core-ml-2-in-xamarinios"></a>核心機器學習服務 2 在 Xamarin.iOS 中

核心機器學習服務是機器學習技術，可在 iOS、 macOS、 tvOS 和 watchOS。 它可讓應用程式，以根據機器學習模型進行預測。

在 iOS 12 中，核心 ML 會包含 API 的批次處理。 此 API 讓核心 ML 更有效率，而且可在其中使用模型來進行預測的一連串的情況下提升效能。

## <a name="sample-app-marshabitatcoremltimer"></a>範例應用程式：MarsHabitatCoreMLTimer

為了示範如何使用核心 ML 批次預測，看看[MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)範例應用程式。 核心 ML 模型定型以預測成本上的建置 habitat Mars，此範例會使用根據各種不同的輸入： 數目 solar 面板、 greenhouses，數目以及英畝數目。

本文件中的程式碼片段來自此範例。

## <a name="generate-sample-data"></a>產生範例資料

在  `ViewController`，範例應用程式`ViewDidLoad`方法呼叫`LoadMLModel`，載入包含的核心 ML 模型：

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

然後，範例應用程式會建立 100,000`MarsHabitatPricerInput`物件做為輸入使用循序的核心 ML 預測。 每個產生的範例有隨機的值為太陽能面板、 greenhouses，數目和英畝數目的數目：

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

點選任何應用程式的三個按鈕會執行兩個序列的預測： 一個使用`for`迴圈，並使用新的批次的另一個`GetPredictions`iOS 12 中導入的方法：

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

`for`迴圈版本的測試這個逐一查看指定的輸入數目，呼叫[ `GetPrediction` ](xref:CoreML.MLModel.GetPrediction*)每個及捨棄結果。 方法逾做出預測需要多久：

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

## <a name="getpredictions-new-batch-api"></a>GetPredictions （新批次 API）

建立測試批次版`MLArrayBatchProvider`輸入陣列中的物件 (因為這是必要的輸入的參數，如`GetPredictions`方法)，建立 [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
物件，可防止預測的 cpu，限制的計算，並使用`GetPredictions`API 以擷取一次並捨棄結果的預測：

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

在模擬器和裝置，`GetPredictions`迴圈為基礎的核心 ML 預測比更快速地完成。

## <a name="related-links"></a>相關連結

- [範例應用程式 – MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [在核心 ML 中，第 1 部分 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/708/)
- [在核心 ML 中，第 2 部分 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/709/)
- [在 Xamarin.iOS 中的核心機器學習服務簡介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [核心機器學習服務 (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [使用核心機器學習服務模型](https://developer.apple.com/machine-learning/build-run-models/)
