---
title: Xamarin.Forms圖形：填滿規則
description: Xamarin.Forms圖形填滿規則會決定某個點是否在圖形的填滿區域中。
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 92fcb86f9acac159cc79cae8e71b180fe229b7a6
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85794995"
---
# <a name="xamarinforms-shapes-fill-rules"></a>Xamarin.Forms圖形：填滿規則

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

有數個 Xamarin.Forms 圖形類別具有 `FillRule` 類型的屬性 `FillRule` 。 其中包括 `Polygon` 、 `Polyline` 和 `GeometryGroup` 。

`FillRule`列舉會定義 `EvenOdd` 和 `Nonzero` 成員。 每個成員都代表不同的規則，用來判斷某個點是否在圖形的填滿區域中。

> [!IMPORTANT]
> 所有圖形都會被視為針對填滿規則的目的而關閉。

## <a name="evenodd"></a>EvenOdd

`EvenOdd`填滿規則會以任何方向從點到無限大繪製一個光線，並計算該光線相交之圖形內的線段數目。 如果這個數位是奇數，則點位於內部。 如果此數位為偶數，則此點為 [外部]。

下列 XAML 範例會建立並呈現複合圖形， `FillRule` 預設為 `EvenOdd` ：

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此範例中，會顯示由一系列同心環形組成的複合圖形：

![具有 EvenOdd 填滿規則的複合圖形](fillrule-images/evenodd.png "具有 EvenOdd 填滿規則的複合圖形")

請注意，在 [複合] 圖形中，不會填滿中間和第三個環形。 這是因為從這兩個環形的任一點所繪製的光線會通過偶數的區段：

![具有 EvenOdd 填滿規則的批註複合圖形](fillrule-images/evenodd-annotated.png "具有 EvenOdd 填滿規則的批註複合圖形")

在上圖中，紅色圓圈代表點，而線條代表任意光線。 就重點而言，兩個任意光線都會通過偶數的直線線段。 因此，不會填滿點所在的環形。 就較低的點而言，兩個任意光線都會通過奇數的直線線段。 因此，會填滿點所在的環形。

## <a name="nonzero"></a>零下

`Nonzero`填滿規則會以任何方向從點到無限大繪製光線，然後檢查圖形線段與光線相交的位置。 從零開始，計數會在每次區段從左至右與光線相交時遞增，並在每次區段從右至左時遞減。 計算交點之後，如果結果為零，則點會在多邊形外。 否則，它會在內。

下列 XAML 範例會建立並呈現複合圖形，並將 `FillRule` 設定為 `Nonzero` ：

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

在此範例中，會顯示由一系列同心環形組成的複合圖形：

![具有非零填滿規則的複合圖形](fillrule-images/nonzero.png "具有非零填滿規則的複合圖形")

請注意，在 [複合] 圖形中，所有環形都會填滿。 這是因為所有區段都是以相同的方向執行，因此從任何點繪製的光線會跨越一個或多個區段，而交叉點的總和不會等於零：

![具有非零填滿規則的批註複合圖形](fillrule-images/nonzero-annotated.png "具有非零填滿規則的批註複合圖形")

在上方的影像中，紅色箭號代表線段的繪製方向，而黑色箭號代表從最內層環形的某個點執行的任意光線。 起始值為零，針對光線交會的每個線段，值會增加 1，因為線段由左至右與光線交會。

具有以不同方向執行之區段的更複雜圖形，必須更清楚地示範 `Nonzero` 填滿規則的行為。 下列 XAML 範例會建立與上一個範例類似的圖形，不同之處在于它是使用而不是所建立的 `PathGeometry` `EllipseGeometry` ：

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

在此範例中，會繪製一連串的弧線線段，而不會關閉：

![具有非零填滿規則的複合圖形](fillrule-images/nonzero-gaps.png "具有非零填滿規則的複合圖形")

在上圖中，不會填滿來自中央的第三個弧線。 這是因為指定光線中的值與其路徑中的區段之間的總和為零：

![具有非零填滿規則的批註複合圖形](fillrule-images/nonzero-gaps-annotated.png "具有非零填滿規則的批註複合圖形")

在上圖中，紅色圓形代表一個點，黑色線條代表從非填滿區域中的點往外移動的任意光線，而紅色箭號代表線段繪製的方向。 如您所見，橫跨區段之光線值的總和為零：

- 以對角線方式向右移動的任意光線會跨越以不同方向執行的兩個區段。 因此，區段會彼此取消，讓值為零。
- 以對角線方向向左移動的任意光線會跨越總共六個區段。 不過，交點會彼此取消，因此零是最後的總和。

零的總和會導致不會填滿環形。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
