---
title: SkiaSharp 中的路徑和文字
description: 本文將探討 SkiaSharp 路徑和文字的交集，並以範例程式碼示範這項處理。
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0964ad7d2bf517a6a4c7cf7965c346629716166
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936015"
---
# <a name="paths-and-text-in-skiasharp"></a>SkiaSharp 中的路徑和文字

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索路徑和文字的交集_

在新式圖形系統中，文字字型是字元外框的集合，通常是由二次方貝茲曲線所定義。 因此，許多新式圖形系統都包含一個可將文字字元轉換成圖形路徑的功能。

您已經看到，您可以對文字字元的外框進行筆觸，並加以填滿。 這可讓您以特定的筆劃寬度顯示這些字元外框，甚至是路徑[**效果**](effects.md)一文中所述的路徑效果。 但是，您也可以將字元字串轉換成 `SKPath` 物件。 這表示文字外框可以用於與使用[**路徑和區域裁剪**](clipping.md)一文中所述的技術進行裁剪。

除了使用路徑效果來對字元外框進行筆觸，您也可以根據從字元字串衍生的路徑建立路徑效果，甚至可以結合這兩種效果：

![文字路徑效果](text-paths-images/pathsandtextsample.png)

在前一篇有關[**路徑效果**](effects.md)的文章中，您已瞭解的 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) 方法 `SKPaint` 可以如何取得繪製路徑的外框。 您也可以使用此方法搭配衍生自字元外框的路徑。

最後，本文將示範路徑和文字的另一個交集：的 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法可 `SKCanvas` 讓您顯示文字字串，讓文字的基線遵循彎曲的路徑。

## <a name="text-to-path-conversion"></a>文字到路徑的轉換

的 [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) 方法會 `SKPaint` 將字元字串轉換成 `SKPath` 物件：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和 `y` 引數指出文字左邊基線的起點。 它們在這裡扮演的角色與的 `DrawText` 方法相同 `SKCanvas` 。 在路徑中，文字左邊的基準會有座標（x，y）。

`GetTextPath`如果您只想要填滿或筆劃結果路徑，則會大材小用方法。 一般的 `DrawText` 方法可讓您這麼做。 `GetTextPath`方法更適用于涉及路徑的其他工作。

其中一項工作是 [裁剪]。 [**裁剪文字**] 頁面會根據 "CODE" 一字的字元外框來建立裁剪路徑。 這個路徑會延伸到頁面的大小，以裁剪包含**裁剪文字**原始程式碼影像的點陣圖：

