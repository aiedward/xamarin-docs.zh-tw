---
title: Xamarin.Forms圖形：路徑
description: Xamarin.FormsPath 類別可以用來繪製曲線和複雜的圖形。
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 384dcef3c2b480166f17e91d547f8cda39c83dc0
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85132966"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.Forms圖形：路徑

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Path`類別衍生自 `Shape` 類別，而且可以用來繪製曲線和複雜圖形。 這些曲線和圖形通常會使用物件來描述 `Geometry` 。 如需 `Path` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Path` 會定義下列屬性：

- `Data`，屬於類型 `Geometry` ，指定要繪製的形狀。
- `RenderTransform`，屬於類型 `Transform` ，表示在繪製路徑之前，要套用至其幾何的轉換。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

如需轉換的詳細資訊，請參閱[ Xamarin.Forms 路徑轉換](path-transforms.md)。

## <a name="create-a-path"></a>建立路徑

下列 XAML 範例顯示如何使用特殊的縮寫語法（稱為路徑標記語法）繪製多邊形：

```xaml
<Path Data="M 10,50 L 200,70"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100" />
```

`Data`字串的開頭是 "moveto" 命令，由指定 `M` ，它會建立路徑的起始點。 `L`是 line 命令，它會從起點到指定的結束點建立直線。

> [!NOTE]
> 路徑標記語法僅適用于 XAML。

如需路徑標記語法的詳細資訊，請參閱[ Xamarin.Forms 路徑標記語法](path-markup-syntax.md)。

## <a name="path-geometry"></a>路徑幾何

您可以使用 `Geometry` 物件（用來設定 `Path` 物件的屬性）來描述曲線和圖形 `Data` 。

有各種不同的 `Geometry` 物件可供選擇。 `EllipseGeometry`、 `LineGeometry` 和類別會 `RectangleGeometry` 描述相當簡單的圖形。 若要建立更複雜的圖形或建立曲線，請使用 `PathGeometry` 。

`PathGeometry`物件是由一或多個 `PathFigure` 物件所組成。 每個 `PathFigure` 物件都代表不同的形狀。 每個 `PathFigure` 物件本身都是由一個或多個物件所組成 `PathSegment` ，每個物件都代表圖形的連接部分。 區段類型包括下列各項： `LineSegment` 、 `BezierSegment` 和 `ArcSegment` 。

在下列範例中， `Path` 是用來繪製三角形：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
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

如需幾何的詳細資訊，請參閱[ Xamarin.Forms 幾何](geometries.md)。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
- [Xamarin.Forms幾何圖形](geometries.md)
- [Xamarin.Forms路徑標記語法](path-markup-syntax.md)
- [Xamarin.Forms路徑轉換](path-transforms.md)
