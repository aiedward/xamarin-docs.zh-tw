---
title: Xamarin.Forms 形狀
description: Xamarin.Forms 圖形是可讓您在螢幕上繪製圖案的檢視類型。
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 07765fdb9f67f21415db495da1b635b0507b7ed1
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559203"
---
# <a name="no-locxamarinforms-shapes"></a>Xamarin.Forms 形狀

![發行前 API](~/media/shared/preview.png)

`Shape`是的類型，可 [`View`](xref:Xamarin.Forms.View) 讓您在螢幕上繪製圖形。 `Shape` 物件可用於版面配置類別和大部分的控制項中，因為該 `Shape` 類別衍生自 `View` 類別。

Xamarin.Forms 圖形可在 `Xamarin.Forms.Shapes` iOS、Android、macOS、通用 Windows 平臺 (UWP) 和 Windows Presentation Foundation (WPF) 上的命名空間中使用。

> [!IMPORTANT]
> Xamarin.Forms 圖形目前為實驗性，而且只能透過設定旗標來使用 `Shapes_Experimental` 。 如需詳細資訊，請參閱 [實驗旗標](~/xamarin-forms/internals/experimental-flags.md)。

`Shape` 會定義下列屬性：

- `Aspect`型別為的， `Stretch` 描述圖形如何填滿其配置空間。 此屬性的預設值為 `Stretch.None`。
- `Fill`型別為的 `Brush` ，表示用來繪製圖形內部的筆刷。
- `Stroke`型別為的 `Brush` ，表示用來繪製形狀大綱的筆刷。
- `StrokeDashArray`型別為的 `DoubleCollection` ，代表值的集合， `double` 這些值表示用來勾勒出形狀的虛線和間距模式。
- `StrokeDashOffset`型別為的 `double` ，指定虛線模式內的距離（以虛線開始）。 這個屬性的預設值是0.0。
- `StrokeLineCap`型別為的， `PenLineCap` 描述線條或線段開頭和結尾的圖形。 此屬性的預設值為 `PenLineCap.Flat`。
- `StrokeLineJoin`，類型為 `PenLineJoin` ，指定在圖形頂點上使用的聯結類型。 此屬性的預設值為 `PenLineJoin.Miter`。
- `StrokeMiterLimit`型別為的，會將 `double` 斜接長度的比例指定為形狀的一半 `StrokeThickness` 。 這個屬性的預設值是10.0。
- `StrokeThickness`型別為的 `double` ，表示圖形大綱的寬度。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

Xamarin.Forms 定義衍生自類別的許多物件 `Shape` 。 這些是 `Ellipse` 、 `Line` 、 `Path` 、 `Polygon` 、 `Polyline` 和 `Rectangle` 。

## <a name="paint-shapes"></a>繪製圖形

`Brush` 物件是用來繪製圖案 `Stroke` 和 `Fill` ：

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

在此範例中，會指定的筆觸和填滿 `Ellipse` ：

![繪製圖形](images/ellipse.png "繪製圖形")

> [!IMPORTANT]
> `Brush` 物件使用型別轉換子，可 [`Color`](xref:Xamarin.Forms.Color) 針對屬性指定值 `Stroke` 。

如果您未指定的 `Brush` 物件 `Stroke` ，或設定 `StrokeThickness` 為0，則不會繪製圖形周圍的框線。

