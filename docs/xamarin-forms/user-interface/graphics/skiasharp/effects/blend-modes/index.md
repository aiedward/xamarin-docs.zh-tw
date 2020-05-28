---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b5b03779b9f0847621456b25582fb8ea04f8386d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131699"
---
# <a name="skiasharp-blend-modes"></a>SkiaSharp blend 模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

這些文章著重于的 [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) 屬性 [`SKPaint`](xref:SkiaSharp.SKPaint) 。 `BlendMode`屬性的類型是 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) ，具有29個成員的列舉。

`BlendMode`屬性會決定當繪圖物件（通常稱為「_來源_」）呈現在現有繪圖物件（稱為「_目的地_」）之上時，會發生什麼事。 一般來說，我們預期新的繪圖物件會遮蔽其底下的物件。 但只有在預設 blend 模式為時才會發生， `SKBlendMode.SrcOver` 這表示來源是在目的地_上_繪製。 其他28個成員 `SKBlendMode` 會造成其他效果。 在圖形程式設計中，以各種方式結合繪圖物件的技巧稱為「_合成_」。

## <a name="the-skblendmodes-enumeration"></a>SKBlendModes 列舉

SkiaSharp blend 模式與 W3C 撰寫[**和混合層級 1**](https://www.w3.org/TR/compositing-1/)規格中所述的類似。 Skia [**SkBlendMode 總覽**](https://skia.org/user/api/SkBlendMode_Overview)也提供有用的背景資訊。 如需 blend 模式的一般簡介，維琪百科中的[**blend 模式**](https://en.wikipedia.org/wiki/Blend_modes)一文是不錯的開端。 Adobe Photoshop 支援 blend 模式，因此在該內容中有其他有關 blend 模式的線上資訊。

列舉的29個成員 `SKBlendMode` 可以分成三個類別：

| Porter-Duff | 分離    | 不可分離 |
| ---
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------ |---標題：描述： ms. 生產： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------ |---標題：描述： ms. 生產： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------- | | `Clear`     | `Modulate`   | `Hue`         | | `Src`       | `Screen`     | `Saturation`  | | `Dst`       | `Overlay`    | `Color`       | | `SrcOver`   | `Darken`     | `Luminosity`  | | `DstOver`   | `Lighten`    |               | | `SrcIn`     | `ColorDodge` |               | | `DstIn`     | `ColorBurn`  |               | | `SrcOut`    | `HardLight`  |               | | `DstOut`    | `SoftLight`  |               | | `SrcATop`   | `Difference` |               | | `DstATop`   | `Exclusion`  |               | | `Xor`       | `Multiply`   |               | | `Plus`      |              |               |

這三個類別的名稱在後續討論中會採用更多意義。 成員在此列出的順序與列舉的定義相同 `SKBlendMode` 。 第一個資料行中的13個列舉成員，其整數值為0到12。 第二個數據行是對應到整數13到24的列舉成員，而第三個數據行中的成員具有25到28的值。

在 W3C 撰寫**和混合層級 1**檔中，這些 blend 模式的討論方式_大致_相同，但有一些差異：在 `Src` W3C 檔中稱為「_複製_」模式，並稱為「 `Plus` _較淺_」。 W3C 檔會定義不包含在中的_一般_blend 模式， `SKBlendModes` 因為它會與相同 `SrcOver` 。 `Modulate`Blend 模式（在第一個資料行的頂端）並未包含在 W3C 檔中，而模式的討論則在 `Multiply` 前面 `Screen` 。

因為 `Modulate` blend 模式對 Skia 而言是唯一的，所以會將其視為額外的 Porter Duff 模式，並作為可分離的模式加以討論。

## <a name="the-importance-of-transparency"></a>透明度的重要性

在過去，複合是與_Alpha_色板的概念一起開發。 在 `SKCanvas` 物件和全彩色點陣圖之類的顯示介面中，每個圖元都包含4個位元組：每個紅色、綠色和藍色元件各有1個位元組，另一個則用於透明度。 此 Alpha 元件為0代表完整透明度，而若為完全不透明度，則為0xFF，而這些值之間有不同的透明度層級。

許多 blend 模式都依賴透明度。 通常，當 `SKCanvas` 第一次取得 `PaintSurface` 處理常式，或 `SKCanvas` 建立以在點陣圖上繪製時，第一個步驟就是這個呼叫：

```csharp
canvas.Clear();
```

這個方法會將畫布的所有圖元以透明黑色圖元取代，相當於 `new SKColor(0, 0, 0, 0)` 或整數0x00000000。 所有圖元的所有位元組都會初始化為零。

`SKCanvas`在處理常式中取得的繪圖介面 `PaintSurface` 可能看起來有白色背景，但這只是因為 `SKCanvasView` 本身具有透明背景，而且頁面具有白色背景。 您可以藉由將的 Xamarin.Forms `BackgroundColor` 屬性設定 `SKCanvasView` 為色彩來示範這個事實 Xamarin.Forms ：

```csharp
canvasView.BackgroundColor = Color.Red;
```

或者，在衍生自的類別中 `ContentPage` ，您可以設定頁面背景色彩：

```csharp
BackgroundColor = Color.Red;
```

您會在 SkiaSharp 圖形背後看到這個紅色背景，因為 SkiaSharp 畫布本身是透明的。

[**SkiaSharp 透明度**](../../basics/transparency.md)一文顯示在複合影像中使用透明度來排列多個圖形的一些基本技巧。 Blend 模式超越了，但是對 blend 模式而言，透明度仍然很重要。

## <a name="skiasharp-porter-duff-blend-modes"></a>[SkiaSharp Porter-Duff blend 模式](porter-duff.md)

使用 Porter-Duff blend 模式，根據來源和目的地影像來撰寫場景。

## <a name="skiasharp-separable-blend-modes"></a>[SkiaSharp 能分離的 blend 模式](separable.md)

使用可分離的 blend 模式來改變紅色、綠色和藍色色彩。

## <a name="skiasharp-non-separable-blend-modes"></a>[SkiaSharp 不可分離的 blend 模式](non-separable.md)

使用不可分離的 blend 模式來改變色調、飽和度或亮度。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
