---
title: Xamarin.Forms 圖形：幾何
description: Xamarin.Forms geometry 類別可讓您描述2D 圖形的幾何。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2798ee872e9db86a31beb6021f6bf4c3d82caaa2
ms.sourcegitcommit: a0de974875f8fa1a29f7abc990137246789ad85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100630250"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.Forms 圖形：幾何

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Geometry`類別以及從中衍生的類別，可讓您描述2d 圖形的幾何。 `Geometry` 從兩個或多個幾何物件建立的物件可以很簡單（例如矩形和圓形）或複合。 此外，也可以建立包含弧形和曲線的更複雜幾何。

`Geometry`類別是定義不同分類幾何的數個類別的父類別：

- `EllipseGeometry`，表示橢圓形或圓形的幾何。
- `GeometryGroup`，代表可將多個幾何物件合併成單一物件的容器。
- `LineGeometry`，代表線條的幾何。
- `PathGeometry`，代表可以由弧形、曲線、橢圓形、線條和矩形組成的複雜圖形幾何。
- `RectangleGeometry`，代表矩形或方形的幾何。

> [!NOTE]
> 另外還有一個 `RoundedRectangleGeometry` 衍生自類別的類別 `GeometryGroup` 。 如需詳細資訊，請參閱 [RoundRectangleGeometry](#roundrectanglegeometry)。

`Geometry`和 `Shape` 類別看起來很類似，因為它們會描述2d 圖形，但有一個重要的差異。 類別 `Geometry` 衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 類別，而 `Shape` 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別。 因此， `Shape` 物件可以自行轉譯和參與配置系統，而物件則 `Geometry` 不能。 雖然物件 `Shape` 比物件更容易使用 `Geometry` ， `Geometry` 物件更具用途。 當 `Shape` 物件用來轉譯2d 圖形時， `Geometry` 物件可以用來定義2d 圖形的幾何區域，以及定義用於裁剪的區域。

下列類別具有可設定為物件的屬性 `Geometry` ：

- `Path`類別會使用 `Geometry` 來描述其內容。 您可以 `Geometry` 將 `Path.Data` 屬性設定為 `Geometry` 物件，並設定 `Path` 物件的 `Fill` 和屬性，藉以呈現 `Stroke` 。
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別具有 `Clip` 類型的屬性， `Geometry` 可定義元素內容的大綱。 當 `Clip` 屬性設定為 `Geometry` 物件時，只會顯示區域內的區域 `Geometry` 。 如需詳細資訊，請參閱 [具有幾何的剪輯](#clip-with-a-geometry)。

衍生自類別的類別 `Geometry` 可以分為三個類別：簡單的幾何、路徑幾何和複合幾何。

## <a name="simple-geometries"></a>簡單幾何

簡單的 geometry 類別為 `EllipseGeometry` 、 `LineGeometry` 和 `RectangleGeometry` 。 它們可用來建立基本幾何形狀，例如圓形、線條和矩形。 這些相同的圖形以及更複雜的圖形都可以使用 `PathGeometry` 或結合 geometry 物件來建立，但這些類別提供了更簡單的方法來產生這些基本幾何圖形。

### <a name="ellipsegeometry"></a>EllipseGeometry

橢圓形幾何代表幾何或橢圓形或圓形，且是由中心點、x 半徑和 y 半徑所定義。

`EllipseGeometry`類別會定義下列屬性：

- `Center`型別為的， [`Point`](xref:Xamarin.Forms.Point) 代表幾何的中心點。
- `RadiusX`，類型為 `double` ，代表幾何的 x 半徑值。 這個屬性的預設值是0.0。
- `RadiusY`， `double` 代表幾何的 y 半徑值的型別。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

下列範例顯示如何在物件中建立和轉譯 `EllipseGeometry` `Path` ：

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

在此範例中，的中心 `EllipseGeometry` 設定為 (50，50) ，而 x 半徑和 y 半徑都設為50。 這會建立一個紅色圓圈，其直徑為100與裝置無關的單位，其內部會繪製藍色：

![EllipseGeometry](geometry-images/ellipse.png "EllipseGeometry")

### <a name="linegeometry"></a>LineGeometry

線條幾何代表線條的幾何，並藉由指定線條的起點和結束點來定義。

`LineGeometry`類別會定義下列屬性：

- `StartPoint`， [`Point`](xref:Xamarin.Forms.Point) 代表線條的起點。
- `EndPoint`，類型為 [`Point`](xref:Xamarin.Forms.Point) ，代表線條的結束點。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

下列範例顯示如何在物件中建立和轉譯 `LineGeometry` `Path` ：

```xaml
<Path Stroke="Black">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

