---
title: SkiaSharp 點陣圖基本概念
description: 本文說明如何從各種來源載入 SkiaSharp 點陣圖並將它們顯示在 Xamarin.Forms 應用程式，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: cbce6f414586597dc2b2788aa18b03228c128018
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130955"
---
# <a name="bitmap-basics-in-skiasharp"></a>SkiaSharp 點陣圖基本概念

_從各種來源載入點陣圖並加以顯示。_

SkiaSharp 點陣圖的支援是很龐大。 本文章涵蓋的基本概念&mdash;如何載入點陣圖，以及如何顯示它們：

![](bitmaps-images/bitmapssample.png "顯示的兩個點陣圖")

SkiaSharp 點陣圖必須是類型的物件[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/)。 有許多方式可建立點陣圖，但這篇文章會限制本身[ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/System.IO.Stream/)方法，從.NET 載入點陣圖`Stream`物件。

**基本點陣圖**頁面**SkiaSharpFormsDemos**程式示範如何從三個不同的來源載入點陣圖：

- 透過網際網路
- 從資源內嵌在可執行檔
- 從使用者的相片媒體櫃

三個`SKBitmap`物件的下列三個來源定義為中的欄位[ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs)類別：

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

若要載入點陣圖，URL 為基礎，您可以使用[ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0)類別。 您應該只有一個執行個體具現化`HttpClient`並重複使用它，因此將它儲存為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用時`HttpClient`iOS 和 Android 應用程式，您會想要設定專案屬性中所述的文件上**[傳輸層安全性 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**。

因為它是最方便的方式使用`await`運算子搭配`HttpClient`，不能在執行的程式碼`BasicBitmapsPage`建構函式。 相反地，它屬於`OnAppearing`覆寫。 URL 會指向使用一些範例點陣圖 Xamarin 網站上的區域。 在網站上的套件可讓附加調整大小的點陣圖為特定寬度的規格：


```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Android 使用時，會引發例外狀況`Stream`傳回從`GetStreamAsync`在`SKBitmap.Decode`方法因為它正在執行主執行緒上的長時間作業。 基於這個理由，點陣圖檔案的內容複製到`MemoryStream`物件使用`CopyToAsync`。

靜態`SKBitmap.Decode`方法會負責解碼點陣圖檔案。 它會搭配 JPEG、 PNG、 GIF 和其他數種熱門的點陣圖格式，並將結果儲存在內部 SkiaSharp 格式。 在此時`SKCanvasView`需要失效，以允許`PaintSurface`處理常式來更新顯示。 

## <a name="loading-a-bitmap-resource"></a>正在載入點陣圖資源

程式碼，以載入點陣圖的最簡單方法直接在您的應用程式中包括點陣圖資源。 **SkiaSharpFormsDemos**計畫包含名為的資料夾**媒體**包含名為的點陣圖檔案**monkey.png**。 在 [**屬性**] 對話方塊對這個檔案中，您必須提供這類檔案**建置動作**的**內嵌資源**！

每個內嵌的資源都*資源識別碼*構成的專案名稱、 資料夾與檔案名稱，所有已連線的週期： **SkiaSharpFormsDemos.Media.monkey.png**。 您可以指定該資源，以取得存取此資源識別碼做為引數[ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法[ `Assembly` ](xref:System.Reflection.Assembly)類別：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

這`Stream`物件可以直接傳遞`SKBitmap.Decode`方法。

## <a name="loading-a-bitmap-from-the-photo-library"></a>從相片庫載入點陣圖

您也可讓使用者從裝置的圖片庫載入相片。 Xamarin.Forms 本身所未提供這項功能。 作業要求的相依性服務，例如本文所述[挑選相片圖片庫從](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

**IPhotoLibrary.cs**中的檔案**SkiaSharpFormsDemos**專案和三個**PhotoLibrary.cs**平台專案中的檔案已經過修改從那篇文章。 此外，Android **MainActivity.cs**一文所述，檔案已修改和 iOS 專案具有權限可存取兩行的底端與相片媒體櫃**info.plist**檔案。

`BasicBitmapsPage`建構函式加入`TapGestureRecognizer`到`SKCanvasView`點選的通知。 在點選，收到`Tapped`處理常式會取得存取圖片選擇器相依性服務和呼叫`GetImageStreamAsync`。 如果`Stream`會傳回物件，則內容會複製到`MemoryStream`、 視需要一些平台。 程式碼的其餘部分則類似於其他兩種技術：

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

請注意，`Tapped`處理常式會呼叫`InvalidateSurface`方法`SKCanvasView`物件。 這會產生新的呼叫`PaintSurface`處理常式。

## <a name="displaying-the-bitmaps"></a>顯示點陣圖

`PaintSurface`處理常式需要顯示三個點陣圖。 處理常式會假設電話處於直向模式，並將畫布以垂直方式分割成三個相等的部分。

會顯示與最簡單的第一個點陣圖[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/)方法。 您只需要指定是點陣圖左上角放置所在的 X 和 Y 座標：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

雖然`SKPaint`定義參數，它有預設值是`null`，您可以忽略它。 點陣圖的像素會直接轉換為一對一的對應與顯示表面的像素。

程式可以取得包含點陣圖的像素尺寸[ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/)並[ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/)屬性。 這些內容可讓程式，以計算要放置點陣圖畫布右上第三個中央座標：

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

版本會顯示其他兩個點陣圖[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/)使用`SKRect`參數：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

第三個新版[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/)有兩個`SKRect`本文中不使用引數來指定要顯示，但該版本的點陣圖矩形子集。

以下是顯示從內嵌的資源點陣圖載入點陣圖的程式碼：

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

點陣圖縮放至矩形中，這就是為什麼 monkey 水平縮放這些螢幕擷取畫面中的維度：

[![](bitmaps-images/basicbitmaps-small.png "三個基本的點陣圖頁面的螢幕擷取畫面")](bitmaps-images/basicbitmaps-large.png#lightbox "三個基本的點陣圖頁面的螢幕擷取畫面")

第三個映像&mdash;如果您執行程式，並從您自己的圖片庫載入相片，只可以看到&mdash;也會顯示在矩形中，但該矩形的位置和大小會調整，以維持點陣圖的長寬比。 此計算是稍微複雜，因為它需要計算縮放比例取決於點陣圖和目的地矩形的大小和置中顯示該區域內的矩形：

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

如果沒有點陣圖具有尚未載入從圖片庫中，則`else`區塊顯示一些要提示使用者點選螢幕的文字。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [從 圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
