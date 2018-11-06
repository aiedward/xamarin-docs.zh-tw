---
title: Xamarin.Essentials：裝置顯示資訊
description: 本文件描述 Xamarin.Essentials 中的 DeviceDisplay 類別，可提供執行應用程式之裝置的畫面計量。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674882"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials：裝置顯示資訊

![發行前的 NuGet](~/media/shared/pre-release.png)

**DeviceDisplay** 類別提供執行應用程式之裝置的畫面計量。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>畫面計量

除了基本的裝置資訊外，**DeviceDisplay** 類別也包含裝置畫面和方向的相關資訊。

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

**DeviceDisplay** 類別也會公開可以訂閱的事件，只要畫面計量有任何變更，就會觸發該事件：

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

沒有差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 必須在 UI 執行緒上存取 `ScreenMetrics`，否則將擲回例外狀況。 您可以使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法，在 UI 執行緒上執行該程式碼。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

沒有差異。

--------------


## <a name="api"></a>API

- [DeviceDisplay 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文件](xref:Xamarin.Essentials.DeviceDisplay)
