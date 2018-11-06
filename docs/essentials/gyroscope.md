---
title: Xamarin.Essentials：陀螺儀
description: Xamarin.Essentials 中的陀螺儀類別可讓您監視裝置的陀螺儀感應器，該感應器會測量裝置三個主軸周圍的旋轉。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1d42658160855e260e0d159c58a1f95e7a8c7d4c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674701"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials：陀螺儀

![發行前的 NuGet](~/media/shared/pre-release.png)

**陀螺儀**類別可讓您監視裝置的陀螺儀感應器，該感應器是裝置三個主軸周圍的旋轉。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>使用陀螺儀

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

陀螺儀功能的運作方式是呼叫 `Start` 和 `Stop` 方法，以觀察陀螺儀的變化。 所有變化都會透過 `ReadingChanged` 事件傳回。 以下是範例使用方式：

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [陀螺儀原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [陀螺儀 API 文件](xref:Xamarin.Essentials.Gyroscope)
