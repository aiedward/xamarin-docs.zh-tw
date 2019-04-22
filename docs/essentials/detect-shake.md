---
title: Xamarin.Essentials:偵測搖動
description: Xamarin.Essentials 中的 Accelerometer 類別可讓您偵測裝置的搖動。
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2a14d739806fef353472a5186a9dbedd8e218662
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59019304"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials:偵測搖動

**[Accelerometer](accelerometer.md)** 可讓您監視裝置的加速計感應器，其會在立體空間中表示裝置的加速。 此外，其也可讓您在使用者搖動裝置時登錄事件。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>使用偵測搖動

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要偵測裝置的搖動，您必須透過呼叫 `Start` 及 `Stop` 方法來使用 Accelerometer 功能，以接聽加速計的變更，及偵測搖動。 每當偵測到搖動時就會引發 `ShakeDetected ` 事件。 建議針對 `SensorSpeed` 使用 `Game` 或更快的項目。 以下是範例使用方式：

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>實作詳細資料

偵測搖動 API 會從加速計使用原始讀數，以計算加速。 其會使用簡易的佇列機制，偵測 3/4 的最近加速計事件是否在最後半秒內發生。 加速是透過從加速計新增 X、Y、Z 讀數的平方，並將其與特定閾值比較計算得出。

## <a name="api"></a>API

- [Accelerometer 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Accelerometer API 文件](xref:Xamarin.Essentials.Accelerometer)
