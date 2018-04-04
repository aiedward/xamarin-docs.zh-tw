---
title: 邊界和邊框距離
description: 邊界和邊框間距屬性控制版面配置時的行為項目會呈現使用者介面中。 這篇文章會示範兩個屬性，以及如何設定它們之間的差異。
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 545468d3b02f9651c45fcaebe159351aafea6432
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="margin-and-padding"></a>邊界和邊框距離

_邊界和邊框間距屬性控制版面配置時的行為項目會呈現使用者介面中。這篇文章會示範兩個屬性，以及如何設定它們之間的差異。_

## <a name="overview"></a>總覽

邊界和邊框距離是相關的版面配置的概念：

- [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)屬性代表項目和其相鄰的項目之間的距離，而且用來控制的項目呈現位置，並呈現位置的相鄰項目。 `Margin` 您可以在指定值[配置](~/xamarin-forms/user-interface/controls/layouts.md)和[檢視](~/xamarin-forms/user-interface/controls/views.md)類別。
- [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性代表其子項目，項目之間的距離，而且用來分隔控制項從它自己的內容。 `Padding` 您可以在指定值[配置](~/xamarin-forms/user-interface/controls/layouts.md)類別。

下圖說明兩個概念：

[![](margin-and-padding-images/margins-and-padding-sml.png "邊界和邊框距離概念")](margin-and-padding-images/margins-and-padding.png#lightbox "邊界和邊框距離概念")

請注意， [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)的值為加總。 因此，如果兩個相鄰的項目指定 20 像素的邊界，項目之間的距離會 40 像素為單位。 此外，邊界和邊框距離會同時套用時，邊界和邊框距離，將會是項目及任何內容之間的距離，加法。

## <a name="specifying-a-thickness"></a>指定線條粗細

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性都屬於型別[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)。 有三種可能性建立時`Thickness`結構：

- 建立[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)的單一統一值所定義的結構。 單一值會套用至左、 頂端、 右側和底部的側邊的項目。
- 建立[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)水平和垂直值所定義的結構。 對稱水平的值會套用到左邊和右邊的項目，以對稱套用至項目的上方和下方側邊的垂直值。
- 建立[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)四個相異值套用至左、 頂端、 右側和底部的側邊的項目所定義的結構。

下列 XAML 程式碼範例會顯示所有的三種可能性：

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
> `Thickness` 值可以是負數，，這通常會裁剪，或物件內容。

## <a name="summary"></a>總結

本文示範之間的差異[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性，以及如何設定這些屬性。 在使用者介面中呈現的項目時，屬性會控制配置行為。


## <a name="related-links"></a>相關連結

- [Margin](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)
- [填補](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)
- [粗細](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)