[![裁剪文字頁面的三重螢幕擷取畫面](text-paths-images/clippingtext-small.png)](text-paths-images/clippingtext-large.png#lightbox "裁剪文字頁面的三重螢幕擷取畫面")

類別的函式 [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) 會將儲存為內嵌資源的點陣圖載入至方案的 [**媒體**] 資料夾中：

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

`PaintSurface`處理常式一開始會建立 `SKPaint` 適用于文字的物件。 `Typeface`屬性已設定 `TextSize` ，以及，雖然針對這個特定應用程式， `TextSize` 屬性純粹是任意的。 另請注意，沒有任何 `Style` 設定。

`TextSize`和 `Style` 屬性設定不是必要的，因為此 `SKPaint` 物件僅用於 `GetTextPath` 使用文字字串 "CODE" 的呼叫。 然後，處理常式會測量結果 `SKPath` 物件，並套用三個轉換來將它置中，並將其調整為頁面的大小。 接著，您可以將路徑設定為裁剪路徑：

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

設定裁剪路徑之後，即可顯示點陣圖，並將其裁剪成字元外框。 請注意，使用的 [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) 方法 `SKRect` 會計算填滿頁面的矩形，同時保留長寬比。

[**文字路徑效果**] 頁面會將單一符號字元轉換成路徑，以建立1d 路徑效果。 接著會使用具有此路徑效果的油漆物件，針對該相同字元之較大版本的外框進行筆觸：

[![[文字路徑效果] 頁面的三重螢幕擷取畫面](text-paths-images/textpatheffect-small.png)](text-paths-images/textpatheffect-large.png#lightbox "[文字路徑效果] 頁面的三重螢幕擷取畫面")

類別中的大部分工作都 [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) 是出現在欄位和函式中。 `SKPaint`定義為欄位的兩個物件會用於兩個不同的用途：第一個（名為 `textPathPaint` ）是用來將具有50的連字號轉換成 `TextSize` 1d 路徑效果的路徑。 第二個（ `textPaint` ）是用來顯示與該路徑效果較大的連字號版本。 基於這個理由， `Style` 這個第二個 paint 物件的會設定為 `Stroke` ，但 `StrokeWidth` 不會設定屬性，因為在使用1d 路徑效果時，不需要該屬性：

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

此函式會先使用 `textPathPaint` 物件來測量具有50之的連字號 `TextSize` 。 接著，該矩形中央座標的否定會傳遞至 `GetTextPath` 方法，以將文字轉換成路徑。 結果路徑在字元的中央有（0，0）點，這適用于1D 路徑效果。

您可能會認為，在此函式 `SKPathEffect` 結尾建立的物件可以設定為的 `PathEffect` 屬性， `textPaint` 而不是儲存為欄位。 但這不是很好用，因為它會扭曲 `MeasureText` 處理常式中的呼叫結果 `PaintSurface` ：

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

該 `MeasureText` 呼叫會用來將頁面上的字元置中。 為避免發生問題，在 `PathEffect` 測量文字之後但顯示之前，屬性會設定為油漆物件。

## <a name="outlines-of-character-outlines"></a>字元外框輪廓

一般來說，的方法會藉 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) `SKPaint` 由套用繪製屬性來將一個路徑轉換成另一個，最值得注意的是筆觸寬度和路徑效果。 在沒有路徑效果的情況下使用時，會 `GetFillPath` 有效地建立概述另一個路徑的路徑。 這是在「[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)」一文中的點一下 [**路徑**] 頁面中示範。

您也可以 `GetFillPath` 在從傳回的路徑上呼叫， `GetTextPath` 但一開始您可能不會完全確定這看起來是什麼樣子。

[**字元外框大綱**] 頁面會示範這項技術。 所有相關的程式碼都位於 `PaintSurface` 類別的處理常式中 [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) 。

此函式會一開始先建立一個 `SKPaint` 名為 `textPaint` 的物件，其中包含以 `TextSize` 頁面大小為基礎的屬性。 這會使用方法轉換成路徑 `GetTextPath` 。 要在 `GetTextPath` 螢幕上有效置中路徑的座標引數：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`然後，處理常式會建立名為的新路徑 `outlinePath` 。 這會變成呼叫中的目的地路徑 `GetFillPath` 。 `StrokeWidth`25 的屬性會導致 `outlinePath` 描述文字字元的25圖元範圍路徑的外框。 此路徑接著會以紅色顯示，筆觸寬度為5：

[![字元外框大綱頁面的三向螢幕擷取畫面](text-paths-images/characteroutlineoutlines-small.png)](text-paths-images/characteroutlineoutlines-large.png#lightbox "字元外框大綱頁面的三向螢幕擷取畫面")

仔細查看，而您會看到 [重迭]，其中路徑外框會加上尖角。 這些是此程式的一般構件。

## <a name="text-along-a-path"></a>沿著路徑的文字

文字通常會顯示在水準基準上。 文字可以旋轉成以垂直或對角線的方式執行，但基準仍然是直線。

不過，有時候當您想要讓文字沿著曲線執行時。 這是的方法的用途 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) `SKCanvas` ：

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

第一個引數中指定的文字，會在指定為第二個引數的路徑中執行。 您可以使用引數，從路徑開頭的位移開始文字 `hOffset` 。 一般來說，路徑會形成文字的基準：文字 ascenders 位於路徑的一端，而文字下行則在另一端。 但是，您可以使用引數，從路徑位移文字基準 `vOffset` 。

這個方法沒有提供設定之屬性的指導方針，可 `TextSize` `SKPaint` 讓文字大小從路徑開頭開始到結尾。 有時候您可以自行找出該文字大小。 其他時候，您必須使用路徑測量函式，以在下一篇文章中描述[**路徑資訊和列舉**](information.md)。

「**圓形文字**」程式會圍繞圓形環繞文字。 很容易就能判斷圓形的圓周，因此可以很容易地調整文字大小，使其完全符合。 `PaintSurface`類別的處理常式會 [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) 根據頁面大小來計算圓形的半徑。 該圓形會變成 `circularPath` ：

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

`TextSize` `textPaint` 然後會調整的屬性，讓文字寬度符合圓形的圓周：

[![圓形文字頁面的三重螢幕擷取畫面](text-paths-images/circulartext-small.png)](text-paths-images/circulartext-large.png#lightbox "圓形文字頁面的三重螢幕擷取畫面")

文字本身也被選擇稍微迴圈：「圓形」一詞同時是句子的主旨和介係詞片語的物件。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
