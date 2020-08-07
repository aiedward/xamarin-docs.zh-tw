---
title: Xamarin.Forms形狀
description: Xamarin.Forms圖形是可讓您在螢幕上繪製圖形的檢視類型。
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6a0771ac0dbbbc89301aeca3812c3b49e14655a2
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918465"
---
# <a name="no-locxamarinforms-shapes"></a>Xamarin.Forms形狀

![發行前版本 API](~/media/shared/preview.png)

`Shape`是的一種類型 [`View`](xref:Xamarin.Forms.View) ，可讓您在螢幕上繪製圖形。 `Shape`物件可以在版面配置類別和大部分的控制項內使用，因為 `Shape` 類別衍生自 `View` 類別。

Xamarin.Forms圖形適用于 `Xamarin.Forms.Shapes` iOS、Android、macOS 上的命名空間、通用 Windows 平臺 (UWP) ，以及 (WPF) 的 Windows Presentation Foundation。

> [!IMPORTANT]
> Xamarin.Forms圖形目前為實驗性，而且只能透過設定旗標來使用 `Shapes_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`Shape` 會定義下列屬性：

- `Aspect`，屬於類型 `Stretch` ，描述圖形如何填滿其已配置的空間。 此屬性的預設值為 `Stretch.None`。
- `Fill`，屬於類型 `Brush` ，表示用來繪製圖形內部的筆刷。
- `Stroke`，屬於類型 `Brush` ，表示用來繪製圖形外框的筆刷。
- `StrokeDashArray`，屬於類型 `DoubleCollection` ，表示值的集合，表示 `double` 用來勾勒出圖形的虛線和間距模式。
- `StrokeDashOffset`，屬於類型 `double` ，指定虛線模式內虛線開始的距離。 這個屬性的預設值為0.0。
- `StrokeLineCap`，屬於類型 `PenLineCap` ，描述行或線段開頭和結尾的圖形。 此屬性的預設值為 `PenLineCap.Flat`。
- `StrokeLineJoin`，屬於類型 `PenLineJoin` ，可指定用於圖形頂點的聯結類型。 此屬性的預設值為 `PenLineJoin.Miter`。
- `StrokeMiterLimit`，屬於類型 `double` ，可指定將斜接長度的比率限制為形狀的一半 `StrokeThickness` 。 這個屬性的預設值為10.0。
- `StrokeThickness`，屬於類型 `double` ，表示圖形外框的寬度。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

Xamarin.Forms定義從類別衍生的一些物件 `Shape` 。 這些是 `Ellipse` 、 `Line` 、 `Path` 、 `Polygon` 、 `Polyline` 和 `Rectangle` 。

## <a name="paint-shapes"></a>繪製圖案

`Brush`物件可用來繪製圖形的 `Stroke` 和 `Fill` ：

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

在此範例中，會指定的筆觸和填滿 `Ellipse` ：

![繪製圖案](images/ellipse.png "繪製圖案")

> [!IMPORTANT]
> `Brush`物件使用類型轉換子，可讓您為 [`Color`](xref:Xamarin.Forms.Color) 屬性指定值 `Stroke` 。

如果您未指定的 `Brush` 物件 `Stroke` ，或將設定 `StrokeThickness` 為0，則不會繪製圖形周圍的框線。

如需物件的詳細資訊 `Brush` ，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。 如需有效值的詳細資訊 [`Color`](xref:Xamarin.Forms.Color) ，請參閱[ Xamarin.Forms 中的色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="stretch-shapes"></a>Stretch 圖形

`Shape`物件具有 `Aspect` 類型的屬性 `Stretch` 。 這個屬性會決定物件內容的延展方式 `Shape` ，以填滿 `Shape` 物件的版面配置空間。 `Shape`物件的版面配置空間是 `Shape` 配置系統組態的空間量 Xamarin.Forms ，因為有明確的 `WidthRequest` 和設定， `HeightRequest` 或因為其 `HorizontalOptions` 和 `VerticalOptions` 設定。

`Stretch` 列舉會定義下列成員：

- `None`，表示內容會保留其原始大小。 此為 `Shape.Aspect` 屬性的預設值。
- `Fill`，這表示內容已調整大小以填滿目的地維度。 不會保留長寬比。
- `Uniform`，這表示內容已調整大小以符合目的尺寸，同時保留長寬比。
- `UniformToFill`，表示內容已調整大小以填滿目的地維度，同時保留外觀比例。 如果目的矩形的長寬比與來源不同，則會裁剪來源內容使其符合目的尺寸。

下列 XAML 顯示如何設定 `Aspect` 屬性：

```xaml
<Path Aspect="Uniform"
      Stroke="Yellow"
      StrokeThickness="1"
      Fill="Red"
      BackgroundColor="LightGray"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <!-- Path data goes here -->
    </Path.Data>  
