---
title: SkiaSharp 線條和路徑
description: 使用 SkiaSharp 繪製線條和圖形路徑
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: f7c0fcc87253b1d5ae9d612c52ef3a32c5790c5e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 線條和路徑

_使用 SkiaSharp 繪製線條和圖形路徑_

[上一節](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)所示範的 SkiaSharp`SKCanvas`類別包含數種方法來繪製的矩形、 省略符號，以及圓角的矩形。 此區段和稍後的章節涵蓋各種不同的類別以建立及呈現連接*圖形路徑*。

圖形路徑是繪圖線條和曲線 SkiaSharp 最通用的方法。 本章節將說明如何使用`SKPath`物件繪製的直線，並使用很小的直線的集合 (稱為*聚合線條*) 繪製曲線，您可以在數學上定義。 下節將討論各種支援的曲線`SKPath`。

本節中的所有範例程式會都出現標題底下**線條和路徑**在首頁上的[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式，然後在[**路徑**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Paths)方案的資料夾。

## <a name="lines-and-stroke-capslinesmd"></a>[線條和筆觸端點](lines.md)

了解如何使用 SkiaSharp 使用不同的筆觸端點繪製線條。

## <a name="path-basicspathsmd"></a>[路徑的基本概念](paths.md)

瀏覽 SkiaSharp SKPath 物件組合在一起的直線和曲線。

## <a name="the-path-fill-typesfill-typesmd"></a>[路徑填滿類型](fill-types.md)

探索 SkiaSharp 路徑填滿類型使用不同的效果。

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[聚合線條和參數化的方程式](polylines.md)

您可以使用 SkiaSharp 轉譯之參數的方程式，您可以定義任何一行。

## <a name="dots-and-dashesdotsmd"></a>[點和虛線](dots.md)

主要在 SkiaSharp 中繪製虛線和破折線線條的複雜性。

## <a name="finger-paintingfinger-paintmd"></a>[手指繪製](finger-paint.md)

您可以使用手指在畫布上繪製。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
