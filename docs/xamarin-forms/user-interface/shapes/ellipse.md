---
title: Xamarin.Forms圖形：橢圓形
description: Xamarin.Forms橢圓形類別可以用來繪製橢圓形和圓形。
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 142deccbcd29548e2d72b7144a01322f9909d98f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101279"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.Forms圖形：橢圓形

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Ellipse`類別衍生自 `Shape` 類別，可用於繪製橢圓形和圓形。 如需 `Ellipse` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Ellipse`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。

## <a name="create-an-ellipse"></a>建立橢圓形

下列 XAML 範例顯示如何繪製實心橢圓形：

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

下列 XAML 範例顯示如何繪製圓形：

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