在此範例中， `LineGeometry` 是從 (10、20) 繪製至 (100130) ：

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> 將呈現的的屬性設定為沒有 `Fill` `Path` `LineGeometry` 任何作用，因為線條沒有內部。

### <a name="rectanglegeometry"></a>RectangleGeometry

矩形幾何代表矩形或正方形的幾何，並以 `Rect` 指定其相對位置及其高度和寬度的結構來定義。

`RectangleGeometry`類別會定義 `Rect` 類型的屬性， `Rect` 表示矩形的維度。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

下列範例顯示如何在物件中建立和轉譯 `RectangleGeometry` `Path` ：

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

矩形的位置和維度是由 `Rect` 結構定義。 在此範例中，位置是 (10，10) ，寬度是150，而高度是100裝置獨立單位：

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>路徑幾何

路徑幾何描述可由弧線、曲線、橢圓形、線條和矩形組成的複雜圖形。

`PathGeometry`類別會定義下列屬性：

- `Figures`型別 `PathFigureCollection` ，代表 `PathFigure` 描述路徑內容的物件集合。
- `FillRule`，類型為 `FillRule` ，決定幾何中包含的交集區域如何合併。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

如需列舉的詳細資訊 `FillRule` ，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

> [!NOTE]
> `Figures`屬性是類別的 `ContentProperty` `PathGeometry` ，因此不需要從 XAML 明確設定。

`PathGeometry`是由物件集合所組成 `PathFigure` ，每個物件都會 `PathFigure` 描述幾何中的圖形。 每一個 `PathFigure` 都是由一或多個 `PathSegment` 物件組成，每個物件都描述圖形的區段。 有許多類型的區段：

- `ArcSegment`，會在兩個點之間建立橢圓形弧線。
- `BezierSegment`，會在兩個點之間建立三次方貝茲曲線。
- `LineSegment`，這會在兩個點之間建立線條。
- `PolyBezierSegment`，可建立一系列的三次方貝茲曲線。
- `PolyLineSegment`，可建立一系列的行。
- `PolyQuadraticBezierSegment`，它會建立一連串二次方貝茲曲線。
- `QuadraticBezierSegment`，這會建立二次方貝茲曲線。

上述所有類別都是衍生自抽象 `PathSegment` 類。

內的區段 `PathFigure` 會合並成單一幾何圖形，其中每個區段的結束點都是下一個區段的起點。 的 `StartPoint` 屬性 `PathFigure` 會指定繪製第一個區段的起點。 每個後續區段都會從前一個區段的結束點開始。 例如，您 `10,50` `10,150` 可以將 `StartPoint` 屬性設定為 `10,50` ，並 `LineSegment` 使用的屬性設定來建立的 `Point` `10,150` 垂直線，以定義的垂直線。

```xaml
<Path Stroke="Black">
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

您可以使用物件的組合來建立更複雜的幾何 `PathSegment` ，也可以使用中的多個物件來建立 `PathFigure` `PathGeometry` 。

### <a name="create-an-arcsegment"></a>建立 ArcSegment

會在 `ArcSegment` 兩個點之間建立橢圓形弧線。 橢圓形弧線的定義是由其起點和終點、X 軸和 y 半徑、X 軸旋轉因數、值指出弧線是否應大於180度，以及描述弧線繪製方向的值。

`ArcSegment`類別會定義下列屬性：

- `Point`型別為的， [`Point`](xref:Xamarin.Forms.Point) 表示橢圓形弧線的端點。這個屬性的預設值是 (0，0) 。
- `Size`型別 [`Size`](xref:Xamarin.Forms.Size) ，代表弧形的 x 和 y 半徑。這個屬性的預設值是 (0，0) 。
- `RotationAngle`， `double` 代表以度數為單位的型別，以度為單位，表示橢圓形圍繞 X 軸旋轉的量。 這個屬性的預設值為 0。
- `SweepDirection`，類型為 `SweepDirection` ，指定繪製弧線的方向。 此屬性的預設值為 `SweepDirection.CounterClockwise`。
- `IsLargeArc`， `bool` 表示弧線是否應大於180度的型別。 此屬性的預設值為 `false`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> `ArcSegment`類別不包含弧形起點的屬性。它只會定義它所代表之弧線的結束點。 弧線的起點是 `PathFigure` 要加入之的目前點 `ArcSegment` 。

`SweepDirection` 列舉會定義下列成員：

- `CounterClockwise`，指定以順時針方向繪製弧形。
- `Clockwise`，指定以逆時針方向繪製弧形。

下列範例顯示如何在物件中建立和轉譯 `ArcSegment` `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，從 (10，10) 到 (200100) 繪製橢圓弧線。