如需物件的詳細資訊 `Brush` ，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。 如需有效值的詳細資訊 [`Color`](xref:Xamarin.Forms.Color) ，請參閱[中的 Xamarin.Forms 色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="stretch-shapes"></a>Stretch 圖案

`Shape` 物件具有 `Aspect` 類型的屬性 `Stretch` 。 這個屬性 `Shape` 會決定物件的內容如何延展以填滿 `Shape` 物件的版面配置空間。 `Shape`物件的版面配置空間是 `Shape` 配置系統組態的空間量 Xamarin.Forms ，因為其和設定是明確的 `WidthRequest` 和 `HeightRequest` 設定 `HorizontalOptions` `VerticalOptions` 。

`Stretch` 列舉會定義下列成員：

- `None`，表示內容會保留其原始大小。 此為 `Shape.Aspect` 屬性的預設值。
- `Fill`，表示內容已調整大小以填滿目的維度。 不會保留長寬比。
- `Uniform`，表示內容會調整大小以符合目的尺寸，同時保留外觀比例。
- `UniformToFill`，表示內容已調整大小以填滿目的尺寸，同時保留外觀比例。 如果目的矩形的長寬比與來源不同，則會裁剪來源內容使其符合目的尺寸。

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

在此範例中， `Path` 物件會繪製一個核心。 `Path`物件的 `WidthRequest` 和 `HeightRequest` 屬性會設定為100與裝置無關的單位，且其 `Aspect` 屬性會設定為 `Uniform` 。 因此，物件的內容會調整大小以符合目的尺寸，同時保留外觀比例：

![Stretch 圖案](images/aspect.png "Stretch 圖案")

## <a name="draw-dashed-shapes"></a>繪製虛線圖形

`Shape` 物件具有 `StrokeDashArray` 類型的屬性 `DoubleCollection` 。 這個屬性工作表示值的集合 `double` ，表示用來勾勒出形狀的虛線和間距模式。 `DoubleCollection`是 `ObservableCollection` `double` 值的。 集合中的每一個都會 `double` 指定虛線或間距的長度。 集合中的第一個專案（位於索引0）指定破折號的長度。 集合中的第二個專案（位於索引1）指定間距的長度。 因此，具有偶數索引值的物件會指定破折號，而具有奇數索引值的物件則指定間距。

`Shape` 物件也具有 `StrokeDashOffset` 類型的屬性， `double` 它會指定虛線模式內的距離（以虛線開始）。 若無法設定這個屬性，將會產生 `Shape` 實心外框。

您可以藉由設定和屬性來繪製虛線圖形 `StrokeDashArray` `StrokeDashOffset` 。 `StrokeDashArray`屬性應設定為一或多個 `double` 值，每個配對都以單一逗號和/或一或多個空格分隔。 例如，"0.5 1.0" 和 "0.5，1.0" 都有效。

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

在此範例中，會繪製具有虛線筆觸的填滿矩形：

![虛線矩形](images/dashed-rectangle.png "虛線")

## <a name="control-line-ends"></a>控制行尾

一行有三個部分：開頭帽、行內文和結束端點。 開始和結束帽會線上條的開頭和結尾描述圖形，或區段的開頭和結尾。

`Shape` 物件具有 `StrokeLineCap` 類型的屬性， `PenLineCap` 描述線條開頭和結尾的圖形，或區段。 `PenLineCap` 列舉會定義下列成員：

- `Flat`，代表未延伸超過行最後一個點的端點。 這相當於無行帽，而且是屬性的預設值 `StrokeLineCap` 。
- `Square`，代表高度等於線條粗細且長度等於線條粗細一半的矩形。
- `Round`，代表具有等於線條粗細之直徑的半圓。

> [!IMPORTANT]
> `StrokeLineCap`如果您在沒有起點或終點的圖形上設定屬性，則屬性不會有任何作用。 例如，如果您在或上設定此屬性，則這個屬性不會有任何作用 `Ellipse` `Rectangle` 。

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

在此範例中，會將紅線四捨五入在行的開頭和結尾：

![行帽](images/linecap.png "行帽")

## <a name="control-line-joins"></a>控制行聯結

`Shape` 物件具有 `StrokeLineJoin` 類型的屬性， `PenLineJoin` 可指定在圖形頂點上使用的聯結類型。 `PenLineJoin` 列舉會定義下列成員：

- `Miter`，代表一般角度頂點。 此為 `StrokeLineJoin` 屬性的預設值。
- `Bevel`，代表斜面頂點。
- `Round`，代表圓角頂點。

> [!NOTE]
> 當 `StrokeLineJoin` 屬性設定為時 `Miter` ，可以將 `StrokeMiterLimit` 屬性設定為， `double` 以限制圖形中的行聯結的斜接長度。

下列 XAML 顯示如何設定 `StrokeLineJoin` 屬性：

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

在此範例中，深藍色的聚合線條在其頂點上有四捨五入的聯結：

![行聯結](images/linejoin.png "行聯結")

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 刷](~/xamarin-forms/user-interface/brushes/index.md)
- [色彩 Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)