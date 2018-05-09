---
title: Xamarin.Essentials 磁力計
description: 磁力計類別可讓您監視裝置的磁力計感應器表示相對於地球磁場的裝置的方向。
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e43834756e6a582bd0fd30a5655da8d087a971b7
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials 磁力計

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**磁力計**類別可讓您監視裝置的磁力計感應器表示相對於地球磁場的裝置的方向。

## <a name="using-magnetometer"></a>使用磁力計

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

磁力計功能的運作方式是呼叫`Start`和`Stop`方法，以接聽磁力計的變更。 任何變更會透過送回`ReadingChanged`事件。 以下是範例使用方式：

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Microteslas 中會傳回所有資料。

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 盡量 （不保證會傳回在 UI 執行緒上） 取得感應器資料。
- **遊戲**– 速率適合遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 適用於螢幕方向變更預設速率。
- **Ui** – 速率適用於一般使用者介面。

## <a name="api"></a>API

- [磁力計原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Magnetometer)
- [磁力計 API 文件](xref:Xamarin.Essentials.Magnetometer)
