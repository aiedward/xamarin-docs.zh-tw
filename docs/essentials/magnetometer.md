---
title: Xamarin.Essentials：磁力計
description: Xamarin.Essentials 中的磁力計類別可讓您監視裝置的磁力計感應器，該感應器會指出裝置相對於地球磁場的方向。
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2fe610195f881f3d20ecc327c02dd2dfbced35ce
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675051"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials：磁力計

![發行前的 NuGet](~/media/shared/pre-release.png)

**磁力計**類別可讓您監視裝置的磁力計感應器，該感應器會指出裝置相對於地球磁場的方向。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>使用磁力計

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

磁力計功能的運作方式是呼叫 `Start` 和 `Stop` 方法，以觀察磁力計的變化。 所有變化都會透過 `ReadingChanged` 事件傳回。 以下是範例使用方式：

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
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

所有資料會以微特斯拉為單位傳回。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [磁力計原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [磁力計 API 文件](xref:Xamarin.Essentials.Magnetometer)
