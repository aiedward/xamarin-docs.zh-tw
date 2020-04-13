---
title: 第26章摘要。 自訂版面配置
description: 使用 Xamarin.表單創建行動應用程式:第 26 章摘要。 自訂版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770932"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>第26章摘要。 自訂版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.forms 包括從[`Layout<View>`](xref:Xamarin.Forms.Layout`1)派生 的多個類:

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`，及
- `RelativeLayout`.

本章介紹如何創建派生自`Layout<View>`的類。

## <a name="an-overview-of-layout"></a>佈局概述

沒有處理 Xamarin.表單佈局的集中式系統。 每個元素負責確定其自己的大小,以及如何在特定區域內呈現自身。

### <a name="parents-and-children"></a>父母和兒童

每個有孩子的元素都負責將這些兒童定位到自己內部。 家長最終決定其子級應基於其可用大小和子級希望的大小。

### <a name="sizing-and-positioning"></a>尺寸與定位

佈局從可視化樹的頂部開始,然後從頁面開始,然後繼續遍通所有分支。 佈局中最重要的公共方法由[`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))定義`VisualElement`。 作為其他元素的父元素的每個元素都要求`Layout`其每個子元素[`Rectangle`](xref:Xamarin.Forms.Rectangle)以 值的形式為子級提供相對於自身的大小和位置。 這些`Layout`調用通過可視化樹傳播。

元素必須調用`Layout`到,並設置以下唯讀屬性。 它們與傳遞給方法的`Rectangle`一致:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)類型`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)類型`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)類型`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)類型`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)類型`double`

在`Layout`呼叫之前,`Height``Width`並且&ndash;具有模擬值 1。

呼叫`Layout`還會觸發對以下受保護方法的調用:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)),調用
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))可以重寫。

最後,觸發以下事件:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

該方法`OnSizeAllocated``Page`被`Layout`和 覆蓋,這是 Xamarin 中僅有的兩個類。 重寫的方法呼叫

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)衍生品`Page`[`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout)`Layout`和 衍生品,這要求
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))用於`Page`衍生品和[`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))`Layout`衍生工具。

`LayoutChildren`然後調用`Layout`元素的每個子級。 如果至少有一個子級具有新`Bounds`設置,則觸發以下事件:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)用於`Page`衍生品與[`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged)`Layout`衍生工具

### <a name="constraints-and-size-requests"></a>限制和大小要求

為了`LayoutChildren`智慧地調`Layout`用 其所有孩子,它必須知道兒童*的首選*或*所需的*大小。 因此,`Layout`每個子級的呼叫通常前面是呼叫

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

該書出版後,`GetSizeRequest`該方法被棄用,代之以

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

該方法`Measure`容納[`Margin`](xref:Xamarin.Forms.View.Margin)屬性 ,並[`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags)包含類型 參數,該參數包含兩個成員:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)不含邊距

對於許多元素,`GetSizeRequest``Measure`或從其呈現器獲取元素的本機大小。 這兩種方法都有寬度和高度*約束*的參數。 例如,將使用`Label`寬度約束來確定如何換行多行文本。

與`GetSizeRequest``Measure`都傳回類型[`SizeRequest`](xref:Xamarin.Forms.SizeRequest)的值 ,該值具有兩個屬性:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)類型`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)類型`Size`

這兩個值通常相同,通常可以忽略該`Minimum`值。

`VisualElement`還定義了類似於從`GetSizeRequest``GetSizeRequest`調用 的受保護方法:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))返回`SizeRequest`值

該方法現已棄用並取代為:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

`Layout`衍生或`Layout<T>`必須覆寫或的每個`OnSizeRequest`類別`OnMeasure`都已寫出到或 。 這是佈局類確定其自身大小的位置,該大小通常基於其子級的大小,它通過調用`GetSizeRequest``Measure`或 子級獲取。 在呼叫`OnSizeRequest``OnMeasure`或`GetSizeRequest`之後`Measure`, 或根據以下屬性進行調整:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)的類型`double`,`Request`影響屬性`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)的類型`double`,`Request`影響屬性`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)的類型`double`,`Minimum`影響屬性`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)的類型`double`,`Minimum`影響屬性`SizeRequest`

### <a name="infinite-constraints"></a>無限規範

`GetSizeRequest`傳遞給 (`Measure`或`OnSizeRequest`)`OnMeasure`和 (或 ) 的約束參數`Double.PositiveInfinity`可以是無限的 (即 , 值的值 )。 但是,從`SizeRequest`這些方法返回的不能包含無限維度。

無限約束表示請求的大小應反映元素的自然大小。 對其子`StackLayout``GetSizeRequest`級的`Measure`垂直 調用 (或 ) 具有無限高度約束。 具有無限寬度約束的`GetSizeRequest`子級`Measure`上的水準堆疊佈局調用 (或)。 對其`AbsoluteLayout`子`GetSizeRequest`級`Measure`的調用 (或 ) 具有無限寬度和高度限制。

### <a name="peeking-inside-the-process"></a>在流程內窺視

