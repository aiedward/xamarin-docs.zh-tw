---
title: 第4章的摘要。 捲動堆疊
description: 建立 Mobile Apps Xamarin.Forms ：第4章的摘要。 捲動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 26b9aa33267c56a82edfd38a1d7eefa6a5cd82a2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370360"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第4章的摘要。 捲動堆疊

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

本章主要著重于介紹 *版面* 配置的概念，這是 Xamarin.Forms 用來組織視覺顯示頁面上多個視圖的類別和技術的整體術語。

版面配置牽涉到數個衍生自和的類別 [`Layout`](xref:Xamarin.Forms.Layout)  [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 。 本章著重于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md)3.0 中引進的 Xamarin.Forms 方式可用於類似 `StackLayout` 但有更多彈性的方式。

本章也會介紹 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 、 [`Frame`](xref:Xamarin.Forms.Frame) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 類別。

## <a name="stacks-of-views"></a>視圖堆疊

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 衍生自 `Layout<View>` ，並繼承 [`Children`](xref:Xamarin.Forms.Layout`1) 型別的屬性 `IList<View>` 。 您將多個視圖專案加入至這個集合，並將 `StackLayout` 它們顯示在水準或垂直堆疊中。

將的 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定 `StackLayout` 為列舉的成員 [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) ，也就是 [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) 或 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) 。 預設為 `Vertical`。

將的 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性設定為 `StackLayout` `double` 值，以指定子系之間的間距。 預設值為6。

在程式碼中，您可以 `Children` `StackLayout` 如 ColorLoop 範例所示，將專案加入至或迴圈中的集合 `for` `foreach` ，也可以 [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) `Children` 使用 [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)中所示範的個別視圖清單來初始化集合。 子系必須衍生自， `View` 但可包含其他 `StackLayout` 物件。

## <a name="scrolling-content"></a>滾動內容

如果 `StackLayout` 包含的子系太多而無法顯示在頁面上，您可以將放入， `StackLayout` [`ScrollView`](xref:Xamarin.Forms.ScrollView) 以允許滾動。

將的 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 屬性設定 `ScrollView` 為您想要滾動的視圖。 這通常是 `StackLayout` ，但它可以是任何觀點。

將的 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 屬性設定 `ScrollView` 為 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) 屬性、、或的成員 [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal) [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both) 。 預設為 `Vertical`。 如果的內容 `ScrollView` 是 `StackLayout` ，則這兩個方向應該是一致的。

[**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例會示範如何使用 `ScrollView` 和 `StackLayout` 來顯示可用的色彩。 此範例也會示範如何使用 .NET 反映來取得結構的所有公用靜態屬性和欄位， `Color` 而不需要明確地列出它們。

## <a name="the-expands-option"></a>展開選項

當 `StackLayout` 堆疊其子系時，每個子系都會在的總高度內 `StackLayout` ，根據子系的大小和其和屬性的設定，在其高度的高度中佔用特定位置 `HorizontalOptions` `VerticalOptions` 。 這些屬性是指派給類型的值 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 。

`LayoutOptions`結構會定義兩個屬性：

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)具有四個成員（、、 [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End) 和）的列舉型別。[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 型別為 `bool`

為了方便起見，此 `LayoutOptions` 結構也會定義類型的八個靜態唯讀欄位 `LayoutOptions` ，其中包含兩個實例屬性的所有組合：

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

下列討論牽涉到 `StackLayout` 預設的垂直方向。 水準 `StackLayout` 類似。

若為垂直 `StackLayout` ，此 `HorizontalOptions` 設定會決定子系在的寬度內的水準定位方式 `StackLayout` 。 的 `Alignment` 設定 `Start` 、 `Center` 或 `End` 會導致子系的水準不受限制。 子系決定其本身的寬度，並放置在的左邊、中央或右邊 `StackLayout` 。 `Fill`選項會讓子系成為水準限制，並填滿的寬度 `StackLayout` 。

若是垂直的 `StackLayout` ，每個子系都是垂直不受限制的，並且會根據子系的高度取得垂直位置，在此情況下，此 `VerticalOptions` 設定是不相關的。

如果垂直 `StackLayout` 本身沒有限制， &mdash; 則為，如果其 `VerticalOptions` 設定為 `Start` 、或，則的高度為其子系的 `Center` `End` `StackLayout` 總高度。

但是，如果垂直的設定是垂直限制的，則的 `StackLayout` &mdash; `VerticalOptions` `Fill` &mdash; 高度 `StackLayout` 會是其容器的高度，而這可能會大於其子系的總高度。 如果是這種情況，而且至少有一個子系具有 `VerticalOptions` 旗標的設定 `Expands` ，則 `true` 中的額外空間 `StackLayout` 會在所有子系中以相同的旗 `Expands` `true` 標配置。 子系的總高度會等於的高度 `StackLayout` ，而設定的部分則會 `Alignment` `VerticalOptions` 決定子系在其位置的垂直位置。

[**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例會示範這一點。

## <a name="frame-and-boxview"></a>框架和 BoxView

這兩個矩形視圖通常用於展示用途。

此 [`Frame`](xref:Xamarin.Forms.Frame) 視圖會在另一個視圖周圍顯示矩形框架，可能是像這樣的版面配置 `StackLayout` 。 `Frame`[`Content`](xref:Xamarin.Forms.ContentView.Content)從 [`ContentView`](xref:Xamarin.Forms.ContentView) 您設定為要顯示在中之視圖的繼承屬性 `Frame` 。 `Frame`預設是透明的。 設定下列三個屬性來自訂框架的外觀：

- [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)要使其成為可見的屬性。 `OutlineColor` `Color.Accent` 當您不知道基礎色彩配置時，通常會將設定為。
- [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)屬性可以設定為 `true` ，以在 iOS 裝置上顯示黑色陰影。
- 將 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性設定為 `Thickness` 值，以在框架和框架的內容之間保留一個空格。 預設值是所有邊的20個單位。

`Frame`具有的預設 `HorizontalOptions` `VerticalOptions` 值和值 `LayoutOptions.Fill` ，這表示 `Frame` 將會填滿其容器。 使用其他設定時，的大小 `Frame` 會根據其內容的大小。

`Frame` [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例中會示範。

會 [`BoxView`](xref:Xamarin.Forms.BoxView) 顯示其屬性所指定之色彩的矩形區域 [`Color`](xref:Xamarin.Forms.BoxView.Color) 。

如果 `BoxView` 受限 (其 `HorizontalOptions` 和 `VerticalOptions` 屬性具有) 的預設設定 `LayoutOptions.Fill` ， `BoxView` 就會填滿它的可用空間。 如果 `BoxView` 不受限制 (以及 `HorizontalOptions` `LayoutOptions` `Start` 、或) 的設定， `Center` `End` 則預設維度為40個單位的正方形。 `BoxView`可以在一個維度中受到限制，而另一個維度則不受限制。

通常，您會設定的 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性， `BoxView` 以提供特定的大小。 [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例會說明這一點。

您可以使用的數個實例， `StackLayout` 結合中的 `BoxView` 和數個 `Label` 實例 `Frame` 來顯示特定的色彩，然後將這些視圖放入中的， `StackLayout` `ScrollView` 以建立 [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) 範例中所顯示的吸引色彩清單：

[![色彩區塊的三重螢幕擷取畫面](images/ch04fg11-small.png "色彩清單")](images/ch04fg11-large.png#lightbox "色彩清單")

## <a name="a-scrollview-in-a-stacklayout"></a>StackLayout 中的 ScrollView？

將放入 `StackLayout` `ScrollView` 很常見，但是將放 `ScrollView` 在中 `StackLayout` 也是很方便的。 理論上，這應該不可能，因為垂直的子系 `StackLayout` 是垂直不受限制的。 但 `ScrollView` 必須是垂直限制的。 它必須獲得特定的高度，才能判斷其子系的大小以供滾動。

秘訣是為的設定提供 `ScrollView` 子系 `StackLayout` `VerticalOptions` `FillAndExpand` 。 [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)範例會示範這一點。

**BlackCat** 範例也會示範如何定義和存取內嵌于共用程式庫中的程式資源。 您也可以使用 (Sap) 的共用資產專案來達成此目的，但此程式很難處理，如 [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) 範例所示。

## <a name="related-links"></a>相關連結

- [第4章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第4章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第4章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scrollview.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
