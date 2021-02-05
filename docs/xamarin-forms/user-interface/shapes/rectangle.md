---
title: Xamarin.Forms 圖形：矩形
description: Xamarin.Forms矩形類別可以用來繪製矩形。
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9080f509f9327f18f0cd66b0a497bdf03ac4975a
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585854"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.Forms 圖形：矩形

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Rectangle`類別衍生自 `Shape` 類別，而且可以用來繪製矩形和方塊。 如需 `Rectangle` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Rectangle` 會定義下列屬性：

- `RadiusX`，屬於類型 `double` ，這是用來將矩形圓角四捨五入的 X 軸半徑。 這個屬性的預設值是0.0。
- `RadiusY`，屬於類型 `double` ，這是用來將矩形圓角四捨五入的 y 軸半徑。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

`Rectangle`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。 如需屬性的詳細資訊 `Aspect` ，請參閱 [Stretch shapes](index.md#stretch-shapes)。

## <a name="create-a-rectangle"></a>建立矩形

若要繪製矩形，請建立 `Rectangle` 物件並設定其 `WidthRequest` 和 `HeightRequest` 屬性。 若要繪製矩形內的，請將其 `Fill` 屬性設定為 [`Brush`](xref:Xamarin.Forms.Brush) 衍生的物件。 若要為矩形提供外框，請將其 `Stroke` 屬性設定為 [`Brush`](xref:Xamarin.Forms.Brush) 衍生的物件。 `StrokeThickness`屬性指定矩形大綱的粗細。 如需物件的詳細資訊 `Brush` ，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。

若要為矩形圓角，請設定其 `RadiusX` 和 `RadiusY` 屬性。 這些屬性會設定用來將矩形圓角四捨五入的 X 軸和 y 軸半徑。

若要繪製正方形，請讓 `WidthRequest` 物件的和 `HeightRequest` 屬性 `Rectangle` 相等。

下列 XAML 範例顯示如何繪製實心矩形：

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

在此範例中，會繪製維度 150x50 (裝置獨立單位) 的紅色填滿矩形：

![填滿矩形](rectangle-images/filled.png "填滿矩形")

下列 XAML 範例顯示如何使用圓角繪製實心矩形：

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

在此範例中，會繪製具有圓角的藍色填滿矩形：

![具有圓角的矩形](rectangle-images/rounded.png "具有圓角的矩形")

如需繪製虛線矩形的詳細資訊，請參閱 [繪製虛線圖形](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 刷](~/xamarin-forms/user-interface/brushes/index.md)
