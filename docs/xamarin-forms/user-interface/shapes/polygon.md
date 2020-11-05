---
title: Xamarin.Forms 圖形：多邊形
description: Xamarin.Forms多邊形類別可以用來繪製多邊形，也就是形成封閉圖形的一連串線線。
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ff3d99759c161924260a465cf59a31c21795339d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373844"
---
# <a name="no-locxamarinforms-shapes-polygon"></a>Xamarin.Forms 圖形：多邊形

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polygon`類別衍生自 `Shape` 類別，而且可以用來繪製多邊形，也就是形成封閉圖形的一連串線線。 如需 `Polygon` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polygon` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是 [`Point`](xref:Xamarin.Forms.Point) 描述多邊形頂點的結構集合。
- `FillRule`，類型為 `FillRule` ，指定圖形內部填滿的決定方式。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

此 `PointsCollection` 類型是 `ObservableCollection` 物件的 [`Point`](xref:Xamarin.Forms.Point) 。 `Point`結構會定義 `X` 類型的和屬性，其 `Y` `double` 代表2d 空間中的 x 和 y 座標組。 因此， `Points` 屬性應該設定為 x 座標和 y 座標組的清單，以描述多邊形頂點，並以單一逗號和/或一或多個空格分隔。 例如，"40，10 70，80" 和 "40 10，70 80" 都有效。

如需列舉的詳細資訊 `FillRule` ，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

## <a name="create-a-polygon"></a>建立多邊形

若要繪製多邊形，請建立 `Polygon` 物件，並將其 `Points` 屬性設定為圖形的頂點。 會自動繪製線，以連接第一個和最後一個點。 若要繪製多邊形內的，請將其 `Fill` 屬性設定為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為多邊形提供外框，請將其 `Stroke` 屬性設定為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定多邊形外框的粗細。

下列 XAML 範例顯示如何繪製填滿的多邊形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

在此範例中，會繪製代表三角形的填滿多邊形：

![填滿多邊形](polygon-images/filled.png "填滿多邊形")

下列 XAML 範例顯示如何繪製虛線多邊形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

在此範例中，多邊形外框是虛線：

![虛線多邊形](polygon-images/dashed.png "虛線多邊形")

如需繪製虛線多邊形的詳細資訊，請參閱 [繪製虛線圖形](index.md#draw-dashed-shapes)。

下列 XAML 範例顯示使用預設填滿規則的多邊形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

在此範例中，會使用填滿規則來決定每個多邊形的填滿行為 `EvenOdd` 。

![EvenOdd 多邊形](polygon-images/evenodd.png "EvenOdd 多邊形")

下列 XAML 範例顯示使用 `Nonzero` 填滿規則的多邊形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![非零的多邊形](polygon-images/nonzero.png "非零的多邊形")

在此範例中，會使用填滿規則來決定每個多邊形的填滿行為 `Nonzero` 。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 圖形：填滿規則](fillrules.md)