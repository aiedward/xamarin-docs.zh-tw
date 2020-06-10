---
title： "SkiaSharp color filters" 描述： "使用色彩篩選來轉換色彩與轉換或資料表。"
skiasharp 的 assetid： 774E7B55-AEC8-4F12-B657-1C0CEE01AD63 author： davidbritch ms-chap： dabritch ms. date： 08/28/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="skiasharp-color-filters"></a>SkiaSharp 色彩篩選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

色彩篩選可以將點陣圖（或其他影像）中的色彩轉譯為其他色彩，以 posterization 效果：

![色彩篩選範例](color-filters-images/ColorFiltersExample.png "色彩篩選範例")

若要使用色彩篩選，請將的 [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) 屬性設定 `SKPaint` 為 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) 類別的其中一個靜態方法所建立之類型的物件。 本文示範： 

- 使用方法建立的色彩轉換 [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 。
- 使用方法建立的色彩表 [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 。

## <a name="the-color-transform"></a>色彩轉換

色彩轉換牽涉到使用矩陣來修改色彩。 就像大部分2D 圖形系統一樣，SkiaSharp 會使用大部分的矩陣，將座標點轉換為 SkiaSharp 中發行項[**矩陣轉換中**](../transforms/matrix.md)的 iscussed。 [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)也支援矩陣轉換，但矩陣會轉換 RGB 色彩。 若要瞭解這些色彩轉換，必須熟悉矩陣概念。 

色彩轉換矩陣具有四個數據列和五個數據行的維度：

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

它會將 RGB 來源色彩（R、G、B、A）轉換成目的地色彩（R '、G '、B '、A '）。 

為了準備矩陣乘法，會將來源色彩轉換成5×1矩陣：

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

這些 R、G、B 和值都是從0到255的原始位元組。 它們_不_會正規化為0到1範圍內的浮點值。

轉譯因數需要額外的資料格。 這類似于使用3×3矩陣轉換二維座標點，如有關使用矩陣來轉換座標點一文中的「 [**3 到**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix)3」矩陣的原因一節中所述。

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

大部分的矩陣都是由通常在0到2範圍內的乘法因數所組成。 不過，最後一個資料行（M15 到 M45）包含公式中新增的值。 這些值的範圍通常是從0到255。 結果會在0和255的值之間壓制。

身分識別矩陣為：

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

這不會變更色彩。 轉換公式為：

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

M44 資料格非常重要，因為它會保留不透明度。 一般來說，M41、M42 和 Imb-m43 都是零，因為您可能不想要以紅色、綠色和藍色值為基礎的不透明度。 但是，如果 M44 為零，則 ' 將為零，且不會顯示任何內容。

色彩矩陣最常見的用途之一，就是將色彩點陣圖轉換成灰階點陣圖。 這牽涉到紅色、綠色和藍色值加權平均值的公式。 針對使用 sRGB （「標準紅色綠色藍色」）色彩空間的影片顯示，此公式為：

灰色-網底 = 0.2126 ·R + 0.7152 ·G + 0.0722 ·位元組

若要將色彩點陣圖轉換成灰階點陣圖，R '、G ' 和 B ' 結果必須全部等於相同的值。 矩陣如下：

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

沒有對應至此矩陣的 SkiaSharp 資料類型。 相反地，您必須以資料列順序中的20個值陣列來表示矩陣 `float` ：第一列、第二列，依此類推。

靜態 [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) 方法具有下列語法：

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

其中 `matrix` 是20個值的陣列 `float` 。 在 c # 中建立陣列時，很容易就能將數位格式化，使其類似于4×5矩陣。 這會在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的 [**灰階矩陣**] 頁面中示範：

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

`DrawBitmap`這段程式碼中使用的方法是來自[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例所包含的**BitmapExtension.cs**檔案。 

以下是在 iOS、Android 和通用 Windows 平臺上執行的結果：

[![灰階矩陣](color-filters-images/GrayScaleMatrix.png "灰階矩陣")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

留意第四個數據列和第四個數據行中的值。 這是重要的因素，乘以轉換色彩之 A 值的原始色彩值。 如果該儲存格為零，將不會顯示任何內容，而且可能很容易找到問題。

使用色彩矩陣進行實驗時，您可以從來源的觀點或目的地的觀點來處理轉換。 來源的紅色圖元如何對目的地的紅色、綠色和藍色圖元造成影響？ 這是由矩陣的第一個資料_行_中的值所決定。 或者，目的地紅色圖元應該如何受到來源的紅色、綠色和藍色圖元的影響？ 這是由矩陣的第一個資料_列_所決定。

如需有關如何使用色彩轉換的一些概念，請參閱重新[**著色影像**](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images)頁面。 討論的顧慮 Windows Forms，而矩陣的格式不同，但概念也相同。

**蠟筆矩陣**會藉由 attenuating 來源紅色圖元來計算目的地紅色圖元，並稍微強調紅色和綠色圖元。 這個程式會以類似綠色和藍色圖元的方式發生：

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

結果是將色彩的濃度設為靜音，如下所示：

[![蠟筆矩陣](color-filters-images/PastelMatrix.png "蠟筆矩陣")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>色彩表

靜態 [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) 方法有兩個版本：

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

陣列一律包含256專案。 在 `CreateTable` 具有一個資料表的方法中，相同的資料表會用於紅色、綠色和藍色元件。 這是一個簡單的查閱資料表：如果來源色彩是（R，G，B），而目的地色彩是（R '，B '，G '），則會使用來源元件編制索引來取得目的地元件 `table` ：

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

在第二個方法中，四個色彩元件都可以有個別的色彩表，或相同的色彩資料表可能會在兩個或多個元件之間共用。

如果您想要將第二個方法的其中一個引數設定 `CreateTable` 為包含順序值0到255的色彩資料表，您可以改用 `null` 。 呼叫通常會 `CreateTable` 有 Alpha 色板的 `null` 第一個引數。

在有關[存取 SkiaSharp 點陣圖圖元位](../bitmaps/pixel-bits.md#posterization)文章中的**Posterization**一節中，您已瞭解如何修改點陣圖的個別圖元位，以減少其色彩解析度。 這是一種稱為_posterization_的技術。 

您也可以使用色彩表來分離點陣圖。 [子**表資料表**] 頁面的 [程式集] 會建立一個色彩資料表，將其索引對應至將底部6個位設定為零的位元組：

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

程式會選擇僅針對綠色和藍色的通道使用此色彩表。 紅色通道會繼續具有完整的解析度：

[![色調分離表](color-filters-images/PosterizeTable.png "色調分離表")](color-filters-images/PosterizeTable-Large.png#lightbox)

您可以針對不同的色彩通道使用各種色彩表來進行各種效果。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
