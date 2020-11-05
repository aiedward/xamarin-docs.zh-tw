---
title: SkiaSharp 效果
description: 瞭解如何使用漸層、點陣圖平鋪、blend 模式、模糊和其他效果來變更圖形的一般顯示。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6179f94b43f12a7bf4b91a05702c0539f3c8658
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367786"
---
# <a name="skiasharp-effects"></a>SkiaSharp 效果

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp [`SKPaint`](xref:SkiaSharp.SKPaint) 類別會定義可分類為一般 _效果_ 的六個屬性。 這些屬性會以某種方式改變一般顯示的圖形。 SkiaSharp 效果分成六個類別：

## <a name="path-effects"></a>[路徑效果](../curves/effects.md)

將的 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) 屬性設定為 `SKPaint` 類型的物件， [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 以顯示虛線，或使用從路徑建立的模式來筆劃或填滿區域。 此系列的路徑效果先前在 SkiaSharp 的文章 [**路徑效果**](../curves/effects.md)中有討論。

## <a name="shaders"></a>[著色器](shaders/index.md)

將的 [`Shader`](xref:SkiaSharp.SKPaint.Shader) 屬性設定為 `SKPaint` 類型的物件， [`SKShader`](xref:SkiaSharp.SKShader) 以顯示線性或圓形漸層、並排顯示點陣圖，以及 Perlin 雜訊模式。

## <a name="blend-modes"></a>[混合模式](blend-modes/index.md)

將的 [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) 屬性設定 `SKPaint` 為列舉的成員 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) ，以管理在目的地上顯示來源圖形時所發生的情況。 SkiaSharp 支援所有 CSS 複合和 blend 模式，包括 Porter-Duff 模式、可分離的 blend 模式，以及不可分離的 blend 模式。

## <a name="mask-filters"></a>[遮罩濾鏡](mask-filters.md)

將的屬性設定為類型的物件，以 [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) `SKPaint` [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) 進行模糊和其他 Alpha 效果。

## <a name="image-filters"></a>[影像濾鏡](image-filters.md)

將的屬性設定為類型的物件，以 [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) `SKPaint` [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 模糊點陣圖和建立陰影、浮雕或 engraving 效果。

## <a name="color-filters"></a>[色彩濾鏡](color-filters.md)

將的 [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) 屬性設定為 `SKPaint` 類型的物件 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) ，以使用資料表或矩陣轉換來改變色彩。

這些文章的所有範例程式碼都在 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)中。 從首頁選取 [ **SkiaSharp 效果** ]。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)