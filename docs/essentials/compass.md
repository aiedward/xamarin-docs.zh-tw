---
title: Xamarin.Essentials：羅盤
description: 本文件描述 Xamarin.Essentials 中的羅盤類別，可讓您監視裝置的磁北方位。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 51812f9b4f88d77bf553a26ef3a6802239e338e0
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675493"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials：羅盤

![發行前的 NuGet](~/media/shared/pre-release.png)

**羅盤**類別可讓您監視裝置的磁北方位。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>使用羅盤

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

羅盤功能的運作方式是呼叫 `Start` 和 `Stop` 方法，以觀察羅盤的變化。 所有變化都會透過 `ReadingChanged` 事件傳回。 請看以下範例：

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 不會提供用於擷取羅盤方位的 API。 我們利用加速計和磁力計來計算磁北方位，這個方法由 Google 推薦。

在罕見情況下，您可能會看到不一致的結果，代表感應器需要校正，這涉及以 8 字形移動您的裝置。 最佳方法是開啟 Google 地圖、點選您所在位置的點，然後選取 [校正羅盤]。

請注意，同時從應用程式執行多個感應器可能會調整感應器速度。

## <a name="low-pass-filter"></a>低通濾器

由於 Android 羅盤值的更新和計算方式，可能需要將值平滑。 可以套用「低通濾器」來平均角度的正弦和餘弦值，並且可以透過在 `Compass` 類別上設定 `ApplyLowPassFilter` 屬性來開啟：

```csharp
Compass.ApplyLowPassFilter = true;
```

這只會在 Android 平台上套用。 可以在[這裡](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860)閱讀詳細資訊。

--------------

## <a name="api"></a>API

- [羅盤原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [羅盤 API 文件](xref:Xamarin.Essentials.Compass)
