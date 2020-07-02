---
title: Xamarin.Forms圖形：路徑標記語法
description: Xamarin.Forms路徑標記語法可讓您簡潔地在 XAML 中指定路徑幾何。
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 124c739f68ce8a3fcbc359a07513a2bcb178578f
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853127"
---
# <a name="xamarinforms-shapes-path-markup-syntax"></a>Xamarin.Forms圖形：路徑標記語法

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Forms路徑標記語法可讓您簡潔地在 XAML 中指定路徑幾何。 語法會指定為屬性的字串值 `Path.Data` ：

```xaml
<Path Stroke="Black"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

路徑標記語法是由選擇性值以及 `FillRule` 一或多個圖表描述所組成。 此語法可以表示為： `<Path Data="` *[fillRule]* *figureDescription* *[figureDescription]* * `" ... />`

在此語法中：

- *fillRule*是選擇性 `Xamarin.Forms.Shapes.FillRule` 的，它會指定幾何是否應使用 `EvenOdd` 或 `Nonzero` `FillRule` 。 `F0`是用來指定 `EvenOdd` 填滿規則，而 `F1` 則是用來指定 `Nonzero` 填滿規則。 如需填滿規則的詳細資訊，請參閱[ Xamarin.Forms 圖形：填滿規則](fillrules.md)。
- *figureDescription*代表由移動命令、繪製命令和選擇性的關閉命令所組成的圖表。 Move 命令會指定圖表的起點。 [繪製] 命令會描述圖形的內容，而選擇性的 [關閉] 命令則會關閉圖表。

在上述範例中，路徑標記語法會使用 move 命令（ `M` ）、一系列使用 line 命令（）的直線來指定起點，並使用 `L` close 命令（）關閉路徑 `Z` 。

在路徑標記語法中，命令前後不需要空格。 此外，兩個數字不需要以逗號或空白字元分隔，但只有在字串是明確的時才可以達到此目的。

> [!TIP]
> 路徑標記語言會使用與可調整向量圖形（SVG）影像路徑定義相容的語法，因此適用于從 SVG 格式移植圖形。

## <a name="move-command"></a>移動命令

Move 命令會指定新圖形的起點。 此命令的語法為： `M` *startPoint*或 `m` *startPoint*。

在此語法中， *startPoint*是 [`Point`](xref:Xamarin.Forms.Point) 指定新圖起點的結構。 如果您在移動命令之後列出多個點，則會將線條繪製到這些點。

`M 10,10`是有效的 move 命令的範例。

## <a name="draw-commands"></a>繪製命令

繪製命令可以由數個圖形命令組成。 下列繪製命令可供使用：

- 行（ `L` 或 `l` ）。
- 水平線（ `H` 或 `h` ）。
- 垂直線（ `V` 或 `v` ）。
- 橢圓形弧線（ `A` 或 `a` ）。
- 三次方貝茲曲線（ `C` 或 `c` ）。
- 二次方貝茲曲線（ `Q` 或 `q` ）。
- 平滑的三次方貝茲曲線（ `S` 或 `s` ）。
- 平滑二次方貝茲曲線（ `T` 或 `t` ）。

每個 draw 命令都會以不區分大小寫的字母來指定。 依序輸入多個相同類型的命令時，可以省略重複的命令項目。 例如 `L 100,200 300,400` ，相當於 `L 100,200 L 300,400` 。

### <a name="line-command"></a>線條命令

Line 命令會在目前的點和指定的結束點之間建立直線。 此命令的語法為： `L` *端點*或 `l` *端點*。

在此語法中，*端點*是一個 [`Point`](xref:Xamarin.Forms.Point) ，代表行的結束點。

`L 20,30`和 `L 20 30` 是有效行命令的範例。

如需將直線建立為物件的相關資訊 `PathGeometry` ，請參閱[Create a LineSegment](geometries.md#create-a-linesegment)。

### <a name="horizontal-line-command"></a>水平線命令

水平線命令會在目前的點和指定的 x 座標之間建立水平線。 此命令的語法為： `H` *x*或 `h` *x*。

在此語法中， *x*是 `double` ，表示線條結束點的 x 座標。

`H 90` 為有效水平線命令的範例。

### <a name="vertical-line-command"></a>垂直線命令

[垂直線] 命令會在目前的點和指定的 y 座標之間建立一條垂直線。 此命令的語法為： `V` *y*或 `v` *y*。

在此語法中， *y*是 `double` ，表示線條結束點的 y 座標。

`V 90` 為有效垂直線命令的範例。

### <a name="elliptical-arc-command"></a>橢圓形弧線命令

橢圓形 arc 命令會在目前的點和指定的結束點之間建立橢圓形弧線。 此命令的語法為： `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endpoint*或 `a` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endpoint*。

在此語法中：

