---
title: 邊界和邊框間距
description: 邊界和邊框間距屬性控制版面配置時的行為項目會呈現使用者介面中。 這篇文章會示範兩個屬性，以及如何設定它們之間的差異。
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370928"
---
# <a name="margin-and-padding"></a>邊界和邊框間距

_邊界和邊框間距屬性控制版面配置時的行為項目會呈現使用者介面中。這篇文章會示範兩個屬性，以及如何設定它們之間的差異。_

## <a name="overview"></a>總覽

邊界和邊框距離是相關的版面配置概念：

- [ `Margin` ](xref:Xamarin.Forms.View.Margin)屬性代表項目和其相鄰的項目之間的距離，而且用來控制此項目的呈現位置，並呈現位置的與其相鄰項目。 `Margin` 值可以在上指定[版面配置](~/xamarin-forms/user-interface/controls/layouts.md)並[檢視](~/xamarin-forms/user-interface/controls/views.md)類別。
- [ `Padding` ](xref:Xamarin.Forms.Layout.Padding)屬性代表項目與其子項目，之間的距離，而且用來區隔自身的內容控制項。 `Padding` 值可以在上指定[版面配置](~/xamarin-forms/user-interface/controls/layouts.md)類別。

下圖說明兩個概念：

[![](margin-and-padding-images/margins-and-padding-sml.png "邊界和邊框距離概念")](margin-and-padding-images/margins-and-padding.png#lightbox "邊界和邊框距離概念")

請注意， [ `Margin` ](xref:Xamarin.Forms.View.Margin)的值為加總。 因此，如果兩個相鄰的項目指定 20 個像素的邊界，項目之間的距離會 40 像素。 此外，邊界和邊框距離是加總時兩者都會套用，在於項目和任何內容之間的距離會邊界，加上邊框間距。

## <a name="specifying-a-thickness"></a>指定線條粗細

[ `Margin` ](xref:Xamarin.Forms.View.Margin)並[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)屬性都屬於類型[ `Thickness` ](xref:Xamarin.Forms.Thickness)。 有三種可能性建立時`Thickness`結構：

- 建立[ `Thickness` ](xref:Xamarin.Forms.Thickness)單一的統一值所定義的結構。 單一值會套用至左、 頂端、 右側和底部的側邊的項目。
- 建立[ `Thickness` ](xref:Xamarin.Forms.Thickness)水平和垂直值所定義的結構。 左邊和右邊的項目，垂直值數採對稱式套用至項目的上下兩邊對稱套用水平的值。
- 建立[ `Thickness` ](xref:Xamarin.Forms.Thickness)四個不同的值套用至左、 頂端、 右側和底部的側邊的項目所定義的結構。

下列 XAML 程式碼範例會顯示所有的三個可能值：

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
> `Thickness` 值可以是負數，，這通常會裁剪，或物件的內容。

## <a name="summary"></a>總結

這篇文章示範之間的差異[ `Margin` ](xref:Xamarin.Forms.View.Margin)並[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)屬性，以及如何設定它們。 當項目會呈現使用者介面中，屬性會控制版面配置行為。


## <a name="related-links"></a>相關連結

- [Margin](xref:Xamarin.Forms.View.Margin)
- [填補](xref:Xamarin.Forms.Layout.Padding)
- [粗細](xref:Xamarin.Forms.Thickness)
