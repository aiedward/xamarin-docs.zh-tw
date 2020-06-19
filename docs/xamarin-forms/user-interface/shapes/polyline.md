---
title: Xamarin.Forms圖形：折線
description: 聚合 Xamarin.Forms 線條類別可以用來繪製一連串的連接直線。
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6c884caa7bfb301f949f353f3c6c3445704aa89
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101359"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.Forms圖形：折線

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Polyline`類別衍生自 `Shape` 類別，可用於繪製一連串的連接直線。 如需 `Polyline` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polyline` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是 `Point` 描述折線頂點的結構集合。
- `FillRule`，屬於類型 `FillRule` ，指定如何結合折線中的交集區域。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

## <a name="create-a-polyline"></a>建立折線

下列 XAML 範例顯示如何繪製多邊形：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          HeightRequest="100"
          WidthRequest="500" />
```

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
