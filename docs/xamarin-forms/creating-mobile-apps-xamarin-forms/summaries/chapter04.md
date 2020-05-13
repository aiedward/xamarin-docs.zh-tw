---
title: 第4章的摘要。 捲動堆疊
description: 使用 Xamarin 建立 Mobile Apps：第4章的摘要。 捲動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5313dd34839d6a5d21432161b9fd3a0ffce6e816
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83149945"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第4章的摘要。 捲動堆疊

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

本章主要著重于引進*版面*配置概念，這是 Xamarin. Forms 用來組織頁面上多個視圖視覺顯示的整體詞彙。

版面配置牽涉到數個衍生自和的類別 [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 。 本章著重于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> 在 [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) 3.0 Xamarin 中引進的會以類似 `StackLayout` 但具有更大彈性的方式來使用。

本章也引進了 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 、 [`Frame`](xref:Xamarin.Forms.Frame) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 類別。

## <a name="stacks-of-views"></a>視圖堆疊

[`StackLayout`](xref:Xamarin.Forms.StackLayout)衍生自 `Layout<View>` ，並繼承 [`Children`](xref:Xamarin.Forms.Layout`1) 類型的屬性 `IList<View>` 。 您可以將多個視圖專案新增至此集合，並 `StackLayout` 以水準或垂直堆疊顯示它們。

將的 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定 `StackLayout` 為列舉的成員 [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) ，不論是 [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) 或 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) 。 預設值為 `Vertical`。

將的 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性設定 `StackLayout` 為 `double` 值，以指定子系之間的間距。 預設值為6。

在程式碼中，您可以將專案加入至 `Children` `StackLayout` 或迴圈中的集合（ `for` `foreach` 如[**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)範例中所示），或者您也可以 `Children` 使用個別視圖的清單來初始化集合，如[**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)中所示。 子系必須衍生自， `View` 但可以包含其他 `StackLayout` 物件。

## <a name="scrolling-content"></a>滾動內容

如果 `StackLayout` 包含太多子系而無法顯示在頁面上，您可以將放 `StackLayout` 在中 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 以允許滾動。

將的 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 屬性設定 `ScrollView` 為您要滾動的視圖。 這通常是 `StackLayout` ，但它可以是任何觀點。

將的 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 屬性設定 `ScrollView` 為 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) 、、或屬性的成員 [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both) 。 預設值為 `Vertical`。 如果的內容 `ScrollView` 是 `StackLayout` ，這兩個方向應該是一致的。

[**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例會示範如何使用 `ScrollView` 和 `StackLayout` 來顯示可用的色彩。 此範例也會示範如何使用 .NET 反映來取得結構的所有公用靜態屬性和欄位， `Color` 而不需要明確列出它們。

## <a name="the-expands-option"></a>展開選項

當 `StackLayout` 堆疊其子系時，每個子系會在的總高度中佔用特定 `StackLayout` 位置，這取決於子系的大小和其 `HorizontalOptions` 和屬性的設定 `VerticalOptions` 。 這些屬性會指派為類型的值 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 。

`LayoutOptions`結構會定義兩個屬性：

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)具有四個成員、、 [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) 、 [`End`](xref:Xamarin.Forms.LayoutAlignment.End) 和的列舉類型[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)類型為`bool`

為了方便起見，此 `LayoutOptions` 結構也會定義類型的八個靜態唯讀欄位 `LayoutOptions` ，其中包含兩個實例屬性的所有組合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

下列討論牽涉到 `StackLayout` 具有預設垂直方向的。 水準 `StackLayout` 類似。

若為垂直 `StackLayout` ， `HorizontalOptions` 設定會決定子系在寬度內的水準位置 `StackLayout` 。 `Alignment` `Start` 、或的設定 `Center` `End` 會使子系成為不受限制的。 子系會決定其本身的寬度，並位於的左邊、中央或右方 `StackLayout` 。 `Fill`選項會將子系設為水準限制，並填滿的寬度 `StackLayout` 。

針對垂直 `StackLayout` ，每個子系會垂直地受到限制，並根據子系的高度取得垂直位置，在此情況下，此 `VerticalOptions` 設定是不相關的。

如果垂直 `StackLayout` 本身不受限制， &mdash; 如果其 `VerticalOptions` 設定為 `Start` 、或，則的高度會是其子系的 `Center` `End` `StackLayout` 總高度。

不過，如果垂直 `StackLayout` 會受到垂直限制， &mdash; 如果其設定為，則的 `VerticalOptions` `Fill` &mdash; 高度 `StackLayout` 會是其容器的高度，其可能大於其子系的總高度。 如果是這種情況，而且至少有一個子系的 `VerticalOptions` 設定具有的 `Expands` 旗標，則 `true` 中的額外空間 `StackLayout` 會平均配置給所有具有旗標的子系 `Expands` `true` 。 子系的總高度會等於的高度 `StackLayout` ，而 `Alignment` 設定的部分會決定子系在 `VerticalOptions` 其位置的垂直定位方式。

這會在[**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例中示範。

## <a name="frame-and-boxview"></a>Frame 和 BoxView

這兩個矩形視圖通常用於展示用途。

此 [`Frame`](xref:Xamarin.Forms.Frame) 視圖會在另一個視圖周圍顯示矩形框架，這可以是之類的版面配置 `StackLayout` 。 `Frame`繼承自 [`Content`](xref:Xamarin.Forms.ContentView.Content) 的屬性 [`ContentView`](xref:Xamarin.Forms.ContentView) ，您可以將其設定為要顯示在中的視圖 `Frame` 。 `Frame`預設是透明的。 設定下列三個屬性來自訂框架的外觀：

- [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)要使其可見的屬性。 `OutlineColor` `Color.Accent` 當您不知道基礎色彩配置時，通常會將設為。
- [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)屬性可以設定為 `true` ，以在 iOS 裝置上顯示黑色陰影。
- 將 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性設定為 `Thickness` 值，以在框架和框架的內容之間保留一個空格。 預設值是所有邊的20個單位。

`Frame`具有的預設 `HorizontalOptions` `VerticalOptions` 值和值 `LayoutOptions.Fill` ，這表示 `Frame` 將會填入其容器。 使用其他設定時，的大小 `Frame` 會以其內容的大小為基礎。

`Frame`會在[**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例中示範。

會 [`BoxView`](xref:Xamarin.Forms.BoxView) 顯示其屬性所指定之色彩的矩形區域 [`Color`](xref:Xamarin.Forms.BoxView.Color) 。

如果受到條件 `BoxView` 約束（其 `HorizontalOptions` 和 `VerticalOptions` 屬性具有的預設設定 `LayoutOptions.Fill` ），則會 `BoxView` 填滿它的可用空間。 如果 `BoxView` 不受限制（具有 `HorizontalOptions` `LayoutOptions` 、或的和設定 `Start` `Center` `End` ），則其預設維度為40單位正方形。 `BoxView`可以在一個維度中受到限制，而不受限制。

通常，您會設定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 的和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性， `BoxView` 以提供特定的大小。 [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例會說明這一點。

您可以使用的數個實例 `StackLayout` 結合 `BoxView` 中的和數個實例， `Label` `Frame` 以顯示特定的色彩，然後將中的每個視圖放入中的 `StackLayout` `ScrollView` ，以建立[**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)範例中所顯示之色彩的吸引人清單：

[![顏色塊的三向螢幕擷取畫面](images/ch04fg11-small.png "色彩清單")](images/ch04fg11-large.png#lightbox "色彩清單")

## <a name="a-scrollview-in-a-stacklayout"></a>StackLayout 中的 ScrollView？

將放入 `StackLayout` `ScrollView` 是很常見的，但將放 `ScrollView` 在中 `StackLayout` 也很方便。 理論上，這不是可行的，因為垂直的子系 `StackLayout` 是垂直不受限制的。 但 `ScrollView` 必須是垂直限制的。 它必須具有特定的高度，才能判斷其子系的大小以進行滾動。

秘訣是提供的子系的 `ScrollView` `StackLayout` `VerticalOptions` 設定 `FillAndExpand` 。 這會在[**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)範例中示範。

**BlackCat**範例也會示範如何定義和存取內嵌在共用程式庫中的程式資源。 這也可以透過共用資產專案（Sap）來達成，但此程式有點棘手，因為[**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)範例會示範。

## <a name="related-links"></a>相關連結

- [第4章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第4章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第4章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
