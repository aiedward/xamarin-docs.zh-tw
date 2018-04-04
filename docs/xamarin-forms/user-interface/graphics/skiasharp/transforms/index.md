---
title: SkiaSharp 轉換
description: 深入了解轉換為顯示 SkiaSharp 圖形
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 836f404e25679f01e1bd330582cf469299c73243
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="skiasharp-transforms"></a>SkiaSharp 轉換

_深入了解轉換為顯示 SkiaSharp 圖形_

SkiaSharp 支援傳統的圖形會實作為的方法的轉換[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)物件。 轉換數學上，變更座標和您在中指定的大小`SKCanvas`呈現圖形物件的繪圖功能。 轉換通常會繪製重複圖形或動畫很方便的。 一些技術&mdash;例如旋轉點陣圖或文字&mdash;不一定要使用的轉換。

SkiaSharp 轉換支援下列作業：

- *轉譯*為從一個位置到另一個的 shift 座標
- *標尺*以增加或減少座標和大小
- *旋轉*旋轉點的座標
- *扭曲*移位協調水平或垂直矩形會變成平行四邊形

這些值稱為*仿射*轉換。 仿射轉換一律保留平行線，並永遠不會導致變成無限座標或大小。 正方形永遠不會轉換成任何項目以外的平行四邊形和圓形永遠不會轉換成橢圓形以外的任何項目。

SkiaSharp 也支援非仿射轉換 (也稱為*投射*或*觀點來看*轉型) 為基礎的標準 3-3 轉換矩陣。 非仿射轉換可讓方塊，以轉換成任何凸四邊形 （四邊圖與所有內部角度小於 180 度為單位）。 非仿射轉換可能會造成座標或大小變得無限的但它們都是很重要的 3D 效果。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 和 Xamarin.Forms 轉換之間的差異

Xamarin.Forms 也支援類似 SkiaSharp 中的轉換。 Xamarin.Forms [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)類別會定義下列轉換屬性：

- `TranslationX` 和 `TranslationY`
- `Scale`
- `Rotation`、`RotationX` 和 `RotationY`

`RotationX`和`RotationY`屬性都是建立 quasi 3D 效果的觀點來看轉換。

有數個重要差異 SkiaSharp 轉換和 Xamarin.Forms 轉換：

第一個差別是 SkiaSharp 轉換會套用至整個`SKCanvas`物件時 Xamarin.Forms 轉換會套用至個別`VisualElement`衍生項目。 (您可以將套用的 Xamarin.Forms 轉換`SKCanvasView`物件本身，因為`SKCanvasView`衍生自`VisualElement`，但在`SKCanvasView`，SkiaSkarp 轉換套用。)

SkiaSharp 轉換是相對於左上角`SKCanvas`Xamarin.Forms 轉換是相對於左上角`VisualElement`會套用。 套用縮放比例時，此差異就很重要，因為這些轉換會在相對於某個特定點永遠會旋轉來轉換。

非常大的差異是 SKiaSharp 轉換*方法*Xamarin.Forms 轉換時*屬性*。 這是的語意差異以外的語法差異： SkiaSharp 轉換執行的作業，在 Xamarin.Forms 轉換設為狀態時。 SkiaSharp 轉換套用到後續繪製的圖形物件，但不是套用轉換之前繪製的圖形物件。 相反地，Xamarin.Forms 轉換會套用到先前呈現的項目屬性設定為。 SkiaSharp 轉換是累計的因為這些方法會呼叫;與另一個值設定屬性時，會取代 Xamarin.Forms 轉換。

本節中的所有範例程式會都出現標題底下**轉換**在首頁上的[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式，然後在[**轉換**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms)方案的資料夾。

## <a name="the-translate-transformtranslatemd"></a>[平移轉換](translate.md)

了解如何使用轉換轉換移位 SkiaSharp 圖形。

## <a name="the-scale-transformscalemd"></a>[縮放轉換](scale.md)

探索 SkiaSharp 標尺轉換縮放至各種大小的物件。

## <a name="the-rotate-transformrotatemd"></a>[旋轉轉換](rotate.md)

瀏覽 SkiaSharp 旋轉轉換的可能動畫和作用中。

## <a name="the-skew-transformskewmd"></a>[傾斜轉換](skew.md)

請參閱如何傾斜轉換時，可以在 SkiaSharp 中建立傾斜的圖形物件。

## <a name="matrix-transformsmatrixmd"></a>[矩陣轉換](matrix.md)

深入 SkiaSharp 多用途的轉換矩陣的轉換。

## <a name="touch-manipulationstouchmd"></a>[觸控操作](touch.md)

使用矩陣轉換實作拖曳、 縮放及旋轉的觸控操作。

## <a name="non-affine-transformsnon-affinemd"></a>[非仿射轉換](non-affine.md)

超越 oridinary 與非仿射轉換效果。

## <a name="3d-rotation3d-rotationmd"></a>[3D 旋轉](3d-rotation.md)

使用非仿射轉換旋轉 2D 在 3D 空間中的物件。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
