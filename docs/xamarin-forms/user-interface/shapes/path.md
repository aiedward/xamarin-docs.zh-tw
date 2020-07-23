---
title: Xamarin.Forms圖形：路徑
description: Xamarin.FormsPath 類別可以用來繪製曲線和複雜的圖形。
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8e23e4151c4841dd4dce80ba0358471c64a26f39
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937603"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.Forms圖形：路徑

![發行前版本 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Path`類別衍生自 `Shape` 類別，而且可以用來繪製曲線和複雜圖形。 這些曲線和圖形通常會使用物件來描述 `Geometry` 。 如需 `Path` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Path` 會定義下列屬性：

- `Data`，屬於類型 `Geometry` ，指定要繪製的形狀。
- `RenderTransform`，屬於類型 `Transform` ，表示在繪製路徑之前，要套用至其幾何的轉換。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

如需轉換的詳細資訊，請參閱[ Xamarin.Forms 路徑轉換](path-transforms.md)。

## <a name="create-a-path"></a>建立路徑

若要繪製路徑，請建立 `Path` 物件並設定其 `Data` 屬性。 設定屬性的方法有兩種 `Data` ：

- 您可以 `Data` 使用路徑標記語法，在 XAML 中設定的字串值。 使用此方法時，此 `Path.Data` 值會耗用圖形的序列化格式。 一般來說，您不會在建立此字串值之後手動編輯它。 相反地，您可以使用設計工具來運算元據，並將它匯出為屬性所能使用的字串片段 `Data` 。
- 您可以將 `Data` 屬性設定為 `Geometry` 物件。 這可以是特定 `Geometry` 物件，或作為 `GeometryGroup` 可將多個 geometry 物件結合成單一物件的容器。

### <a name="create-a-path-with-path-markup-syntax"></a>使用路徑標記語法建立路徑

下列 XAML 範例顯示如何使用路徑標記語法繪製三角形：

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

`Data`字串的開頭為 move 命令，由所表示 `M` ，它會建立路徑的絕對起始點。 `L`是 line 命令，它會從起點到指定的結束點建立直線。 `Z`是 [關閉] 命令，它會建立將目前點連接到起點的線條。 結果為三角形：

![路徑三角形](path-images/triangle.png "路徑三角形")

> [!NOTE]
> 路徑標記語法僅適用于 XAML。

如需路徑標記語法的詳細資訊，請參閱[ Xamarin.Forms 路徑標記語法](path-markup-syntax.md)。

### <a name="create-a-path-with-geometry-objects"></a>建立具有 Geometry 物件的路徑

您可以使用 `Geometry` 物件（用來設定 `Path` 物件的屬性）來描述曲線和圖形 `Data` 。 有各種不同的 `Geometry` 物件可供選擇。 `EllipseGeometry`、 `LineGeometry` 和類別會 `RectangleGeometry` 描述相當簡單的圖形。 若要建立更複雜的圖形或建立曲線，請使用 `PathGeometry` 。

`PathGeometry`物件是由一或多個 `PathFigure` 物件所組成。 每個 `PathFigure` 物件都代表不同的形狀。 每個 `PathFigure` 物件本身都是由一個或多個物件所組成 `PathSegment` ，每個物件都代表圖形的連接部分。 區段類型包含下列 `LineSegment` 、 `BezierSegment` 和 `ArcSegment` 類別。

下列 XAML 範例顯示如何使用物件繪製三角形 `PathGeometry` ：

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

在此範例中，三角形的起點是（10100）。 從（10100）到（100100），以及從（100100）繪製到（100，50）的線段。 然後會連接到第一個和最後一個區段，因為 `PathFigure.IsClosed` 屬性設定為 `true` 。 結果為三角形：

![路徑三角形](path-images/triangle.png "路徑三角形")

如需幾何的詳細資訊，請參閱[ Xamarin.Forms 幾何](geometries.md)。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
- [Xamarin.Forms幾何圖形](geometries.md)
- [Xamarin.Forms路徑標記語法](path-markup-syntax.md)
- [Xamarin.Forms路徑轉換](path-transforms.md)
