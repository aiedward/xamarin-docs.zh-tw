---
title: Xamarin.Forms筆刷：線性漸層
description: Xamarin.FormsLinearGradientBrush 類別會使用線性漸層繪製區域。
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 734ecae4fdafd71f0c88ddc5e4b4ed0c672f2019
ms.sourcegitcommit: 579ec4f2884fa391e5e214a3952cd6004c521eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919628"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.Forms筆刷：線性漸層

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)

`LinearGradientBrush`類別衍生自 `GradientBrush` 類別，並繪製具有線性漸層的區域，這會將兩個或多個色彩沿著一條線（稱為「漸層軸」）來混合。 `GradientStop`物件是用來指定漸層中的色彩和其位置。 如需物件的詳細資訊 `GradientStop` ，請參閱[ Xamarin.Forms 筆刷：](gradient.md)漸層。

`LinearGradientBrush`類別會定義下列屬性：

- `StartPoint`，屬於類型 [`Point`](xref:Xamarin.Forms.Point) ，表示線性漸層的起始二維座標。 這個屬性的預設值為 (0，0) 。
- `EndPoint`，屬於類型 [`Point`](xref:Xamarin.Forms.Point) ，表示線性漸層的結束二維座標。 這個屬性的預設值為 (1，1) 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`LinearGradientBrush`類別也是傳回的 `IsEmpty` 方法， `bool` 表示是否已為任何物件指派筆刷 `GradientStop` 。

> [!NOTE]
> 您也可以使用 CSS 函數來建立線性漸層 `linear-gradient()` 。

## <a name="create-a-lineargradientbrush"></a>建立 LinearGradientBrush

線性漸層筆刷的漸層停駐點沿著漸層軸的位置。 您可以使用筆刷的和屬性來變更漸層軸的方向和大小 `StartPoint` `EndPoint` 。 藉由操作這些屬性，您可以建立水準、垂直和對角線漸層、反轉漸層方向、緊縮漸層散佈等等。

`StartPoint`和 `EndPoint` 屬性相對於所繪製的區域。  (0，0) 代表正在繪製之區域的左上角，而 (1，1) 代表所繪製之區域的右下角。 下圖顯示對角線性漸層筆刷的漸層軸：

![具有對角線漸層軸的框架](lineargradient-images/gradient-axis.png)

在此圖中，虛線會顯示漸層軸，這會反白顯示從起點到結束點的漸層插補路徑。

### <a name="create-a-horizontal-linear-gradient"></a>建立水平線性漸層

若要建立水平線性漸層，請建立 `LinearGradientBrush` 物件，並將其設 `StartPoint` 為 (0，0) ，並將其設定 `EndPoint` 為 (1，0) 。 然後，將兩個或多個 `GradientStop` 物件新增至 `LinearGradientBrush.GradientStops` 集合，以指定漸層中的色彩和其位置。

下列 XAML 範例顯示 `LinearGradientBrush` 設定為之的水準 `Background` [`Frame`](xref:Xamarin.Forms.Frame) ：

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

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 是以 `LinearGradientBrush` 水準方式從黃色插補綠色的來繪製：

![以水準 LinearGradientBrush 繪製的框架](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>建立垂直線性漸層

若要建立垂直線性漸層，請建立 `LinearGradientBrush` 物件，並將其設 `StartPoint` 為 (0，0) ，並將其設定 `EndPoint` 為 (0，1) 。 然後，將兩個或多個 `GradientStop` 物件新增至 `LinearGradientBrush.GradientStops` 集合，以指定漸層中的色彩和其位置。

下列 XAML 範例會顯示已 `LinearGradientBrush` 設定為 `Background` 之的垂直 [`Frame`](xref:Xamarin.Forms.Frame) ：

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

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以 `LinearGradientBrush` 垂直方式從黃色插到綠色的來繪製：

![以垂直 LinearGradientBrush 繪製的框架](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>建立對角線的線性漸層

若要建立對角線性漸層，請建立 `LinearGradientBrush` 物件，並將其設 `StartPoint` 為 (0，0) ，並將其設定 `EndPoint` 為 (1，1) 。 然後，將兩個或多個 `GradientStop` 物件新增至 `LinearGradientBrush.GradientStops` 集合，以指定漸層中的色彩和其位置。

下列 XAML 範例顯示 `LinearGradientBrush` 設定為 `Background` 之的對角線 [`Frame`](xref:Xamarin.Forms.Frame) ：

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

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會繪製，其會以 `LinearGradientBrush` 對角線方式從黃色插補綠色：

![以對角線 LinearGradientBrush 繪製的框架](lineargradient-images/diagonal.png)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)
- [Xamarin.Forms筆刷：漸層](gradient.md)
