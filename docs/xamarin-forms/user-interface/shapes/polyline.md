---
title: Xamarin.Forms 圖形：折線
description: 聚合 Xamarin.Forms 線條類別可以用來繪製一系列連接的直線。
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b331ea54e1ae0d5746090d155cb14465bcec0307
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97938988"
---
# <a name="no-locxamarinforms-shapes-polyline"></a>Xamarin.Forms 圖形：折線

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polyline`類別衍生自 `Shape` 類別，而且可以用來繪製一連串的連接直線。 聚合線條類似多邊形，但折線中的最後一個點未連接到第一個點。 如需 `Polyline` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polyline` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是描述聚合線條 `Point` 頂點的結構集合。
- `FillRule`，類型為 `FillRule` ，指定如何合併聚合線條中的交集區域。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

此 `PointsCollection` 類型是 `ObservableCollection` 物件的 [`Point`](xref:Xamarin.Forms.Point) 。 `Point`結構會定義 `X` 類型的和屬性，其 `Y` `double` 代表2d 空間中的 x 和 y 座標組。 因此，您 `Points` 應該將屬性設定為 x 座標和 y 座標組的清單，以描述以單一逗號和/或一或多個空格分隔的聚合線條頂點點。 例如，"40，10 70，80" 和 "40 10，70 80" 都有效。

如需列舉的詳細資訊 `FillRule` ，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

## <a name="create-a-polyline"></a>建立折線

若要繪製折線，請建立 `Polyline` 物件，並將其 `Points` 屬性設定為圖形的頂點。 若要為聚合線條提供外框，請將其 `Stroke` 屬性設定為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定折線大綱的粗細。

> [!IMPORTANT]
> 如果您將的 `Fill` 屬性設定 `Polyline` 為 [`Color`](xref:Xamarin.Forms.Color) ，即使起點和終點不相交，也會繪製聚合線條的內部空間。

下列 XAML 範例顯示如何繪製聚合線條：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red" />
```

在此範例中，會繪製紅色的聚合線條：

![聚合線條](polyline-images/stroke.png "聚合線條")

下列 XAML 範例顯示如何繪製虛線的聚合線條：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

在此範例中，折線是虛線：

![虛線折線](polyline-images/dashed.png "虛線折線")

如需繪製虛線折線的詳細資訊，請參閱 [繪製虛線圖形](index.md#draw-dashed-shapes)。

下列 XAML 範例顯示使用預設填滿規則的聚合線條：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

在此範例中，會使用填滿規則來決定聚合線條的填滿行為 `EvenOdd` 。

![EvenOdd 聚合線條](polyline-images/evenodd.png "EvenOdd polyine")

下列 XAML 範例顯示使用 `Nonzero` 填滿規則的聚合線條：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![非零的聚合線條](polyline-images/nonzero.png "非零的聚合線條")

在此範例中，會使用填滿規則來決定聚合線條的填滿行為 `Nonzero` 。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 圖形：填滿規則](fillrules.md)
