---
title: SkiaSharp 色彩篩選
description: 使用色彩篩選來轉換具有轉換或資料表的色彩。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 809477fe466ee7a8f0985308896c14341f2dd460
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561933"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp 色彩篩選

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

色彩篩選器可以轉譯點陣圖 (或其他影像) 的色彩以 posterization 效果的其他色彩：

![色彩篩選範例](color-filters-images/ColorFiltersExample.png "色彩篩選範例")

若要使用色彩篩選，請將的 [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) 屬性設定 `SKPaint` 為 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) 該類別中的其中一個靜態方法所建立之類型的物件。 本文示範： 

- 使用方法建立的色彩轉換 [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 。
- 使用方法建立的色彩表 [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 。

## <a name="the-color-transform"></a>色彩轉換

色彩轉換牽涉到使用矩陣來修改色彩。 和大部分2D 圖形系統一樣，SkiaSharp 使用的矩陣大多是在 SkiaSharp 的文章 [**矩陣轉換**](../transforms/matrix.md)中，將座標點轉換成 iscussed。 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)也支援矩陣轉換，但矩陣會轉換 RGB 色彩。 您必須熟悉矩陣概念，才能瞭解這些色彩轉換。 

色彩轉換矩陣的維度有四個數據列和五個數據行：

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

它會將 RGB 來源色彩轉換 (R、G、B、) 到目的地色彩 (R '、G '、B '、A ' ) 。 

為了準備矩陣乘法，來源色彩會轉換成5×1矩陣：

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

這些 R、G、B 和值都是從0到255的原始位元組。 它們 _不_ 會正規化為範圍0到1的浮點值。

轉譯因數需要額外的儲存格。 這類似于使用三×3矩陣來轉換二維座標點，如同使用矩陣來轉換座標點一節中的 [**3 x 3 矩陣的原因**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) 所述。

4×5矩陣乘以5×1矩陣，而產品是具有轉換色彩的4×1矩陣：

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

以下是 R '、G '、B ' 和 A ' 的個別公式：

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

大部分的矩陣都是由通常介於0到2範圍的乘法因數所組成。 不過，最後一個資料行 (M15 至 M45) 包含在公式中加入的值。 這些值的範圍通常是從0到255。 結果會在0到255的值之間壓制。

身分識別矩陣為：

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

這樣就不會變更色彩。 轉換公式如下：

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

M44 資料格很重要，因為它會保留不透明度。 一般而言，M41、M42 和 M43 都是零，因為您可能不想要以紅色、綠色和藍色值為基礎。 但是，如果 M44 為零，則 ' 將會是零，且不會顯示任何專案。

色彩矩陣最常見的用途之一，就是將色彩點陣圖轉換成灰色縮放點陣圖。 這涉及紅色、綠色和藍色值的加權平均值公式。 針對使用 sRGB ( 「標準紅色綠色藍色」 ) 色彩空間的影片顯示，此公式為：

灰色-陰影 = 0.2126 ·R + 0.7152 ·G + 0.0722 ·B

若要將色彩點陣圖轉換成灰階點陣圖，R '、G ' 和 B ' 結果全都必須等於相同的值。 矩陣為：

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

沒有對應至此矩陣的 SkiaSharp 資料類型。 相反地，您必須以資料列順序，將矩陣表示為20個值的陣列 `float` ：第一個資料列、第二個數據列等等。

靜態 [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 方法具有下列語法：

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

其中 `matrix` 是20個值的陣列 `float` 。 在 c # 中建立陣列時，很容易就能將數位格式化，使其類似于4×5矩陣。 這會在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的**灰色調整矩陣**頁面中示範：

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

`DrawBitmap`此程式碼中使用的方法是來自[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例隨附的**BitmapExtension.cs**檔案。 

以下是在 iOS、Android 和通用 Windows 平臺上執行的結果：

[![灰色縮放矩陣](color-filters-images/GrayScaleMatrix.png "灰色縮放矩陣")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

請留意第四個數據列和第四個數據行中的值。 這是一項重要的因素，會乘以 A ' 轉換色彩值的原始色彩值。 如果該儲存格為零，則不會顯示任何內容，而且可能難以找出問題。

使用色彩矩陣進行實驗時，您可以從來源的觀點或目的地的觀點來處理轉換。 來源的紅色圖元如何構成目的地的紅色、綠色和藍色圖元？ 這是由矩陣第一個資料 _行_ 中的值所決定。 或者，目的地紅色圖元應該如何受到來源的紅色、綠色和藍色圖元影響？ 這是由矩陣的第一個資料 _列_ 所決定。

如需有關如何使用色彩轉換的一些概念，請參閱重新 [**著色影像**](/dotnet/framework/winforms/advanced/recoloring-images) 頁面。 討論 Windows Forms 和矩陣是不同的格式，但概念相同。

**蠟筆矩陣**會 attenuating 來源的紅色圖元來計算目的地紅色圖元，並稍微強調紅色和綠色圖元。 此程式會類似于綠色和藍色圖元：

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

結果是將色彩的濃度靜音，如下所示：

[![蠟筆矩陣](color-filters-images/PastelMatrix.png "蠟筆矩陣")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>色彩表

靜態 [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 方法有兩個版本：

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

陣列一律包含256個專案。 在 `CreateTable` 具有一個資料表的方法中，會針對紅色、綠色和藍色元件使用相同的資料表。 這是簡單的查閱資料表：如果來源色彩 (R、G、B) ，而目的色彩是 (R '、B '、G ' ) ，則會使用來源元件編制索引來取得目的地元件 `table` ：

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

在第二個方法中，四個色彩元件中的每一個都可以有個別的色彩表，或相同的色彩表可能會在兩個或多個元件之間共用。

如果您想要將第二個方法的其中一個引數設定 `CreateTable` 為色彩資料表（順序中包含0到255的值），您可以 `null` 改用。 通常， `CreateTable` 呼叫會有 Alpha 色板的 `null` 第一個引數。

在有關[存取 SkiaSharp 點陣圖圖元位](../bitmaps/pixel-bits.md#posterization)的文章中的**Posterization**一節中，您已瞭解如何修改點陣圖的個別圖元位，以降低其色彩解析度。 這是一種稱為 _posterization_的技術。 

您也可以使用色彩表來分離點陣圖。 [重結合 **資料表** ] 頁面的函式會建立色彩表，將其索引對應至底部6位設為零的位元組：

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

此程式只會針對綠色和藍色通道選擇使用此色彩表。 紅色通道會持續獲得完整的解決方式：

[![分離表](color-filters-images/PosterizeTable.png "分離表")](color-filters-images/PosterizeTable-Large.png#lightbox)

您可以針對不同的色彩通道使用各種色彩表來產生各種效果。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)