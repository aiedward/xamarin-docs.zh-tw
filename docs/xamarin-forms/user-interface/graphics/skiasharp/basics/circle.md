---
title: SkiaSharp 繪圖簡單的圓形
description: 本文說明在 Xamarin.Forms 應用程式，包括畫布和 [小畫家] 的 SkiaSharp 繪圖基本概念，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e06a1310fad01da11c8d8b115df504cc19426344
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615219"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>SkiaSharp 繪圖簡單的圓形

_了解 SkiaSharp 繪圖，包括畫布和繪製的基本概念_

這篇文章介紹的概念，繪製圖形中使用 SkiaSharp，包括建立 Xamarin.Forms`SKCanvasView`物件來裝載圖形中，處理`PaintSurface`事件，並使用`SKPaint`物件來指定色彩和其他繪圖屬性。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式包含這個 SkiaSharp 文章的系列的所有範例程式碼。 有權使用的第一頁**簡單的圓形**，並叫用的頁面類別[ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 此程式碼示範如何在頁面中央，半徑為 100 像素繪製圓形。 外框的圓形是紅色，和內部的圓形是藍色。

![](circle-images/circleexample.png "紅色外框的藍色圓形")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) Page 類別衍生自`ContentPage`且包含兩個`using`SkiaSharp 命名空間指示詞：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

下列類別的建構函式會建立[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)物件，將附加的處理常式[ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/)事件，並設定`SKCanvasView`物件做為頁面的內容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`佔用頁面的整個內容區域。 您也可以結合`SKCanvasView`其他 xamarin.forms`View`衍生項目，您會看到另一個例子。

`PaintSurface`事件處理常式是您用來執行您的繪圖。 這個方法通常稱為多次，在程式執行時，因此它應該維護所有資訊重新建立必要的圖形顯示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/)伴隨事件的物件有兩個屬性：

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) 型別 [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) 型別 [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

`SKImageInfo`結構包含資訊的繪圖介面，最重要的是，它是寬度和高度，單位為像素。 `SKSurface`物件都代表本身的繪圖介面。 此程式的繪圖介面是視訊的顯示，但在其他程式`SKSurface`物件也可以代表您使用 SkiaSharp 上繪製的點陣圖。

最重要的屬性`SKSurface`已[ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/)型別的[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)。 這個類別是繪製您用來執行實際的繪圖內容的圖形。 `SKCanvas`物件封裝的圖形狀態，其中包含圖形的轉換和裁剪。

以下是一般啟動`PaintSurface`事件處理常式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/)方法會清除畫布，以透明色彩。 多載可讓您指定在畫布的背景色彩。

這裡的目標是要繪製填滿藍色的紅色圓圈。 因為這個特定的圖形影像會包含兩個不同的色彩，則必須在兩個步驟中完成作業。 第一個步驟是圓形的繪製外框。 若要指定色彩和線條的其他特性，建立並初始化[ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/)物件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/)屬性會指出您想要*筆劃*行 （在此案例的圓形外框） 而非*填滿*內部。 三個成員[ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/)列舉如下所示：

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

預設為 `Fill`。 您可以使用第三個選項來將線條描邊，並使用相同的色彩填滿內部。

設定[ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/)屬性設為值型別的[ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/)。 其中一種方式來取得`SKColor`值是藉由轉換 Xamarin.Forms`Color`值`SKColor`值使用擴充方法[ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/)。 [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/)類別在`SkiaSharp.Views.Forms`命名空間包含的其他方法的 Xamarin.Forms 值和 SkiaSharp 值之間轉換。

[ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/)屬性表示線條的粗細。 這裡，它設為 25 像素。

您可以使用該`SKPaint`繪製圓形物件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

座標的指定相對於在顯示介面的左上角。 X 座標從右邊增加和 Y 座標增加下降。 通常在圖形的相關討論中，數學標記法 （x，y） 用來代表一個點。 點 （0，0） 會顯示介面的左上角，通常稱為*原點*。

前兩個引數`DrawCircle`指示的圓形中心的 X 和 Y 座標。 這些會指派一半的寬度和高度放在顯示介面的中央的圓形中心的顯示介面。 第三個引數會指定圓形的半徑，和最後一個引數是`SKPaint`物件。

填滿的圓形內部，您可以變更的兩個屬性`SKPaint`物件，然後呼叫`DrawCircle`一次。 此程式碼也會顯示的替代方式，以取得`SKColor`其中一個的許多欄位的值[ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/)結構：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
此時`DrawCircle`呼叫會使用新的屬性的 circle 填滿`SKPaint`物件。

以下是在 iOS、 Android 和通用 Windows 平台上執行的程式：

[![](circle-images/simplecircle-small.png "簡單的圓形頁面的三個螢幕擷取畫面")](circle-images/simplecircle-large.png#lightbox "簡單的圓形頁面的三個螢幕擷取畫面")

當自行執行程式，您可以開啟電話或模擬器側邊，以查看如何重新繪製圖形。 每次需要重新繪製的圖形`PaintSurface`再次呼叫事件處理常式。

`SKPaint`物件是比圖形的繪製屬性的集合。 這些物件是非常輕量。 您可以重複使用`SKPaint`物件，此程式執行，或者您可以建立多個`SKPaint`的繪圖內容的各種組合的物件。 您可以建立並初始化這些物件的外部`PaintSurface`事件處理常式，而且您可以將它們儲存為欄位頁面類別中。

雖然指定圓形的外框的寬度為 25 像素&mdash;或四分之一圓形的半徑的&mdash;它似乎是越窄，而且沒有做的原因： 藍色圓形被遮蔽的線條寬度的一半。 引數`DrawCircle`方法定義抽象圓形的幾何座標。 藍色的內部會調整成最接近像素，該維度，但 25 像素寬大綱 information officer 幾何圓形&mdash;一半內部和外部的下半部。

中的下一個範例[整合 Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)文件會以視覺化方式示範。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
