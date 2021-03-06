---
title: Xamarin.Forms 圖形：橢圓形
description: Xamarin.Forms橢圓形類別可以用來繪製橢圓形和圓形。
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3130fd4cb054799ca9e0a61d13cacb2629320b7
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585828"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.Forms 圖形：橢圓形

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Ellipse`類別衍生自 `Shape` 類別，而且可以用來繪製橢圓形和圓形。 如需 `Ellipse` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Ellipse`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。 如需屬性的詳細資訊 `Aspect` ，請參閱 [Stretch shapes](index.md#stretch-shapes)。

## <a name="create-an-ellipse"></a>建立橢圓形

若要繪製橢圓形，請建立 `Ellipse` 物件並設定其 `WidthRequest` 和 `HeightRequest` 屬性。 若要繪製橢圓形內的，請將其 `Fill` 屬性設定為 [`Brush`](xref:Xamarin.Forms.Brush) 衍生的物件。 若要為橢圓形提供外框，請將其 `Stroke` 屬性設定為 [`Brush`](xref:Xamarin.Forms.Brush) 衍生的物件。 `StrokeThickness`屬性會指定橢圓形大綱的粗細。 如需物件的詳細資訊 `Brush` ，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。

若要繪製圓形，請讓 `WidthRequest` 物件的和 `HeightRequest` 屬性 `Ellipse` 相等。

下列 XAML 範例顯示如何繪製實心橢圓形：

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

在此範例中，會繪製具有維度 150x50 (與裝置無關單位) 的紅色填滿橢圓形：

![填滿橢圓形](ellipse-images/filled.png "填滿橢圓形")

下列 XAML 範例顯示如何繪製圓形：

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

在此範例中，會繪製維度 150x150 (裝置獨立單位) 的紅色圓圈：

![未空心圓形](ellipse-images/circle.png "Circle")

如需繪製虛線橢圓形的詳細資訊，請參閱 [繪製虛線圖形](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 刷](~/xamarin-forms/user-interface/brushes/index.md)
