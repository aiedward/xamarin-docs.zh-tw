---
title: 第26章的摘要。 自訂版面配置
description: 使用 Xamarin 建立 Mobile Apps：第26章的摘要。 自訂版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770932"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>第26章的摘要。 自訂版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin 包含數個衍生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1)的類別：

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`，及
- `RelativeLayout`第 1 課：建立 Windows Azure 儲存體物件{2}。

本章說明如何建立您自己的類別，衍生自 `Layout<View>`。

## <a name="an-overview-of-layout"></a>版面配置總覽

沒有處理 Xamarin 的集中式系統。表單版面配置。 每個元素都會負責判斷其本身的大小，以及如何在特定區域內呈現其本身。

### <a name="parents-and-children"></a>父和子系

具有子系的每個元素都會負責將這些子系放在本身內。 它是最終根據其可用大小和子系想要的大小，決定其子系之大小的父系。

### <a name="sizing-and-positioning"></a>調整大小和位置

版面配置會從視覺化樹狀結構的頂端與頁面開始，然後繼續進行所有分支。 版面配置中最重要的公用方法是由 `VisualElement`定義[`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) 。 屬於其他專案之父系的每個專案都會呼叫其每個子系的 `Layout`，以[`Rectangle`](xref:Xamarin.Forms.Rectangle)值的形式，為子系提供相對於其本身的大小和位置。 這些 `Layout` 呼叫會透過視覺化樹狀結構傳播。

需要呼叫 `Layout`，元素才會出現在螢幕上，並會設定下列唯讀屬性。 它們與傳遞至方法的 `Rectangle` 一致：

- 類型為 [ 的 `Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)`Rectangle`
- 類型為 [ 的 `X`](xref:Xamarin.Forms.VisualElement.X)`double`
- 類型為 [ 的 `Y`](xref:Xamarin.Forms.VisualElement.Y)`double`
- 類型為 [ 的 `Width`](xref:Xamarin.Forms.VisualElement.Width)`double`
- 類型為 [ 的 `Height`](xref:Xamarin.Forms.VisualElement.Height)`double`

在 `Layout` 呼叫之前，`Height` 和 `Width` 具有 &ndash;1 的 mock 值。

`Layout` 的呼叫也會觸發下列受保護方法的呼叫：

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double))，其會呼叫
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))，可以覆寫。

最後，會引發下列事件：

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`OnSizeAllocated` 方法會由 `Page` 和 `Layout`覆寫，這是 Xamarin 中唯一可以有子系的兩個類別。 覆寫的方法呼叫

- 針對 `Layout` 衍生的 `Page` 衍生和[`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) ，其會呼叫
- 適用于 `Layout` 衍生的 `Page` 衍生和[`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 。

`LayoutChildren` 接著會針對每個元素的子系呼叫 `Layout`。 如果至少有一個子系具有新的 `Bounds` 設定，則會引發下列事件：

- 適用于 `Layout` 衍生的 `Page` 衍生和[`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)

### <a name="constraints-and-size-requests"></a>條件約束和大小要求

若要讓 `LayoutChildren` 在其所有子系上以智慧方式呼叫 `Layout`，它必須知道子系的*慣用*或*所需*大小。 因此，每個子系的 `Layout` 呼叫通常會在呼叫之前

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

發行本書之後，`GetSizeRequest` 方法已被取代，並以

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

`Measure` 方法會容納[`Margin`](xref:Xamarin.Forms.View.Margin)屬性，並包含型別[`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags)的引數，其中包含兩個成員：

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)不包含邊界

對於許多元素而言，`GetSizeRequest` 或 `Measure` 會從其轉譯器取得專案的原生大小。 這兩種方法都有 width 和 height*條件約束*的參數。 例如，`Label` 會使用 width 條件約束來決定如何包裝多行文字。

`GetSizeRequest`和 `Measure` 都會傳回類型[`SizeRequest`](xref:Xamarin.Forms.SizeRequest)的值，其中包含兩個屬性：

- 類型為 [ 的 `Request`](xref:Xamarin.Forms.SizeRequest.Request)`Size`
- 類型為 [ 的 `Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)`Size`

這兩個值通常是相同的，而且通常可以忽略 `Minimum` 的值。

`VisualElement` 也會定義受保護的方法，類似于從 `GetSizeRequest`呼叫的 `GetSizeRequest`：

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))會傳回 `SizeRequest` 值

該方法現在已被取代，並以取代：

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

衍生自 `Layout` 或 `Layout<T>` 的每個類別都必須覆寫 `OnSizeRequest` 或 `OnMeasure`。 這是配置類別判斷其本身大小的位置，通常是以其子系的大小為基礎，它是藉由呼叫子系的 `GetSizeRequest` 或 `Measure` 來取得。 在呼叫 `OnSizeRequest` 或 `OnMeasure`之前和之後，`GetSizeRequest` 或 `Measure` 會根據下列屬性進行調整：

- `double`類型的[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)會影響 `SizeRequest` 的 `Request` 屬性
- `double`類型的[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)會影響 `SizeRequest` 的 `Request` 屬性
- `double`類型的[`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)會影響 `SizeRequest` 的 `Minimum` 屬性
- `double`類型的[`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)會影響 `SizeRequest` 的 `Minimum` 屬性

### <a name="infinite-constraints"></a>無限條件約束

傳遞給 `GetSizeRequest` （或 `Measure`）和 `OnSizeRequest` （或 `OnMeasure`）的條件約束引數可以是無限的（亦即，`Double.PositiveInfinity`的值）。 不過，從這些方法傳回的 `SizeRequest` 不能包含無限維度。

