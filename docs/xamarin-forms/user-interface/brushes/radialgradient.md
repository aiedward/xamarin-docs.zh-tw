---
title: Xamarin.Forms 筆刷：放射狀漸層
description: Xamarin.FormsRadialGradientBrush 類別會繪製具有放射狀漸層的區域。
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 10645e12b81b1ca4ee8e0790ed3a673a23a4ba26
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366928"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.Forms 筆刷：放射狀漸層

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`RadialGradientBrush`類別衍生自 `GradientBrush` 類別，並繪製具有放射狀漸層的區域，這會將兩個或多個色彩混合在圓形上。 `GradientStop` 物件是用來指定漸層和其位置中的色彩。 如需物件的詳細資訊 `GradientStop` ，請參閱[ Xamarin.Forms 筆刷：](gradient.md)漸層。

`RadialGradientBrush`類別會定義下列屬性：

- `Center`， [`Point`](xref:Xamarin.Forms.Point) 代表放射狀漸層圓形中心點的型別。 這個屬性的預設值是 (0.5，0.5) 。
- `Radius`， `double` 代表放射狀漸層圓形半徑的型別。 這個屬性的預設值是0.5。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

`RadialGradientBrush`類別也有一個 `IsEmpty` 方法，它會傳回 `bool` ，表示是否已將任何物件指派給筆刷 `GradientStop` 。

> [!NOTE]
> 您也可以使用 CSS 函式來建立星形漸層 `radial-gradient()` 。

## <a name="create-a-radialgradientbrush"></a>建立 RadialGradientBrush

放射狀漸層筆刷的漸層停駐點會沿著圓形所定義的漸層軸放置。 漸層軸從圓形中央 radiates 至其圓周。 您可以使用筆刷的和屬性來變更圓形的位置和 `Center` 大小 `Radius` 。 圓形會定義漸層的結束點。 因此，1.0 的漸層停駐點定義了圓形圓周的色彩。 0.0 的漸層停駐點定義了圓形中心的色彩。

若要建立放射狀漸層，請建立 `RadialGradientBrush` 物件並設定其 `Center` 和 `Radius` 屬性。 然後，將兩個或多個 `GradientStop` 物件加入至 `RadialGradientBrush.GradientStops` 集合，以指定漸層和其位置中的色彩。

下列 XAML 範例顯示將 `RadialGradientBrush` 設定為 `Background` 之的 [`Frame`](xref:Xamarin.Forms.Frame) ：

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

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於的中央 `Frame` ：

![以中央 RadialGradientBrush 繪製的框架](radialgradient-images/center.png)

下列 XAML 範例會將星形漸層的中心移至左上角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於下列位置的左上角 `Frame` ：

![使用左上角繪製的框架 RadialGradientBrush](radialgradient-images/top-left.png)

下列 XAML 範例會將放射狀漸層的中心移至右下角 [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `RadialGradientBrush` 從紅色插到深藍色的來繪製。 放射狀漸層的中心位於下列位置的右下角 `Frame` ：

![使用右下 RadialGradientBrush 繪製的框架](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 筆刷：漸層](gradient.md)