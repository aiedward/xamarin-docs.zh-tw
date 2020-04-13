---
title: 'Xamarin.Essentials: OrientationSensor'
description: OrientationSensor 類別可讓您監視裝置在三維空間中的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5423872da7966bc7e4bb88e278d76b709f114158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70120707"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

**OrientationSensor** 類別可讓您監視裝置在三維空間中的方向。

> [!NOTE]
> 此類別可用來判斷裝置在 3D 空間中的方向。 如果需要確定設備的視頻顯示是處於縱向或橫向模式,請使用`Orientation``ScreenMetrics`[`DeviceDisplay`](device-display.md)類中可用的物件的屬性。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` 的啟用方式是呼叫 `Start` 方法以監視裝置方向的變更，並透過呼叫 `Stop` 方法來停用。 所有變化都會透過 `ReadingChanged` 事件傳回。 以下是範例使用方式：

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
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

`OrientationSensor`讀數以[`Quaternion`](xref:System.Numerics.Quaternion)描述 基於兩個 3D 座標系的裝置方向的 讀數的形式報告回來:

裝置 (通常是手機或平板電腦) 具有含有下列座標軸的 3D 座標系統：

- 正 X 軸指向直向模式顯示器的右邊。
- 正 Y 軸指向直向模式顯示器的上方。
- 正 Z 軸指向螢幕背面方向。

地球的 3D 座標系統有下列座標軸：

- 正 X 軸與地球表面相切並指向東邊。
- 正 Y 軸也與地球表面相切並指向北邊。
- 正 Z 軸與地球表面垂直並指向上方。

`Quaternion` 描述裝置座標系統相對於地球座標系統的旋轉。

`Quaternion` 值非常接近於繞著軸旋轉。 若旋轉軸是正規化向量 (<sub>x</sub>、<sub>y</sub>、<sub>z</sub>) 且旋轉角度是 Θ，則四元數的 (X, Y, Z, W) 分量是：

(<sub>x</sub>·sin(Θ/2)、<sub>y</sub>·sin(Θ/2)、<sub>z</sub>·sin(Θ/2), cos(Θ/2))

這些是右手座標系統，因此右手的拇指指向旋轉軸的正方向，食指的曲線指出正角度的旋轉方向。

範例：

- 當裝置平放在桌上且螢幕朝上，且裝置頂端 (直向模式) 指向北邊時，兩個座標系統相對齊。 `Quaternion` 值代表識別四元數 (0, 0, 0, 1)。 所有旋轉都能以相對於此位置的方式來分析。

- 當裝置平放在桌上且螢幕朝上，且裝置頂端 (直向模式) 指向西邊時，`Quaternion` 值是 (0, 0, 0.707, 0.707)。 裝置已沿地球的 Z 軸旋轉 90 度。

- 當以正常方式拿裝置使得其頂端 (直向模式) 指向天空，且裝置的背面朝向北邊，則裝置已沿 X 軸旋轉 90 度。 `Quaternion` 值是 (0.707, 0, 0, 0.707)。

- 若裝置的左邊緣且頂端朝向北邊，則裝置已沿 Y 軸旋轉 &ndash;90 度 (或沿負 Y 軸旋轉 90 度)。 `Quaternion` 值是 (0, -0.707, 0, 0.707)。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [OrientationSensor 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文件](xref:Xamarin.Essentials.OrientationSensor)
