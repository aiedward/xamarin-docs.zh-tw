---
title: ''
description: 本文探討在應用程式中顯示 SkiaSharp 圖形的轉換 Xamarin.Forms ，並使用範例程式碼示範這項功能。
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e20ea5d1d3f813b04a927601fbe1180ff39ed176
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140188"
---
# <a name="skiasharp-transforms"></a>SkiaSharp 轉換

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解用於顯示 SkiaSharp 圖形的轉換_

SkiaSharp 支援實作為物件方法的傳統圖形轉換 [`SKCanvas`](xref:SkiaSharp.SKCanvas) 。 在數學上，轉換會改變您在繪圖函式中指定的座標和大小， `SKCanvas` 因為繪圖物件會呈現出來。 轉換通常會方便繪製重複的圖形或動畫。 某些技術 &mdash; （例如旋轉點陣圖或文字） &mdash; 在沒有使用轉換的情況下不可行。

SkiaSharp 轉換支援下列作業：

- *轉譯*成從一個位置到另一個位置的 shift 座標
- *縮放*以增加或減少座標和大小
- *旋轉*以旋轉點周圍的座標
- *扭曲*以水準或垂直方式變換座標，讓矩形變成平行四邊形

這些稱為*仿射*轉換。 仿射轉換一律會保留平行線條，而且永遠不會造成座標或大小變成無限。 正方形絕不會轉換成平行四邊形以外的任何專案，而且圓形永遠不會轉換成橢圓形以外的任何專案。

SkiaSharp 也支援以標準的 3 x 3 轉換矩陣為基礎的非仿射轉換（也稱為「 *projective* 」或「*透視圖*」轉換）。 非仿射轉換可讓正方形轉換成任何凸四邊形，這是四邊的圖形，所有內部角都小於180度。 非仿射轉換可能會使座標或大小變成無限，但對於3D 效果而言，這是很重要的。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 與轉換之間的差異 Xamarin.Forms

Xamarin.Forms也支援類似于 SkiaSharp 中的轉換。 Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) 類別會定義下列轉換屬性：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)、 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) 和[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationX`和 `RotationY` 屬性是建立准3d 效果的透視圖轉換。

SkiaSharp 轉換和轉換之間有幾個重要的差異 Xamarin.Forms ：

第一個差異在於，SkiaSharp 轉換會套用至整個 `SKCanvas` 物件，而 Xamarin.Forms 轉換會套用至個別 `VisualElement` 的衍生。 （您可以將 Xamarin.Forms 轉換套用至 `SKCanvasView` 物件本身，因為 `SKCanvasView` 衍生自 `VisualElement` ，但在該範圍內 `SKCanvasView` ，SkiaSkarp 轉換則適用）。

SkiaSharp 轉換會相對於的左上角， `SKCanvas` 而 Xamarin.Forms 轉換會相對於其套用的的左上角 `VisualElement` 。 這項差異在套用縮放和旋轉轉換時很重要，因為這些轉換一律會相對於特定點。

最大的差別在於，SKiaSharp 轉換是*方法*，而 Xamarin.Forms 轉換是*屬性*。 這是除了語法差異之外的語義差異： SkiaSharp 轉換會在 Xamarin.Forms 轉換設定狀態時執行作業。 SkiaSharp 轉換會套用至後續繪製的繪圖物件，而不是套用轉換之前繪製的繪圖物件。 相反地， Xamarin.Forms 轉換會在屬性設定後立即套用至先前呈現的專案。 SkiaSharp 轉換會在呼叫方法時累計，Xamarin.Forms當屬性是以另一個值設定時，就會取代轉換。

本節中的所有範例程式都會出現在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式的 [ **SkiaSharp 轉換**] 區段中。 原始程式碼可以在方案的 [[**轉換**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms)] 資料夾中找到。

## <a name="the-translate-transform"></a>[平移轉換](translate.md)

瞭解如何使用轉譯轉換來移位 SkiaSharp 圖形。

## <a name="the-scale-transform"></a>[縮放轉換](scale.md)

探索將物件調整成各種大小的 SkiaSharp 縮放轉換。

## <a name="the-rotate-transform"></a>[旋轉轉換](rotate.md)

探索 SkiaSharp 旋轉轉換的可能效果和動畫。

## <a name="the-skew-transform"></a>[傾斜轉換](skew.md)

瞭解「扭曲」轉換如何建立傾斜的繪圖物件。

## <a name="matrix-transforms"></a>[矩陣轉換](matrix.md)

使用多功能轉換矩陣深入探索 SkiaSharp 轉換。

## <a name="touch-manipulations"></a>[觸控操作](touch.md)

使用矩陣轉換來執行觸控操作，以進行拖曳、縮放和旋轉。

## <a name="non-affine-transforms"></a>[非仿射轉換](non-affine.md)

透過非仿射轉換效果，超越 oridinary。

## <a name="3d-rotation"></a>[3D 旋轉](3d-rotation.md)

使用非仿射轉換來旋轉3D 空間中的2D 物件。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
