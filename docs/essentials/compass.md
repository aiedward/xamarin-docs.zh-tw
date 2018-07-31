---
title: Xamarin.Essentials： 羅盤
description: 本文件說明 Xamarin.Essentials，可讓您監視裝置的磁性北部標題中的羅盤類別。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c3fe98c384a87bdc08ce94e7537d1a6343767561
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353879"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials： 羅盤

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**羅盤**類別可讓您監視裝置的磁性北部標題。

## <a name="using-compass"></a>使用羅盤

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

Compass 功能的運作方式是呼叫`Start`和`Stop`方法，以接聽的羅盤的變更。 任何變更會傳送回到`ReadingChanged`事件。 請看以下範例：

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

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 不會提供 API 擷取羅盤的方位。 我們使用加速計和計算磁性北部標題下，建議使用由 Google 磁力計。

在罕見情況下，您可能看到不一致的結果感應器需要校正，因為這牽涉到在 圖 8 影片中移動您的裝置。 開啟 Google 地圖、 點選做為位置的點，然後選取 這是這麼做，最好**校正羅盤**。

請注意，從您的應用程式中執行多個感應器，在相同的時間可能需要調整的感應器速度。

## <a name="low-pass-filter"></a>低通濾波器

由於如何 Android 羅盤更新並計算可能平滑值需要值。 A_低傳遞篩選條件_可以套用的角度的正弦和餘弦函數值加以平均，而且可以藉由設定開啟`ApplyLowPassFilter`上的屬性`Compass`類別：

```csharp
Compass.ApplyLowPassFilter = true;
```

這只會套用的 Android 平台。 可以讀取更多資訊[此處](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860)。

--------------

## <a name="api"></a>API

- [羅盤的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [羅盤的 API 文件](xref:Xamarin.Essentials.Compass)
