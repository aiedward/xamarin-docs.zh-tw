---
title: 'Xamarin.Essentials: OrientationSensor'
description: OrientationSensor 類別可讓您監視在 3d 空間中的裝置的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080461"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**OrientationSensor**類別可讓您監視裝置，以三個維度空間中的方向。

> [!NOTE]
> 這個類別是裝置的決定在 3D 空間中的方向。 如果您需要確定裝置的視訊顯示為直向或橫向模式，請使用`Orientation`屬性`ScreenMetrics`物件可從[ `DeviceDisplay` ](device-display.md)類別。

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

`OrientationSensor`啟用藉由呼叫`Start`方法來監視裝置的方向，並停用的變更，藉由呼叫`Stop`方法。 任何變更會透過送回`ReadingChanged`事件。 以下是範例使用方式：

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

`OrientationSensor` 讀取的格式回報給[ `Quaternion` ](xref:System.Numerics.Quaternion)描述兩個 3D 座標系統為基礎的裝置的方向：

裝置 （通常透過電話或平板電腦） 有 3D 的座標系統具有下列軸：

- X 軸指向右側直向模式中顯示的正數。
- 正數 Y 軸指向直向模式中的裝置頂端。
- 正的 Z 軸點超出螢幕。

3D 地球座標系統具有下列軸：

- 正數 X 軸是到地球表面的正切函數，並指出東部。
- 正數 Y 軸也是地球和北部點之介面的正切函數。
- 正的 Z 軸是垂直地球與註冊點的介面。

`Quaternion`描述相對於地球座標系統的裝置的座標系統的旋轉角度。

A`Quaternion`值非常密切相關沿著軸的旋轉。 已正規化的向量旋轉的軸是否 (<sub>x</sub>、<sub>y</sub>、<sub>z</sub>)，而且的旋轉角度 Θ，然後 X、 Y、 Z （W） 四元數的元件：

(<sub>x</sub>·sin(Θ/2)，<sub>y</sub>·sin(Θ/2)，<sub>z</sub>·sin(Θ/2)、 cos(Θ/2))

這些是右手座標系統，因此與軸之捲動方塊的正方向旋轉軸指向右側，指的曲線表示旋轉正面角度的方向。

例如：

* 當裝置是出在資料表上的一般模式螢幕的頂端 （處於縱向模式） 裝置指向北，朝上對齊兩個座標系統。 `Quaternion`值代表四元數 （0，0，0，1）。 您可以分析所有旋轉，相對於這個位置。

* 當裝置是出在資料表上的一般模式，其螢幕和西指向 （處於縱向模式） 的裝置頂端`Quaternion`值是 （0，0、 0.707，0.707）。 裝置已旋轉 90 度繞著 Z 軸的地球。

* 使 （中直向模式） 的第一個點朝 sky 和北部面對裝置的背面，裝置會保留垂直，裝置已沿著 X 軸旋轉 90 度。 `Quaternion`值是 （0.707，0，0，0.707）。

* 如果裝置位於與其左邊的緣所在的資料表中，因此最上層指向北，裝置已旋轉&ndash;90 度，繞著 Y 軸 （或 90 度負的 Y 軸）。 `Quaternion`值是 0、-0.707、 0 （0.707）。

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 盡量 （不保證會傳回在 UI 執行緒上） 取得感應器資料。
- **遊戲**– 速率適合遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 適用於螢幕方向變更預設速率。
- **Ui** – 速率適用於一般使用者介面。

如果您的事件處理常式不一定，在 UI 執行緒上執行，如果事件處理常式需要存取使用者介面項目，使用[ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) UI 執行緒上執行該程式碼的方法。

## <a name="api"></a>API

- [OrientationSensor 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文件](xref:Xamarin.Essentials.OrientationSensor)
