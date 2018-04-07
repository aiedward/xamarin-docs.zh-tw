---
title: 路徑和文字
description: 瀏覽路徑和文字的交集
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/01/2017
ms.openlocfilehash: 9b3f906a23ed0d51237a244f3944104acc76e259
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="paths-and-text"></a>路徑和文字

_瀏覽路徑和文字的交集_

在現代的圖形系統中文字的字型會是字元外框輪廓，通常由二次方貝茲曲線的集合。 因此，許多現代圖形系統包含文字的字元轉換成圖形路徑的功能。 

您所見，您可以繪製外框的文字字元，以及填入。 這可讓您顯示中所述的特定筆劃寬度，即使路徑效果與這些字元外的框輪廓[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)發行項。 但是，也可以轉換成字元字串`SKPath`物件。 這表示文字概述可用來裁剪，以及所述的技術[**裁剪路徑和地區**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)發行項。 

除了使用路徑效果繪製字元大綱，您也可以建立路徑為基礎的效果衍生自字元字串的路徑，您甚至可以合併兩個的效果：

![](text-paths-images/pathsandtextsample.png "文字路徑效果")

在[前一篇文章](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)您已看到如何[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/)方法`SKPaint`可以取得繪製路徑的外框。 您也可以使用這個方法，以衍生自字元外框輪廓的路徑。

