---
title: SkiaSharp 轉換
description: 本文將探討在 Xamarin.Forms 應用程式，顯示 SkiaSharp 圖形的轉換，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 39de35c8e43ebf2b3db25724ad761e3576ba5057
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644949"
---
# <a name="skiasharp-transforms"></a>SkiaSharp 轉換

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_深入了解顯示 SkiaSharp 圖形的轉換_

SkiaSharp 支援傳統的圖形會實作為方法的轉換[ `SKCanvas` ](xref:SkiaSharp.SKCanvas)物件。 轉換以數學方式，改變您在中指定的大小與座標`SKCanvas`呈現圖形物件的繪圖功能。 轉換通常會繪製重複性的圖形或動畫很方便的。 一些技巧&mdash;例如旋轉點陣圖或文字&mdash;不一定要使用的轉換。

SkiaSharp 轉換支援下列作業：

- *轉譯*shift 座標從一個位置到另一個
- *擴展*來增加或減少座標和大小
- *旋轉*旋轉的點座標
- *扭曲*移位座標的水平或垂直方式，讓矩形變成平行四邊形

這些值稱為*仿射*轉換。 仿射轉換一律保留平行線，並永遠不會造成座標或大小，成為無限。 正方形永遠不會轉換成的平行四邊形以外的任何項目和圓形永遠不會轉換成橢圓形以外的任何項目。

SkiaSharp 也支援非仿射轉換 (也稱為*投射*或是*觀點來看*轉換) 為基礎的標準 3-3 轉換矩陣。 非仿射轉換可讓方塊，以轉換成任何凸面的方形，也就是所有的內部角度四邊圖小於 180 度。 座標或大小，成為無限的可能會導致非仿射轉換，但它們都是很重要的 3D 效果。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 和 Xamarin.Forms 轉換之間的差異

Xamarin.Forms 也支援類似於在 SkiaSharp 的轉換。 Xamarin.Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別會定義下列轉換屬性：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX)，及 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationX`和`RotationY`屬性都是建立準 3D 效果的觀點來看轉換。

有數個重要差異 SkiaSharp 轉換和 Xamarin.Forms 轉換：

第一個差別是 SkiaSharp 轉換會套用至整個`SKCanvas`物件時的 Xamarin.Forms 轉換會套用至個別`VisualElement`衍生項目。 (您可以將套用的 Xamarin.Forms 轉換`SKCanvasView`物件本身，因為`SKCanvasView`衍生自`VisualElement`，但在其中`SKCanvasView`，SkiaSkarp 轉換套用。)

SkiaSharp 轉換是相對於左上角`SKCanvas`左上角相對 Xamarin.Forms 轉換時`VisualElement`會套用。 套用調整規模時，此差異很重要，因為這些轉換永遠是相對於特定的點旋轉會轉換。

真的很大的差異是 SKiaSharp 轉換*方法*Xamarin.Forms 轉換時*屬性*。 這是除了語法差異之外的語義差異:SkiaSharp 轉換會在 Xamarin. Forms 轉換設定狀態時執行操作。 SkiaSharp 轉換套用至後續繪製的圖形物件，但不是會繪製前套用轉換的圖形物件。 相反地，Xamarin.Forms 轉換適用於先前呈現的項目，此屬性設定。 SkiaSharp 轉換是累計的因為方法會呼叫;當此屬性設定與另一個值，會取代 Xamarin.Forms 轉換。

在本節中的所有範例程式會都出現在**SkiaSharp 轉換**一節[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式。 中可以找到原始程式碼[**轉換**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms)方案的資料夾。

## <a name="the-translate-transformtranslatemd"></a>[平移轉換](translate.md)

了解如何使用平移轉換移位 SkiaSharp 圖形。

## <a name="the-scale-transformscalemd"></a>[縮放轉換](scale.md)

探索 SkiaSharp 縮放轉換，調整以各種不同大小的物件。

## <a name="the-rotate-transformrotatemd"></a>[旋轉轉換](rotate.md)

瀏覽的效果和動畫設有 SkiaSharp 旋轉轉換。

## <a name="the-skew-transformskewmd"></a>[傾斜轉換](skew.md)

了解如何傾斜轉換可建立傾斜的圖形物件。

## <a name="matrix-transformsmatrixmd"></a>[矩陣轉換](matrix.md)

更深入了解 SkiaSharp 轉換具有多樣化的轉換矩陣。

## <a name="touch-manipulationstouchmd"></a>[觸控操作](touch.md)

使用矩陣轉換實作拖曳、 調整及旋轉的觸控操作。

## <a name="non-affine-transformsnon-affinemd"></a>[非仿射轉換](non-affine.md)

超越 oridinary 與非仿射轉換效果。

## <a name="3d-rotation3d-rotationmd"></a>[3D 旋轉](3d-rotation.md)

您可以使用非仿射轉換，旋轉 3D 空間中的 2D 物件。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
