---
title: SkiaSharp 色彩篩選器
description: 將色彩轉換或資料表使用的色彩篩選。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 7edb504a228612d7f1f1fee10a50a467fbb5fc6c
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057096"
---
# <a name="skiasharp-color-filters"></a>SkiaSharp 色彩篩選器

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

色彩篩選可以轉譯成其他色彩之色調分離例如效果的點陣圖 （或其他映像） 中的色彩：

![色彩篩選器範例](color-filters-images/ColorFiltersExample.png "色彩篩選器範例")

若要使用的色彩篩選，將[ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter)屬性`SKPaint`物件的型別[ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)建立其中一種靜態方法，該類別中。 這篇文章示範： 

- 使用建立色彩轉換[ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*)方法。
- 使用建立色彩表[ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*)方法。

## <a name="the-color-transform"></a>將色彩轉換

將色彩轉換牽涉到使用矩陣來修改色彩。 SkiaSharp 大部分 2D 圖形和系統一樣，大部分用於轉換座標點為 iscussed 文件中使用矩陣[ **SkiaSharp 中轉換的矩陣**](../transforms/matrix.md)。 [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter)也支援矩陣轉換，但矩陣轉換 RGB 色彩的支援。 疐裾矩陣概念，才能了解這些色彩轉換。 

色彩轉換矩陣有四個資料列和五個資料行的維度：

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

它會將 RGB 來源色彩 (R、 G、 B、 A) 轉換為目的地的色彩 (R'，G'，B '、 ')。 

在矩陣相乘的準備，來源色彩轉換成 5 × 1 矩陣：

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

這些 R、 G、 B 和 A 值是從 0 到 255 範圍內的原始位元組。 它們_不_正規化為 0 到 1 的範圍中的浮點值。

轉譯因數需要額外的儲存格。 這相當於使用 3 × 3 矩陣轉換二維座標點一節中所述[**原因 3-3 矩陣**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix)有關使用矩陣轉換的文章座標點。

4 × 5 矩陣乘以 5 × 1 矩陣中，而且產品是 4 × 1 的矩陣轉換後的色彩：

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

以下是不同的公式，R 的 '，G'，B' 和 ':

R' = M11·R + M12·G + M13·B + M14·A + M15 

G' = M21·R + M22·G + M23·B + M24·A + M25 

B' = M31·R + M32·G + M33·B + M34·A + M35 

A' = M41·R + M42·G + M43·B + M44·A + M45 

大部分的矩陣乘法類的因素，通常是在 0 到 2 的範圍所組成。 不過，最後一個資料行 (透過 M45 M15) 包含在公式中新增的值。 這些值通常範圍從 0 到 255 之間。 結果會限制的值為 0 與 255 之間。

身分識別矩陣是：

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

這並不會變更色彩。 轉換公式如下：

R' = R 

G' = G

B' = B

A' = A

M44 儲存格是非常重要，因為它會保留不透明度。 它通常是 M41、 M42 和 M43 是所有的零，則您可能不想讓紅色、 綠色和藍色值為基礎的不透明度。 但是，如果 M44 是零，則 A' 將會是零，並不會顯示。

色彩矩陣的最常見的用途之一是將色彩點陣圖轉換成灰階點陣圖。 這牽涉到紅色、 綠色和藍色值的加權平均的公式。 使用 （「 標準紅色綠色藍色 」） 的 sRGB 色彩空間的顯示器，此公式是：

灰色陰影 = 0.2126·R + 0.7152·G + 0.0722·B

若要將色彩點陣圖轉換成灰階點陣圖，R'，G'，和 B 的結果必須全部等於該相同的值。 矩陣是：

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

沒有對應至此矩陣 SkiaSharp 資料類型。 您必須改為代表矩陣為 20 個陣列`float`中資料列順序的值： 第一個資料列，則第二個資料列，等等。

靜態[ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*)方法具有下列語法：

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

何處`matrix`是 20 個陣列`float`值。 建立陣列時C#，就可以輕鬆地格式化數字，讓它們看起來如 4 × 5 矩陣。 這示範於**灰階矩陣**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例：

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

`DrawBitmap`在這段程式碼中使用的方法是來自**BitmapExtension.cs**隨附的檔案[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例。 

以下是在 iOS、 Android 和通用 Windows 平台上執行的結果：

[![灰階矩陣](color-filters-images/GrayScaleMatrix.png "灰階矩陣")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

請留意中的第四個資料列和第四個資料行的值。 這是重要的因素，乘以 A 原始色彩的值 ' 已轉換的色彩值。 如果該資料格為零，將不會顯示，問題可能很難尋找。

當實驗使用色彩矩陣，您可以從檢視方塊的來源或目的端的觀點來看處理轉換。 紅色的像素的來源應該如何影響到目的地的紅色、 綠色和藍色像素？ 在第一個值所決定_資料行_的矩陣。 或者，應該目的紅色像素影響來源的紅色、 綠色和藍色像素？ 第一個由_資料列_的矩陣。

如需如何使用色彩轉換的一些概念，請參閱 < [**著色的映像**](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images)頁面。 這個討論 Windows Form 的矩陣是不同的格式，但概念都一樣。

**淡色矩陣**attenuating 來源紅色像素，並稍微強調的紅色和綠色的像素計算目的地紅色像素。 此程序就會發生同樣的綠色和藍色的像素：

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

結果是靜音的色彩的濃度，您可以在這裡看到：

[![淡色矩陣](color-filters-images/PastelMatrix.png "淡色矩陣")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>色彩表

靜態[ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*)方法有兩種版本：

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

陣列一律包含 256 個項目。 在 `CreateTable`方法有一個資料表中，同一個資料表用於紅色、 綠色和藍色元件。 它是一個簡單的查閱資料表： 如果來源色彩是 (R、 G、 B)，而目的色彩是 (R'，B'、 G')，然後透過編製索引所取得的目的地元件`table`來源元件：

R' = 資料表 [R]

G' = 資料表 [G]

B' = 資料表 [B]

在第二個方法中，每四個色彩元件可以有不同的色彩表，或可能會在兩個或多個元件之間共用相同的色彩表。

如果您想要有一個引數設定為第二個`CreateTable`色彩表，其中包含 0 到 255 的值序列中的方法，您可以使用`null`改。 經常`CreateTable`呼叫有`null`alpha 色頻的第一個引數。

上一節**色調分離**有關的文章[存取 SkiaSharp 點陣圖像素位元](../bitmaps/pixel-bits.md#posterization)，您可了解如何修改來降低其色彩解析度的點陣圖的像素位元。 這是呼叫的技巧_色調分離_。 

您也可以 posterize 色彩表的點陣圖。 建構函式**Posterize 資料表**頁面建立設為零的 6 位元會對應至下一個位元組索引色彩表：

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

程式會選擇只將這個色彩表用於的綠色和藍色色頻。 紅色色頻，繼續讓完整的解決方法：

[![Posterize 表格](color-filters-images/PosterizeTable.png "Posterize 資料表")](color-filters-images/PosterizeTable-Large.png#lightbox)

您可以使用各種不同的色彩表的各種效果的不同的色頻。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
