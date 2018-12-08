---
title: SkiaSharp 混合模式
description: 使用 blend 來定義圖形物件堆疊在另一個時，會發生什麼情況的模式。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 3ea05563ecbca95d26d692d5424c30e961229ac5
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051039"
---
# <a name="skiasharp-blend-modes"></a>SkiaSharp 混合模式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

這些文章著重[ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode)屬性[ `SKPaint` ](xref:SkiaSharp.SKPaint)。 `BlendMode`屬性的類型是[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)，列舉型別與 29 的成員。

`BlendMode`屬性會決定圖形化的物件時，會發生什麼事 (通常稱為_來源_) 會呈現在現有的圖形物件上 (稱為_目的地_)。 一般來說，我們會預期要遮蔽的物件，其下的新圖形化物件。 但只是預設的 blend 模式是因為發生此情形`SKBlendMode.SrcOver`，這表示，要繪製之來源_透過_目的地。 其他 28 成員`SKBlendMode`造成其他的副作用。 在圖形程式設計中，結合各種方式的圖形物件的技巧即所謂_複合 （compositing)_。

## <a name="the-skblendmodes-enumeration"></a>SKBlendModes 列舉

SkiaSharp blend 模式緊密對應至 W3C 中所述[**複合 （compositing） 和混合層級 1** ](https://www.w3.org/TR/compositing-1/)規格。 Skia [ **SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)也會提供有用的背景資訊。 混合模式的一般簡介[**混合模式**](https://en.wikipedia.org/wiki/Blend_modes)在維基百科的文章是不錯的起點。 這樣就很多其他線上資訊在該內容中的混合模式中 Adobe Photoshop 支援混合模式。

29 成員`SKBlendMode`列舉型別可以分為三類：

| Porter Duff | 可加以分隔    | 非可加以分隔 |
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

在討論中旁聽遵循更有意義上需要這三個類別的名稱。 這裡列出的成員的順序會定義相同`SKBlendMode`列舉型別。 13 列舉成員的第一個資料行中有 0 到 12 的整數值。 第二個資料行對應至整數 13 到 24，列舉成員，而第三個資料行中的成員擁有 25 至 28 的值。

模式會討論這些 blend_大約_W3C 的相同順序**複合 （compositing） 和混合層級 1**文件，但有一些差異：`Src`模式稱為_副本_W3C 文件中並`Plus`稱為_較淺_。 W3C 文件會定義_Normal_不包含在混合模式`SKBlendModes`因為它會與相同`SrcOver`。 `Modulate` （在第一個資料行頂端） 的混合模式不包含在 W3C 文件，並討論`Multiply`模式之前`Screen`。

因為`Modulate`專屬於 Skia 混合模式，將會討論為額外的 Porter Duff 模式，以及可分隔模式。

## <a name="the-importance-of-transparency"></a>重要性的透明度

在過去，複合 （compositing） 所開發的概念搭配_alpha 色頻_。 在顯示介面 例如`SKCanvas`物件和全彩點陣圖，每個像素包含 4 個位元組： 1 位元組每個紅色、 綠色和藍色元件，以及額外的位元組，透明度。 完整的透明度為 0 而完全不透明，具有不同的層級的那些值之間的透明的 0xFF 此 alpha 元件。

混合模式的許多依賴透明度。 通常，當`SKCanvas`中第一次取得`PaintSurface`處理常式，或當`SKCanvas`建立若要繪製的點陣圖，第一個步驟是此呼叫：

```csharp
canvas.Clear();
```

這個方法會以透明黑色像素為單位，相當於取代的畫布上的所有像素`new SKColor(0, 0, 0, 0)`或整數 0x00000000。 所有像素的所有位元組會都初始化為零。

之繪圖介面的`SKCanvas`中取得`PaintSurface`處理常式可能會顯示具有白色背景上，但這只是因為`SKCanvasView`本身有透明背景，以及頁面具有白色背景。 您可以對自己示範這項事實，藉由設定 Xamarin.Forms`BackgroundColor`屬性`SKCanvasView`Xamarin.Forms 色彩：

```csharp
canvasView.BackgroundColor = Color.Red;
```

或衍生自的類別中`ContentPage`，您可以設定網頁的背景色彩：

```csharp
BackgroundColor = Color.Red;
```

您會看到這個 SkiaSharp 圖形背後的紅色背景因為 SkiaSharp 畫布本身是透明的。

發行項[ **SkiaSharp 透明度**](../../basics/transparency.md)中使用透明度來排列複合映像中的多個圖形顯示一些基本技術。 混合模式介紹其他東西，但透明度會保留重要的混合模式。 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[SkiaSharp Porter Duff 混合模式](porter-duff.md)

您可以使用 Porter Duff 混合模式來撰寫來源和目的地的映像為基礎的場景。

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[SkiaSharp 分隔的混合模式](separable.md)

您可以使用分隔的混合模式 alter 紅色、 綠色和藍色色彩。

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[SkiaSharp 非可加以分隔的混合模式](non-separable.md)

使用非分隔混合模式來變更色調、 飽和度或亮度。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
