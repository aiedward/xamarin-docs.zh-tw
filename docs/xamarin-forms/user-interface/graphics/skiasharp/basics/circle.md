---
title: 繪製簡單的圓形
description: 了解 SkiaSharp 繪圖，包括畫布和繪製的基本概念
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9e5b18bd6a565639e2ca22f4637a67c4f48717f6
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="drawing-a-simple-circle"></a>繪製簡單的圓形

_了解 SkiaSharp 繪圖，包括畫布和繪製的基本概念_

本文介紹的概念使用 SkiaSharp，包括建立 Xamarin.Forms 中繪製圖形`SKCanvasView`物件來裝載的圖形，處理`PaintSurface`事件，並使用`SKPaint`物件，以指定色彩和其他的繪圖屬性。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式包含此一系列的 SkiaSharp 發行項的所有範例程式碼。 第一頁有權使用**簡單圓形**並叫用網頁類別[ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 此程式碼會示範如何繪製圓形半徑為 100 像素為單位的頁面中央。 外框的圓形是紅色，、 圓形的內部是藍色。

![](circle-images/circleexample.png "加上紅框藍色圓形")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)頁面類別衍生自`ContentPage`和包含兩個`using`SkiaSharp 命名空間指示詞：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

下列類別的建構函式會建立[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)物件、 附加的處理常式[ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/)事件及設定`SKCanvasView`物件做為頁面的內容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`佔用頁面的整個內容區域。 您也可以結合`SKCanvasView`其他 xamarin.forms`View`衍生項目，您會看到在其他範例。

`PaintSurface`事件處理常式會是您用來執行您的繪圖。 這個方法通常稱為多次執行您的程式時，因此它應該維護的所有資訊重新建立必要的圖形顯示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/)伴隨事件的物件有兩個屬性：

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) 型別 [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) 型別 [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

`SKImageInfo`結構中包含資訊的繪圖介面中，最重要的是，它就寬度和高度，單位為像素。 `SKSurface`物件都代表繪圖介面本身。 在本程式之繪圖介面是視訊的顯示，但在其他程式`SKSurface`物件也可以代表使用 SkiaSharp 上繪製的點陣圖。

最重要的屬性的`SKSurface`是[ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/)型別的[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)。 這個類別是圖形的繪製您用來執行實際的繪圖內容。 `SKCanvas`物件封裝的圖形狀態，其中包含圖形的轉換和裁剪。

以下是典型啟動`PaintSurface`事件處理常式：

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

[ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/)方法清除畫布，透明的色彩。 多載可讓您指定在畫布上的背景色彩。

現在的目標是繪製填滿藍色的紅色圓圈。 這個特定的圖形映像包含兩個不同的色彩，因為該工作需要在兩個步驟。 第一個步驟是繪製圓形外的框。 若要指定色彩和線條的其他特性，建立並初始化[ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/)物件：

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

[ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/)屬性會指出您想要*筆劃*列 （在本例中的圓形外框） 而非*填滿*內部。 三個成員[ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/)列舉型別如下：

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

預設值為 `Fill`。 用於繪製線條，以及使用相同的色彩填滿內部的第三個選項。

設定[ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/)屬性與值型別的[ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/)。 其中一種方式取得`SKColor`值是藉由轉換 Xamarin.Forms`Color`值設定為`SKColor`值使用之擴充方法[ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/)。 [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/)類別`SkiaSharp.Views.Forms`命名空間包含的其他方法的 Xamarin.Forms 值和 SkiaSharp 值之間轉換。

[ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/)屬性表示線條的粗細。 這裡設為 25 個像素。

您可以使用該`SKPaint`繪製圓形物件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

指定的座標相對於顯示介面的左上角。 X 座標增加向右和向的 Y 座標增加。 通常在圖形的相關討論，數學表示法 （x，y） 用來代表點。 點 （0，0） 會顯示介面的左上角，通常稱為*原點*。

前兩個引數`DrawCircle`表示圓形中心的 X 和 Y 座標。 這些會指派半高度與寬度的圓形的中心放顯示介面的中央顯示介面。 第三個引數指定圓形的半徑，和最後一個引數是`SKPaint`物件。

填滿的圓形內部時，您可以將修改的兩個屬性`SKPaint`物件並呼叫`DrawCircle`一次。 此程式碼也會顯示取得的替代方式`SKColor`中的許多欄位的其中一個值[ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/)結構：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
這次`DrawCircle`呼叫填滿的圓形，使用新的屬性`SKPaint`物件。

以下是執行 iOS、 Android 和通用 Windows 平台上的程式：

[![](circle-images/simplecircle-small.png "簡單的圓形頁面的三個螢幕擷取畫面")](circle-images/simplecircle-large.png#lightbox "簡單圓形頁面的三個螢幕擷取畫面")

當執行您自己的程式，您可以開啟電話或模擬器側邊，以查看圖形重繪的方式。 每次需要重新繪製的圖形`PaintSurface`事件處理常式會呼叫一次。

`SKPaint`物件是比的繪圖屬性的集合。 這些物件是非常輕微。 您可以重複使用`SKPaint`像此程式會執行，或者您可以建立多個`SKPaint`各種組合繪製屬性的物件。 您可以建立並初始化這些物件的外部`PaintSurface`事件處理常式，而且您可以將它們儲存成欄位頁面類別中。

雖然為圓形的外框的寬度指定為 25 個像素&mdash;或四分之一圓形的半徑的&mdash;它似乎是越窄，而且做的原因： 線條的半形遮住藍色圓形。 引數`DrawCircle`方法定義抽象的地理座標的圓形。 藍色內部的大小調整成最接近像素，該維度，但 25 像素寬大綱跨越幾何圓形&mdash;內部和外部一半一半。

中的下一個範例[整合 xamarin.forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)文件會以視覺化方式示範。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
