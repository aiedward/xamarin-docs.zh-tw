---
title: Xamarin.Essentials： 指南針
description: 本文件描述的指南針類別 Xamarin.Essentials，可讓您監視裝置的磁性 north 標題中。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 63818014a9b3bdbef479055cbbcfbf8d348080fc
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080458"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials： 指南針

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**指南針**類別可讓您監視裝置的磁性 north 標題。

## <a name="using-compass"></a>使用指南針

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

指南針功能的運作方式是呼叫`Start`和`Stop`方法，以接聽羅盤的變更。 任何變更會透過送回`ReadingChanged`事件。 請看以下範例：

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(CompassChangedEventArgs e)
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
            // Some other exception has occured
        }
    }
}
```

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[感應器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 盡量 （不保證會傳回在 UI 執行緒上） 取得感應器資料。
- **遊戲**– 速率適合遊戲 （不保證會傳回在 UI 執行緒上）。
- **一般**– 適用於螢幕方向變更預設速率。
- **Ui** – 速率適用於一般使用者介面。

如果您的事件處理常式不一定，在 UI 執行緒上執行，如果事件處理常式需要存取使用者介面項目，使用[ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) UI 執行緒上執行該程式碼的方法。

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 不提供 API 來擷取羅盤的標題。 我們利用加速計和磁力計計算磁性 north 標題、 Google 建議這樣做。 

在罕見情況下，您可能看到不一致的結果因為感應器需要校正，牽涉到圖 8 移動中移動您的裝置。 這是開啟 Google 地圖、 點選做為位置的點，然後選取這樣做，最好**校正指南針**。

請注意，在相同的時間，從您的應用程式執行多個感應器可能需要調整感應器速度。

--------------

## <a name="api"></a>API

- [指南針原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [羅盤的 API 文件](xref:Xamarin.Essentials.Compass)
