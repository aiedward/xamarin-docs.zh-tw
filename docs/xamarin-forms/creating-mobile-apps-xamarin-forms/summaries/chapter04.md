---
title: 第4章摘要。 捲動堆疊
description: 使用 Xamarin.表單創建行動應用程式:第 4 章摘要。 捲動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032880"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第4章摘要。 捲動堆疊

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

本章主要介紹*佈局*的概念,這是 Xamarin. Forms 用於組織頁面上多個視圖的可視化顯示的類和技術的總術語。

佈局涉及派生自[`Layout`](xref:Xamarin.Forms.Layout)[`Layout<T>`](xref:Xamarin.Forms.Layout`1)和的多個類。 本章重點介紹[`StackLayout`](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> Xamarin.forms [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) 3.0 中引入的方法`StackLayout`與類似 但具有更大的靈活性類似。

本章中還介紹了[`ScrollView`](xref:Xamarin.Forms.ScrollView)[`Frame`](xref:Xamarin.Forms.Frame)、[`BoxView`](xref:Xamarin.Forms.BoxView)和 類。

## <a name="stacks-of-views"></a>檢視堆疊

[`StackLayout`](xref:Xamarin.Forms.StackLayout)衍生的`Layout<View>`類型 屬性[`Children`](xref:Xamarin.Forms.Layout`1)並繼承`IList<View>`。 向此集合添加多個檢視項,並在`StackLayout`水準或垂直堆疊中顯示它們。

[`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)將 的`StackLayout`屬性[`StackOrientation`](xref:Xamarin.Forms.StackOrientation)設定為 枚舉的[`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical)[`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)成員,或 。 預設值為 `Vertical`。

將[`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)的屬性`StackLayout`設置為`double`值 以指定子級之間的間距。 默認值為 6。

在代碼中,可以將項添加到`Children``StackLayout``for``foreach`或迴圈中的集合中,如[**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)範例所示,也可以使用單個檢視的`Children`清單初始 化集合,如[**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)所示。 子級必須派生,`View`但可以包括`StackLayout`其他 物件。

## <a name="scrolling-content"></a>捲動內容

如果`StackLayout`包含太多子項無法顯示在頁面上,則可以將`StackLayout`[`ScrollView`](xref:Xamarin.Forms.ScrollView)中的放在中以允許滾動。

將[`Content`](xref:Xamarin.Forms.ScrollView.Content)的屬性`ScrollView`設置為要滾動的視圖。 這通常是一個`StackLayout`,但它可以是任何視圖。

