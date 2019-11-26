---
title: Xamarin.Essentials 平台擴充
description: 當必須使用像是矩形、大小、點等平台類型時，Xamarin.Essentials 可提供數種平台擴充方法。
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4a80c004dd55486db18a3149dcd889a4673d7438
ms.sourcegitcommit: 1c87135a47780f34102952d4b140850b4f08b075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536492"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin. Essentials：平臺擴充功能

當必須使用像是矩形、大小、點等平台類型時，Xamarin.Essentials 可提供數種平台擴充方法。 這代表您可在 `System` 版的這些類型 (其 iOS、Android 及 UWP 特定類型) 間轉換。 

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>使用平台擴充

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

全部的平台擴充模組皆僅可從 iOS、Android 或 UWP 專案呼叫。

### <a name="point"></a>點

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
