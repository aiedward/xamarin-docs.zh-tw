---
title: Xamarin.Forms 筆刷：漸層
description: Xamarin.FormsGradientBrush 類別是抽象類別，描述漸層的漸層停駐點。
ms.prod: xamarin
ms.assetid: 24763E56-74EC-4082-897B-E4EAACCADFEE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 025832a6f51f94812e49cfb5245700b62d98ced3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374338"
---
# <a name="no-locxamarinforms-brushes-gradients"></a>Xamarin.Forms 筆刷：漸層

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`GradientBrush`類別衍生自 `Brush` 類別，而且是描述漸層的抽象類別，它是由漸層停駐點所組成。 漸層筆刷會使用多種色彩繪製區域，這些色彩會沿著某個軸逐漸相互融合。 衍生自的類別會 `GradientBrush` 描述解釋漸層停駐點的不同方式，並提供下列漸層 Xamarin.Forms 筆刷：

- `LinearGradientBrush`，用線性漸層繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：線性](lineargradient.md)漸層。
- `RadialGradientBrush`，用放射狀漸層繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：放射狀](radialgradient.md)漸層。

`GradientBrush`類別會定義型別為 `GradientStops` 的屬性 `GradientStopsCollection` ，其代表筆刷的漸層停駐點，每個物件都會指定色彩和筆刷漸層軸上的位移。 `GradientStopsCollection`是 `ObservableCollection` `GradientStop` 物件的。 `GradientStops`屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

> [!NOTE]
> `GradientStops`屬性是類別的 `ContentProperty` `GradientBrush` ，因此不需要從 XAML 明確設定。

## <a name="gradient-stops"></a>漸層停駐點

漸層停駐點是漸層筆刷的構成要素，並指定漸層中的色彩以及沿著漸層軸的位置。 使用物件指定漸層停駐點 `GradientStop` 。

`GradientStop`類別會定義下列屬性：

- `Color`，代表漸層停駐色的型別 [`Color`](xref:Xamarin.Forms.Color) 。 此屬性的預設值為 `Color.Default`。
- `Offset`，代表漸層 `float` 向量中漸層停駐點的位置。 這個屬性的預設值為0，有效值的範圍為 0.0-1.0。 這個值越接近0，色彩就越接近漸層的開始。 同樣地，這個值越接近1，色彩就越接近漸層的結尾。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!IMPORTANT]
> 漸層所使用的座標系統相對於輸出區域的周框方塊。 0 表示週框方塊的 0%，1 表示週框方塊的 100%。 因此， (0.5，0.5) 描述周框方塊中間的點， (1，1) 描述周框方塊右下角的點。

下列 XAML 範例會建立 `LinearGradientBrush` 具有四個色彩的對角線：

```xaml
<LinearGradientBrush StartPoint="0,0"
                     EndPoint="1,1">
    <GradientStop Color="Yellow"
                  Offset="0.0" />
    <GradientStop Color="Red"
                  Offset="0.25" />
    <GradientStop Color="Blue"
                  Offset="0.75" />             
    <GradientStop Color="LimeGreen"
                  Offset="1.0" />
</LinearGradientBrush>                                                       
```

漸層停駐點之間的每個點色彩，會以兩個周框漸層指定的色彩組合來插入。 下圖顯示上一個範例中的漸層停駐點：

![以對角線 LinearGradientBrush 繪製的框架](gradient-images/gradient-stops.png)

在此圖中，圓形會標示漸層停駐點的位置，而虛線會顯示漸層軸。 第一個漸層停駐點會在0.0 的位移指定黃色。 第二個漸層停駐點在位移0.25 指定紅色的紅色。 當您沿著漸層軸從左至右移動時，這兩個漸層之間的點會逐漸從黃色變更為紅色。 第三個漸層停駐點會指定位移為0.75 的藍色色彩。 第二個和第三個漸層停駐點之間的點會逐漸從紅色變成藍色。 第四個漸層停駐點會在1.0 的位移處指定偏淺綠色。 第三個和第四個漸層停駐點之間的點會逐漸從藍色變成淡黃綠色。

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms 筆刷：線性漸層](lineargradient.md)
- [Xamarin.Forms 筆刷：放射狀漸層](radialgradient.md)