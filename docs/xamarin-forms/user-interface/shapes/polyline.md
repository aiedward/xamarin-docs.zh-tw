---
title: Xamarin.Forms圖形：折線
description: 聚合 Xamarin.Forms 線條類別可以用來繪製一連串的連接直線。
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fee7dd2a2e5b713b3a82fc2e1227b21caddbceaa
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243789"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.Forms圖形：折線

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polyline`類別衍生自 `Shape` 類別，可用於繪製一連串的連接直線。 除了折線中的最後一個點未連接到第一個點以外，折線類似于多邊形。 如需 `Polyline` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polyline` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是 `Point` 描述折線頂點的結構集合。
- `FillRule`，屬於類型 `FillRule` ，指定如何結合折線中的交集區域。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`PointsCollection`型別是 `ObservableCollection` 物件的 [`Point`](xref:Xamarin.Forms.Point) 。 `Point`結構會定義 `X` `Y` 類型的和屬性，其 `double` 代表2d 空間中的 x 和 y 座標配對。 因此， `Points` 屬性應該設定為 x 座標和 y 座標配對的清單，描述以單一逗號和/或一或多個空格分隔的折線頂點點。 例如，「40，10 70，80」和「40 10，70 80」都是有效的。

`FillRule` 列舉會定義下列成員：

- `EvenOdd`表示規則，判斷某個點是否在折線的填滿區域中。 它會在任何方向繪製從點到無限大的光線，並計算光線相交之圖形內的線段數目。 如果這個數位是奇數，則點位於內部。 如果此數位為偶數，則此點為 [外部]。
- `Nonzero`表示規則，判斷某個點是否在折線的填滿區域中。 它會在任何方向繪製從點到無限大的光線，然後檢查圖形的線段與光線相交的位置。 從零開始，計數會在每次區段從左至右與光線相交時遞增，並在每次區段從右至左時遞減。 計算交點之後，如果結果為零，則點會在聚合線條外。 否則，它會在內。

## <a name="create-a-polyline"></a>建立折線

若要繪製一個折線，請建立一個 `Polyline` 物件，並將其 `Points` 屬性設定為圖形的頂點。 若要為折線提供外框，請將其 `Stroke` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定 [折線外框] 的粗細。

> [!IMPORTANT]
> 如果您將的 `Fill` 屬性設定 `Polyline` 為 [`Color`](xref:Xamarin.Forms.Color) ，即使起點和終點不相交，還是會繪製聚合線條的內部空間。

下列 XAML 範例示範如何繪製一條折線：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red" />
```

在此範例中，會繪製紅色的折線：

![聚合線條](polyline-images/stroke.png "聚合線條")

下列 XAML 範例顯示如何繪製虛線的折線：

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

在此範例中，折線是虛線：

![虛線折線](polyline-images/dashed.png "虛線折線")

如需繪製虛線折線的詳細資訊，請參閱[繪製虛線形狀](index.md#draw-dashed-shapes)。

下列 XAML 範例顯示使用預設填滿規則的折線：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

在此範例中，會使用填滿規則來決定折線的填滿行為 `EvenOdd` 。

![EvenOdd 的折線](polyline-images/evenodd.png "EvenOdd polyine")

下列 XAML 範例顯示使用 `Nonzero` 填滿規則的聚合線條：

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![非零的折線](polyline-images/nonzero.png "非零的折線")

在此範例中，會使用填滿規則來決定折線的填滿行為 `Nonzero` 。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
