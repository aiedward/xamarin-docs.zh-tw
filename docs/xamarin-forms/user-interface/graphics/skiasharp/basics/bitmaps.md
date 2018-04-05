---
title: 點陣圖的基本概念
description: 從各種來源載入點陣圖並加以顯示。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: 688c6218f9ac66e3dfd6cd157e43f9b639e124c6
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="bitmap-basics"></a>點陣圖的基本概念

_從各種來源載入點陣圖並加以顯示。_

龐大的 SkiaSharp 中的點陣圖支援。 本文件涵蓋的基本概念&mdash;如何載入點陣圖，以及如何顯示它們：

![](bitmaps-images/bitmapssample.png "顯示的兩個點陣圖")

SkiaSharp 點陣圖必須是類型的物件[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/)。 有許多方法來建立點陣圖，但這篇文章限制本身[ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/)方法，載入從點陣圖[ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/)參考點陣圖檔案的物件。 方便使用[ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/)類別衍生自`SKStream`因為它有一個建構函式可接受.NET [ `Stream` ](https://developer.xamarin.com/api/type/System.IO.Stream/)物件。

**基本點陣圖**頁面**SkiaSharpFormsDemos**程式會示範如何從三個不同的來源載入點陣圖：

- 透過網際網路
- 從可執行檔中內嵌的資源
- 從使用者的相片程式庫

三個`SKBitmap`物件做為欄位中定義這些三個來源[ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs)類別：

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>從 Web 載入點陣圖

若要載入點陣圖，根據 URL，您可以使用[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)類別，執行中的下列程式碼所示`BasicBitmapsPage`建構函式。 在 Xamarin 網站上的某些範例點陣圖區域會指向的 URL。 在網站上的封裝可讓您附加的調整大小的點陣圖特定寬度規格：

```csharp
Uri uri = new Uri("http://developer.xamarin.com/demo/IMG_3256.JPG?width=480");
WebRequest request = WebRequest.Create(uri);
request.BeginGetResponse((IAsyncResult arg) =>
{
    try
    {
        using (Stream stream = request.EndGetResponse(arg).GetResponseStream())
        using (MemoryStream memStream = new MemoryStream())
        {
            stream.CopyTo(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            using (SKManagedStream skStream = new SKManagedStream(memStream))
            {
                webBitmap = SKBitmap.Decode(skStream);
            }
        }
    }
    catch
    {
    }

    Device.BeginInvokeOnMainThread(() => canvasView.InvalidateSurface());

}, null);
```

已成功下載點陣圖，回呼方法會傳遞至`BeginGetResponse`方法執行。 `EndGetResponse`呼叫必須處於`try`封鎖萬一發生錯誤。 `Stream`取自物件`GetResponseStream`不足夠，在某些平台，因此點陣圖內容會複製到`MemoryStream`物件。 此時，`SKManagedStream`建立物件。 現在這個參考中的點陣圖檔案可能是 JPEG 或 PNG 檔案。 `SKBitmap.Decode`方法將解碼的點陣圖檔案，並將結果儲存在內部 SkiaSharp 格式。

回呼方法傳遞至`BeginGetResponse`執行之後建構函式已完成執行，這表示`SKCanvasView`失效允許需要`PaintSurface`處理常式，以更新顯示。 不過，`BeginGetResponse`回呼會在執行時，次要執行緒中執行，因此需要使用`Device.BeginInvokeOnMainThread`執行`InvalidateSurface`使用者介面執行緒中的方法。

## <a name="loading-a-bitmap-resource"></a>載入點陣圖資源

程式碼，以載入點陣圖的最簡單方法直接在您的應用程式中包括點陣圖資源。 **SkiaSharpFormsDemos**程式包括名為的資料夾**媒體**包含名為點陣圖檔案**monkey.png**。 在**屬性**對話方塊對這個檔案中，您必須提供這類檔案**建置動作**的**內嵌資源**！

每個內嵌的資源具有*資源識別碼*所組成的專案名稱、 資料夾與檔案名稱，所有已連線以句號： **SkiaSharpFormsDemos.Media.monkey.png**。 您可以藉由指定該資源取得存取此資源做為引數的 ID [ `GetManifestResourceStream` ](https://developer.xamarin.com/api/member/System.Reflection.Assembly.GetManifestResourceStream/p/System.String/)方法[ `Assembly` ](https://developer.xamarin.com/api/type/System.Reflection.Assembly/)類別：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

這`Stream`物件可以直接轉換成`SKManagedStream`物件。

## <a name="loading-a-bitmap-from-the-photo-library"></a>從相片程式庫載入點陣圖

它也可讓使用者從裝置的圖片庫載入相片。 Xamarin.Forms 本身所未提供此功能。 作業要求的相依性服務，例如文件所述的一個[挑選相片圖片庫從](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

**IPicturePicker.cs**檔案和三個**PicturePickerImplementation.cs**從這個發行項的檔案已複製的不同專案**SkiaSharpFormsDemos**方案，並提供新的命名空間名稱。 此外，在 Android **Weatherapp**檔案已修改文章中所述，iOS 專案具有與兩個線條的底端相片類別庫的存取權限**info.plist**檔案。

`BasicBitmapsPage`建構函式加入`TapGestureRecognizer`至`SKCanvasView`點選要收到通知。 在收到點選，`Tapped`處理常式會取得存取圖片選擇器相依服務和呼叫`GetImageStreamAsync`。 如果`Stream`傳回物件時，則內容會複製到`MemoryStream`、 依部分平台。 程式碼的其餘部分是類似於其他兩種技術：

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPicturePicker picturePicker = DependencyService.Get<IPicturePicker>();

    using (Stream stream = await picturePicker.GetImageStreamAsync())
    {
        if (stream != null)
        {
            using (MemoryStream memStream = new MemoryStream())
            {
                stream.CopyTo(memStream);
                memStream.Seek(0, SeekOrigin.Begin);

                using (SKManagedStream skStream = new SKManagedStream(memStream))
                {
                    libraryBitmap = SKBitmap.Decode(skStream);
                }
            }
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

請注意，`Tapped`處理常式呼叫`InvalidateSurface`方法`SKCanvasView`物件。 這會產生新的呼叫`PaintSurface`處理常式。

## <a name="displaying-the-bitmaps"></a>顯示點陣圖

`PaintSurface`處理常式需要顯示三個點陣圖。 此處理常式會假設電話處於直向模式，而且畫布垂直分成三個相等的部分。

會顯示與最簡單的第一個點陣圖[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/)方法。 您只需要指定的 X 和 Y 座標位置點陣圖的左上角的放置：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

雖然`SKPaint`參數定義，其具有預設值是`null`，您可以忽略它。 點陣圖的像素只會傳輸為一對一的對應與顯示表面的像素。

程式可以取得的點陣圖的像素尺寸[ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/)和[ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/)屬性。 這些內容可讓程式能夠計算座標中央畫布左上第三個點陣圖：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

其他兩個點陣圖顯示的版本[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/)與`SKRect`參數：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

第三個版本[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有兩個`SKRect`這篇文章不能用來指定要顯示，但該版本的點陣圖矩形子集的引數。

若要顯示點陣圖從內嵌的資源點陣圖載入的程式碼如下：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

點陣圖會延伸以矩形，這就是為什麼猴子水平伸展這些螢幕擷取畫面中的維度：

[![](bitmaps-images/basicbitmaps-small.png "三個基本點陣圖頁面的螢幕擷取畫面")](bitmaps-images/basicbitmaps-large.png#lightbox "三個基本點陣圖頁面的螢幕擷取畫面")

第三個影像&mdash;只可以看到您執行程式並從您自己的圖片庫載入相片&mdash;也會顯示在矩形中，但該矩形的位置和大小會調整，以維持點陣圖的長寬比。 此計算會稍微更為複雜，因為它需要計算縮放比例根據點陣圖和目的地矩形的大小和置中以該區域的矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

如果沒有點陣圖尚未載入從圖片庫中，然後在`else`區塊會顯示某些文字以提示使用者點選螢幕。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [從 圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
