---
title: Xamarin.Essentials色彩轉換器
description: 中的 ColorConverters 類別 Xamarin.Essentials 提供數個 helper 方法和擴充方法，以處理 system.web. Color。
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
ms.custom: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57b85a909855594c70bfd529d7a794b4bd7bca7e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130217"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials：色彩轉換器

中的**ColorConverters**類別會 Xamarin.Essentials 針對 system.web. Color 提供數個 helper 方法。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>使用色彩轉換器

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

使用時， `System.Drawing.Color` 您可以使用的內建轉換器 Xamarin.Forms ，從 Hsl、Hex 或 UInt 建立色彩。

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>使用色彩擴充

`System.Drawing.Color` 上的擴充方法可讓您套用不同屬性：

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

還有數種其他擴充方法，包含：

- GetComplementary
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

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

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
