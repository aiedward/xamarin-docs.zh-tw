---
title: Xamarin.Essentials： 磁力計
description: Xamarin.Essentials 磁力計類別可讓您監視裝置的磁力計感應器，表示相對於地球磁場的裝置的方向。
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3827b9a57ec2667a8716f5b56bfb4631b979d43a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353785"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials： 磁力計

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**磁力計**類別可讓您監視裝置的磁力計感應器表示相對於地球磁場的裝置的方向。

## <a name="using-magnetometer"></a>使用磁力計

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

磁力計功能的運作方式是呼叫`Start`和`Stop`方法，以接聽磁力計的變更。 任何變更會傳送回到`ReadingChanged`事件。 以下是範例使用方式：

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

在 microteslas 會傳回所有資料。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [磁力計原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [磁力計 API 文件](xref:Xamarin.Essentials.Magnetometer)
