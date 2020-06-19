---
title: Xamarin.Forms圖形：矩形
description: Xamarin.Forms矩形類別可以用來繪製矩形。
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da9649a4abb2cb65930d98576eda81739b711886
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101364"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.Forms圖形：矩形

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Rectangle`類別衍生自 `Shape` 類別，而且可以用來繪製矩形。 如需 `Rectangle` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Rectangle` 會定義下列屬性：

- `RadiusX`，屬於類型 `double` ，這是用來四捨五入矩形角落的 X 軸半徑。 這個屬性的預設值為0.0。
- `RadiusY`，屬於類型 `double` ，這是用來四捨五入矩形角落的 y 軸半徑。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`Rectangle`類別會將 `Aspect` 繼承自類別的屬性設定 `Shape` 為 `Stretch.Fill` 。

## <a name="create-a-rectangle"></a>建立矩形

下列 XAML 範例示範如何繪製實心矩形，並以圓角表示：

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           RadiusX="12"
           RadiusY="24"           
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
