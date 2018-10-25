---
title: SkiaSharp 線條和路徑
description: 本文說明如何在 Xamarin.Forms 應用程式中，繪製線條和圖形路徑中使用 SkiaSharp，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 3f2597c67459e407ac066ee19d54d134d60f3076
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615131"
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 線條和路徑

_若要繪製線條和圖形的路徑中使用 SkiaSharp_

[上一節](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)示範，SkiaSharp`SKCanvas`類別包含數種方法來繪製圓形、 橢圓形、 矩形、 圓角的矩形、 文字和點陣圖。 本節和稍後的章節涵蓋與建立和轉譯連線的各種類別*圖形路徑*。

圖形路徑是繪製線條和曲線中 SkiaSharp 最通用的方法。 本章節將說明如何使用[ `SKPath` ](xref:SkiaSharp.SKPath)物件來繪製直線，並使用很小的直線，線條的集合 (稱為*聚合線條*) 繪製曲線，您可以在演算法來定義。 在稍後的章節[ **SkiaSharp 曲線和路徑**](../curves/index.md)討論的各種支援的曲線各種`SKPath`。

在本節中的所有範例程式會都出現標題底下**線條和路徑**在首頁上的[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式，然後在[**路徑**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths)該方案的資料夾。

## <a name="lines-and-stroke-capslinesmd"></a>[線條和筆觸端點](lines.md)

了解如何使用 SkiaSharp 繪製線條使用不同的筆觸端點。

## <a name="path-basicspathsmd"></a>[路徑的基本概念](paths.md)

瀏覽 SkiaSharp`SKPath`組合在一起的直線和曲線的物件。

## <a name="the-path-fill-typesfill-typesmd"></a>[路徑填滿類型](fill-types.md)

探索使用 SkiaSharp 路徑填滿類型不同的效果。

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[聚合線條和參數化的方程式](polylines.md)

您可以使用 SkiaSharp 來呈現您可以定義參數化的方程式的任一行。

## <a name="dots-and-dashesdotsmd"></a>[點和虛線](dots.md)

掌握在 SkiaSharp 繪圖虛線和破折線線條的複雜性。

## <a name="finger-paintingfinger-paintmd"></a>[手指繪製](finger-paint.md)

您可以使用手指來在畫布上繪製。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
