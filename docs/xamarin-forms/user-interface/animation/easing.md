---
title: 在 Xamarin.Forms 中的 easing 函式
description: Xamarin.Forms 包含加/減速類別，可讓您指定的傳送函式，以控制動畫加速或減慢它們執行的方式。 這篇文章會示範如何使用預先定義的 easing 函式，以及如何建立自訂的 easing 函式。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 0be72e29ebfed9a3f43d96fd66b7f4597459a834
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656774"
---
# <a name="easing-functions-in-xamarinforms"></a>在 Xamarin.Forms 中的 easing 函式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms 包含加/減速類別，可讓您指定的傳送函式，以控制動畫加速或減慢它們執行的方式。這篇文章會示範如何使用預先定義的 easing 函式，以及如何建立自訂的 easing 函式。_


[ `Easing` ](xref:Xamarin.Forms.Easing)類別定義數個可供動畫的 easing 函式：

- [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn)加/減速函數不斷彈跳直到開始動畫。
- [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut)加/減速函數不斷彈跳直到結尾動畫。
- [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn)緩時變 easing 函式可加速動畫。
- [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut)加/減速函數可在開始時，動畫加速和減速的動畫在結束。
- [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut)加/減速函數快速減速的動畫。
- [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) Easing 函式會使用常數的速度，以及預設值 easing 函式。
- [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn)加/減速函數順利加速動畫。
- [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut)加/減速函數順利加速開頭時，動畫和順暢減速的動畫在結束。
- [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut)加/減速函數順利減速的動畫。
- [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) Easing 函式會導致非常快速地加速結尾動畫。
- [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) Easing 函式會導致快速結尾減速的動畫。

`In`和`Out`後置詞表示 easing 函式所提供的效果為明顯在動畫結束時，或同時的開頭。

此外，您可以建立自訂的 easing 函式。 如需詳細資訊，請參閱 <<c0> [ 自訂 Easing 函式](#customeasing)。

## <a name="consuming-an-easing-function"></a>使用 Easing 函式

中的動畫擴充方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別允許 easing 函式指定為最終的方法參數，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

藉由指定動畫的 easing 函式，動畫速度會變得非線性，並產生 easing 函式所提供的效果。 省略時建立動畫的 easing 函式會導致使用預設的動畫[ `Linear` ](xref:Xamarin.Forms.Easing.Linear) easing 函式，這會產生線性速度。

如需使用的動畫擴充方法的詳細資訊[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別，請參閱[簡單動畫](~/xamarin-forms/user-interface/animation/simple.md)。 Easing 函式也可供[ `Animation` ](xref:Xamarin.Forms.Animation)類別。 如需詳細資訊，請參閱 <<c0> [ 自訂動畫](~/xamarin-forms/user-interface/animation/custom.md)。

<a name="customeasing" />

## <a name="custom-easing-functions"></a>自訂 Easing 函式

有三種主要的方法，來建立自訂的 easing 函式：

1. 建立採用的方法`double`引數，並傳回`double`結果。
1. 建立 `Func<double, double>`。
1. 指定做為引數的加/減速函式[ `Easing` ](xref:Xamarin.Forms.Easing)建構函式。

在所有三個情況下，自訂的 easing 函式應傳回引數為 1 的 0 和 1 的引數為 0。 不過，您可以傳回任何值引數的值 0 和 1 之間。 現在將依序討論每一種方法。

### <a name="custom-easing-method"></a>自訂 Easing 方法

可以定義自訂的 easing 函式，為採用的方法`double`引數，並傳回`double`結果，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

`CustomEase`方法就會截斷輸入的值為 0、 0.2、 0.4、 0.6、 0.8 版和 1 的值。 因此， [ `Image` ](xref:Xamarin.Forms.Image)轉譯中不連續跳動，而不是順利執行個體。

### <a name="custom-easing-func"></a>自訂 Easing 函式

自訂的 easing 函式也可以定義為`Func<double, double>`，如下列程式碼範例所示：

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

`CustomEase` `Func`代表 easing 函式，一開始很快速，速度會變慢和反轉課程中，，然後反轉課程再次移至 很快加速結尾。 因此，在整體移動[ `Image` ](xref:Xamarin.Forms.Image)執行個體向下，它也會暫時反轉動畫中途的課程。

### <a name="custom-easing-constructor"></a>自訂 Easing 建構函式

自訂的 easing 函式也可以定義做為引數[ `Easing` ](xref:Xamarin.Forms.Easing)建構函式，如下列程式碼範例所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自訂的 easing 函式指定為 lambda 函式引數[ `Easing` ](xref:Xamarin.Forms.Easing)建構函式，並使用`Math.Cos`方法來建立由是否抑制緩慢的拖放效果`Math.Exp`方法。 因此， [ `Image` ](xref:Xamarin.Forms.Image)轉譯，使其出現於它最終的休止位置卸除執行個體。

## <a name="summary"></a>總結

這篇文章會示範如何使用預先定義的 easing 函式，以及如何建立自訂的 easing 函式。 包含 Xamarin.Forms [ `Easing` ](xref:Xamarin.Forms.Easing)可讓您指定傳輸函式可控制如何動畫加速或減慢它們執行的類別。



## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [Easing 函式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [加/減速](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
