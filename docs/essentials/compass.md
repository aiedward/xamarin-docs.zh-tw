---
title: Xamarin.Essentials：羅盤
description: 本文件描述 Xamarin.Essentials 中的羅盤類別，可讓您監視裝置的磁北方位。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 54ce725a319e0222179945ece558338c8a152653
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150119"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials：羅盤

**羅盤**類別可讓您監視裝置的磁北方位。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>使用羅盤

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

羅盤功能的運作方式是呼叫 `Start` 和 `Stop` 方法，以觀察羅盤的變化。 所有變化都會透過 `ReadingChanged` 事件傳回。 範例如下：

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

# <a name="android"></a>[Android](#tab/android)

Android 不會提供用來抓取羅盤標題的 API。 我們利用加速計和磁力計來計算磁北方位，這個方法由 Google 推薦。

在罕見情況下，您可能會看到不一致的結果，代表感應器需要校正，這涉及以 8 字形移動您的裝置。 最佳方法是開啟 Google 地圖、點選您所在位置的點，然後選取 [校正羅盤]****。

同時從您的應用程式執行多個感應器可能會調整感應器速度。

## <a name="low-pass-filter"></a>低通濾器

由於 Android 羅盤值的更新和計算方式，可能需要將值平滑。 可以套用_低度傳遞篩選準則_，以平均角度的正弦和余弦值，而且可以使用可接受參數的方法多載來開啟 `Start` `bool applyLowPassFilter` ：

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

僅會在 Android 平台上套用此項，在 iOS 及 UWP 上則會略過此參數。  可以在[這裡](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860)閱讀詳細資訊。

--------------

## <a name="api"></a>API

- [羅盤原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [羅盤 API 文件](xref:Xamarin.Essentials.Compass)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Compass-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