[**"探索兒童大小"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)顯示簡單佈局的約束和大小請求資訊。

## <a name="deriving-from-layoutview"></a>從佈局\<檢視>派生

自訂佈局類別的類別`Layout<View>`。 它負有兩項責任:

- 覆蓋`OnMeasure`以調`Measure`用 佈局的所有子級。 傳回佈局本身的請求大小
- 覆蓋`LayoutChildren`以呼`Layout`叫 佈局的所有子級

這些`for`重`foreach`寫 中的 或 迴圈`IsVisible`應跳過`false`其 屬性設置為 的任何子級。

不保證呼叫`OnMeasure`。 `OnMeasure`如果佈局的父級正在管理佈局的大小(例如,填充頁面的佈局),則不會調用。 因此,`LayoutChildren`不能`OnMeasure`依賴 呼叫期間獲得的子大小。 通常,`LayoutChildren`必須自行調`Measure`用 佈局的子級,或者可以實現某種大小緩存邏輯(稍後將討論)。

### <a name="an-easy-example"></a>簡單的的例子

[**垂直StackDemo"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含簡化[`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)類別及其使用的展示。

### <a name="vertical-and-horizontal-positioning-simplified"></a>垂直和水平定位簡化

`VerticalStack`必須執行的作業之一發生在`LayoutChildren`重寫期間。 該方法使用子屬性`HorizontalOptions`來確定如何將子級放置在`VerticalStack`其插槽中。 您可以改為呼叫靜態方法[`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))。 此方法調用`Measure`子級,並使用`HorizontalOptions``VerticalOptions`其和 屬性將子級放置在指定的矩形中。

### <a name="invalidation"></a>失效

元素屬性中的更改通常會影響該元素在佈局中的顯示方式。 必須使佈局失效才能觸發新佈局。

`VisualElement`定義受保護的方法[`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure),它通常由任何可綁定屬性的屬性更改處理程式調用,其更改會影響元素的大小。 此方法`InvalidateMeasure`觸發[`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件 。

該`Layout`類定義一個名稱類似[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)的 受保護`Layout`方法 , 派生應調用任何影響其介量和大小的任何更改。

### <a name="some-rules-for-coding-layouts"></a>編碼佈局的一些規則

1. 由`Layout<T>`導數定義的屬性應由可結合屬性支援,屬性變更的處理程式應呼叫`InvalidateLayout`。

2. 定義`Layout<T>`附加可綁定屬性的派生應重寫[`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*)以向其子級添加屬性更改的處理程式[`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*)並 刪除該處理程式。 處理程式應檢查這些附加的可綁定屬性中的更改,並通過調用進行`InvalidateLayout`回應。

3. 實現`Layout<T>`子大小緩存的導數應重`InvalidateLayout`寫[`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)並在調用這些方法時清除緩存。

### <a name="a-layout-with-properties"></a>具有屬性的佈局

[`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的類假定其所有子級大小相同,並將子級從一行(或列)包裝到下一行。 它定義屬性`Orientation``StackLayout`,如`ColumnSpacing`和`RowSpacing`和`Grid`屬性 ,如 ,它緩存子大小。

[**"PhotoWrap"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例`WrapLayout`將`ScrollView`in 中放入 用於顯示庫存照片的 。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許無約束尺寸!

[`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的圖集旨在顯示其內部的所有子級。 因此,它不能處理無約束維度,如果遇到異常,則引發異常。

[**「光格」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範`UniformGridLayout`例 :

[![相片格的三重螢幕截圖](images/ch26fg08-small.png "整合格佈局")](images/ch26fg08-large.png#lightbox "整合格佈局")

### <a name="overlapping-children"></a>重疊子級

`Layout<T>`衍生物可以重疊其子項。 但是,子級按集合中`Children`的順序呈現,而不是按其`Layout`方法調用的順序呈現。

這個`Layout`類別定義了兩種方法,允許您在集合中移動子級:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View))將子項目移至集合的開頭
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View))將子項目移至集合的末尾

對於重疊的子級,集合末尾的子級在集合的開頭直觀地顯示在子級的頂部。

[`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類定義附加屬性以指示呈現順序,從而允許其一個子項顯示在其他子級的頂部。 [**學生卡片檔**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)範例展示以下情況:

[![學生卡檔案格格的三重螢幕截圖](images/ch26fg10-small.png "重疊佈局子級")](images/ch26fg10-large.png#lightbox "重疊佈局子級")

### <a name="more-attached-bindable-properties"></a>附加的更多可繫結屬性

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫[`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)中的 類定義附加的可綁定屬性,以`Point`指定兩個值和一個厚度`BoxView`值, 並操作元素以類似於線條。

[**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)示例使用它來繪製 3D 立方體。

### <a name="layout-and-layoutto"></a>佈局與佈局到

`Layout<T>`導數可以調用`LayoutTo``Layout`而不是 為佈局設置動畫。 類[`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)可以這樣做,[**動畫單元Cube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)示例演示了它。

## <a name="related-links"></a>相關連結

- [第26章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第26章 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂佈局](~/xamarin-forms/user-interface/layouts/custom.md)
