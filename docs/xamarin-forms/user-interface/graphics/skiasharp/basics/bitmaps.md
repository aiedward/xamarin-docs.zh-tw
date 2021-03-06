---
title: SkiaSharp 中的點陣圖基本概念
description: 本文說明如何從各種來源載入 SkiaSharp 中的點陣圖，並將它們顯示在 Xamarin.Forms 應用程式中，並使用範例程式碼示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3b73aabd184060e273d7a1e3e20f6e78eb0ba0d1
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373545"
---
# <a name="bitmap-basics-in-skiasharp"></a>SkiaSharp 中的點陣圖基本概念

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_從各種來源載入點陣圖，並加以顯示。_

SkiaSharp 中的點陣圖支援相當廣泛。 本文只涵蓋 &mdash; 如何載入點陣圖和如何顯示點陣圖的基本概念：

![顯示兩個位圖](bitmaps-images/basicbitmaps-small.png)

在 [SkiaSharp 點陣圖](../bitmaps/index.md)一節中可以找到更深入的點陣圖探索。

SkiaSharp 點陣圖是類型的物件 [`SKBitmap`](xref:SkiaSharp.SKBitmap) 。 有許多方式可以建立點陣圖，但是本文會將自己限制為 [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) 方法，從 .net 物件載入點陣圖 `Stream` 。

**SkiaSharpFormsDemos** 程式中的 **基本點陣圖** 頁面示範如何從三個不同的來源載入點陣圖：

- 從網際網路
- 從可執行檔中內嵌的資源
- 從使用者的相片媒體櫃

這三個來源的三個 `SKBitmap` 物件會定義為類別中的欄位 [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) ：

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

若要根據 URL 載入點陣圖，您可以使用 [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) 類別。 您應該只具現化一個實例 `HttpClient` ，並重複使用它，將它儲存為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用搭配 `HttpClient` iOS 和 Android 應用程式時，您會想要設定 **[ (TLS) 1.2 的傳輸層安全性](~/cross-platform/app-fundamentals/transport-layer-security.md)** 檔中所述的專案屬性。

因為搭配使用運算子最方便，所以 `await` `HttpClient` 無法在函式中執行程式碼 `BasicBitmapsPage` 。 相反地，它是覆寫的一部分 `OnAppearing` 。 此處的 URL 指向 Xamarin 網站上的某個區域，其中包含一些範例點陣圖。 網站上的套件可讓您附加規格，以將點陣圖大小調整為特定寬度：

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

使用從方法傳回的時，Android 作業系統 `Stream` 會引發例外 `GetStreamAsync` 狀況， `SKBitmap.Decode` 因為它會在主執行緒上執行冗長的作業。 基於這個理由，會使用將點陣圖檔案的內容複寫到 `MemoryStream` 物件 `CopyToAsync` 。

靜態 `SKBitmap.Decode` 方法負責解碼點陣圖檔案。 它適用于 JPEG、PNG 和 GIF 點陣圖格式，並以內部 SkiaSharp 格式儲存結果。 此時， `SKCanvasView` 必須失效才能讓 `PaintSurface` 處理常式更新顯示。

## <a name="loading-a-bitmap-resource"></a>載入點陣圖資源

就程式碼而言，載入點陣圖最簡單的方法就是直接在您的應用程式中包含點陣圖資源。 **SkiaSharpFormsDemos** 套裝程式含名為 **Media** 的資料夾，其中包含數個位圖檔案，包括一個名為 **monkey.png** 的點陣圖檔案。 針對儲存為程式資源的點陣圖，您必須使用 [**屬性**] 對話方塊為檔案提供 **Embedded 資源** 的 **組建動作**！

每個內嵌資源都有一個 *資源識別碼* ，其中包含專案名稱、資料夾和檔案名，並以句點連接： **SkiaSharpFormsDemos.Media.monkey.png**。 您可以藉由指定該資源識別碼作為 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) 類別之方法的引數，來取得此資源的存取權 [`Assembly`](xref:System.Reflection.Assembly) ：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

這個 `Stream` 物件可以直接傳遞給 `SKBitmap.Decode` 方法。

## <a name="loading-a-bitmap-from-the-photo-library"></a>從相片庫載入點陣圖

使用者也可以從裝置的圖片庫載入相片。 這項功能本身並不提供 Xamarin.Forms 。 作業需要相依性服務，例如 [從圖片媒體櫃挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)的文章中所述。

**SkiaSharpFormsDemos** 專案中的 **IPhotoLibrary.cs** 檔案，以及平臺專案中的三個 **PhotoLibrary.cs** 檔案已從該文章中調整。 此外，Android **MainActivity.cs** 檔已如文章所述修改過，而且 iOS 專案已獲得存取相片媒體櫃的許可權，並有兩行指向 **plist** 檔案的底部。

此函式 `BasicBitmapsPage` 會將加入至， `TapGestureRecognizer` 以在點擊時 `SKCanvasView` 收到通知。 收到點一下時， `Tapped` 處理常式會取得圖片選擇器相依性服務的存取權，並呼叫 `PickPhotoAsync` 。 如果 `Stream` 傳回物件，則會將它傳遞給 `SKBitmap.Decode` 方法：

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

`PaintSurface`處理常式必須顯示三個位圖。 此處理程式會假設電話處於直向模式，並將畫布垂直分割成三個相等的部分。

第一個點陣圖會以最簡單的 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 方法來顯示。 您只需要指定 X 和 Y 座標，即點陣圖左上角的位置：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

雖然已 `SKPaint` 定義參數，但是它的預設值是， `null` 您可以忽略它。 點陣圖的圖元只會以一對一的對應傳送至顯示介面的圖元。 在下一節中，您會在 `SKPaint` [**SkiaSharp 透明度**](transparency.md)的下一節中看到此引數的應用程式。

程式可以使用和屬性取得點陣圖的圖元尺寸 [`Width`](xref:SkiaSharp.SKBitmap.Width) [`Height`](xref:SkiaSharp.SKBitmap.Height) 。 這些屬性可讓程式計算座標，將點陣圖放在畫布的上三分之一的中央：

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

另外兩個位圖會以具有參數的版本顯示 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) `SKRect` ：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

的第三個版本 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 有兩個 `SKRect` 引數，可指定要顯示之點陣圖的矩形子集，但不會在本文中使用該版本。

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

點陣圖會延伸至矩形的維度，這也是在這些螢幕擷取畫面中，會以水準方式延伸的原因：

[![基本點陣圖頁面的三重螢幕擷取畫面](bitmaps-images/basicbitmaps-small.png)](bitmaps-images/basicbitmaps-large.png#lightbox "基本點陣圖頁面的三重螢幕擷取畫面")

第三個影像 &mdash; ，您只能看到當您執行程式並從自己的圖片庫載入相片 &mdash; 時，也會顯示在矩形內，但會調整矩形的位置和大小，以維持點陣圖的外觀比例。 這項計算更牽涉到，因為它需要根據點陣圖和目的地矩形的大小來計算縮放比例，並將矩形置中在該區域中：

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

如果尚未從圖片庫載入點陣圖，則 `else` 區塊會顯示一些文字以提示使用者將螢幕上點下。

您可以顯示具有各種透明度的點陣圖，而 [**SkiaSharp 透明度**](transparency.md) 的下一篇文章會說明如何。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)