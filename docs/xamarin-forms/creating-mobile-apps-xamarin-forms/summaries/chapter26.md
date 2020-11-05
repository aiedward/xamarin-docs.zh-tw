---
title: 第26章的摘要。 自訂版面配置
description: 建立 Mobile Apps，包含 Xamarin.Forms ：第26章的摘要。 自訂版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 377de0ff7e48971f9214cf86aec19ce14859139e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366226"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>第26章的摘要。 自訂版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

Xamarin.Forms 包含數個衍生自的類別 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ：

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`，以及
- `RelativeLayout`.

本章將說明如何建立您自己的衍生自的類別 `Layout<View>` 。

## <a name="an-overview-of-layout"></a>版面配置總覽

沒有處理版面配置的集中式系統 Xamarin.Forms 。 每個元素都負責判斷其本身的大小，以及如何在特定區域內呈現本身。

### <a name="parents-and-children"></a>父代和子系

具有子系的每個專案都會負責將這些子系放在本身內。 最後，它會根據其可用大小和子系想要的大小，判斷其子系的大小。

### <a name="sizing-and-positioning"></a>調整大小和定位

版面配置會從視覺化樹狀結構頂端的頁面開始，然後繼續執行所有分支。 版面配置中最重要的公用方法是 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。由定義的矩形) # A3 `VisualElement` 。 每個父代至其他專案的父元素都會呼叫 `Layout` 其每個子系，以值的形式為子系提供相對於本身的大小與位置 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 。 這些 `Layout` 呼叫會傳播至視覺化樹狀結構。

需要呼叫才能 `Layout` 讓元素出現在螢幕上，並且會設定下列唯讀屬性。 它們與 `Rectangle` 傳遞給方法的一致：

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) 型別為 `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) 型別為 `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) 型別為 `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) 型別為 `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) 型別為 `double`

在呼叫之前 `Layout` ，其 `Height` `Width` mock 值為 &ndash; 1。

呼叫也會 `Layout` 觸發下列受保護方法的呼叫：

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double))，呼叫
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))，可以覆寫。

最後，會引發下列事件：

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`OnSizeAllocated`方法會由和覆 `Page` 寫 `Layout` ，這是中只有兩個類別 Xamarin.Forms 可以有子系。 覆寫的方法呼叫

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) 針對 `Page` 衍生的 [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) 和 `Layout` 衍生的，其呼叫
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 適用于 `Page` 衍生 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 和 `Layout` 衍生。

`LayoutChildren` 然後呼叫 `Layout` 每個元素的子系。 如果至少有一個子系具有新的 `Bounds` 設定，則會引發下列事件：

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) 適用于 `Page` 衍生 [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) 和 `Layout` 衍生

### <a name="constraints-and-size-requests"></a>條件約束和大小要求

`LayoutChildren`若要在智慧 `Layout` 上呼叫其所有子系，它必須知道子系的 *慣用* 或 *所需* 大小。 因此，對每個子系的呼叫 `Layout` 通常會在之前呼叫

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

發行本書之後， `GetSizeRequest` 方法已被取代，並取代為

- [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3

`Measure`方法會容納 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性，並且包含類型的引數 [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags) ，其中有兩個成員：

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) 不包含邊界

針對許多元素， `GetSizeRequest` 或 `Measure` 從其轉譯器取得元素的原生大小。 這兩種方法都有寬度和高度 *條件約束* 的參數。 例如， `Label` 會使用寬度條件約束來決定如何換行多行文字。

和都會傳回 `GetSizeRequest` `Measure` 類型的值 [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) ，其中有兩個屬性：

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) 型別為 `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) 型別為 `Size`

這兩個值通常是相同的，而且 `Minimum` 通常可以忽略此值。

`VisualElement` 也會定義受保護的方法，類似于 `GetSizeRequest` 從呼叫的方法 `GetSizeRequest` ：

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) 傳回 `SizeRequest` 值

該方法現在已被取代，並以下列內容取代：

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

