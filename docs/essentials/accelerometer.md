---
title: Xamarin.Essentials： 加速計
description: Xamarin.Essentials 加速計類別可讓您監視裝置的加速計感應器，表示這三個維度空間中的裝置加速。
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b5a24e214eb129b4d53b94586632791c8827447b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353837"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials： 加速計

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**加速計**類別可讓您監視裝置的加速計感應器表示的三個維度空間中的裝置加速。

## <a name="using-accelerometer"></a>使用加速計

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

加速計功能的運作方式是呼叫`Start`和`Stop`接聽變更為 加速的方法。 任何變更會傳送回到`ReadingChanged`事件。 以下是範例使用方式：

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
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

加速計的數據會回報中。G 是 gravitation 單位強制等於所施加的地球重力欄位 (9.81 m/s ^2)。

座標系統被定義相對於其預設方向電話的畫面。 裝置的螢幕方向變更時，不會交換這兩個軸。

X 軸是水平及點向右，Y 軸是垂直的並指向上方和 Z 軸點往螢幕的正面的外部。 在此系統中，至螢幕後方的座標會有負面的 Z 值。

例如：

* 當裝置位於一般的資料表上，且會在其左側向右推入時，x 加速值為正數。

* 裝置位於一般的資料表上，加速值時，+1.00 G 或 (+ 9.81 m/s ^2)，對應到裝置的加速 (0 m/s ^2) 受到重力強制減 (-9.81 m/s ^2) 和正規化與 g。

* 裝置何時位於一般的資料表上，並加速 m/s 的天空推向 ^2 加速值會等於 + 9.81 分別對應至裝置的加速 (+ m/s ^2) 受到重力強制減 (-9.81 m/s ^2) 及標準化中。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [加速計原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [加速計 API 文件](xref:Xamarin.Essentials.Accelerometer)
