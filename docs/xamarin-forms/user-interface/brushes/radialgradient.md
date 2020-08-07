---
title: Xamarin.Forms筆刷：放射狀漸層
description: Xamarin.FormsRadialGradientBrush 類別會使用放射漸層繪製區域。
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dd915384c630cc08a2bd79a52dd22ccb40835dfa
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918937"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.Forms筆刷：放射狀漸層

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`RadialGradientBrush`類別衍生自 `GradientBrush` 類別，並繪製具有放射狀漸層的區域，這會將兩個或多個色彩混合在一個圓形上。 `GradientStop`物件是用來指定漸層中的色彩和其位置。 如需物件的詳細資訊 `GradientStop` ，請參閱[ Xamarin.Forms 筆刷：](gradient.md)漸層。

`RadialGradientBrush`類別會定義下列屬性：

- `Center`，屬於類型 [`Point`](xref:Xamarin.Forms.Point) ，表示星形漸層圓形的中心點。 此屬性的預設值為 (0.5，0.5) 。
- `Radius`，屬於類型 `double` ，表示放射狀漸層的圓形半徑。 這個屬性的預設值為0.5。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`RadialGradientBrush`類別也有傳回的 `IsEmpty` 方法， `bool` 表示是否已將任何物件指派給筆刷 `GradientStop` 。

> [!NOTE]
> 放射狀漸層也可以使用 `radial-gradient()` CSS 函數來建立。

## <a name="create-a-radialgradientbrush"></a>建立 RadialGradientBrush

放射狀漸層筆刷的漸層停駐會沿著圓形所定義的漸層軸來定位。 漸層軸從圓形的中心 radiates 到其圓周。 您可以使用筆刷的和屬性來變更圓形的位置和 `Center` 大小 `Radius` 。 圓形定義漸層的結束點。 因此，在1.0 的漸層停駐點會定義圓形圓周的色彩。 在0.0 的漸層停駐點定義圓形中心的色彩。

若要建立放射漸層，請建立 `RadialGradientBrush` 物件並設定其 `Center` 和 `Radius` 屬性。 然後，將兩個或多個 `GradientStop` 物件新增至 `RadialGradientBrush.GradientStops` 集合，以指定漸層中的色彩和其位置。

下列 XAML 範例顯示 `RadialGradientBrush` 設定為 `Background` 之的 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml    
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- Center defaults to (0.5,0.5)
             Radius defaults to (0.5) -->
        <RadialGradientBrush>
            <GradientStop Color="Red"
                          Offset="0.1" />
            <GradientStop Color="DarkBlue"
                          Offset="1.0" />
        </RadialGradientBrush>
    </Frame.Background>
</Frame>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會使用 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於的中心 `Frame` ：

![以中央 RadialGradientBrush 繪製的框架](radialgradient-images/center.png)

下列 XAML 範例會將放射漸層的中心移至的左上角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會使用 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於的左上角 `Frame` ：

![以左上角 RadialGradientBrush 繪製的框架](radialgradient-images/top-left.png)

下列 XAML 範例會將放射漸層的中心移至的右下角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會使用 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於的右下方 `Frame` ：

![以右下 RadialGradientBrush 繪製的框架](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms筆刷：漸層](gradient.md)