最後，本文將示範另一個路徑和文字的交集： [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法`SKCanvas`可讓您顯示的文字字串，使文字的基準遵循曲線的路徑。

## <a name="text-to-path-conversion"></a>路徑轉換成文字

[ `GetTextPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetTextPath/p/System.String/System.Single/System.Single/)方法`SKPaint`字元將字串轉換成`SKPath`物件：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和`y`引數表示文字的左邊算起的基準線的起點。 中的相同 role 此處為玩`DrawText`方法`SKCanvas`。 在該路徑中，左邊算起的基準線會有座標 （x，y）。 

`GetTextPath`方法是大材小用，如果您只想要填滿或繪製結果的路徑。 法線`DrawText`方法可讓您執行此作業。 `GetTextPath`方法會更有用包含路徑的其他工作。

其中一個工作裁剪。 **裁剪文字**頁面會建立裁剪路徑根據字元外框的文字 「 程式碼 」。 此路徑會延伸來裁剪包含影像的點陣圖頁面大小以**裁剪文字**原始程式碼：

[![](text-paths-images/clippingtext-small.png "三個螢幕擷取畫面的結束時間裁剪文字頁面")](text-paths-images/clippingtext-large.png#lightbox "裁剪文字頁面的三個螢幕擷取畫面")

[ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs)類別建構函式載入點陣圖儲存為內嵌資源中**媒體**方案的資料夾：

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

`PaintSurface`處理常式一開始會建立`SKPaint`適用於文字的物件。 `Typeface`屬性設定，以及`TextSize`，雖然此特定應用程式的`TextSize`屬性是純粹 arbirtrary。 也請注意有沒有`Style`設定。

`TextSize`和`Style`屬性設定不需要因為這`SKPaint`物件只會用於`GetTextPath`呼叫使用的文字字串"CODE"。 此處理常式然後測量結果`SKPath`物件，並套用的中央，並調整頁面大小的三個轉換。 路徑可以再將設定為裁剪路徑：

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

裁剪路徑設定之後，可以顯示點陣圖，，則會裁剪以字元外框。 請注意，使用[ `AspectFill` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRect.AspectFill/p/SkiaSharp.SKSize/)方法`SKRect`計算的填滿頁面，同時保留外觀比例的矩形。

**文字路徑效果**頁面將單一連字號字元轉換成路徑，以建立 1d 路徑效果。 小畫家物件並且將此路徑的影響會被用來繪製外框的較大的相同字元的版本：

[![](text-paths-images/textpatheffect-small.png "文字路徑效果頁面的三個螢幕擷取畫面")](text-paths-images/textpatheffect-large.png#lightbox "文字路徑效果頁面的三個螢幕擷取畫面")

中的工作更[ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs)類別中的欄位和建構函式，就會發生。 這兩個`SKPaint`物件定義欄位適用於兩個不同的用途如下： 第一個 (名為`textPathPaint`) 用來轉換以連字號`TextSize`的 50%到 1d 路徑效果的路徑。 第二個 (`textPaint`) 用來顯示的連字號，並且將該路徑的影響較大的版本。 基於這個原因，`Style`這個第二個 [小畫家] 的物件設定為`Stroke`，但`StrokeWidth`屬性沒有設定，因為該屬性就不需要使用 1d 路徑效果時：

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

建構函式會先使用`textPathPaint`物件以測量與連字號`TextSize`為 50。 接著會傳遞至該矩形的中心座標的否定`GetTextPath`方法，將文字轉換成路徑。 結果路徑具有 （0，0） 中的字元，相當適合 1d 路徑效果的中心點。

您可能會認為`SKPathEffect`結尾的建構函式所建立的物件可能會設定為`PathEffect`屬性`textPaint`而不是儲存為一個欄位。 但此開啟出不是很好運作，因為它會扭曲結果的`MeasureText`呼叫中`PaintSurface`處理常式：

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

確認`MeasureText`呼叫用於置中對齊頁面上的字元。 若要避免問題，`PathEffect`測量文字後，但它會顯示屬性設定為 [小畫家] 物件。

## <a name="outlines-of-character-outlines"></a>字元外框輪廓的外框

通常[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/)方法`SKPaint`藉由套用 [小畫家] 屬性，將一個路徑轉換成另一個最值得注意的是筆劃寬度及路徑效果。 如果使用任何路徑的效果，`GetFillPath`有效建立概述另一個路徑的路徑。 中所示範的此**點選外框以路徑**頁面[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)發行項。

您也可以呼叫`GetFillPath`從傳回的路徑上`GetTextPath`但是一開始您可能無法完全確定所希望的外觀。

**字元大綱外框輪廓**頁面示範的技術。 所有相關的程式碼位於`PaintSurface`處理常式的[ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs)類別。

建構函式一開始會建立`SKPaint`名為物件`textPaint`與`TextSize`屬性根據頁面大小。 這會轉換成路徑，使用`GetTextPath`方法。 座標的引數`GetTextPath`有效地置中在螢幕上的路徑：

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

`PaintSurface`處理常式接著會建立名為的新路徑`outlinePath`。 這會變成目的地路徑的呼叫中`GetFillPath`。 `StrokeWidth`屬性 25 原因`outlinePath`來描述 25 像素寬路徑繪製文字字元的外框。 此路徑然後筆劃寬度為 5 的紅色顯示：

[![](text-paths-images/characteroutlineoutlines-small.png "字元大綱外框輪廓頁面的三個螢幕擷取畫面")](text-paths-images/characteroutlineoutlines-large.png#lightbox "字元大綱外框輪廓頁面的三個螢幕擷取畫面")

請仔細查看，您會看到重疊項目路徑外框，使尖角。 這些是標準的成品，此程序。

## <a name="text-along-a-path"></a>沿著路徑的文字

水平的基準，通常會顯示文字。 文字方塊可以旋轉至垂直或對角線，執行但基準仍然是直線。

有的時間，不過，當您想要執行沿著曲線文字。 這是目的[ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法`SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

第一個引數中指定的文字對執行做為第二個引數所指定的路徑。 您可以開始的位移，從具有路徑的開頭文字`hOffset`引數。 路徑通常 form 文字的基準線： 文字上升部位路徑的某一邊，而文字容納彼此。 指定與路徑中的文字比較基準的位移，但`vOffset`引數。

這個方法沒有功能設定會提供指引`TextSize`屬性`SKPaint`，使大小完全執行路徑的開頭到結尾的文字。 有時候您可以找出您自己的文字大小。 有時候，您必須使用路徑測量函數，以在未來的發行項中說明。

**循環文字**程式圓形周圍包裝文字。 所以可以輕鬆地判斷圓的圓周，因此很容易就能調整大小以完全符合的文字。 `PaintSurface`處理常式的[ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs)類別計算根據頁面大小圓形的半徑。 該圓圈會變成`circularPath`:

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

`TextSize`屬性`textPaint`再調整，讓文字寬度符合圓形的圓周：

[![](text-paths-images/circulartext-small.png "三個螢幕擷取畫面的循環的文字頁面")](text-paths-images/circulartext-large.png#lightbox "循環的文字頁面的三個螢幕擷取畫面")

選擇文字本身也是有些循環： word"circle"是兩者的句子的主旨和前置詞片語的物件。 

## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
