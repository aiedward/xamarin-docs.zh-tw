---
title: Xamarin.Forms 筆刷：線性漸層
description: Xamarin.FormsLinearGradientBrush 類別會使用線性漸層繪製區域。
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44eb40a63c5b1117ba6eb58bde3a3d6a4a49154b
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940053"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.Forms 筆刷：線性漸層

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`LinearGradientBrush`類別衍生自 `GradientBrush` 類別，並繪製具有線性漸層的區域，這會將兩個或多個色彩沿著稱為漸層軸的線混合。 `GradientStop` 物件是用來指定漸層和其位置中的色彩。 如需物件的詳細資訊 `GradientStop` ，請參閱[ Xamarin.Forms 筆刷：](gradient.md)漸層。

`LinearGradientBrush`類別會定義下列屬性：

- `StartPoint`型別 [`Point`](xref:Xamarin.Forms.Point) ，表示線性漸層的起始二維座標。 這個屬性的預設值是 (0，0) 。
- `EndPoint`，類型為 [`Point`](xref:Xamarin.Forms.Point) ，表示線性漸層的結束二維座標。 這個屬性的預設值是 (1，1) 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

`LinearGradientBrush`類別也 `IsEmpty` 會傳回，以 `bool` 表示是否已將任何物件指派給筆刷 `GradientStop` 。

> [!NOTE]
> 您也可以使用 CSS 函數來建立線性漸層 `linear-gradient()` 。

## <a name="create-a-lineargradientbrush"></a>建立 LinearGradientBrush

線性漸層筆刷的漸層停駐點沿著漸層軸放置。 您可以使用筆刷的和屬性來變更漸層軸的方向和大小 `StartPoint` `EndPoint` 。 藉由操作這些屬性，您就可以建立水準、垂直和對角線漸層、反轉漸層方向、緊縮漸層漸層等。

`StartPoint`和 `EndPoint` 屬性會相對於正在繪製的區域。  (0、0) 代表正在繪製之區域的左上角，而 (1，1) 代表正在繪製之區域的右下角。 下圖顯示對角線性漸層筆刷的漸層軸：

![具有對角漸層軸的框架](lineargradient-images/gradient-axis.png)

在此圖中，虛線會顯示漸層軸，以反白顯示從起點到終點的漸層的插補路徑。

### <a name="create-a-horizontal-linear-gradient"></a>建立水平線性漸層

若要建立水平線性漸層，請建立 `LinearGradientBrush` 物件，並將其設定 `StartPoint` 為 (0、0) 和 `EndPoint` (1，0) 。 然後，將兩個或多個 `GradientStop` 物件加入至 `LinearGradientBrush.GradientStops` 集合，以指定漸層和其位置中的色彩。

下列 XAML 範例顯示已 `LinearGradientBrush` 設定為之的水準 `Background` [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->
        <LinearGradientBrush EndPoint="1,0">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>  
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會使用 `LinearGradientBrush` ，以水準方式從黃色插到綠色來繪製：

![使用水準 LinearGradientBrush 繪製的框架](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>建立垂直線性漸層

若要建立垂直線性漸層，請建立 `LinearGradientBrush` 物件，並將其設定 `StartPoint` 為 (0、0) 和 `EndPoint` (0、1) 。 然後，將兩個或多個 `GradientStop` 物件加入至 `LinearGradientBrush.GradientStops` 集合，以指定漸層和其位置中的色彩。

下列 XAML 範例顯示已 `LinearGradientBrush` 設定為之的垂直 `Background` [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->    
        <LinearGradientBrush EndPoint="0,1">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `LinearGradientBrush` 垂直從黃色插到綠色的來繪製：

![以垂直 LinearGradientBrush 繪製的框架](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>建立對角線的線性漸層

若要建立對角線性漸層，請建立 `LinearGradientBrush` 物件，並將其設定 `StartPoint` 為 (0、0) 和 `EndPoint` (1，1) 。 然後，將兩個或多個 `GradientStop` 物件加入至 `LinearGradientBrush.GradientStops` 集合，以指定漸層和其位置中的色彩。

下列 XAML 範例顯示 `LinearGradientBrush` 設定為之的對角線 `Background` [`Frame`](xref:Xamarin.Forms.Frame) ：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0)      
             Endpoint defaults to (1,1) -->
        <LinearGradientBrush>
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `LinearGradientBrush` 從黃色到綠色的插補來繪製：

![以對角線 LinearGradientBrush 繪製的框架](lineargradient-images/diagonal.png)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 筆刷：漸層](gradient.md)
