---
title: 第4章的摘要。 滾動堆疊
description: 使用 Xamarin 建立 Mobile Apps：第4章的摘要。 滾動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032880"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第4章的摘要。 滾動堆疊

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

本章主要著重于引進*版面*配置概念，這是 Xamarin. Forms 用來組織頁面上多個視圖視覺顯示的整體詞彙。

版面配置牽涉到數個衍生自[`Layout`](xref:Xamarin.Forms.Layout)和[`Layout<T>`](xref:Xamarin.Forms.Layout`1)的類別。 本章著重于[`StackLayout`](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> 在 3.0 Xamarin 中引進的[`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md)可以用類似 `StackLayout` 但具有更大彈性的方式來使用。

本章也介紹了[`ScrollView`](xref:Xamarin.Forms.ScrollView)、 [`Frame`](xref:Xamarin.Forms.Frame)和[`BoxView`](xref:Xamarin.Forms.BoxView)類別。

## <a name="stacks-of-views"></a>視圖堆疊

[`StackLayout`](xref:Xamarin.Forms.StackLayout)衍生自 `Layout<View>`，並繼承 `IList<View>`類型的[`Children`](xref:Xamarin.Forms.Layout`1)屬性。 您可以將多個視圖專案加入此集合中，`StackLayout` 會在水準或垂直堆疊中顯示它們。

將 `StackLayout` 的[`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)屬性設定為[`StackOrientation`](xref:Xamarin.Forms.StackOrientation)列舉的成員，不論是[`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical)或[`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)。 預設值為 `Vertical`。

將 `StackLayout` 的[`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)屬性設定為 `double` 值，以指定子系之間的間距。 預設值為6。

在程式碼中，您可以將專案加入至 `for` 或 `foreach` 迴圈中 `StackLayout` 的 `Children` 集合，如[**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)範例中所示，或者您可以使用個別視圖的清單來初始化 `Children` 集合，如[**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)中所示。 子系必須衍生自 `View`，但可以包含其他 `StackLayout` 物件。

## <a name="scrolling-content"></a>滾動內容

如果 `StackLayout` 包含太多要顯示在頁面上的子系，您可以將 `StackLayout` 放在[`ScrollView`](xref:Xamarin.Forms.ScrollView)中以允許滾動。

將 `ScrollView` 的[`Content`](xref:Xamarin.Forms.ScrollView.Content)屬性設定為您要滾動的視圖。 這通常是 `StackLayout`，但它可以是任何觀點。

將 `ScrollView` 的[`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)屬性設定為[`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation)屬性、 [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)、 [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)或[`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)的成員。 預設值為 `Vertical`。 如果 `ScrollView` 的內容是 `StackLayout`，這兩個方向應該是一致的。