### <a name="create-a-beziersegment"></a>建立 System.windows.media.beziersegment>

會在 `BezierSegment` 兩個點之間建立三次方貝茲曲線。 三次方貝茲曲線是由四個點所定義：起點、結束點和兩個控制點。

`BezierSegment`類別會定義下列屬性：

- `Point1`，類型為 [`Point`](xref:Xamarin.Forms.Point) ，表示曲線的第一個控制點。 這個屬性的預設值是 (0，0) 。
- `Point2`， [`Point`](xref:Xamarin.Forms.Point) 代表曲線的第二個控制點的型別。 這個屬性的預設值是 (0，0) 。
- `Point3`， [`Point`](xref:Xamarin.Forms.Point) 代表曲線結束點的型別。 這個屬性的預設值是 (0，0) 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> `BezierSegment`類別不包含曲線起點的屬性。 曲線的起點是 `PathFigure` 要加入之的目前點 `BezierSegment` 。

三次方貝茲曲線的兩個控制點會像磁鐵一樣地運作，以吸引本身的部分，並產生曲線。 第一個控制點會影響曲線的開始部分。 第二個控制點會影響曲線的結束部分。 曲線不一定會通過任何控制點。 相反地，每個控制點都會將其部分行移至本身，但不會到本身。

下列範例顯示如何在物件中建立和轉譯 `BezierSegment` `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會將三次方貝茲曲線繪製自 (10，10) 到 (300，10) 。 曲線有兩個指向 (100、0) 和 (200200) 的控制點：

![折線圖：顯示貝茲曲線。](geometry-images/beziersegment.png "System.windows.media.beziersegment>")

### <a name="create-a-linesegment"></a>建立 LineSegment

會在 `LineSegment` 兩個點之間建立線條。

`LineSegment`類別會定義 `Point` 類型的屬性， [`Point`](xref:Xamarin.Forms.Point) 代表線段的結束點。 這個屬性的預設值是 (0、0) ，而且是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

> [!NOTE]
> `LineSegment`類別不包含行起點的屬性。 它只會定義端點。 該行的起點是 `PathFigure` 要加入之的目前點 `LineSegment` 。

下列範例顯示如何 `LineSegment` 在物件中建立和轉譯物件 `Path` ：

```xaml
<Path Stroke="Black"      
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，從 (10100) 到 (100100) ，以及從 (100100) 到 (100，50) 繪製線段。 此外， `PathFigure` 也會關閉，因為它的 `IsClosed` 屬性設定為 `true` 。 這會導致繪製三角形：

