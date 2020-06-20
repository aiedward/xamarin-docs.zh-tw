---
title: Xamarin.Forms圖形：幾何
description: Xamarin.Formsgeometry 類別可讓您描述2D 圖形的幾何。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05fcd1ada62bffa4829ad422ee5eda36d9d75ae3
ms.sourcegitcommit: 16847681df17ed59b3b3528761c02e8fb48ffc4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104258"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.Forms圖形：幾何

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

類別以及從中 `Geometry` 衍生的類別，可讓您描述2d 圖形的幾何。 `Geometry`物件可以很簡單，例如矩形和圓形，或從兩個或多個 geometry 物件所建立的複合。 您可以使用類別來建立更複雜的幾何 `PathGeometry` ，這可讓您描述弧形和曲線。

`Geometry`和 `Shape` 類別看起來很相似，因為它們都描述2d 圖形，但有重要的差異。 類別 `Geometry` 衍生自 `BindableObject` 類別，而 `Shape` 類別衍生自 `View` 類別。 因此， `Shape` 物件可以呈現自己並參與版面配置系統，而 `Geometry` 物件則不能。 雖然 `Shape` 物件比物件更容易使用 `Geometry` ， `Geometry` 物件的用途更高。 當 `Shape` 物件用來呈現2d 圖形時， `Geometry` 物件可以用來定義2d 圖形的幾何區域，以及定義要裁剪的區域。

所有幾何的基類都是抽象類別 `Geometry` 。 衍生自這個類別的類別可分為三個類別：簡單幾何、路徑幾何和複合幾何。

## <a name="simple-geometries"></a>簡單幾何

簡單的 geometry 類別為 `EllipseGeometry` 、 `LineGeometry` 和 `RectangleGeometry` 。 它們可用來建立基本幾何形狀，例如圓形、線條和矩形。 您可以使用或將 geometry 物件結合在一起，以建立這些相同的圖形以及更複雜的圖形， `PathGeometry` 但這些類別提供較簡單的方法來產生這些基本幾何形狀。

### <a name="ellipsegeometry"></a>EllipseGeometry

橢圓形 geometry 代表幾何或橢圓形或圓形，而且是由中心點、x 半徑和 y 半徑所定義。

`EllipseGeometry`類別會定義下列屬性：

- `Center`，屬於類型 `Point` ，表示幾何的中心點。
- `RadiusX`，屬於類型 `double` ，表示 geometry 的 x 半徑值。 這個屬性的預設值為0.0。
- `RadiusY`，屬於類型 `double` ，表示幾何的 y 半徑值。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

下列範例顯示如何建立和轉譯 `EllipseGeometry` ：

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

在此範例中，的中心 `EllipseGeometry` 設定為（50，50），而 x 半徑和 y 半徑皆設定為50。 這會建立直徑為100的圓形，其內部為藍色繪製。

### <a name="linegeometry"></a>LineGeometry

線條幾何代表線條的幾何，而則是藉由指定線條的起點和終點來定義。

`LimeGeometry`類別會定義下列屬性：

- `StartPoint`，屬於類型 `Point` ，表示行的起始點。
- `EndPoint`，屬於類型 `Point` ，表示行的結束點。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

下列範例顯示如何建立和轉譯 `LineGeometry` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

在此範例中， `LineGeometry` 會從（10，20）繪製到（100130）。

### <a name="rectanglegeometry"></a>RectangleGeometry

矩形 geometry 代表一個矩形，並使用 `Rect` 指定其相對位置及其高度和寬度的結構來定義。

`RectangleGeometry`類別會定義下列屬性：

- `Rect`，屬於類型 `FormsRect` ，表示矩形的維度。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

下列範例顯示如何建立和轉譯 `RectangleGeometry` ：

```xaml
<Path Fill="Blue"
      Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="50,50,25,25" />
  </Path.Data>
</Path>
```

矩形的位置和維度是由結構所定義 `Rect` 。 在此範例中，位置是（50，50），而高度和寬度都是25，這會建立正方形。

