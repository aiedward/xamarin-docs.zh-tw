---
title: Xamarin.Essentials：裝置顯示資訊
description: 本檔說明中的 DeviceDisplay 類別 Xamarin.Essentials ，此類別提供應用程式執行所在裝置的畫面計量。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: afb39563a0da85e73e7c7c8d774647066019efe0
ms.sourcegitcommit: 28ba903991a3c90a9420bda85e3abc16453314df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "102996537"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials：裝置顯示資訊

**DeviceDisplay** 類別提供應用程式執行所在的裝置畫面計量資訊，並可要求防止畫面在應用程式正在執行時休眠。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

Xamarin.Essentials在您的類別中新增的參考：

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

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

## <a name="keep-screen-on"></a>保持螢幕開啟

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

# <a name="android"></a>[Android](#tab/android)

沒有差異。

# <a name="ios"></a>[iOS](#tab/ios)

- 必須在 UI 執行緒上存取 `DeviceDisplay`，否則將擲回例外狀況。 您可以使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法，在 UI 執行緒上執行該程式碼。

# <a name="uwp"></a>[UWP](#tab/uwp)

沒有差異。

--------------

## <a name="api"></a>API

- [DeviceDisplay 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文件](xref:Xamarin.Essentials.DeviceDisplay)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Display-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