![折線圖：顯示三角形。](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>建立 PolyBezierSegment

會 `PolyBezierSegment` 建立一或多個三次方貝茲曲線。

`PolyBezierSegment`類別會定義型別為 `Points` 的屬性， `PointCollection` 其代表定義的點 `PolyBezierSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 物件的。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

> [!NOTE]
> `PolyBezierSegment`類別不包含曲線起點的屬性。 曲線的起點是 `PathFigure` 要加入之的目前點 `PolyBezierSegment` 。

下列範例顯示如何在物件中建立和轉譯 `PolyBezierSegment` `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會 `PolyBezierSegment` 指定兩個三次方貝茲曲線。 第一個曲線是從 (10、10) 到 (150100) ，且控制項點為 (0、0) ，另一個控制點為 (100，0) 。 第二個曲線是從 (150100) 到 (300，10) ，且控制點為 (150，0) ，另一個控制點為 (200，0) ：

![折線圖會顯示兩個連接的貝茲曲線。](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>建立 PolyLineSegment

會 `PolyLineSegment` 建立一或多個線段。

`PolyLineSegment`類別會定義型別為 `Points` 的屬性， `PointCollection` 其代表定義的點 `PolyLineSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 物件的。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

> [!NOTE]
> `PolyLineSegment`類別不包含行起點的屬性。 該行的起點是 `PathFigure` 要加入之的目前點 `PolyLineSegment` 。

下列範例顯示如何在物件中建立和轉譯 `PolyLineSegment` `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會 `PolyLineSegment` 指定兩行。 第一行是從 (10、10) 到 (50、10) ，而第二行是從 (50，10) 到 (50，50) ：

![折線圖：以右角顯示兩行。](geometry-images/polylinesegment.png "PolyLineSegment")

### <a name="create-a-polyquadraticbeziersegment"></a>建立 PolyQuadraticBezierSegment

會 `PolyQuadraticBezierSegment` 建立一或多個二次方貝茲曲線。

`PolyQuadraticBezierSegment`類別會定義型別為 `Points` 的屬性， `PointCollection` 其代表定義的點 `PolyQuadraticBezierSegment` 。 `PointCollection`是 `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) 物件的。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

> [!NOTE]
> `PolyQuadraticBezierSegment`類別不包含曲線起點的屬性。 曲線的起點是 `PathFigure` 要加入之的目前點 `PolyQuadraticBezierSegment` 。

下列範例顯示如何 `PolyQuadraticBezierSegment` 在物件中建立和轉譯 `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會 `PolyQuadraticBezierSegment` 指定兩個貝茲曲線。 第一個曲線是從 (10，10) 到 (150，50) ，且 (100100) 有一個控制點。 第二個曲線是從 (100100) 到 (15200) 和 (0100) 上的控制點：

![折線圖會顯示兩個連接的重迭貝茲曲線。](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>建立 System.windows.media.quadraticbeziersegment>

會在 `QuadraticBezierSegment` 兩個點之間建立二次方貝茲曲線。

`QuadraticBezierSegment`類別會定義下列屬性：

- `Point1`， [`Point`](xref:Xamarin.Forms.Point) 代表曲線控制點的型別。 這個屬性的預設值是 (0，0) 。
- `Point2`， [`Point`](xref:Xamarin.Forms.Point) 代表曲線結束點的型別。 這個屬性的預設值是 (0，0) 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> `QuadraticBezierSegment`類別不包含曲線起點的屬性。 曲線的起點是 `PathFigure` 要加入之的目前點 `QuadraticBezierSegment` 。

下列範例顯示如何在物件中建立和轉譯 `QuadraticBezierSegment` `Path` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會將二次方貝茲曲線從 (10，10) 繪製至 (300，10) 。 曲線的控制點位於 (200200) ：

![折線圖：顯示二次方貝茲曲線。](geometry-images/quadraticbeziersegment.png "System.windows.media.quadraticbeziersegment>")

### <a name="create-complex-geometries"></a>建立複雜的幾何

您可以使用物件的組合來建立更複雜的幾何 `PathSegment` 。 下列範例會使用、和來建立圖形 `BezierSegment` `LineSegment` `ArcSegment` ：

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中， `BezierSegment` 會先使用四個點來定義。 然後，此範例 `LineSegment` 會新增，它會在的結束點與所 `BezierSegment` 指定的點之間繪製 `LineSegment` 。 最後， `ArcSegment` 會從的結束點將繪製至所 `LineSegment` 指定的點 `ArcSegment` 。

您可以使用中的多個物件來建立更複雜的幾何 `PathFigure` `PathGeometry` 。 下列範例會 `PathGeometry` 從七個 `PathFigure` 物件建立，其中包含多個 `PathSegment` 物件：

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

在此範例中，會使用和物件的組合以及單一物件來繪製 "Hello" 這個字 `LineSegment` `BezierSegment` `ArcSegment` ：

![多個 PathFigure 物件](geometry-images/multiple-pathfigures.png "多個 PathFigure 物件")

## <a name="composite-geometries"></a>複合幾何

您可以使用來建立複合幾何物件 `GeometryGroup` 。 `GeometryGroup`類別會建立一個或多個物件的複合幾何 `Geometry` 。 任何數目的 `Geometry` 物件都可以加入至 `GeometryGroup` 。

`GeometryGroup`類別會定義下列屬性：

- `Children`，類型為 `GeometryCollection` ，物種定義的物件 `GeomtryGroup` 。 `GeometryCollection`是 `ObservableCollection` `Geometry` 物件的。
- `FillRule`，類型為 `FillRule` ，指定中的交集區域如何 `GeometryGroup` 合併。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> `Children`屬性是類別的 `ContentProperty` `GeometryGroup` ，因此不需要從 XAML 明確設定。

如需列舉的詳細資訊 `FillRule` ，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

若要繪製複合幾何，請將必要的 `Geometry` 物件設定為的子系 `GeometryGroup` ，並將其顯示為 `Path` 物件。 下列 XAML 顯示此範例：

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

在此範例中， `EllipseGeometry` 會合並四個具有相同 x 半徑和 y 半徑座標但具有不同中心座標的物件。 這會建立四個重迭的圓形，其內部會因為預設 `EvenOdd` 填滿規則而填滿橙色：

![折線圖會顯示四個已填滿區域的重迭圓形。](geometry-images/geometrygroup.png "GeometryGroup")

### <a name="roundrectanglegeometry"></a>RoundRectangleGeometry

圓角矩形幾何代表矩形的幾何（或正方形）（圓角），並以圓角半徑和 [`Rect`](xref:Xamarin.Forms.Rect) 指定其相對位置及其高度和寬度的結構來定義。

`RoundRectangleGeometry`衍生自類別的類別會 `GeometryGroup` 定義下列屬性：

- `CornerRadius`，屬於類型的 [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) ，也就是幾何的圓角半徑。
- `Rect`， [`Rect`](xref:Xamarin.Forms.Rect) 代表矩形維度的型別。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> 使用的填滿規則 `RoundRectangleGeometry` 為 `FillRule.Nonzero` 。 如需填滿規則的詳細資訊，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。

下列範例顯示如何在物件中建立和轉譯 `RoundRectangleGeometry` `Path` ：

```xaml
<Path Fill="Blue"
      Stroke="Red">
    <Path.Data>
        <RoundRectangleGeometry CornerRadius="5"
                                Rect="10,10,150,100" />
    </Path.Data>
</Path>
```

矩形的位置和維度是由 [`Rect`](xref:Xamarin.Forms.Rect) 結構定義。 在此範例中，位置是 (10，10) ，寬度是150，而高度是100裝置獨立單位。 此外，矩形邊角會以5個裝置獨立單位的半徑四捨五入。

## <a name="clip-with-a-geometry"></a>具有幾何的剪輯

[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別具有 `Clip` 類型的屬性， `Geometry` 可定義元素內容的大綱。 當 `Clip` 屬性設定為 `Geometry` 物件時，只會顯示區域內的區域 `Geometry` 。

下列範例顯示如何使用 `Geometry` 物件做為的剪輯區域 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

在此範例中， `EllipseGeometry` `RadiusX` `RadiusY` 會將和值設定為100，而 `Center` (180180) 的值則設定為的 `Clip` 屬性 [`Image`](xref:Xamarin.Forms.Image) 。 只會顯示位於橢圓形區域內的影像部分：

![使用 EllipseGeometry 來裁剪影像](geometries-images/clip-ellipsegeometry.png "使用 EllipseGeometry 來裁剪影像")

> [!NOTE]
> 簡單的幾何、路徑幾何和複合幾何都可以用來裁剪 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件。

## <a name="other-features"></a>其他功能

`GeometryHelper`類別提供下列 helper 方法：

- `FlattenGeometry`將 a 壓平 `Geometry` 合併成的 `PathGeometry` 。
- `FlattenCubicBezier`，會將三次方貝茲曲線壓平合併成一個 `List<Point>` 集合。
- `FlattenQuadraticBezier`，會將二次方貝茲曲線壓平合併成一個 `List<Point>` 集合。
- `FlattenArc`，會將橢圓形弧線壓平合併成一個 `List<Point>` 集合。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 圖形：填滿規則](fillrules.md)
