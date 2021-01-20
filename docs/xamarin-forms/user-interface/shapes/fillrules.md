---
title: Xamarin.Forms 圖形：填滿規則
description: Xamarin.Forms 圖形填滿規則可決定某個點是否位於圖形的填滿區域中。
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3daf08c688be41652ae2573b0bf58e2ace2072c6
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609140"
---
# <a name="no-locxamarinforms-shapes-fill-rules"></a>Xamarin.Forms 圖形：填滿規則

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

數個 Xamarin.Forms Shapes 類別具有 `FillRule` 類型的屬性 `FillRule` 。 其中包括 `Polygon` 、 `Polyline` 和 `GeometryGroup` 。

`FillRule`列舉會定義 `EvenOdd` 和 `Nonzero` 成員。 每個成員都代表不同的規則，用來判斷某個點是否在圖形的填滿區域中。

> [!IMPORTANT]
> 基於填滿規則的目的，所有圖形都會被視為已關閉。

## <a name="evenodd"></a>EvenOdd

`EvenOdd`填滿規則會以任何方向從點到無限大繪製光線，並計算光線相交之形狀內的區段數目。 如果這個數位是奇數，則點是在內部。 如果這個數位為偶數，則點是在外部。

下列 XAML 範例會建立並轉譯複合圖形， `FillRule` 預設為 `EvenOdd` ：

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

在此範例中，會顯示一系列同心圓所組成的複合圖形：

![具有 EvenOdd 填滿規則的複合圖形](fillrule-images/evenodd.png "具有 EvenOdd 填滿規則的複合圖形")

在複合圖形中，請注意中央和第三個環形未填滿。 這是因為從這兩個環形中任何點所繪製的光線都會通過偶數的區段：

![具有 EvenOdd 填滿規則的標注複合圖形](fillrule-images/evenodd-annotated.png "具有 EvenOdd 填滿規則的標注複合圖形")

在上圖中，紅色圓圈表示點，而線條則代表任意光線。 在上方，兩個任意光線都會通過偶數的線段。 因此，點不會填滿的環形。 針對較低點，每個任意光線都會通過奇數的線段。 因此，會填滿點所在的環形。

## <a name="nonzero"></a>零

`Nonzero`填滿規則會以任何方向從點到無限大繪製一個光線，然後檢查圖案線段與光線相交的位置。 從零開始，計數會在每次區段從左至右時遞增，並在每次區段跨越光線的時候遞減時遞增。 計算交點之後，如果結果為零，則點位於多邊形之外。 否則，它會在內部。

下列 XAML 範例會建立並轉譯複合圖形，並將其 `FillRule` 設定為 `Nonzero` ：

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

在此範例中，會顯示一系列同心圓所組成的複合圖形：

![圖顯示四個同心圓，全部都已填滿。](fillrule-images/nonzero.png "具有非零填滿規則的複合圖形")

在複合圖形中，請注意所有環形都會填滿。 這是因為所有區段都是以相同的方向執行，因此從任何點繪製的光線會跨越一或多個區段，而交點的總和不等於零：

![圖表顯示上圖中的圓形和方向箭號，以及針對每個相交的圓形以 + 1 標注的光線。](fillrule-images/nonzero-annotated.png "具有非零填滿規則的標注複合圖形")

在上方的影像中，紅色箭號代表繪製線段的方向，而黑色箭號代表從最內層環形的某個點執行的任意光線。 起始值為零，針對光線交會的每個線段，值會增加 1，因為線段由左至右與光線交會。

需要以不同方向執行之區段的更複雜圖形，才能更清楚地展示 `Nonzero` 填滿規則的行為。 下列 XAML 範例會建立與上一個範例類似的圖形，不同之處在于它是使用來建立的， `PathGeometry` 而不是 `EllipseGeometry` ：

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

在此範例中，會繪製一連串的弧形區段，而不會關閉：

![圖顯示四個同心圓，其中的最外層和第三個都已填入。](fillrule-images/nonzero-gaps.png "具有非零填滿規則的複合圖形")

在上圖中，不會填滿中間的第三個弧形。 這是因為指定光線與其路徑中的區段之間的值總和為零：

![圖表顯示上圖中的圓形與方向箭號，以及兩個以 + 1 標注的光線，或針對每個相交的圓形為-1。](fillrule-images/nonzero-gaps-annotated.png "具有非零填滿規則的標注複合圖形")

在上圖中，紅色圓圈表示點，黑色線條代表從未填滿區域中的點往外移動的任意光線，紅色箭號表示段落繪製的方向。 如您所見，與區段之間的光線之間的值總和為零：

- 以對角線方向移動的任意光線會跨越以不同方向執行的兩個區段。 因此，這些區段會取消彼此的值零。
- 以對角線向左移動的任意光線會跨越總共六個區段。 但是，交點會彼此取消，因此零是最後的總和。

零的總和會導致無法填滿環形。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
