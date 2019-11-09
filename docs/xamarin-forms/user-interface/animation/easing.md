---
title: 在 Xamarin 中緩動函數
description: Xamarin 包含一個緩動類別，可讓您指定傳送函式，以控制動畫在執行時的速度或緩慢。 本文示範如何使用預先定義的緩動函式，以及如何建立自訂的緩動函數。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 56ea31d1e1be8bbad4a27dd7ffd844aa03f75bbb
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842823"
---
# <a name="easing-functions-in-xamarinforms"></a>在 Xamarin 中緩動函數

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin 包含一個緩動類別，可讓您指定傳送函式，以控制動畫在執行時的速度或緩慢。本文示範如何使用預先定義的緩動函式，以及如何建立自訂的緩動函數。_

[`Easing`](xref:Xamarin.Forms.Easing)類別會定義一些可供動畫使用的緩時函數：

- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)的緩時函式會在一開始就會彈跳動畫。
- [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)的緩時函式會在結尾處彈跳動畫。
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)的緩動函式會以緩慢的速度加速動畫。
- [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)的緩時函式會在一開始加速動畫，並在結束時減速動畫。
- [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)簡化功能會快速減速動畫。
- [`Linear`](xref:Xamarin.Forms.Easing.Linear)的緩動函式會使用持續的速度，而且是預設的緩時函數。
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)簡化功能會順暢地加速動畫。
- [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)簡化函式會在一開始就順暢地加速動畫，並在結束時順暢減速動畫。
- [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)簡化功能會順暢地減速動畫。
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)的緩時函式可讓動畫以非常快速的速度加速結束。
- [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)的緩時函式會導致動畫快速減速。

`In` 和 `Out` 尾碼會指出緩動函式所提供的效果在動畫開頭、結尾或兩者都很明顯。

此外，也可以建立自訂的緩接函式。 如需詳細資訊，請參閱[自訂緩時函數](#customeasing)。

## <a name="consuming-an-easing-function"></a>使用緩動函數

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別中的動畫擴充方法可讓您將緩動函式指定為最終的方法參數，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

藉由為動畫指定緩動函式，動畫速度就會變成非線性，並產生緩動函數所提供的效果。 在建立動畫時省略緩動函式會導致動畫使用預設的[`Linear`](xref:Xamarin.Forms.Easing.Linear)緩時函數，以產生線性速度。

如需在[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別中使用動畫擴充方法的詳細資訊，請參閱[簡單動畫](~/xamarin-forms/user-interface/animation/simple.md)。 [`Animation`](xref:Xamarin.Forms.Animation)類別也可以使用緩時函數。 如需詳細資訊，請參閱[自訂動畫](~/xamarin-forms/user-interface/animation/custom.md)。

<a name="customeasing" />

## <a name="custom-easing-functions"></a>自訂緩動函式

建立自訂的緩動函式有三個主要方法：

1. 建立採用 `double` 引數的方法，並傳回 `double` 的結果。
1. 建立 `Func<double, double>`。
1. 指定緩動函數做為[`Easing`](xref:Xamarin.Forms.Easing)的引數。

在這三種情況下，自訂的緩時函數應該會針對0的引數傳回0，並針對1的引數傳回1。 不過，可以在0和1的引數值之間傳回任何值。 現在將會逐一討論每種方法。

### <a name="custom-easing-method"></a>自訂緩動方法

自訂的緩時函數可以定義為採用 `double` 引數的方法，並傳回 `double` 結果，如下列程式碼範例所示：

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

`CustomEase` 方法會將傳入的值截斷為0、0.2、0.4、0.6、0.8 和1的值。 因此， [`Image`](xref:Xamarin.Forms.Image)實例會在離散跳躍中轉譯，而不是順暢。

### <a name="custom-easing-func"></a>自訂緩動函數

自訂的緩時函數也可以定義為 `Func<double, double>`，如下列程式碼範例所示：

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

`CustomEaseFunc` 代表快速啟動、減緩和反轉課程的緩時函式，然後再次反轉課程以快速加速。 因此，雖然[`Image`](xref:Xamarin.Forms.Image)實例的整體移動是按下的，但它也會暫時反轉整個動畫的過程。

### <a name="custom-easing-constructor"></a>自訂緩時函數

自訂的緩時函數也可以定義為[`Easing`](xref:Xamarin.Forms.Easing)的函式的引數，如下列程式碼範例所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自訂的緩時函數會指定為[`Easing`](xref:Xamarin.Forms.Easing)的函式的 lambda 函數引數，並使用 `Math.Cos` 方法來建立 `Math.Exp` 方法所抑制的緩慢卸載效果。 因此， [`Image`](xref:Xamarin.Forms.Image)實例會進行轉譯，讓它看起來會放到其最終的放置位置。

## <a name="summary"></a>總結

本文示範如何使用預先定義的緩動函式，以及如何建立自訂的緩動函數。 Xamarin 包含一個[`Easing`](xref:Xamarin.Forms.Easing)類別，可讓您指定傳送函式，以控制動畫在執行時的速度或緩慢。

## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [簡化函數（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [緩](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
