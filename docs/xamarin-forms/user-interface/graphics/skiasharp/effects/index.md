---
title: SkiaSharp 效果
description: 了解如何改變使用漸層的圖形的正常顯示、 並排顯示的點陣圖，混合模式、 模糊和其他效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 121d505d578aa20e86977c0da5d69626bbad1f53
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289301"
---
# <a name="skiasharp-effects"></a>SkiaSharp 效果

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint)類別會定義可以分類底下的一般詞彙的六個屬性_效果_。 這些是改變正常的圖形以某種方式顯示的屬性。 SkiaSharp 效果分為六個類別：

## <a name="path-effectscurveseffectsmd"></a>[路徑效果](../curves/effects.md)

設定[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)屬性`SKPaint`物件的型別[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)顯示虛線，或如何繪製，或使用路徑從建立的圖樣填滿區域。 路徑效果稍早在本系列文章中涵蓋[ **SkiaSharp 路徑效果**](../curves/effects.md)。

## <a name="shadersshadersindexmd"></a>[著色器](shaders/index.md)

設定[ `Shader` ](xref:SkiaSharp.SKPaint.Shader)屬性`SKPaint`物件的型別[ `SKShader` ](xref:SkiaSharp.SKShader)顯示線性或循環的漸層、 並排顯示的點陣圖和 Perlin 雜訊模式。

## <a name="blend-modesblend-modesindexmd"></a>[混合模式](blend-modes/index.md)

設定[ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode)屬性`SKPaint`成員[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)列舉，來控管的來源圖形會顯示在目的地上時，會發生什麼事。 SkiaSharp 支援所有 CSS 複合 （compositing） 和混合模式中，包括 Porter Duff 模式、 分隔的混合模式，以及非可加以分隔的混合模式。

## <a name="mask-filtersmask-filtersmd"></a>[遮罩篩選器](mask-filters.md)

設定[ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter)屬性`SKPaint`物件的型別[ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter)模糊和其他 alpha 的效果。

## <a name="image-filtersimage-filtersmd"></a>[映像篩選器](image-filters.md)

設定[ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter)屬性`SKPaint`物件的型別[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)模糊的點陣圖，並建立延伸陰影，浮凸、 或是 engraving 效果。

## <a name="color-filterscolor-filtersmd"></a>[色彩篩選器](color-filters.md)

設定[ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter)屬性`SKPaint`物件的型別[ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)改變色彩使用資料表或矩陣轉換。

這些文章是在所有範例程式都碼[ **SkiaSharpFormsDemos**](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)。 從 [首頁] 頁面中，選取**SkiaSharp 效果**。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
