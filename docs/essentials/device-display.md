---
title: Xamarin.Essentials： 裝置顯示資訊
description: 本文件說明在 Xamarin.Essentials，提供應用程式執行所在裝置的螢幕計量 DeviceDisplay 類別。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38816512"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials： 裝置顯示資訊

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**DeviceDisplay**類別提供應用程式執行相關裝置的螢幕度量資訊。

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>畫面計量

除了基本裝置資訊**DeviceDisplay**類別包含裝置的螢幕和方向的相關資訊。

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

**DeviceDisplay**類別也會公開任何畫面度量變更時會觸發訂閱的事件：

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [DeviceDisplay 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文件](xref:Xamarin.Essentials.DeviceDisplay)
