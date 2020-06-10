---
title： "SkiaSharp 中的點陣圖基本概念" 描述： "本文說明如何從各種來源載入 SkiaSharp 中的點陣圖，並將其顯示在 Xamarin.Forms 應用程式中，並以範例程式碼示範。
skiasharp 的 assetid： 32C95DFF-9065-42D7-966C-D3DBD16906B3 author： davidbritch ms-chap： dabritch ms. date： 07/17/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="bitmap-basics-in-skiasharp"></a>SkiaSharp 中的點陣圖基本概念

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_從各種來源載入點陣圖並加以顯示。_

SkiaSharp 中的點陣圖支援相當廣泛。 本文只涵蓋 &mdash; 如何載入點陣圖和如何顯示它們的基本概念：

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

在[SkiaSharp 點陣圖](../bitmaps/index.md)一節中可以找到更深入的點陣圖探索。

SkiaSharp 點陣圖是類型的物件 [`SKBitmap`](xref:SkiaSharp.SKBitmap) 。 建立點陣圖的方式有很多種，但本文會將本身限制為 [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) 從 .net 物件載入點陣圖的方法 `Stream` 。

**SkiaSharpFormsDemos**程式中的 [**基本點陣圖**] 頁面會示範如何從三個不同的來源載入點陣圖：

- 透過網際網路的
- 從可執行檔中內嵌的資源
- 從使用者的相片媒體櫃

`SKBitmap`這三個來源的三個物件會定義為類別中的欄位 [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) ：

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

若要載入以 URL 為基礎的點陣圖，您可以使用 [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) 類別。 您應該只具現化的一個實例 `HttpClient` 並重複使用它，因此請將它儲存為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用搭配 `HttpClient` iOS 和 Android 應用程式時，您會想要設定專案屬性，如**[傳輸層安全性（TLS） 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 檔中所述。

因為搭配使用運算子是最方便的 `await` 方式 `HttpClient` ，所以無法在此函式中執行程式碼 `BasicBitmapsPage` 。 相反地，它是覆寫的一部分 `OnAppearing` 。 這裡的 URL 會指向 Xamarin 網站上的某個區域，其中包含一些範例點陣圖。 網站上的封裝可讓您附加規格，將點陣圖大小調整為特定寬度：

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

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

當使用方法中傳回的時，Android 作業系統 `Stream` 會引發例外 `GetStreamAsync` 狀況， `SKBitmap.Decode` 因為它會在主執行緒上執行冗長的作業。 基於這個理由，點陣圖檔案的內容會使用複製到 `MemoryStream` 物件 `CopyToAsync` 。

靜態 `SKBitmap.Decode` 方法負責解碼點陣圖檔案。 其適用于 JPEG、PNG 和 GIF 點陣圖格式，並將結果儲存為內部 SkiaSharp 格式。 此時，必須失效， `SKCanvasView` 才能讓 `PaintSurface` 處理常式更新顯示。

## <a name="loading-a-bitmap-resource"></a>載入點陣圖資源

就程式碼而言，載入點陣圖最簡單的方法，就是直接在您的應用程式中包含點陣圖資源。 **SkiaSharpFormsDemos**套裝程式含名為**Media**的資料夾，其中包含數個位圖檔案，包括一個名為**monkey.png**。 對於儲存為程式資源的點陣圖，您必須使用 [**屬性**] 對話方塊為檔案提供**內嵌資源**的**組建動作**！

每個內嵌資源都有一個*資源識別碼*，其中包含專案名稱、資料夾和檔案名，所有都是依期間連接： **SkiaSharpFormsDemos.Media.monkey.png**。 您可以藉由將資源識別碼指定為類別之方法的引數，來取得此資源的存取權 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) [`Assembly`](xref:System.Reflection.Assembly) ：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

這個 `Stream` 物件可以直接傳遞至 `SKBitmap.Decode` 方法。

## <a name="loading-a-bitmap-from-the-photo-library"></a>從相片媒體櫃載入點陣圖

使用者也可以從裝置的圖片庫載入相片。 這項設施本身不提供 Xamarin.Forms 。 作業需要相依性服務，例如[從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)一文中所述。

**SkiaSharpFormsDemos**專案中的**IPhotoLibrary.cs**檔案和平臺專案中的三個**PhotoLibrary.cs**檔案已從該文章中改編。 此外，Android **MainActivity.cs**檔案已如文章中所述修改過，而且 iOS 專案已獲得存取相片媒體櫃的許可權，並在**plist**檔案的底部有兩行。

此函式 `BasicBitmapsPage` 會將新增 `TapGestureRecognizer` 至，以在點擊時 `SKCanvasView` 收到通知。 當您收到點一下時， `Tapped` 處理常式會取得圖片選擇器相依性服務的存取權，並呼叫 `PickPhotoAsync` 。 如果 `Stream` 傳回物件，則會將它傳遞至 `SKBitmap.Decode` 方法：

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

請注意， `Tapped` 處理常式也會呼叫 `InvalidateSurface` 物件的方法 `SKCanvasView` 。 這會產生新的 `PaintSurface` 處理常式呼叫。

## <a name="displaying-the-bitmaps"></a>顯示點陣圖

`PaintSurface`處理常式必須顯示三個位圖。 此處理程式假設電話是直向模式，並將畫布垂直分割成三個相等的部分。

第一個點陣圖會以最簡單的 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 方法顯示。 您只需要指定 X 和 Y 座標，點陣圖的左上角就會放在此處：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

雖然已 `SKPaint` 定義參數，但其預設值為， `null` 而您可以忽略它。 點陣圖的圖元只會以一對一的對應傳輸到顯示介面的圖元。 您會在下一節的 `SKPaint` [**SkiaSharp 透明度**](transparency.md)中看到此引數的應用程式。

程式可以使用和屬性取得點陣圖的圖元尺寸 [`Width`](xref:SkiaSharp.SKBitmap.Width) [`Height`](xref:SkiaSharp.SKBitmap.Height) 。 這些屬性可讓程式計算座標，以將點陣圖放在畫布的上三分之一的中央：

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

其他兩個位圖會以具有參數的版本顯示 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) `SKRect` ：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

第三個版本 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 有兩個 `SKRect` 引數，可指定要顯示之點陣圖的矩形子集，但此版本未在本文中使用。

以下程式碼顯示從內嵌資源點陣圖載入的點陣圖：

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

點陣圖會延展到矩形的維度，這就是在這些螢幕擷取畫面中，可將猴子水準延展的原因：

[![](bitmaps-images/basicbitmaps-small.png "A triple screenshot of the Basic Bitmaps page")](bitmaps-images/basicbitmaps-large.png#lightbox "A triple screenshot of the Basic Bitmaps page")

第三個影像 &mdash; ，只有在您執行程式並從自己的圖片庫載入相片時，才會 &mdash; 顯示在矩形中，但是矩形的位置和大小會調整以維持點陣圖的外觀比例。 這項計算比較複雜，因為它需要根據點陣圖和目的地矩形的大小來計算縮放比例，並將矩形置中在該區域內：

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

如果尚未從圖片庫載入任何點陣圖，則 `else` 區塊會顯示一些文字，以提示使用者點擊畫面。

您可以使用各種透明度來顯示點陣圖，而[**SkiaSharp 透明度**](transparency.md)的下一篇文章則說明。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