每個衍生自或的類別都 `Layout` `Layout<T>` 必須覆寫 `OnSizeRequest` 或 `OnMeasure` 。 這是版面配置類別決定其本身大小的位置，通常是根據其子系的大小（藉由呼叫或子系取得 `GetSizeRequest` ） `Measure` 。 在呼叫或之後 `OnSizeRequest` ， `OnMeasure` `GetSizeRequest` 或 `Measure` 根據下列屬性進行調整：

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)類型的 `double` 會影響的 `Request` 屬性。 `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 類型的 `double` 會影響的 `Request` 屬性。 `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) 類型的 `double` 會影響的 `Minimum` 屬性。 `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) 類型的 `double` 會影響的 `Minimum` 屬性。 `SizeRequest`

### <a name="infinite-constraints"></a>無限條件約束

傳遞給 `GetSizeRequest` (或 `Measure`) 和 (或) 的條件約束引數 `OnSizeRequest` `OnMeasure` 可以是無限的 (例如， `Double.PositiveInfinity`) 的值。 但是， `SizeRequest` 從這些方法傳回的不能包含無限的維度。

無限條件約束表示要求的大小應該反映元素的自然大小。 垂直 `StackLayout` 呼叫會 `GetSizeRequest` `Measure` 在其子系上 (或) ，其具有無限的高度條件約束。 水準堆疊版面配置會 `GetSizeRequest` `Measure` 以無限寬度的條件約束，在其子系上呼叫 (或) 。 `AbsoluteLayout` `GetSizeRequest` `Measure` 在具有無限寬度和高度條件約束的子系上呼叫 (或) 。

### <a name="peeking-inside-the-process"></a>在進程內查看

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)會顯示簡單版面配置的條件約束和大小要求資訊。

## <a name="deriving-from-layoutview"></a>衍生自版面配置\<View>

自訂版面配置類別衍生自 `Layout<View>` 。 這有兩個責任：

- 覆寫 `OnMeasure` 以呼叫 `Measure` 所有版面配置的子系。 傳回配置本身的要求大小
- 覆寫 `LayoutChildren` 以呼叫 `Layout` 所有版面配置的子系

`for` `foreach` 這些覆寫中的或迴圈應該略過其 `IsVisible` 屬性設定為的任何子系 `false` 。

不保證對的呼叫 `OnMeasure` 。 `OnMeasure` 如果配置的父系正在控管版面配置大小 (例如，填滿頁面) 的版面配置，則不會呼叫。 基於這個理由， `LayoutChildren` 無法依賴呼叫期間取得的子大小 `OnMeasure` 。 通常， `LayoutChildren` 它本身必須 `Measure` 在配置的子系上呼叫，或您可以執行某種大小的快取邏輯， (稍後將) 討論。

### <a name="an-easy-example"></a>簡單範例

[**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含簡化的 [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) 類別和其使用示範。

### <a name="vertical-and-horizontal-positioning-simplified"></a>簡化垂直和水準定位

覆 `VerticalStack` 寫期間必須執行的其中一項作業 `LayoutChildren` 。 方法會使用子系的 `HorizontalOptions` 屬性來決定如何在的位置內放置子系 `VerticalStack` 。 您可以改為呼叫靜態方法 [ `Layout.LayoutChildIntoBoundingRect` ] (x： Xamarin.Forms 。LayoutChildIntoBoundingRegion (Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形) # A3。 這個方法會 `Measure` 在子系上呼叫，並使用其 `HorizontalOptions` 和屬性，將 `VerticalOptions` 子系放在指定的矩形內。

### <a name="invalidation"></a>失效

專案屬性的變更通常會影響該專案在版面配置中的顯示方式。 版面配置必須是不正確，才能觸發新的版面配置。

`VisualElement` 定義受保護的方法 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) ，此方法通常會由任何可系結屬性的屬性變更處理常式所呼叫，而該屬性的變更會影響元素的大小。 `InvalidateMeasure`方法會引發 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 事件。

`Layout`類別會定義一個名為的類似受保護方法 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) ，其 `Layout` 衍生應針對影響其定位和調整其子系的變更進行呼叫。

### <a name="some-rules-for-coding-layouts"></a>編碼配置的一些規則

1. 衍生屬性所定義的屬性 `Layout<T>` 應受到可系結屬性的支援，且屬性變更的處理常式應該呼叫 `InvalidateLayout` 。

2. `Layout<T>`定義附加可系結屬性的衍生系應覆寫 [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) ，以將屬性變更的處理常式加入其子系，並 [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) 移除該處理常式。 處理常式應該檢查這些附加的可系結屬性中是否有變更，並藉由呼叫來回應 `InvalidateLayout` 。

3. `Layout<T>` `InvalidateLayout` [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 當呼叫這些方法時，可覆寫和清除快取的衍生。

### <a name="a-layout-with-properties"></a>具有屬性的版面配置

[`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的類別會假設其所有子系的大小都相同，並將子資料列的子系換行 (或資料行) 至下一個資料列。 它會定義 `Orientation` 類似的 `StackLayout` 、和 `ColumnSpacing` 等屬性 `RowSpacing` ，並快取 `Grid` 子大小。

[**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例會將中的放入 `WrapLayout` `ScrollView` ，以顯示股票相片。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許有未受限制的維度！

[`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的是要在其本身內顯示其所有子系。 因此，它無法處理不受限制的維度，而且如果遇到例外狀況，就會引發例外狀況。

[**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範例會示範 `UniformGridLayout` ：

[![相片方格的三重螢幕擷取畫面](images/ch26fg08-small.png "統一格線版面配置")](images/ch26fg08-large.png#lightbox "統一格線版面配置")

### <a name="overlapping-children"></a>重迭的子系

`Layout<T>`衍生的子系可能會重迭。 不過，子系會依其在集合中的順序轉譯 `Children` ，而不是其方法的 `Layout` 呼叫順序。

`Layout`類別會定義兩種可讓您在集合內移動子系的方法：

- [ `LowerChild` ] (x： Xamarin.Forms 。LowerChild (Xamarin.Forms 。View) # A3 以將子系移至集合的開頭
- [ `RaiseChild` ] (x： Xamarin.Forms 。RaiseChild (Xamarin.Forms 。View) # A3 以將子系移至集合的結尾

針對重迭的子系，集合結尾的子系會以視覺化方式出現在集合開頭的子系上方。

[`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)[ [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ] 程式庫中的類別會定義附加屬性來表示轉譯順序，進而允許其中一個子系顯示在其他子系上方。 [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)範例會示範這一點：

[![學生卡片檔案方格的三重螢幕擷取畫面](images/ch26fg10-small.png "重迭的版面配置子系")](images/ch26fg10-large.png#lightbox "重迭的版面配置子系")

### <a name="more-attached-bindable-properties"></a>其他附加的可系結屬性

[`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)[ [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ] 程式庫中的類別會定義附加的可系結屬性，以指定兩個 `Point` 值和粗細值，並將專案操作 `BoxView` 成類似的行。

[**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)範例會使用它來繪製 3d cube。

### <a name="layout-and-layoutto"></a>版面配置和 LayoutTo

`Layout<T>`衍生的可呼叫 `LayoutTo` ，而不是用 `Layout` 來建立版面配置的動畫。 [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)類別會執行此工作，而 [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)範例會示範它。

## <a name="related-links"></a>相關連結

- [第26章全文檢索 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第26章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
