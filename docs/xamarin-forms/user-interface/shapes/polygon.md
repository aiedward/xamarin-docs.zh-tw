---
title: Xamarin.Forms圖形：多邊形
description: Xamarin.Forms多邊形類別可以用來繪製多邊形，這是一系列形成封閉圖形的連接線。
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 628218f47f318aebe7e4d0ff30efdce9617eb2c6
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937640"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.Forms圖形：多邊形

![發行前版本 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Polygon`類別衍生自 `Shape` 類別，可以用來繪製多邊形，這是形成封閉圖形的連接線系列。 如需 `Polygon` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polygon` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是 [`Point`](xref:Xamarin.Forms.Point) 描述多邊形頂點的結構集合。
- `FillRule`，屬於類型 `FillRule` ，指定如何決定圖形的內部填滿。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`PointsCollection`型別是 `ObservableCollection` 物件的 [`Point`](xref:Xamarin.Forms.Point) 。 `Point`結構會定義 `X` `Y` 類型的和屬性，其 `double` 代表2d 空間中的 x 和 y 座標配對。 因此， `Points` 屬性應該設定為 x 座標和 y 座標配對的清單，以描述多邊形頂點點，並以單一逗號和/或一或多個空格分隔。 例如，「40，10 70，80」和「40 10，70 80」都是有效的。

如需列舉的詳細資訊 `FillRule` ，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

## <a name="create-a-polygon"></a>建立多邊形

若要繪製多邊形，請建立 `Polygon` 物件，並將其 `Points` 屬性設定為圖形的頂點。 會自動繪製一條線來連接第一個和最後一個點。 若要在多邊形內繪製，請將其 `Fill` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為多邊形提供外框輪廓，請將其 `Stroke` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定多邊形外框的粗細。

下列 XAML 範例顯示如何繪製實心多邊形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

在此範例中，會繪製代表三角形的填滿多邊形：

![填滿的多邊形](polygon-images/filled.png "填滿的多邊形")

下列 XAML 範例顯示如何繪製虛線多邊形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

在此範例中，多邊形外框輪廓為虛線：

![虛線多邊形](polygon-images/dashed.png "虛線多邊形")

如需繪製虛線多邊形的詳細資訊，請參閱[繪製虛線形狀](index.md#draw-dashed-shapes)。

下列 XAML 範例顯示使用預設填滿規則的多邊形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

在此範例中，每個多邊形的填滿行為是使用 `EvenOdd` 填滿規則來決定。

![EvenOdd 多邊形](polygon-images/evenodd.png "EvenOdd 多邊形")

下列 XAML 範例顯示使用 `Nonzero` 填滿規則的多邊形：

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![非零多邊形](polygon-images/nonzero.png "非零多邊形")

在此範例中，每個多邊形的填滿行為是使用 `Nonzero` 填滿規則來決定。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
- [Xamarin.Forms圖形：填滿規則](fillrules.md)
