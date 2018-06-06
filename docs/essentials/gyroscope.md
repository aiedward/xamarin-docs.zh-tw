---
title: Xamarin.Essentials： 迴轉儀
description: Xamarin.Essentials 迴轉儀類別可讓您監視裝置的迴轉儀感應器，用於測量繞裝置的三個主要的軸旋轉。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2f2961c6cb78293891e186e7e0f749a7aa2fb8fc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783011"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials： 迴轉儀

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**迴轉儀**類別可讓您監視裝置的迴轉儀感應器即繞裝置的三個主要的軸旋轉。

## <a name="using-gyroscope"></a>使用 迴轉儀

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

迴轉儀的功能的運作方式是呼叫`Start`和`Stop`方法，以接聽迴轉儀的變更。 任何變更會透過送回`ReadingChanged`事件。 以下是範例使用方式：

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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 盡量 （不保證會傳回在 UI 執行緒上） 取得感應器資料。
- **遊戲**– 速率適合遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 適用於螢幕方向變更預設速率。
- **Ui** – 速率適用於一般使用者介面。

## <a name="api"></a>API

- [迴轉儀的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [迴轉儀的 API 文件](xref:Xamarin.Essentials.Gyroscope)
