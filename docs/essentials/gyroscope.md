---
title: Xamarin.Essentials： 陀螺儀
description: Xamarin.Essentials 陀螺儀類別可讓您監視裝置的陀螺儀感應器會測量裝置的三個主要軸旋轉。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c83b3a9d8a7801e531006f50f8db2e1ad23e48c
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947211"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials： 陀螺儀

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**陀螺儀**類別可讓您監視裝置的陀螺儀感應器也就是裝置的三個主要軸旋轉。

## <a name="using-gyroscope"></a>使用陀螺儀

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

陀螺儀功能的運作方式是呼叫`Start`和`Stop`方法，以接聽的變更陀螺儀。 任何變更會傳送回到`ReadingChanged`事件。 以下是範例使用方式：

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
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
