---
title: Xamarin.Essentials 平台擴充
description: 當必須使用像是矩形、大小、點等平台類型時，Xamarin.Essentials 可提供數種平台擴充方法。
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545157"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.要點:平台擴展

當必須使用像是矩形、大小、點等平台類型時，Xamarin.Essentials 可提供數種平台擴充方法。 這代表您可在 `System` 版的這些類型 (其 iOS、Android 及 UWP 特定類型) 間轉換。 

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>使用平台擴充

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

全部的平台擴充模組皆僅可從 iOS、Android 或 UWP 專案呼叫。

## <a name="android-extensions"></a>安卓擴展

這些擴展只能從 Android 項目訪問。

### <a name="application-context--activity"></a>應用程式內容文&活動

使用類中的`Platform`平台擴展,您可以訪問`Context`當前`Activity`應用或 正在運行的應用。

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

如果出現需要`Activity`的情況 ,但應用程式尚未完全`WaitForActivityAsync`啟動, 則應使用該方法。

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>活動開發週期

除了獲取當前活動外,您還可以註冊生命週期事件。

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

活動狀態如下:

* 建立時間
* 已繼續
* 已暫停
* 終結
* 儲存實體狀態
* Started
* 已停止

閱讀[活動生命周期](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/)文件以瞭解更多資訊。

## <a name="ios-extensions"></a>iOS 延伸模組

這些擴展只能從 iOS 項目訪問。

### <a name="current-uiviewcontroller"></a>目前 UIView 控制器

存取目前可見`UIViewController`的 :

```csharp
var vc = Platform.GetCurrentUIViewController();
```

如果無法偵測`null`到`UIViewController`, 則此方法將傳回。

## <a name="cross-platform-extensions"></a>跨平台延伸

這些擴展存在於每個平臺中。

### <a name="point"></a>Point

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>大小

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>矩形

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [點轉換器 API 文件](xref:Xamarin.Essentials.PointExtensions)
- [矩形轉換器 API 文件](xref:Xamarin.Essentials.RectangleExtensions)
- [大小轉換器 API 文件](xref:Xamarin.Essentials.SizeExtensions)
