---
title: 'Xamarin.Essentials: OrientationSensor'
description: OrientationSensor 類別可讓您監視裝置在 3d 空間中的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c01fa28e495eb3eceec62885060dce8f096c4086
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947386"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**OrientationSensor**類別可讓您監視裝置，以三個維度空間中的方向。

> [!NOTE]
> 這個類別是用於判斷裝置在 3D 空間中的方向。 如果您需要決定如果裝置的視訊顯示為直向或橫向模式，請使用`Orientation`的屬性`ScreenMetrics`物件可從[ `DeviceDisplay` ](device-display.md)類別。

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

`OrientationSensor`啟用藉由呼叫`Start`方法來監視裝置的方向，並停用的變更，藉由呼叫`Stop`方法。 任何變更會傳送回到`ReadingChanged`事件。 以下是範例使用方式：

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` 數據會回報的形式[ `Quaternion` ](xref:System.Numerics.Quaternion)描述的兩個 3D 座標系統為基礎的裝置方向：

裝置 （通常在手機或平板電腦） 有 3D 座標系統具有下列軸：

- X 軸指向右側的 直向模式中的顯示正值。
- 正 Y 軸指向頂端的 直向模式中的裝置。
- 正 Z 軸點從螢幕中。

地球 3D 座標系統具有下列軸：

- 正 X 軸的地球表面的切值，並指出東部。
- 正 Y 軸也是接觸到的表面和點北的介面。
- 正 Z 軸是垂直的註冊點與地球表面。

`Quaternion`描述相對於地球座標系統的裝置的座標系統的旋轉角度。

A`Quaternion`在座標軸四周旋轉非常密切相關的值。 如果座標軸的旋轉是標準化的向量 (<sub>x</sub>、<sub>y</sub>、<sub>z</sub>)，和旋轉角度是 Θ，則 X、 Y、 Z （W） 元件的四元數是：

(<sub>x</sub>·sin(Θ/2)，<sub>y</sub>·sin(Θ/2)，<sub>z</sub>·sin(Θ/2)、 cos(Θ/2))

這些是右手座標系統，因此具有所指的旋轉軸正向的右手的拇指，手指的曲線表示旋轉的正角度的方向。

例如：

* 當裝置就是一般的資料表上使用裝置 （在直向模式） 的最上層指向北、 面向，其畫面對齊兩個座標系統。 `Quaternion`值代表 identity 四元數 （0，0，0，1）。 相對於這個位置，您可以分析所有的旋轉。

* 當裝置是出在資料表上的一般朝，其畫面和西部、 指向裝置 （在直向模式） 的頂端`Quaternion`值是 0、 0、 0.707 （0.707）。 裝置已旋轉 90 度繞著 Z 軸的地球。

* 裝置會保留垂直，以便 （中直向模式） 的第一個點朝天空及裝置的背面面臨北，裝置已繞著 X 軸的旋轉 90 度。 `Quaternion`值是 （0.707，0，0，0.707）。

* 如果裝置位於其左邊的緣所在的資料表中，因此頂端指向北，裝置已旋轉&ndash;繞著 Y 軸 （或 90 度負 Y 軸） 的 90 度。 `Quaternion`值是 0、-0.707、 0 （0.707）。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [OrientationSensor 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文件](xref:Xamarin.Essentials.OrientationSensor)