</Path>      
```

在此範例中， `Path` 物件會繪製一個心形。 `Path`物件的 `WidthRequest` 和 `HeightRequest` 屬性會設定為100裝置獨立單位，且其 `Aspect` 屬性會設定為 `Uniform` 。 因此，物件的內容會調整大小以符合目的尺寸，同時保留長寬比：

![Stretch 圖形](images/aspect.png "Stretch 圖形")

## <a name="draw-dashed-shapes"></a>繪製虛線形狀

`Shape`物件具有 `StrokeDashArray` 類型的屬性 `DoubleCollection` 。 此屬性代表值的集合 `double` ，這些值表示用來外框的虛線和間距模式。 `DoubleCollection`是 `ObservableCollection` `double` 值的。 集合中的每個都會 `double` 指定虛線或間距的長度。 集合中的第一個專案（位於索引0）會指定虛線的長度。 集合中的第二個專案（位於索引1）指定間距的長度。 因此，具有偶數索引值的物件會指定破折號，而具有奇數索引值的物件則會指定間距。

`Shape`物件也具有 `StrokeDashOffset` 類型的屬性 `double` ，它會指定虛線模式內虛線開始的距離。 若無法設定此屬性，將會產生 `Shape` 實心外框。

您可以藉由設定和屬性來繪製虛線形狀 `StrokeDashArray` `StrokeDashOffset` 。 `StrokeDashArray`屬性應設定為一或多個 `double` 值，每個配對都會以單一逗號和/或一或多個空格分隔。 例如，"0.5 1.0" 和 "0.5，1.0" 都是有效的。

下列 XAML 範例顯示如何繪製虛線矩形：

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           StrokeDashArray="1,1"
           StrokeDashOffset="6"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

在此範例中，繪製了具有虛線筆觸的實心矩形：

![虛線矩形](images/dashed-rectangle.png "虛線")

## <a name="control-line-ends"></a>控制行尾

一行有三個部分：開始端點、行主體和結束端點。 開始和結束大寫會描述線條開頭和結尾的圖形，或區段。

`Shape`物件具有 `StrokeLineCap` 類型的屬性，其 `PenLineCap` 描述線條開頭和結尾的圖形，或區段。 `PenLineCap` 列舉會定義下列成員：

- `Flat`，代表不會延伸超過行最後一個點的上限。 這相當於沒有行帽，而是屬性的預設值 `StrokeLineCap` 。
- `Square`，其代表的高度等於線條粗細，而長度等於線條粗細一半的矩形。
- `Round`，代表其直徑等於線條粗細的半圓。

> [!IMPORTANT]
> `StrokeLineCap`如果您在沒有起點或終點的圖形上設定此屬性，就不會有任何作用。 例如，如果您在、或上設定，這個屬性沒有作用 `Ellipse` `Rectangle` 。

下列 XAML 顯示如何設定 `StrokeLineCap` 屬性：

```xaml
<Line X1="0"
      Y1="20"
      X2="300"
      Y2="20"
      StrokeLineCap="Round"
      Stroke="Red"
      StrokeThickness="12" />
```

在此範例中，紅線會在行的開頭和結尾舍入：

![行首](images/linecap.png "行首")

## <a name="control-line-joins"></a>控制行聯結

`Shape`物件具有 `StrokeLineJoin` 類型的屬性， `PenLineJoin` 可指定用於圖形頂點的聯結類型。 `PenLineJoin` 列舉會定義下列成員：

- `Miter`，代表一般角度頂點。 此為 `StrokeLineJoin` 屬性的預設值。
- `Bevel`，代表斜角頂點。
- `Round`，表示圓角頂點。

> [!NOTE]
> 當 `StrokeLineJoin` 屬性設定為時 `Miter` ，可以將 `StrokeMiterLimit` 屬性設定為， `double` 以限制圖形中線條聯結的斜切長度。

下列 XAML 顯示如何設定 `StrokeLineJoin` 屬性：

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

在此範例中，深藍色的折線會在其頂點上有四捨五入聯結：

![線路聯結](images/linejoin.png "線路聯結")

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms畫](~/xamarin-forms/user-interface/brushes/index.md)
- [色彩于Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)
