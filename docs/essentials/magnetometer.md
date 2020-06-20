---
title: Xamarin.Essentials：磁力計
description: 中的磁力計類別 Xamarin.Essentials 可讓您監視裝置的磁力計感應器，這會指出裝置相對於地球磁場的方向。
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b01bc1fd9c65186952635c5f472b1ac6beb0c9bd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802280"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials：磁力計

**磁力計**類別可讓您監視裝置的磁力計感應器，該感應器會指出裝置相對於地球磁場的方向。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>使用磁力計

Xamarin.Essentials在您的類別中新增的參考：

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

所有資料都會以μT （microteslas）傳回。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [磁力計原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Magnetometer)
- [磁力計 API 文件](xref:Xamarin.Essentials.Magnetometer)
