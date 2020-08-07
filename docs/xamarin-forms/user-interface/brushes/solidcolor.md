---
title: Xamarin.Forms筆刷：純色
description: Xamarin.FormsSolidColorBrush 類別會使用純色繪製區域。
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04e882f9b64d0e1f56e1170d353af4e13b079933
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918915"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms筆刷：純色

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`SolidColorBrush`類別衍生自 `Brush` 類別，並且用來繪製具有純色的區域。 有各種方法可指定的色彩 `SolidColorBrush` 。 例如，您可以 [`Color`](xref:Xamarin.Forms.Color) 使用值或由類別提供的其中一個預先定義物件來指定其色彩 `SolidColorBrush` `Brush` 。

`SolidColorBrush`類別會定義 `Color` 類型的屬性 [`Color`](xref:Xamarin.Forms.Color) ，其代表筆刷的色彩。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，以及樣式化的。

`SolidColorBrush`類別也有傳回的 `IsEmpty` 方法， `bool` 表示筆刷是否已指派色彩。

## <a name="create-a-solidcolorbrush"></a>建立 SolidColorBrush

建立的主要技巧有三種 `SolidColorBrush` 。 您可以從建立 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) 、使用預先定義的筆刷，或 `SolidColorBrush` 使用十六進位標記法建立。

### <a name="use-a-predefined-color"></a>使用預先定義的色彩

Xamarin.Forms包含從值建立的類型轉換器 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) 。 在 XAML 中，這可讓 `SolidColorBrush` 從預先定義的 `Color` 值建立：

```xaml
<Frame Background="DarkBlue"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以深藍色繪製 `SolidColorBrush` ：

![以預先定義的色彩繪製的框架](solidcolor-images/predefined-color.png)

或者，您也 [`Color`](xref:Xamarin.Forms.Color) 可以使用屬性標記語法來指定此值：

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
       <Frame.Background>
           <SolidColorBrush Color="DarkBlue" />
       </Frame.Background>
</Frame>
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會使用繪製， `SolidColorBrush` 其色彩是藉由設定屬性來指定 `SolidColorBrush.Color` 。

### <a name="use-a-predefined-brush"></a>使用預先定義的筆刷

`Brush`類別會定義一組常用的 `SolidColorBrush` 物件。 下列範例會使用其中一個預先定義的 `SolidColorBrush` 物件：

```xaml
<Frame Background="{x:Static Brush.Indigo}"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />       
```

對等的 C# 程式碼為：

```csharp
Frame frame = new Frame
{
    Background = Brush.Indigo,
    BorderColor = Color.LightGray,
    // ...
};
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以靛藍色繪製 `SolidColorBrush` ：

![以預先定義的 SolidColorBrush 繪製的框架](solidcolor-images/predefined-brush.png)

如需 `SolidColorBrush` 類別所提供的預先定義物件清單 `Brush` ，請參閱[純色筆刷](#solid-color-brushes)。

### <a name="use-hexadecimal-notation"></a>使用十六進位標記法

`SolidColorBrush`您也可以使用十六進位標記法來建立物件。 使用此方法時，會以紅色、綠色和藍色的數量來指定色彩，以結合成單一色彩。 使用十六進位標記法來指定色彩的主要格式為 `#rrggbb` ，其中：

- `rr`這是指定紅色相對量的兩位數十六進位數位。
- `gg`這是指定綠色相對量的兩位數十六進位數位。
- `bb`這是指定藍色相對量的兩位數十六進位數位。

此外，您可以將色彩指定為， `#aarrggbb` 其中 `aa` 指定色彩的 Alpha 值或透明度。 這個方法可以讓您建立部分透明的色彩。

下列範例會 `SolidColorBrush` 使用十六進位標記法來設定的色彩值：

```xaml
<Frame Background="#FF9988"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以橙紅色彩繪製 `SolidColorBrush` ：

![使用十六進位標記法所建立的 SolidColorBrush 所繪製的框架](solidcolor-images/hex.png)

如需描述色彩的其他方式，請參閱[中 Xamarin.Forms 的色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="solid-color-brushes"></a>純色筆刷

為了方便起見， `Brush` 類別會提供一組常用的 `SolidColorBrush` 物件，例如 `AliceBlue` 和 `YellowGreen` 。 下圖顯示每個預先定義之筆刷的色彩、其名稱和十六進位值：

[![色彩表，包括色樣、色彩名稱和十六進位值](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [色彩于Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)
