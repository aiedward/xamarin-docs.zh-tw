---
title: Xamarin.Forms 中的 easing 函式
description: Xamarin.Forms 包含加/減速類別，可讓您指定的傳送函式，控制如何動畫加速或慢它們正在執行。 本文示範如何使用預先定義的 easing 函式，以及如何建立自訂 easing 函式。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 9398a1b9cf4e5f6fd18f2213a7cf55e9cbb93ef0
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243138"
---
# <a name="easing-functions-in-xamarinforms"></a>Xamarin.Forms 中的 easing 函式

_Xamarin.Forms 包含加/減速類別，可讓您指定的傳送函式，控制如何動畫加速或慢它們正在執行。本文示範如何使用預先定義的 easing 函式，以及如何建立自訂 easing 函式。_


[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)類別定義可供動畫的 easing 函式數目：

- [ `BounceIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) Easing 函式退開頭動畫。
- [ `BounceOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/) Easing 函式退結尾動畫。
- [ `CubicIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/)緩時變 easing 函式加速動畫。
- [ `CubicInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/) Easing 函式加速開始、 動畫和減速結尾動畫。
- [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/)快速 easing 函式減速動畫。
- [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) Easing 函式會使用常數的速度，以及預設值 easing 函式。
- [ `SinIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/)順暢 easing 函式加速動畫。
- [ `SinInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)順暢 easing 函式加速開始、 動畫和順暢減速結尾動畫。
- [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/)順暢 easing 函式減速動畫。
- [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) Easing 函式會導致非常快速地加速結束時的動畫。
- [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) Easing 函式會導致快速減速結束時的動畫。

`In`和`Out`尾碼表示是否 easing 函式所提供的效果很明顯的動畫，或同時選取結束時，開頭。

此外，您可以建立自訂 easing 函式。 如需詳細資訊，請參閱[自訂 Easing 函式](#customeasing)。

## <a name="consuming-an-easing-function"></a>耗用 Easing 函式

中的動畫擴充方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別允許 easing 函式指定為最後的方法參數，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

藉由指定動畫的 easing 函式，動畫速度會變得非線性，並產生 easing 函式所提供的效果。 省略時建立動畫的 easing 函式會使用預設的動畫[ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) easing 函式，會產生線性速度。

如需有關使用中的動畫擴充方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別，請參閱[簡單動畫](~/xamarin-forms/user-interface/animation/simple.md)。 Easing 函式也可供[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別。 如需詳細資訊，請參閱[自訂動畫](~/xamarin-forms/user-interface/animation/custom.md)。

<a name="customeasing" />

## <a name="custom-easing-functions"></a>自訂 Easing 函式

有三個主要的方法來建立自訂 easing 函式：

1. 建立採用的方法`double`引數，並傳回`double`結果。
1. 建立 `Func<double, double>`。
1. 指定做為引數的加/減速函數[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)建構函式。

在所有的三種情況下，自訂 easing 函式應傳回 0 和 1 的引數 1 的引數為 0。 不過，可以傳回任何值，引數的值 0 和 1 之間。 現在會依次討論每一種方法。

### <a name="custom-easing-method"></a>自訂 Easing 方法

自訂 easing 函式可定義採用的方法為`double`引數，並傳回`double`造成，如下列程式碼範例所示：

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

`CustomEase`方法會截斷傳入值為 0、 0.2、 0.4、 0.6、 0.8 和 1 的值。 因此， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)轉譯中不連續的跳躍點，而不是順暢執行個體。

### <a name="custom-easing-func"></a>自訂 Easing 函式

自訂 easing 函式也可以定義為`Func<double, double>`，如下列程式碼範例所示：

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

`CustomEase` `Func`代表開始 easing 函式快速、 變慢，反轉課程中，然後反轉再次課程結束時快速加速。 因此，雖然整體移動[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)執行個體由上至下，它也會暫時反轉動畫到一半的課程。

### <a name="custom-easing-constructor"></a>自訂 Easing 建構函式

自訂 easing 函式也可以做為引數定義[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)建構函式，如下列程式碼範例所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自訂 easing 函式指定的 lambda 函式引數為[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)建構函式，並使用`Math.Cos`方法來建立抑制透過慢速的置放效果`Math.Exp`方法。 因此， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)轉譯，使它顯示到其最後靜止位置卸除執行個體。

## <a name="summary"></a>總結

本文示範如何使用預先定義的 easing 函式，以及如何建立自訂 easing 函式。 Xamarin.Forms 包含[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)可讓您指定傳輸函式可控制如何動畫加速或慢它們正在執行的類別。



## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [Easing 函式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Easing](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
