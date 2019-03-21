---
title: Xamarin.Essentials Color Converters (Xamarin.Essentials 色彩轉換器)
description: Xamarin.Essentials 中的 ColorConverters 類別，會提供數種 Helper 方法及擴充方法來使用 System.Drawing.Color。
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 13b8c4c109855402928d3923a5910e23f933dc65
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58176026"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials:色彩轉換器

Xamarin.Essentials 中的 **ColorConverters** 類別，會為 System.Drawing.Color 提供數種 Helper 方法。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>使用色彩轉換器

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

使用 `System.Drawing.Color` 時，您可使用 Xamarin.Forms 的內建轉換器，從 Hsl、Hex 或 UInt 建立色彩。

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverers.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>使用色彩擴充

`System.Drawing.Color` 上的擴充方法可讓您套用不同屬性：

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

還有數種其他擴充方法，包含：

* ToUInt
* MultiplyAlpha
* WithHue
* WithAlpha
* WithSaturation
* WithLuminosity


## <a name="using-platform-extensions"></a>使用平台擴充

此外，您可將 System.Drawing.Color 轉換為平台特定的色彩結構。 這些方法僅可從 iOS、Android 及 UWP 專案呼叫。

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);
 
// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```


```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);
 
// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

`ToSystemColor` 方法會套用至 Android.Graphics.Color、UIKit.UIColor 及 Windows.UI.Color。


## <a name="api"></a>API

- [色彩轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [色彩轉換器 API 文件](xref:Xamarin.Essentials.ColorConverters)
- [色彩擴充原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [色彩擴充 API 文件](xref:Xamarin.Essentials.ColorExtensions)
