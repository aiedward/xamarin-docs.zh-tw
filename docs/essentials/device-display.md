---
title: Xamarin.Essentials：裝置顯示資訊
description: 本文件描述 Xamarin.Essentials 中的 DeviceDisplay 類別，可提供執行應用程式之裝置的畫面計量。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d3102f0a4ed5f16c77c4a4768feb4a1565f2dd1a
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898887"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials：裝置顯示資訊

**DeviceDisplay** 類別提供應用程式執行所在的裝置畫面計量資訊，並可要求防止畫面在應用程式正在執行時休眠。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>主要顯示資訊

除了基本的裝置資訊外，**DeviceDisplay** 類別也包含裝置畫面和方向的相關資訊。

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

**DeviceDisplay** 類別也會公開可以訂閱的事件，只要畫面計量有任何變更，就會觸發該事件：

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

**DeviceDisplay** 類別公開稱為 `KeepScreenOn` 的 `bool` 屬性，其可設定為嘗試防止裝置顯示關閉或鎖定。

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

沒有差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 必須在 UI 執行緒上存取 `DeviceDisplay`，否則將擲回例外狀況。 您可以使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法，在 UI 執行緒上執行該程式碼。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

沒有差異。

--------------


## <a name="api"></a>API

- [DeviceDisplay 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文件](xref:Xamarin.Essentials.DeviceDisplay)
