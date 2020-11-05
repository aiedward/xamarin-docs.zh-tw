---
title: SkiaSharp 線條和路徑
description: 本文說明如何使用 SkiaSharp 在應用程式中繪製線條和圖形路徑 Xamarin.Forms ，並使用範例程式碼示範這一點。
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c5a83a626b9a0fab55b84c7d8cc8e4fbaea3683
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375248"
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 線條和路徑

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 繪製線條和圖形路徑_

[上一節](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)示範 SkiaSharp `SKCanvas` 類別包含數個方法來繪製圓形、橢圓形、矩形、圓角矩形、文字和點陣圖。 本章節和後續章節將討論與建立和轉譯 *圖形路徑* 連接的各種類別。

圖形路徑是在 SkiaSharp 中繪製線條和曲線的最一般化方法。 本節說明 [`SKPath`](xref:SkiaSharp.SKPath) 如何使用物件來繪製直線，以及使用一組較小的直線 (稱為「聚合線條) *polyline* 來繪製可以定義演算法的曲線。 [**SkiaSharp 曲線和路徑**](../curves/index.md)的後續章節會討論所支援的各種曲線 `SKPath` 。

此區段中的所有範例程式都會出現在 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式首頁的標題 **行和路徑** 底下，以及該方案的 [ [**路徑**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths)] 資料夾中。

## <a name="lines-and-stroke-caps"></a>[線條和筆觸端點](lines.md)

瞭解如何使用 SkiaSharp 繪製具有不同筆觸帽的線條。

## <a name="path-basics"></a>[路徑的基本概念](paths.md)

探索 `SKPath` 用於組合線條和曲線的 SkiaSharp 物件。

## <a name="the-path-fill-types"></a>[路徑填滿類型](fill-types.md)

探索 SkiaSharp 路徑填滿類型可能產生的不同效果。

## <a name="polylines-and-parametric-equations"></a>[聚合線條和參數化的方程式](polylines.md)

使用 SkiaSharp 來呈現您可以使用參數化方程式定義的任何線條。

## <a name="dots-and-dashes"></a>[點和虛線](dots.md)

在 SkiaSharp 中，以複雜的方式繪製圓點線和虛線。

## <a name="finger-painting"></a>[手指繪製](finger-paint.md)

使用您的手指在畫布上繪製。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)