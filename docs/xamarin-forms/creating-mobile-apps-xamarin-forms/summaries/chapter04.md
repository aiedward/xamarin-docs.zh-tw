---
title: 第 4 章的摘要。 捲動堆疊
description: 使用 Xamarin.Forms 建立行動應用程式： 摘要的第 4 章。 捲動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3571774ddec4182f35cac6f13d4582235e2ff31a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997422"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第 4 章的摘要。 捲動堆疊

本章主要用來介紹的概念*版面配置*，這是整體一詞的類別和 Xamarin.Forms 使用來組織頁面上的多個檢視的視覺顯示的技術。

版面配置包含數個類別衍生自[ `Layout` ](xref:Xamarin.Forms.Layout)並[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)。 本章著重[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。

也引進了在這一章所[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)， [ `Frame` ](xref:Xamarin.Forms.Frame)，以及[ `BoxView` ](xref:Xamarin.Forms.BoxView)類別。

## <a name="stacks-of-views"></a>檢視的堆疊

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 衍生自`Layout<View>`會繼承[ `Children` ](xref:Xamarin.Forms.Layout`1)型別的屬性`IList<View>`。 您將多個檢視項目新增至這個集合中，和`StackLayout`水平或垂直堆疊中顯示它們。

設定[ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)屬性`StackLayout`成員[ `StackOrientation` ](xref:Xamarin.Forms.StackOrientation)列舉型別，任一[ `Vertical` ](xref:Xamarin.Forms.StackOrientation.Vertical)或[`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). 預設值為 `Vertical`。

設定[ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing)屬性`StackLayout`到`double`值，以指定的子系之間的間距。 預設值為 6。

程式碼中，您可以將項目加入`Children`的集合`StackLayout`中`for`或是`foreach`迴圈中所示[ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)範例中，或者您可以初始化`Children`如所示的個別檢視的清單集合[ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)。 子系必須衍生自`View`但可以包含其他`StackLayout`物件。

## <a name="scrolling-content"></a>捲動內容

如果`StackLayout`包含太多的子系，以顯示在頁面上，您可以將放`StackLayout`中[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)允許捲動。

設定[ `Content` ](xref:Xamarin.Forms.ScrollView.Content)屬性`ScrollView`至您想要捲動的檢視。 這通常是`StackLayout`，但它可以是任何檢視。

設定[ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation)屬性`ScrollView`成員[ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation)屬性[ `Vertical` ](xref:Xamarin.Forms.ScrollOrientation.Vertical)， [ `Horizontal` ](xref:Xamarin.Forms.ScrollOrientation.Horizontal)，或[ `Both` ](xref:Xamarin.Forms.ScrollOrientation.Both)。 預設值為 `Vertical`。 如果內容`ScrollView`是`StackLayout`，這兩個方向應該一致。

[ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例示範如何使用`ScrollView`和`StackLayout`來顯示可用的色彩。 此範例也示範如何使用.NET 反映來取得所有的公用靜態屬性和欄位`Color`結構，而不需要明確地列出它們。

## <a name="the-expands-option"></a>展開選項

當`StackLayout`堆疊及其子系，每個子項目所佔用的特定位置中的總高度`StackLayout`相依於子系的大小，以及設定其`HorizontalOptions`和`VerticalOptions`屬性。 這些屬性會指派值的型別[ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/)。

`LayoutOptions`結構會定義兩個屬性：

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) 列舉型別的[ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment)四個成員，與[ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start)， [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center)， [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End)，並 [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 型別 `bool`

為了方便起見，`LayoutOptions`結構也會定義八個靜態唯讀欄位的型別`LayoutOptions`，其中包含兩個執行個體屬性的所有組合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

下面的討論包含`StackLayout`具有預設的垂直方向。 水平`StackLayout`類似。

在垂直`StackLayout`，則`HorizontalOptions`設定會決定如何將子系水平放置的寬度`StackLayout`。 `Alignment`設定`Start`， `Center`，或`End`會導致要以水平方式不受限制的子系。 子系會決定它自己的寬度，並位於 left、 center 或右邊的`StackLayout`。 `Fill`選項會導致要以水平方式受到條件約束的子系，並填滿的寬度`StackLayout`。

在垂直`StackLayout`，每個子項目是以垂直方式不受限制取得垂直位置根據子系的高度，在此情況下`VerticalOptions`設定無關。

如果垂直`StackLayout`本身是無限制&mdash;也就是如果其`VerticalOptions`設定為`Start`， `Center`，或`End`，然後的高度`StackLayout`是其子系的總高度。

不過，如果垂直`StackLayout`垂直限制&mdash;如果其`VerticalOptions`設定為`Fill`&mdash;然後的高度`StackLayout`會其容器，可能會大於總高度及其子系的高度。 如果這種情況，以及至少一個子系是否`VerticalOptions`設定`Expands`旗標`true`，再額外的空間`StackLayout`具有所有這些子系之間平均配置`Expands`旗標`true`。 子系的總高度會接著等於高度`StackLayout`，而`Alignment`屬於`VerticalOptions`設定會決定如何將子系以垂直方式放置在其位置。

這示範於[ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例。

## <a name="frame-and-boxview"></a>框架和 BoxView

這兩個矩形檢視通常用於顯示用途。

[ `Frame` ](xref:Xamarin.Forms.Frame) 檢視會顯示另一個檢視，例如可以是版面配置周圍的矩形框架`StackLayout`。 `Frame` 繼承[ `Content` ](xref:Xamarin.Forms.ContentView.Content)屬性從[ `ContentView` ](xref:Xamarin.Forms.ContentView) ，來檢視內顯示`Frame`。 `Frame`預設是透明的。 設定下列三個屬性，並自訂畫面格的外觀：

- [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor)屬性，以讓它立即可見。 通常會設定`OutlineColor`至`Color.Accent`時您不知道基礎的色彩配置。
- [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)屬性可以設定為`true`iOS 裝置上顯示一個黑色的陰影。
- 設定[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)屬性設`Thickness`来留在框架和框架之間值的內容。 預設值是所有側邊的 20 個單位。

`Frame`有預設值`HorizontalOptions`並`VerticalOptions`的值`LayoutOptions.Fill`，這表示`Frame`會填滿其容器。 其他設定，大小`Frame`根據其內容的大小。

`Frame`所示[ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例。

[ `BoxView` ](xref:Xamarin.Forms.BoxView)會顯示所指定的色彩的矩形區域及其[ `Color` ](xref:Xamarin.Forms.BoxView.Color)屬性。

如果`BoxView`受限於 (其`HorizontalOptions`並`VerticalOptions`屬性有預設值的`LayoutOptions.Fill`)、`BoxView`它填滿可用的空間。 如果`BoxView`是未受限制 (與`HorizontalOptions`並`LayoutOptions`設定`Start`， `Center`，或`End`)，它有 40 單位正方形的預設值的維度。 A`BoxView`可以限制在一個維度中，並在其他未受限制。

通常，您會將[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性`BoxView`並提供特定的大小。 說明這種情形[ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例。

您可以使用數個執行個體`StackLayout`結合`BoxView`和數個`Label`中的執行個體`Frame`若要顯示特定的色彩，並將每個檢視中`StackLayout`在`ScrollView`建立吸引人清單中顯示的色彩[ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)範例：

[![三重的螢幕擷取畫面的色彩區塊](images/ch04fg11-small.png "清單中的色彩")](images/ch04fg11-large.png#lightbox "清單的色彩")

## <a name="a-scrollview-in-a-stacklayout"></a>在 StackLayout ScrollView 嗎？

將放`StackLayout`中`ScrollView`很常見但加`ScrollView`在`StackLayout`也是有時候非常方便。 不應該在理論上來說，這是可能因為是垂直的子系`StackLayout`會以垂直方式不受限制。 但`ScrollView`必須以垂直方式限制。 必須為它提供特定的高度，以便它再捲動決定其子系的大小。

訣竅是給予`ScrollView`的子系`StackLayout``VerticalOptions`設定`FillAndExpand`。 這示範於[ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)範例。

**BlackCat**範例也會示範如何定義及存取內嵌可攜式類別庫 (PCL) 程式資源。 這也可透過共用資產專案 (SAPs)，但程序比較困難，作為[ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)範例會示範。



## <a name="related-links"></a>相關連結

- [第 4 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第 4 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