- `size`是 [`Size`](xref:Xamarin.Forms.Size) ，表示弧線的 x 和 y 半徑。
- `rotationAngle`這是 `double` 代表橢圓形旋轉的（以度為單位）。
- `isLargeArcFlag`如果弧線的角度應為180度或更大，則應該設定為1，否則設定為0。
- `sweepDirectionFlag`如果以正角方向繪製弧線，則應該設定為1，否則設定為0。
- `endPoint`是 [`Point`](xref:Xamarin.Forms.Point) 弧線繪製的。

`A 150,150 0 1,0 150,-150`是有效橢圓形 arc 命令的範例。

如需建立橢圓形弧線做為物件的詳細資訊 `PathGeometry` ，請參閱[建立 ArcSegment](geometries.md#create-an-arcsegment)。

### <a name="cubic-bezier-curve-command"></a>三次方貝茲曲線命令

三次方貝茲曲線命令會使用兩個指定的控制點，在目前的點和指定的結束點之間建立三次方貝茲曲線。 此命令的語法為： `C` *controlPoint1* *controlPoint2* *endpoint*或 `c` *controlPoint1* *controlPoint2* *endpoint*。

在此語法中：

- *controlPoint1*是 [`Point`](xref:Xamarin.Forms.Point) ，表示曲線的第一個控制點，它會決定曲線的起始正切函數。
- *controlPoint2*是 [`Point`](xref:Xamarin.Forms.Point) ，表示曲線的第二個控制點，決定曲線的結束正切函數。
- *端點*是 [`Point`](xref:Xamarin.Forms.Point) ，代表繪製曲線的點。

`C 100,200 200,400 300,200`是有效的三次方貝茲曲線命令的範例。

如需將三次方貝茲曲線建立為物件的詳細資訊 `PathGeometry` ，請參閱[Create a system.windows.media.beziersegment>](geometries.md#create-a-beziersegment)。

### <a name="quadratic-bezier-curve-command"></a>二次方貝茲曲線命令

二次方貝茲曲線命令會使用指定的控制點，在目前的點和指定的結束點之間建立二次方貝茲曲線。 此命令的語法為： `Q` *controlPoint* *端點*或 `q` *controlPoint* *端點*。

在此語法中：

- *controlPoint*是 [`Point`](xref:Xamarin.Forms.Point) 代表曲線控制點的，它會決定曲線的開始和結束正切。
- *端點*是 [`Point`](xref:Xamarin.Forms.Point) ，代表繪製曲線的點。

`Q 100,200 300,200` 為有效二次方貝茲曲線命令的範例。

如需建立二次方貝茲曲線作為物件的詳細資訊 `PathGeometry` ，請參閱[建立 system.windows.media.quadraticbeziersegment>](geometries.md#create-a-quadraticbeziersegment)。

### <a name="smooth-cubic-bezier-curve-command"></a>平滑三次方貝茲曲線命令

平滑三次方貝茲曲線命令會使用指定的控制點，在目前的點和指定的結束點之間建立三次方貝茲曲線。 此命令的語法為： `S` *controlPoint2* *端點*或 `s` *controlPoint2* *端點*。  

在此語法中：

- *controlPoint2*是 [`Point`](xref:Xamarin.Forms.Point) ，表示曲線的第二個控制點，決定曲線的結束正切函數。
- *端點*是 [`Point`](xref:Xamarin.Forms.Point) ，代表繪製曲線的點。

第一個控制點會假設為上一個命令之第二個控制點的反映（相對於目前的點）。 如果沒有上一個命令，或者上一個命令不是三次方貝茲曲線命令或平滑三次方貝茲曲線命令，則會假設第一個控制點與目前的點一致。

`S 100,200 200,300`是有效的平滑三次方貝茲曲線命令的範例。

### <a name="smooth-quadratic-bezier-curve-command"></a>平滑二次方貝茲曲線命令

平滑二次方貝茲曲線命令會使用控制點，在目前的點和指定的結束點之間建立二次方貝茲曲線。 此命令的語法為： `T` *端點*或 `t` *端點*。

在此語法中，*端點*是 [`Point`](xref:Xamarin.Forms.Point) 代表曲線繪製位置的。

控制點會假設為 (相對於目前的點) 上一個命令之控制點的反映。 如果沒有上一個命令，或者上一個命令不是二次方貝茲曲線或平滑二次方貝茲曲線命令，則會假設控制點與目前的點一致。

`T 100,30`是有效的平滑二次方貝茲曲線命令的範例。

## <a name="close-command"></a>關閉命令

Close 命令會結束目前的圖形，並建立將目前點連接到圖表起點的線條。 因此，此命令會在圖形的最後一個區段和第一個區段之間建立行聯結。

[關閉] 命令的語法為： `Z` 或 `z` 。

## <a name="additional-values"></a>其他值

您也可以使用下列區分大小寫的特殊值，而不是標準數值：

- `Infinity`表示 `double.PositiveInfinity` 。
- `-Infinity`表示 `double.NegativeInfinity` 。
- `NaN`表示 `double.NaN` 。

此外，您也可以使用不區分大小寫的科學記號標記法。 因此， `+1.e17` 是有效的值。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms圖形：幾何](geometries.md)
- [Xamarin.Forms圖形：填滿規則](fillrules.md)