## <a name="path-geometries"></a>路徑幾何

路徑幾何描述可由弧形、曲線、橢圓形、線條和矩形組成的複雜圖形。

`PathGeometry`類別會定義下列屬性：

- `Figures`，屬於類型 `PathFigureCollection` ，表示 `PathFigure` 描述路徑內容的物件集合。
- `FillRule`，屬於類型 `FillRule` ，可決定如何結合 geometry 中包含的交集區域。 此屬性的預設值為 `FillRule.EvenOdd`。

> [!NOTE]
> `Figures`屬性是類別的 `ContentProperty` `PathGeometry` ，因此不需要從 XAML 明確設定。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`PathGeometry`是由物件的集合所組成 `PathFigure` ，每個都 `PathFigure` 描述幾何中的圖形。 每個 `PathFigure` 都是由一或多個物件所組成 `PathSegment` ，每個物件都描述圖形的區段。 區段分為許多種類型：

- `ArcSegment`，這會在兩個點之間建立橢圓形弧線。
- `BezierSegment`，這會在兩個點之間建立三次方貝茲曲線。
- `LineSegment`，這會在兩個點之間建立線條。
- `PolyBezierSegment`，它會建立一連串的三次方貝茲曲線。
- `PolyLineSegment`，這會建立一連串的線條。
- `PolyQuadraticBezierSegment`，它會建立一連串二次方貝茲曲線。
- `QuadraticBezierSegment`，它會建立二次方貝茲曲線。

內的區段 `PathFigure` 會合並成單一幾何圖形，其中每個區段的結束點都是下一個區段的起點。 的 `StartPoint` 屬性 `PathFigure` 會指定要從中繪製第一個區段的點。 每個後續區段都會從前一個區段的結束點開始。 例如，從到的垂直線 `10,50` 可以透過 `10,150` 將 `StartPoint` 屬性設定為 `10,50` ，並 `LineSegment` 使用 `Point` 的屬性設定建立 `10,150` 來定義：

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

您可以使用物件的組合 `PathSegment` ，以及在內使用多個物件，來建立更複雜的幾何 `PathFigure` `PathGeometry` 。

## <a name="composite-geometries"></a>複合幾何

複合 geometry 物件可以使用來建立 `GeometryGroup` 。 `GeometryGroup`類別會從一或多個物件建立複合幾何 `Geometry` 。 可以將任何數目的 `Geometry` 物件加入至 `GeometryGroup` 。

下列範例顯示如何在中結合幾何 `GeometryGroup` ：

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此範例中， `EllipseGeometry` 會合並四個具有相同 x 半徑和 y 半徑座標但具有不同中心座標的物件。 這會建立四個重迭的圓形，其內部會以填滿規則來填滿橙色 `EvenOdd` 。

## <a name="clip-geometries"></a>裁剪幾何

[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別具有 `Clip` 類型的屬性， `Geometry` 可定義元素內容的大綱。 當 `Clip` 屬性設定為 `Geometry` 物件時，只會顯示區域內的區域 `Geometry` 。

下列範例顯示如何使用 `Geometry` 物件做為的裁剪區域 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

在此範例中， `EllipseGeometry` 具有 `RadiusX` 和 `RadiusY` 值100的，且 `Center` 值為（180180）會設定為的 `Clip` 屬性 [`Image`](xref:Xamarin.Forms.Image) 。 只會顯示橢圓形區域內的影像部分：

![使用 EllipseGeometry 裁剪影像](geometries-images/clip-ellipsegeometry.png "使用 EllipseGeometry 裁剪影像")

> [!NOTE]
> 簡單的幾何、路徑幾何和複合幾何都可以用來裁剪 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件。

## <a name="other-features"></a>其他功能

`GeometryHelper`類別提供下列 helper 方法：

- `FlattenGeometry`
- `FlattenCubicBezier`
- `FlattenQuadraticBezier`
- `FlattenArc`

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
