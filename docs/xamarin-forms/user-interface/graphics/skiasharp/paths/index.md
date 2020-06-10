---
標題：「SkiaSharp 行和路徑」描述：「本文將說明如何使用 SkiaSharp 在應用程式中繪製線條和圖形路徑 Xamarin.Forms ，並以範例程式碼示範這項功能。」
assetid： 316A15FE-383D-4D06-8641-BAC7EE7474CA ms. 技術： xamarin skiasharp author： davidbritch ms-chap： dabritch ms. date：03/10/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 線條和路徑

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 繪製線條和圖形路徑_

[上一節](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)示範 SkiaSharp `SKCanvas` 類別包含數種繪製圓形、橢圓形、矩形、圓角矩形、文字和點陣圖的方法。 本章節和更新的章節涵蓋了與建立和呈現*圖形路徑*連接的各種類別。

圖形路徑是在 SkiaSharp 中繪製線條和曲線最一般化的方法。 本節說明如何使用 [`SKPath`](xref:SkiaSharp.SKPath) 物件繪製直線，並使用一組小型直線（稱為「聚合線條」）繪製可*polyline*定義以演算法方式的曲線。 [**SkiaSharp 曲線和路徑**](../curves/index.md)的後續章節會討論所支援的各種曲線 `SKPath` 。

本節中的所有範例程式都會出現在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式首頁的標題**行和路徑**底下，以及該方案的 [[**路徑**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths)] 資料夾中。

## <a name="lines-and-stroke-caps"></a>[線條和筆觸端點](lines.md)

瞭解如何使用 SkiaSharp，以不同的筆觸帽繪製線條。

## <a name="path-basics"></a>[路徑的基本概念](paths.md)

探索 SkiaSharp `SKPath` 物件，以結合線條和曲線。

## <a name="the-path-fill-types"></a>[路徑填滿類型](fill-types.md)

探索 SkiaSharp 路徑填滿類型的各種可能效果。

## <a name="polylines-and-parametric-equations"></a>[聚合線條和參數化的方程式](polylines.md)

使用 SkiaSharp 來轉譯任何可使用參數化方程式定義的線條。

## <a name="dots-and-dashes"></a>[點和虛線](dots.md)

掌握在 SkiaSharp 中繪製虛線和虛線的複雜性。

## <a name="finger-painting"></a>[手指繪製](finger-paint.md)

使用您的手指在畫布上繪製。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
