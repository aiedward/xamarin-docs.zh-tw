---
title: Xamarin.Forms 圖形：路徑
description: Xamarin.FormsPath 類別可以用來繪製曲線和複雜圖形。
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80b0bcd84602fc999764e7e0896a44f83401a9dd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558891"
---
# <a name="no-locxamarinforms-shapes-path"></a>Xamarin.Forms 圖形：路徑

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Path`類別衍生自 `Shape` 類別，而且可以用來繪製曲線和複雜圖形。 這些曲線和圖形通常會使用物件來描述 `Geometry` 。 如需 `Path` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Path` 會定義下列屬性：

- `Data`，類型為 `Geometry` ，指定要繪製的圖形。
- `RenderTransform`型別 `Transform` ，代表在繪製之前套用至路徑幾何的轉換。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

如需轉換的詳細資訊，請參閱[ Xamarin.Forms 路徑轉換](path-transforms.md)。

## <a name="create-a-path"></a>建立路徑

若要繪製路徑，請建立 `Path` 物件並設定其 `Data` 屬性。 有兩種方法可設定 `Data` 屬性：

- 您可以 `Data` 使用路徑標記語法，在 XAML 中設定的字串值。 使用這個方法時， `Path.Data` 值會耗用圖形的序列化格式。 一般而言，您不會在建立此字串值之後手動加以編輯。 相反地，您會使用設計工具來運算元據，並將它匯出為屬性可取用的字串片段 `Data` 。
- 您可以將 `Data` 屬性設定為 `Geometry` 物件。 這可以是特定 `Geometry` 物件，或 `GeometryGroup` 做為可將多個幾何物件合併成單一物件的容器。

### <a name="create-a-path-with-path-markup-syntax"></a>使用路徑標記語法建立路徑

下列 XAML 範例顯示如何使用路徑標記語法繪製三角形：

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

`Data`字串的開頭是 move 命令，它是由所表示 `M` ，它會建立路徑的絕對起始點。 `L` 這是 line 命令，它會從起點到指定的結束點建立直線。 `Z` 是 close 命令，它會建立將目前點連接到起點的線。 結果為三角形：

![路徑三角形](path-images/triangle.png "路徑三角形")

> [!NOTE]
> 路徑標記語法只適用于 XAML。

如需路徑標記語法的詳細資訊，請參閱[ Xamarin.Forms 路徑標記語法](path-markup-syntax.md)。

### <a name="create-a-path-with-geometry-objects"></a>建立具有 Geometry 物件的路徑

您可以使用物件來描述曲線和圖形 `Geometry` ，這些物件是用來設定 `Path` 物件的 `Data` 屬性。 有各種 `Geometry` 物件可供選擇。 `EllipseGeometry`、 `LineGeometry` 和類別會 `RectangleGeometry` 描述相當簡單的圖形。 若要建立更複雜的圖形或建立曲線，請使用 `PathGeometry` 。

`PathGeometry` 物件是由一或多個 `PathFigure` 物件所組成。 每個 `PathFigure` 物件都代表不同的形狀。 每個 `PathFigure` 物件都是由一或多個 `PathSegment` 物件組成，每個物件都代表圖形的連接部分。 區段類型包含下列 `LineSegment` 、 `BezierSegment` 和 `ArcSegment` 類別。

下列 XAML 範例顯示如何使用物件繪製三角形 `PathGeometry` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
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

在此範例中，三角形的起始點是 (10100) 。 從 (10100) 到 (100100) ，以及從 (100100) 到 (100，50) 繪製線段。 然後，第一個和最後一個區段會連接，因為 `PathFigure.IsClosed` 屬性會設定為 `true` 。 結果為三角形：

![路徑三角形](path-images/triangle.png "路徑三角形")

如需幾何的詳細資訊，請參閱[ Xamarin.Forms 幾何](geometries.md)。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 幾何](geometries.md)
- [Xamarin.Forms 路徑標記語法](path-markup-syntax.md)
- [Xamarin.Forms 路徑轉換](path-transforms.md)