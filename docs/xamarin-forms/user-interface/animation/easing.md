---
title: 簡化函式 Xamarin.Forms
description: Xamarin.Forms 包含簡化類別，可讓您指定傳送函式，以控制動畫在執行時的速度或速度變慢。 本文示範如何使用預先定義的簡化函式，以及如何建立自訂的簡化功能。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 50b64b394314ae2f63ab1f756f1cc73ba29e59e7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372843"
---
# <a name="easing-functions-in-no-locxamarinforms"></a>簡化函式 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms 包含簡化類別，可讓您指定傳送函式，以控制動畫在執行時的速度或速度變慢。本文示範如何使用預先定義的簡化函式，以及如何建立自訂的簡化功能。_

[`Easing`](xref:Xamarin.Forms.Easing)類別會定義一些可供動畫取用的簡化函式：

- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)緩時函數會在一開始就彈跳動畫。
- [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)緩時函數會在結尾處彈跳動畫。
- 緩時變 [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) 函數會減緩動畫的速度。
- [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)緩時函數會在一開始加速動畫，並在結尾減速動畫。
- [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)緩時函數會快速減速動畫。
- [`Linear`](xref:Xamarin.Forms.Easing.Linear)緩時變函數會使用常數的速度，而且是預設的緩時變函數。
- 緩時函式 [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) 能順暢地加速動畫。
- 緩時函式會在 [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) 一開始就順暢地加速動畫，並在結尾處順暢地減速動畫。
- [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)緩時函數會順暢地減速動畫。
- 緩時變 [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 函數可讓動畫非常快速地加快結束。
- 緩時函式 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 會讓動畫快速減速到結尾。

和後置詞 `In` `Out` 會指出在動畫的開頭、結尾或兩者上，緩時變函數所提供的效果是否很明顯。

此外，也可以建立自訂的緩時函數。 如需詳細資訊，請參閱 [自訂簡化函數](#custom-easing-functions)。

## <a name="consuming-an-easing-function"></a>取用簡化功能

類別中的動畫擴充方法可 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 讓您將簡化函式指定為最終方法參數，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

藉由指定動畫的簡化函式，動畫速度會變成非線性，並產生簡化函式所提供的效果。 在建立動畫時省略緩動函式會導致動畫使用預設的 [`Linear`](xref:Xamarin.Forms.Easing.Linear) 緩時函數，這會產生線性速度。

如需在類別中使用動畫擴充方法的詳細資訊 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) ，請參閱 [簡單的動畫](~/xamarin-forms/user-interface/animation/simple.md)。 此類別也可以取用簡化功能 [`Animation`](xref:Xamarin.Forms.Animation) 。 如需詳細資訊，請參閱 [自訂動畫](~/xamarin-forms/user-interface/animation/custom.md)。

## <a name="custom-easing-functions"></a>自訂簡化函式

有三種主要的方法可以建立自訂的緩時函數：

1. 建立採用引數的方法 `double` ，並傳回 `double` 結果。
1. 建立 `Func<double, double>`。
1. 將「緩時變函數」指定為「函式」的引數 [`Easing`](xref:Xamarin.Forms.Easing) 。

在這三種情況下，自訂簡化函式應該會針對0的引數傳回0，並針對1的引數傳回1。 不過，任何值都可以在0和1的引數值之間傳回。 現在會依序討論每個方法。

### <a name="custom-easing-method"></a>自訂簡化方法

自訂的簡化函式可以定義為採用引數的方法 `double` ，並傳回 `double` 結果，如下列程式碼範例所示：

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

方法會將 `CustomEase` 傳入值截斷為0、0.2、0.4、0.6、0.8 和1值。 因此， [`Image`](xref:Xamarin.Forms.Image) 實例會在離散跳躍中轉譯，而不是順暢地轉譯。

### <a name="custom-easing-func"></a>自訂簡化 Func

自訂的簡化函式也可以定義為 `Func<double, double>` ，如下列程式碼範例所示：

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

`CustomEaseFunc`代表可從快速開始、減緩和反轉課程的簡化函式，然後再次反轉課程，以加快結束速度。 因此，當實例的整體移動 [`Image`](xref:Xamarin.Forms.Image) 朝下時，它也會暫時反轉整個動畫的過程。

### <a name="custom-easing-constructor"></a>自訂簡化函式

自訂的簡化函式也可以定義為函式的引數 [`Easing`](xref:Xamarin.Forms.Easing) ，如下列程式碼範例所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自訂簡化函式指定為函式的 lambda 函式引數 [`Easing`](xref:Xamarin.Forms.Easing) ，並使用 `Math.Cos` 方法來建立方法所抑制的緩慢卸載效果 `Math.Exp` 。 因此，會將 [`Image`](xref:Xamarin.Forms.Image) 實例轉譯，使其看似放置於最後的放置位置。

## <a name="summary"></a>總結

本文示範如何使用預先定義的簡化函式，以及如何建立自訂的簡化功能。 Xamarin.Forms 包含 [`Easing`](xref:Xamarin.Forms.Easing) 類別，可讓您指定傳送函式，以控制動畫執行時的速度或速度變慢。

## <a name="related-links"></a>相關連結

- [非同步支援總覽](~/cross-platform/platform/async.md)
- [ (範例) 簡化函式 ](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [寬鬆](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)