---
title: Xamarin.Forms 筆刷：純色
description: Xamarin.FormsSolidColorBrush 類別會以純色繪製區域。
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3c3caf064ca550086f8e7924786ac8bcaf1badfc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556278"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms 筆刷：純色

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

`SolidColorBrush`類別衍生自 `Brush` 類別，用來以純色繪製區域。 有多種方法可以指定的色彩 `SolidColorBrush` 。 例如，您可以 [`Color`](xref:Xamarin.Forms.Color) 使用值或類別所提供的其中一個預先定義物件來指定其色彩 `SolidColorBrush` `Brush` 。

`SolidColorBrush`類別會定義型別為 `Color` 的屬性， [`Color`](xref:Xamarin.Forms.Color) 其代表筆刷的色彩。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

`SolidColorBrush`類別也有一個 `IsEmpty` 方法，它會傳回 `bool` ，表示筆刷是否已指派色彩。

## <a name="create-a-solidcolorbrush"></a>建立 SolidColorBrush

有三個主要的技巧可用於建立 `SolidColorBrush` 。 您可以從建立 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) ，使用預先定義的筆刷，或建立 `SolidColorBrush` 使用的十六進位標記法。

### <a name="use-a-predefined-color"></a>使用預先定義的色彩

Xamarin.Forms 包含從值建立的型別轉換子 `SolidColorBrush` [`Color`](xref:Xamarin.Forms.Color) 。 在 XAML 中，這可讓您 `SolidColorBrush` 從預先定義的 `Color` 值建立：

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

或者，您 [`Color`](xref:Xamarin.Forms.Color) 可以使用屬性標記語法來指定此值：

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

在此範例中，的背景 [`Frame`](xref:Xamarin.Forms.Frame) 會以藉 `SolidColorBrush` 由設定屬性來指定其色彩的來繪製 `SolidColorBrush.Color` 。

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

如需 `SolidColorBrush` 類別所提供的預先定義物件清單 `Brush` ，請參閱 [純色筆刷](#solid-color-brushes)。

### <a name="use-hexadecimal-notation"></a>使用十六進位標記法

`SolidColorBrush` 您也可以使用十六進位標記法來建立物件。 使用這個方法時，會以紅色、綠色和藍色的數量來指定色彩，以合併成單一色彩。 使用十六進位標記法來指定色彩的主要格式為 `#rrggbb` ，其中：

- `rr` 這是兩位數的十六進位數位，用來指定紅色的相對量。
- `gg` 這是兩位數的十六進位數位，可指定綠色的相對量。
- `bb` 這是兩位數的十六進位數位，可指定藍色的相對量。

此外，您可以指定色彩的指定方式，例如 `#aarrggbb` `aa` 指定色彩的 Alpha 值（或透明）。 這個方法可以讓您建立部分透明的色彩。

下列範例會 `SolidColorBrush` 使用十六進位標記法設定的色彩值：

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

為了方便起見， `Brush` 類別提供一組常用的 `SolidColorBrush` 物件，例如 `AliceBlue` 和 `YellowGreen` 。 下圖顯示每個預先定義之筆刷的色彩、其名稱和其十六進位值：

[![色彩表，包括色樣、色彩名稱和十六進位值](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>相關連結

- [BrushesDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [色彩 Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)