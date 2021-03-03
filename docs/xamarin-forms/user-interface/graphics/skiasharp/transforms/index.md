---
title: SkiaSharp 轉換
description: 本文將探索在應用程式中顯示 SkiaSharp 圖形的轉換 Xamarin.Forms ，並以範例程式碼示範。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6c59a2c669a6a60049b6bb6383faea35a7de3631
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371189"
---
# <a name="skiasharp-transforms"></a>SkiaSharp 轉換

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_深入瞭解顯示 SkiaSharp 圖形的轉換_

SkiaSharp 支援實作為物件方法的傳統圖形轉換 [`SKCanvas`](xref:SkiaSharp.SKCanvas) 。 在數學上，轉換會改變您在繪製函式中指定的座標和大小， `SKCanvas` 以呈現繪圖物件。 轉換通常很方便用來繪製重複的圖形或動畫。 &mdash; &mdash; 在不使用轉換的情況下，不可能使用一些技術來旋轉點陣圖或文字。

SkiaSharp 轉換支援下列作業：

- *轉譯* 成從某個位置到另一個位置的 shift 座標
- *調整規模* 以增加或減少座標和大小
- *旋轉* 以旋轉點周圍的座標
- *扭曲* 以水準或垂直方式變換座標，讓矩形變成平行四邊形

這些稱為 *仿射* 轉換。 仿射轉換一律會保留平行線，而且永遠不會導致座標或大小變成無限。 正方形永遠不會轉換成平行四邊形以外的任何地方，而且圓形永遠不會轉換成橢圓形以外的任何事物。

SkiaSharp 也支援非仿射轉換 (也稱為 *projective* ，或根據標準的 3 x 3 轉換矩陣) 的 *觀點* 轉換。 非仿射轉換可讓正方形轉換成任何凸四邊形，也就是所有內部角度都小於180度的四邊圖。 非仿射轉換可能會使座標或大小變成無限，但對3D 效果而言很重要。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 和轉換之間的差異 Xamarin.Forms

Xamarin.Forms 也支援與 SkiaSharp 中類似的轉換。 Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) 類別會定義下列轉換屬性：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 、 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) 及 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationX`和 `RotationY` 屬性是建立准3d 效果的透視圖轉換。

SkiaSharp 轉換和轉換之間有幾個重要的差異 Xamarin.Forms ：

第一個差別在於， `SKCanvas` 當 Xamarin.Forms 轉換套用至個別衍生時，SkiaSharp 轉換會套用至整個物件 `VisualElement` 。  (您可以將 Xamarin.Forms 轉換套用至 `SKCanvasView` 物件本身，因為 `SKCanvasView` 衍生自 `VisualElement` ，但在該物件內，會套用 `SKCanvasView` SkiaSkarp 轉換。 ) 

SkiaSharp 轉換是相對於的左上角， `SKCanvas` 而 Xamarin.Forms 轉換則是相對於套用這些轉換之的左上角 `VisualElement` 。 這項差異在套用縮放和旋轉轉換時很重要，因為這些轉換一律相對於特定點。

真正的差別在於 SKiaSharp 轉換是 *方法* ，而 Xamarin.Forms 轉換是 *屬性*。 這是語法差異以外的語義差異： SkiaSharp 轉換會在 Xamarin.Forms 轉換設定狀態時執行作業。 SkiaSharp 轉換會套用至後續繪製的繪圖物件，但不適用於套用轉換之前繪製的繪圖物件。 相反地， Xamarin.Forms 轉換會在屬性設定後立即套用至先前轉譯的元素。 SkiaSharp 轉換會在呼叫方法時累計， Xamarin.Forms 當屬性設定為另一個值時，就會取代轉換。

此區段中的所有範例程式都會出現在 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式的 [ **SkiaSharp 轉換**] 區段中。 原始程式碼可在解決方案的 [ [**轉換**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) ] 資料夾中找到。

## <a name="the-translate-transform"></a>[平移轉換](translate.md)

瞭解如何使用轉譯轉換來轉換 SkiaSharp 圖形。

## <a name="the-scale-transform"></a>[縮放轉換](scale.md)

探索 SkiaSharp 規模轉換，以將物件縮放為各種大小。

## <a name="the-rotate-transform"></a>[旋轉轉換](rotate.md)

探索 SkiaSharp 旋轉轉換可以使用的效果和動畫。

## <a name="the-skew-transform"></a>[傾斜轉換](skew.md)

查看扭曲轉換如何建立傾斜繪圖物件。

## <a name="matrix-transforms"></a>[矩陣轉換](matrix.md)

使用多功能轉換矩陣深入探索 SkiaSharp 轉換。

## <a name="touch-manipulations"></a>[觸控操作](touch.md)

使用矩陣轉換來實行觸控操作，以進行拖曳、縮放和旋轉。

## <a name="non-affine-transforms"></a>[非仿射轉換](non-affine.md)

超越非仿射轉換效果的 oridinary。

## <a name="3d-rotation"></a>[3D 旋轉](3d-rotation.md)

使用非仿射轉換來旋轉3D 空間中的2D 物件。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)