將[`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)`ScrollView`的屬性設定為[`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation)屬性的[`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)成員[`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal),[`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)或 。 預設值為 `Vertical`。 如果`ScrollView`的內容`StackLayout`為 , 則兩個方向應一致。

[**「反射顏色」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例展示`ScrollView`了`StackLayout`和顯示可用顏色的使用。 該示例還演示如何使用 .NET 反射`Color`來獲取 結構的所有公共靜態屬性和欄位,而無需顯式列出它們。

## <a name="the-expands-option"></a>展開「選項」

當堆`StackLayout`棧其子級時,每個子級佔用的特定插槽`StackLayout`, 該槽位於 的總高度內,具體取決於子級`HorizontalOptions`的`VerticalOptions`大小及其 和 屬性的設置。 這些屬性為類型[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)分配了 值。

結構`LayoutOptions`定義兩個屬性:

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)具有四個成員的枚舉[`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)[`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)類型[`Center`](xref:Xamarin.Forms.LayoutAlignment.Center),[`End`](xref:Xamarin.Forms.LayoutAlignment.End)、 和[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)類型`bool`

為方便起見,該`LayoutOptions`結構還定義了八個類型`LayoutOptions`的 靜態唯讀欄位,這些欄位包含兩個實例屬性的所有組合:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

以下討論包含有預設`StackLayout`垂直方向的 。 水平`StackLayout`類似。

對於垂直`StackLayout``HorizontalOptions`, 設置確定子級在`StackLayout`寬度 中的水準定位方式。 的`Alignment``Start`設定,`Center``End`或使子級水準不受限制。 子項確定其自己的寬度,並位於`StackLayout`的左側、中心或右側。 該`Fill`選項會導致子級被水準約束並填充`StackLayout`的寬度。

對於垂直`StackLayout`,每個子級垂直不受約束,並根據子級的高度獲得垂直槽,在這種情況下`VerticalOptions`, 設置不相關。

如果`StackLayout`垂直本身不受&mdash;約束 ,即`VerticalOptions`如果其`Start``Center`設置`End`為 、`StackLayout`或 ,則的高度 是其子級的總高度。

但是`StackLayout`,如果垂直是垂直&mdash;約束 的,`VerticalOptions`如果其`Fill`&mdash;設置是`StackLayout`,則的高度 將是其容器的高度,該高度可能大於其子級的總高度。 `VerticalOptions`如果是這種情況,並且如果至少有一個子具有具有`Expands`標誌`true`的 設置,`StackLayout`則`Expands``true`在具有標誌的所有子級中,將平均分配中的額外空間。 然後,子級的總高度將等於的高度`StackLayout``Alignment``VerticalOptions`, 設置部分確定子級在其插槽中的垂直位置。

垂直[**選項演示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例展示這一點。

## <a name="frame-and-boxview"></a>框架與框檢視

這兩個矩形視圖通常用於表示目的。

檢視[`Frame`](xref:Xamarin.Forms.Frame)顯示環繞另一個檢視的矩形框架,該檢視可以是佈局,如`StackLayout`。 `Frame`繼承從您[`Content`](xref:Xamarin.Forms.ContentView.Content)設定為[`ContentView`](xref:Xamarin.Forms.ContentView)要 顯示在中的檢視中的`Frame`屬性 。 默認情況下`Frame`是透明的。 設定以下三個屬性以自訂幀的外觀:

- 使其[`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor)可見的屬性。 當您不知道基礎配色`OutlineColor``Color.Accent`配置 時,通常要設置為。
- 可以將[`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)該屬性設置`true`為 在 iOS 設備上顯示黑色陰影。
- 將[`Padding`](xref:Xamarin.Forms.Layout.Padding)屬性設定為`Thickness`值 ,在框架和框架的內容之間留出空格。 默認值為20個單位。"

`Frame`的 預設`HorizontalOptions`值`VerticalOptions``LayoutOptions.Fill`和`Frame`值 ,這意味著將填充其容器。 對於其他設置,的大小`Frame`取決於其內容的大小。

在`Frame`[**框架文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例中展示 。

將顯示[`BoxView`](xref:Xamarin.Forms.BoxView)由[`Color`](xref:Xamarin.Forms.BoxView.Color)其 屬性指定的矩形顏色區域。

如果`BoxView`受約束(`HorizontalOptions`其`VerticalOptions`和`LayoutOptions.Fill`屬性具有 其預設`BoxView`設置),則填充可用於它的空間。 如果`BoxView`不受約束(`HorizontalOptions``LayoutOptions``Start`與`Center`和`End`的 設置 , 或 ), 它的預設維度為 40 個單位平方。 A`BoxView`可以在一個維度中約束,另一個維度可以不受約束。

通常,您將設置[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)`BoxView`和 屬性,以賦予其特定大小。 「[**大小框檢視」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例說明瞭這一點。

可以使用`StackLayout`多個實體將 a`BoxView`與`Label`多個實體組合在`Frame`中 以顯示特定顏色,然後將每個檢視放在`StackLayout``ScrollView`中,以建立[**ColorBlock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)範例中顯示的有吸引力的顏色清單:

[![顏色塊的三重螢幕截圖](images/ch04fg11-small.png "顏色清單")](images/ch04fg11-large.png#lightbox "顏色清單")

## <a name="a-scrollview-in-a-stacklayout"></a>堆疊佈局中的滾動視圖?

將`StackLayout`放入`ScrollView`中很常見,但`ScrollView`放入`StackLayout`中 有時也很方便。 從理論上講,這應該是不可能的,因為垂直`StackLayout`的子級是垂直無約束的。 但是,`ScrollView`必須垂直約束。 必須為其指定特定高度,以便確定其子級用於滾動的大小。

訣竅是`ScrollView`給`StackLayout`孩子`VerticalOptions`一個`FillAndExpand`設置。 這一點在[**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)示例中進行了演示。

**BlackCat**示例還演示如何定義和訪問嵌入在共用庫中的程序資源。 這也可以通過共享資產專案 (SAP) 來實現,但該過程有點棘手,如[**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)示例所示。

## <a name="related-links"></a>相關連結

- [第四章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第四章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第四章 F# 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
