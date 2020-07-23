---
title: Xamarin.Forms圖形：橢圓形
description: Xamarin.Forms橢圓形類別可以用來繪製橢圓形和圓形。
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4369fdb7c5a35d6b9a192d9222fd82670b7e3f3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933584"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.Forms圖形：橢圓形

![發行前版本 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Ellipse`類別衍生自 `Shape` 類別，可用於繪製橢圓形和圓形。 如需 `Ellipse` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Ellipse`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。 如需屬性的詳細資訊 `Aspect` ，請參閱[延展圖形](index.md#stretch-shapes)。

## <a name="create-an-ellipse"></a>建立橢圓形

若要繪製橢圓形，請建立 `Ellipse` 物件並設定其 `WidthRequest` 和 `HeightRequest` 屬性。 若要在橢圓形內繪製，請將其 `Fill` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為橢圓形提供外框，請將其 `Stroke` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定橢圓形外框的粗細。

若要繪製圓形，請將 `WidthRequest` 物件的和屬性設為 `HeightRequest` `Ellipse` 相等。

下列 XAML 範例顯示如何繪製實心橢圓形：

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

在此範例中，會繪製一個具有維度150x50 （裝置獨立單位）的紅色實心橢圓形：

![實心橢圓形](ellipse-images/filled.png "實心橢圓形")

下列 XAML 範例顯示如何繪製圓形：

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

在此範例中，會繪製一個具有維度150x150 正方形（裝置獨立單位）的紅色圓圈：

![Circle](ellipse-images/circle.png "Circle")

如需繪製虛線橢圓形的詳細資訊，請參閱[繪製虛線形狀](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
