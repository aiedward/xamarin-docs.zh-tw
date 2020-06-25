---
title: Xamarin.Forms圖形：矩形
description: Xamarin.Forms矩形類別可以用來繪製矩形。
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1fd985aa2997be2b35fe3b22606b891aa0b66cf3
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243733"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.Forms圖形：矩形

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Rectangle`類別衍生自 `Shape` 類別，而且可以用來繪製矩形和正方形。 如需 `Rectangle` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Rectangle` 會定義下列屬性：

- `RadiusX`，屬於類型 `double` ，這是用來四捨五入矩形角落的 X 軸半徑。 這個屬性的預設值為0.0。
- `RadiusY`，屬於類型 `double` ，這是用來四捨五入矩形角落的 y 軸半徑。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`Rectangle`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。 如需屬性的詳細資訊 `Aspect` ，請參閱[延展圖形](index.md#stretch-shapes)。

## <a name="create-a-rectangle"></a>建立矩形

若要繪製矩形，請建立 `Rectangle` 物件，並設定其 `WidthRequest` 和 `HeightRequest` 屬性。 若要在矩形內繪製，請將其 `Fill` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 若要為矩形提供外框輪廓，請將其 `Stroke` 屬性設為 [`Color`](xref:Xamarin.Forms.Color) 。 `StrokeThickness`屬性會指定矩形外框的粗細。

若要提供矩形圓角，請設定其 `RadiusX` 和 `RadiusY` 屬性。 這些屬性會設定用來四捨五入矩形角落的 X 軸和 y 軸半徑。

若要繪製正方形，請將 `WidthRequest` 物件的和屬性設為 `HeightRequest` `Rectangle` 相等。

下列 XAML 範例顯示如何繪製填滿的矩形：

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

在此範例中，會繪製具有維度150x50 （裝置獨立單位）的紅色填滿矩形：

![填滿的矩形](rectangle-images/filled.png "填滿的矩形")

下列 XAML 範例示範如何繪製實心矩形，並以圓角表示：

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

如需繪製虛線矩形的詳細資訊，請參閱[繪製虛線形狀](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
