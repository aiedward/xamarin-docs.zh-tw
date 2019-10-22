---
title: 邊界和邊框間距
description: 邊界和填補屬性會在使用者介面中轉譯元素時，控制配置行為。 本文示範兩個屬性之間的差異，以及如何設定它們。
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 66ac81631466131cf1ef44dde39aa768d31b65a1
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772481"
---
# <a name="margin-and-padding"></a>邊界和邊框間距

_邊界和填補屬性會在使用者介面中轉譯元素時，控制配置行為。本文示範兩個屬性之間的差異，以及如何設定它們。_

## <a name="overview"></a>總覽

邊界和填補是相關的版面配置概念：

- [@No__t_1](xref:Xamarin.Forms.View.Margin)屬性代表專案與其相鄰專案之間的距離，用於控制元素的轉譯位置，以及其鄰近專案的呈現位置。 可以在[版面](~/xamarin-forms/user-interface/controls/layouts.md)配置和[視圖](~/xamarin-forms/user-interface/controls/views.md)類別上指定 `Margin` 值。
- [@No__t_1](xref:Xamarin.Forms.Layout.Padding)屬性代表專案和其子專案之間的距離，用來分隔控制項與其本身的內容。 可以在[版面](~/xamarin-forms/user-interface/controls/layouts.md)配置類別上指定 `Padding` 的值。

下圖說明兩個概念：

[![](margin-and-padding-images/margins-and-padding-sml.png "Margins and Padding Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "Margins and Padding Concepts")

請注意， [`Margin`](xref:Xamarin.Forms.View.Margin)值為加法。 因此，如果兩個連續的元素指定20圖元的邊界，元素之間的距離會是40圖元。 此外，當兩個都套用時，邊界和填補都是加總的，因為專案與任何內容之間的距離會是邊界加上填補。

## <a name="specifying-a-thickness"></a>指定粗細

[@No__t_1](xref:Xamarin.Forms.View.Margin)和[`Padding`](xref:Xamarin.Forms.Layout.Padding)屬性都是[`Thickness`](xref:Xamarin.Forms.Thickness)類型。 建立 `Thickness` 結構時，有三種可能性：

- 建立由單一統一值所定義的[`Thickness`](xref:Xamarin.Forms.Thickness)結構。 單一值會套用至專案的左、上、右和右下方。
- 建立由水準和垂直值所定義的[`Thickness`](xref:Xamarin.Forms.Thickness)結構。 水準值會對稱套用到專案的左右側邊，並將垂直值對稱套用到專案的頂端和右下方。
- 建立由四個相異值所定義的[`Thickness`](xref:Xamarin.Forms.Thickness)結構，這些值會套用至專案的左、上、右和右下方。

下列 XAML 程式碼範例顯示這三種可能性：

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` 值可以是負數，通常會裁剪或 overdraws 內容。

## <a name="summary"></a>總結

本文示範了[`Margin`](xref:Xamarin.Forms.View.Margin)和[`Padding`](xref:Xamarin.Forms.Layout.Padding)屬性之間的差異，以及如何設定它們。 當專案在使用者介面中轉譯時，屬性會控制版面配置行為。

## <a name="related-links"></a>相關連結

- [左邊](xref:Xamarin.Forms.View.Margin)
- [寬度](xref:Xamarin.Forms.Layout.Padding)
- [粗細](xref:Xamarin.Forms.Thickness)
