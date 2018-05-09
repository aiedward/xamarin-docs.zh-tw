---
title: Xamarin.Essentials 加速計
description: 加速計類別可讓您監視裝置的加速計感應器表示之三個維度空間中的裝置。
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 33364b5df8edd3a5cc745d0131067bd9f3940d69
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials 加速計

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**加速計**類別可讓您監視裝置的加速計感應器表示之三個維度空間中的裝置。

## <a name="using-accelerometer"></a>使用加速計

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

加速計功能的運作方式是呼叫`Start`和`Stop`接聽的加速功能變更的方法。 任何變更會透過送回`ReadingChanged`事件。 以下是範例使用方式：

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
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

加速計讀數會回報中。G 是 gravitation 單位強制等於諸地球重力欄位 (9.81 m/s ^2)。

座標系統定義的預設方向電話螢幕。 裝置的螢幕方向變更時，不會交換這兩個軸。

X 軸是水平和右邊的點，Y 軸是垂直和點和外部的螢幕的正面朝向點的 Z 軸。 在此系統中，座標至螢幕後方，會有負面的 Z 值。

例如：

* 當裝置位於資料表上的一般模式，其左邊向右推入 x 加速值為正數。

* 裝置所在的資料表上的一般模式，加速值時，+1.00 G 或 (+ 9.81 m/s ^2)，這對應到裝置的加速 (0 m/s ^2) 重力 force 減號 (-9.81 m/s ^2) 及標準化如同 g。

* 當裝置所在的資料表上的一般模式，推向 m/s 的加速功能 sky ^2，加速值等於 + 9.81 其對應至裝置的加速 (+ m/s ^2) 重力 force 減號 (-9.81 m/s ^2) 及標準化中。 

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 盡量 （不保證會傳回在 UI 執行緒上） 取得感應器資料。
- **遊戲**– 速率適合遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 適用於螢幕方向變更預設速率。
- **Ui** – 速率適用於一般使用者介面。

## <a name="api"></a>API

- [加速計原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Acceleromter)
- [加速計 API 文件](xref:Xamarin.Essentials.Accelerometer)