[**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例示範如何使用 `ScrollView` 和 `StackLayout` 來顯示可用的色彩。 此範例也會示範如何使用 .NET 反映來取得 `Color` 結構的所有公用靜態屬性和欄位，而不需要明確列出它們。

## <a name="the-expands-option"></a>展開選項

當 `StackLayout` 堆疊其子系時，每個子系都會佔用相依于 `StackLayout` 總高度的特定位置，而這是根據子系的大小和其 `HorizontalOptions` 和 `VerticalOptions` 屬性的設定而定。 這些屬性會指派[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)類型的值。

`LayoutOptions` 結構會定義兩個屬性：

- 具有四個成員、 [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)、 [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)、 [`End`](xref:Xamarin.Forms.LayoutAlignment.End)和[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)的列舉類型[`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)
- 類型為 [ 的 `Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)`bool`

為了方便起見，`LayoutOptions` 結構也會定義 `LayoutOptions` 類型的八個靜態唯讀欄位，其中包含兩個實例屬性的所有組合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

下列討論牽涉到具有預設垂直方向的 `StackLayout`。 水準 `StackLayout` 類似。

對於垂直 `StackLayout`，`HorizontalOptions` 設定會決定子系在 `StackLayout`寬度的水準位置。 `Start`、`Center`或 `End` 的 `Alignment` 設定，會使子系成為不受限制的。 子系會決定其本身的寬度，並定位在 `StackLayout`的左邊、中央或右方。 `Fill` 選項會使子系受到水準限制，並填滿 `StackLayout`的寬度。

針對垂直 `StackLayout`，每個子系會垂直地受到限制，並根據子系的高度取得垂直位置，在此情況下，`VerticalOptions` 設定無關。

如果垂直 `StackLayout` 本身是不受限制的&mdash;也就是，如果其 `VerticalOptions` 設定為 `Start`、`Center`或 `End`，則 `StackLayout` 的高度是其子系的總高度。

不過，如果垂直 `StackLayout` 會受到垂直限制&mdash;如果 `Fill`的 `VerticalOptions` 設定，則 &mdash;的高度會是其容器的高度，其可能大於其子系的總高度。`StackLayout` 如果是這種情況，而且至少有一個子系具有 `Expands` 旗標為 `true`的 `VerticalOptions` 設定，則 `StackLayout` 中的額外空間會平均配置給所有子系，且 `Expands` 旗標為 `true`。 子系的總高度會等於 `StackLayout`的高度，而 `VerticalOptions` 設定的 `Alignment` 部分則會決定子系在其位置的垂直定位方式。

這會在[**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例中示範。

## <a name="frame-and-boxview"></a>Frame 和 BoxView

這兩個矩形視圖通常用於展示用途。

[ [`Frame`](xref:Xamarin.Forms.Frame) ] 視圖會在另一個視圖周圍顯示矩形框架，這可以是像是 `StackLayout`的版面配置。 `Frame` 會從您設定的[`ContentView`](xref:Xamarin.Forms.ContentView)繼承[`Content`](xref:Xamarin.Forms.ContentView.Content)屬性，以顯示在 `Frame`中。 `Frame` 預設是透明的。 設定下列三個屬性來自訂框架的外觀：

- 要使其可見的[`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)屬性。 當您不知道基礎色彩配置時，通常會將 `OutlineColor` 設定為 `Color.Accent`。
- [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)屬性可以設定為 `true`，以在 iOS 裝置上顯示黑色陰影。
- 將 [ [`Padding`](xref:Xamarin.Forms.Layout.Padding) ] 屬性設定為 `Thickness` 值，以在框架和框架的內容之間保留一個空格。 預設值是所有邊的20個單位。

`Frame` 具有 `LayoutOptions.Fill`的預設 `HorizontalOptions` 和 `VerticalOptions` 值，這表示 `Frame` 會填入其容器。 使用其他設定，`Frame` 的大小取決於其內容的大小。

`Frame` 會在[**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例中示範。

[`BoxView`](xref:Xamarin.Forms.BoxView)會顯示其[`Color`](xref:Xamarin.Forms.BoxView.Color)屬性所指定之色彩的矩形區域。

如果 `BoxView` 受到限制（其 `HorizontalOptions` 和 `VerticalOptions` 屬性具有 `LayoutOptions.Fill`的預設設定），則 `BoxView` 會填滿它的可用空間。 如果 `BoxView` 不受限制（具有 `Start`、`Center`或 `End`的 `HorizontalOptions` 和 `LayoutOptions` 設定），則其預設維度為40單位正方形。 `BoxView` 可以在一個維度中受到限制，而不受限制。

通常，您會設定 `BoxView` 的[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性，以提供特定的大小。 [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例會說明這一點。

您可以使用數個 `StackLayout` 實例，將 `Frame` 中的 `BoxView` 和數個 `Label` 實例結合，以顯示特定的色彩，然後將這些視圖放在 `StackLayout` 的 `ScrollView` 中，以建立[**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)範例中所顯示的引人入勝色彩清單：

[![顏色塊的三向螢幕擷取畫面](images/ch04fg11-small.png "色彩清單")](images/ch04fg11-large.png#lightbox "色彩清單")

## <a name="a-scrollview-in-a-stacklayout"></a>StackLayout 中的 ScrollView？

將 `StackLayout` 放在 `ScrollView` 中是很常見的，但在 `StackLayout` 中放置 `ScrollView` 有時也很方便。 理論上，這不是可行的，因為垂直 `StackLayout` 的子系會垂直地受到限制。 但是 `ScrollView` 必須是垂直限制的。 它必須具有特定的高度，才能判斷其子系的大小以進行滾動。

秘訣是將 `StackLayout` 的 `ScrollView` 子系授與 `FillAndExpand`的 `VerticalOptions` 設定。 這會在[**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)範例中示範。

**BlackCat**範例也會示範如何定義和存取內嵌在共用程式庫中的程式資源。 這也可以透過共用資產專案（Sap）來達成，但此程式有點棘手，因為[**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)範例會示範。

## <a name="related-links"></a>相關連結

- [第4章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第4章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