無限條件約束表示要求的大小應該反映元素的自然大小。 垂直 `StackLayout` 會在其子系上呼叫具有無限高度條件約束的 `GetSizeRequest` （或 `Measure`）。 水準堆疊配置會在其子系上呼叫具有無限寬度限制的 `GetSizeRequest` （或 `Measure`）。 `AbsoluteLayout` 會在其子系上呼叫具有無限寬度和高度條件約束的 `GetSizeRequest` （或 `Measure`）。

### <a name="peeking-inside-the-process"></a>在進程內查看

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)會顯示簡單版面配置的條件約束和大小要求資訊。

## <a name="deriving-from-layoutview"></a>衍生自版面配置\<View >

自訂版面配置類別衍生自 `Layout<View>`。 這有兩個責任：

- 覆寫 `OnMeasure`，以在所有版面配置的子系上呼叫 `Measure`。 傳回版面配置本身要求的大小
- 覆寫 `LayoutChildren` 以在所有版面配置的子系上呼叫 `Layout`

這些覆寫中的 `for` 或 `foreach` 迴圈應該略過 `IsVisible` 屬性設定為 `false`的任何子系。

不保證 `OnMeasure` 的呼叫。 如果配置的父系負責控管版面配置的大小（例如，填滿頁面的版面配置），則不會呼叫 `OnMeasure`。 基於這個理由，`LayoutChildren` 不能依賴在 `OnMeasure` 呼叫期間取得的子大小。 通常，`LayoutChildren` 本身必須在配置的子系上呼叫 `Measure`，或者您可以實作為一些大小的快取邏輯（稍後會討論）。

### <a name="an-easy-example"></a>簡單的範例

[**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含簡化的[`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)類別和其使用示範。

### <a name="vertical-and-horizontal-positioning-simplified"></a>簡化垂直和水準定位

`VerticalStack` 必須執行的其中一個作業會在 `LayoutChildren` 覆寫期間進行。 方法會使用子系的 `HorizontalOptions` 屬性，以決定如何在 `VerticalStack`中將子系定位在其位置內。 您可以改為呼叫靜態方法[`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))。 這個方法會呼叫子系上的 `Measure`，並使用其 `HorizontalOptions` 和 `VerticalOptions` 屬性，將子系放在指定的矩形內。

### <a name="invalidation"></a>無效

元素的屬性變更通常會影響該元素在配置中的顯示方式。 配置必須失效，才會觸發新的版面配置。

`VisualElement` 定義受保護的方法[`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)，這通常是由任何可系結屬性的屬性變更處理常式所呼叫，其變更會影響元素的大小。 `InvalidateMeasure` 方法會引發[`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件。

`Layout` 類別會定義名為[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)的類似受保護方法，其 `Layout` 的衍生應針對影響其定位和大小其子系的任何變更呼叫。

### <a name="some-rules-for-coding-layouts"></a>編碼版面配置的一些規則

1. `Layout<T>` 衍生項所定義的屬性應該由可系結屬性支援，而屬性變更的處理常式應該呼叫 `InvalidateLayout`。

2. 定義附加可系結屬性的 `Layout<T>` 衍生應該覆寫[`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) ，將屬性變更的處理常式加入其子系，並[`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*)移除該處理常式。 處理常式應該檢查這些附加的可系結屬性是否有變更，並藉由呼叫 `InvalidateLayout`來回應。

3. 實作為子大小快取的 `Layout<T>` 衍生應該覆寫 `InvalidateLayout`，並在呼叫這些方法時， [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)和清除快取。

### <a name="a-layout-with-properties"></a>具有屬性的版面配置

[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的[`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs)類別會假設其所有子系都是相同的大小，並將子系從一個資料列（或資料行）包裝到下一個。 它會定義類似 `StackLayout`的 `Orientation` 屬性，並 `ColumnSpacing` 和 `RowSpacing` 屬性（例如 `Grid`），並快取子大小。

[**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例會將 `WrapLayout` 放在 `ScrollView` 中，以顯示股票相片。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許無限制的維度！

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)主要是用來顯示其本身內的所有子系。 因此，它無法處理不受限制的維度，而且如果遇到例外狀況，就會引發例外狀況。

[**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範例會示範 `UniformGridLayout`：

[![相片方格的三向螢幕擷取畫面](images/ch26fg08-small.png "統一方格版面配置")](images/ch26fg08-large.png#lightbox "統一方格版面配置")

### <a name="overlapping-children"></a>重迭的子系

`Layout<T>` 的衍生可以重迭其子系。 不過，子系會依照其在 `Children` 集合中的順序轉譯，而不是呼叫其 `Layout` 方法的順序。

`Layout` 類別會定義兩個方法，可讓您在集合內移動子系：

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View))將子系移至集合的開頭
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View))將子系移至集合結尾

對於重迭的子系，集合結尾的子系會以視覺方式顯示在集合開頭的子系上方。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)類別會定義附加屬性來指示轉譯順序，因而允許其中一個子系顯示在其他專案的上方。 [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)範例會示範這種情況：

[![學生卡片檔案方格的三向螢幕擷取畫面](images/ch26fg10-small.png "重迭版面配置子系")](images/ch26fg10-large.png#lightbox "重迭版面配置子系")

### <a name="more-attached-bindable-properties"></a>更多附加可系結屬性

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)類別會定義附加的可系結屬性，以指定兩個 `Point` 值和粗細值，並將 `BoxView` 專案操作為類似行。

[**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)範例會使用它來繪製 3d cube。

### <a name="layout-and-layoutto"></a>版面配置和 LayoutTo

`Layout<T>` 的衍生可以呼叫 `LayoutTo` 而不是 `Layout` 來建立版面配置的動畫。 [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)類別會執行此工作，而[**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)範例會示範這種情況。

## <a name="related-links"></a>相關連結

- [第26章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第26章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
