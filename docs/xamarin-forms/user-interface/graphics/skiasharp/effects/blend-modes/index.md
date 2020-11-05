---
title: SkiaSharp blend 模式
description: 使用 blend 模式來定義繪圖物件堆疊在彼此上時所發生的情況。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c345edf4c9980497d1fcd877a9142819afa9b56
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368644"
---
# <a name="skiasharp-blend-modes"></a>SkiaSharp blend 模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

這些文章著重于的 [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) 屬性 [`SKPaint`](xref:SkiaSharp.SKPaint) 。 `BlendMode`屬性的類型為 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) ，具有29個成員的列舉。

`BlendMode`屬性（property）會決定當繪圖物件 (通常稱為「 _來源_ ) 在現有繪圖物件上轉譯時所發生的情況， (稱為 _目的地_ ) 。 一般來說，我們預期新的繪圖物件會混淆其下的物件。 但這種情況只是因為預設的 blend 模式為 `SKBlendMode.SrcOver` ，這表示來源是在目的地 _上_ 繪製。 另28個成員 `SKBlendMode` 會造成其他效果。 在圖形程式設計中，以各種方式結合繪圖物件的技巧稱為「 _複合_ 」（撰寫）。

## <a name="the-skblendmodes-enumeration"></a>SKBlendModes 列舉

SkiaSharp blend 模式會與 W3C 撰寫 [**和混合層級 1**](https://www.w3.org/TR/compositing-1/) 規格中所述的內容緊密對應。 Skia [**SkBlendMode 總覽**](https://skia.org/user/api/SkBlendMode_Overview) 也提供實用的背景資訊。 如需 blend 模式的一般簡介，維琪百科的 [**blend 模式**](https://en.wikipedia.org/wiki/Blend_modes) 文章是不錯的開端。 Adobe Photoshop 支援 blend 模式，因此在該內容中有許多關於 blend 模式的其他線上資訊。

列舉的29個成員 `SKBlendMode` 可以分為三個類別：

| Porter-Duff | 分離    | 不可分離 |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

這三個類別的名稱在後續討論中將會有更多意義。 成員在此列出的順序與列舉的定義中的順序相同 `SKBlendMode` 。 第一個資料行中的13個列舉成員具有整數值0到12。 第二個數據行是對應至整數13到24的列舉成員，而第三個數據行中的成員具有25到28之間的值。

在 W3C 撰寫 **和混合層級 1** 檔中，這些 blend 模式的討論順序 _大致_ 相同，但是有一些差異：在 `Src` W3c 檔中稱為「 _複製_ 」模式，且稱為「 `Plus` _較淺_ 」。 W3C 檔會定義不包含在中的 _一般_ blend 模式， `SKBlendModes` 因為它會與相同 `SrcOver` 。 `Modulate`第一個資料) 行頂端的 blend 模式 (不包含在 W3C 檔中，而模式的討論則在 `Multiply` 前面 `Screen` 。

因為 `Modulate` blend 模式對 Skia 而言是唯一的，所以會將它做為額外的 Porter-Duff 模式和可分離模式來討論。

## <a name="the-importance-of-transparency"></a>透明的重要性

在過去，組合是與 _Alpha 通道_ 的概念一起開發。 在顯示介面（例如 `SKCanvas` 物件和全彩點陣圖）中，每個圖元都是由4個位元組組成：紅色、綠色和藍色元件的1個位元組，以及一個用於透明度的額外位元組。 此 Alpha 元件為0（適用于完整透明度）和0xFF （表示完全不透明度），這些值之間有不同的透明度層級。

許多 blend 模式都依賴透明度。 通常會 `SKCanvas` 在處理常式中第一次取得 `PaintSurface` ，或在 `SKCanvas` 建立以在點陣圖上繪製時，第一個步驟是呼叫：

```csharp
canvas.Clear();
```

這個方法會以透明的黑色圖元取代畫布的所有圖元，相當於 `new SKColor(0, 0, 0, 0)` 或整數0x00000000。 所有圖元的所有位元組都會初始化為零。

`SKCanvas`在處理常式中取得之的繪圖介面 `PaintSurface` 可能會有白色背景，但這只是因為 `SKCanvasView` 本身具有透明背景，而且頁面有白色背景。 您可以藉由將的 Xamarin.Forms `BackgroundColor` 屬性設定 `SKCanvasView` 為色彩，來示範這項事實 Xamarin.Forms ：

```csharp
canvasView.BackgroundColor = Color.Red;
```

或者，在衍生自的類別中 `ContentPage` ，您可以設定頁面背景色彩：

```csharp
BackgroundColor = Color.Red;
```

您會看到 SkiaSharp 圖形背後的紅色背景，因為 SkiaSharp 畫布本身是透明的。

[**SkiaSharp 透明度**](../../basics/transparency.md)一文示範了一些在複合影像中使用透明度來排列多個圖形的基本技巧。 Blend 模式超越了這一點，但透明度對 blend 模式而言仍然很重要。

## <a name="skiasharp-porter-duff-blend-modes"></a>[SkiaSharp Porter-Duff blend 模式](porter-duff.md)

使用 Porter-Duff blend 模式，根據來源和目的地影像來撰寫場景。

## <a name="skiasharp-separable-blend-modes"></a>[SkiaSharp 分離的 blend 模式](separable.md)

使用可分離的 blend 模式來改變紅色、綠色和藍色色彩。

## <a name="skiasharp-non-separable-blend-modes"></a>[SkiaSharp 不可分離的 blend 模式](non-separable.md)

使用不可分離的 blend 模式來改變色調、飽和度或亮度。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)