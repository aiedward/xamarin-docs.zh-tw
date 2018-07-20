---
title: 第 26 章的摘要。 自訂版面配置
description: 使用 Xamarin.Forms 建立行動應用程式： 第 26 章摘要。 自訂版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: bdd86595c3c0805d50241eac3a131a50656a9985
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156583"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>第 26 章的摘要。 自訂版面配置

Xamarin.Forms 包含數個類別衍生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout`，及
* `RelativeLayout`.

本章節描述如何建立您自己的類別衍生自`Layout<View>`。

## <a name="an-overview-of-layout"></a>版面配置的概觀

沒有任何集中式的系統，可以處理 Xamarin.Forms 版面配置。 每個項目會負責判斷本身的大小應該是什麼，以及如何在特定區域內呈現其本身。

### <a name="parents-and-children"></a>父代和子系

每個項目子系會負責定位這些子系本身內。 它是最終決定了什麼大小及其子系的父系，應該根據大小可以使用，而且想要的子系的大小。

### <a name="sizing-and-positioning"></a>大小及定位

版面配置頁面的視覺化樹狀結構頂端開始，並繼續進行到所有分支。 最重要的公用方法，在配置中是[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))所定義`VisualElement`。 每個項目，是其他項目呼叫的父`Layout`針對每個子系的大小和位置相對於本身的形式提供子系[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)值。 這些`Layout`呼叫傳遍視覺化樹狀結構。

呼叫`Layout`是必要項目會出現在畫面上，並會導致下列的唯讀屬性，以設定。 它們都必須配合`Rectangle`傳遞至方法：

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) 型別 `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) 型別 `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) 型別 `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) 型別 `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) 型別 `double`

之前`Layout`呼叫時，`Height`並`Width`模擬 （mock） 的值為&ndash;1。

呼叫`Layout`也會觸發下列受保護的方法呼叫：

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double))其會呼叫
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))其中可以覆寫。

最後，會引發下列事件：

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`OnSizeAllocated`方法會覆寫`Page`和`Layout`，這些是可以有子系的 Xamarin.Forms 中只有兩個類別。 覆寫的方法呼叫

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) 針對`Page`衍生項目和[ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout)如`Layout`衍生項目，它會呼叫
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 針對`Page`衍生項目和[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))如`Layout`衍生項目。

`LayoutChildren` 然後呼叫`Layout`針對每個項目的子系。 如果至少一個子系有新`Bounds`設定，則會引發下列事件：

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) 針對`Page`衍生項目和[ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged)如`Layout`衍生項目

### <a name="constraints-and-size-requests"></a>條件約束和大小的要求

針對`LayoutChildren`聰明地呼叫`Layout`在其所有子系，它必須知道*慣用*或*所需*大小的子系。 因此的呼叫`Layout`的每個子系通常前面會加上呼叫

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

發行活頁簿之後，`GetSizeRequest`方法已被取代，並取代為

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

`Measure`方法所能容納[ `Margin` ](xref:Xamarin.Forms.View.Margin)屬性，並包含類型的引數[ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags)，其中包含兩個成員：

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) 不包含邊界

許多項目，如`GetSizeRequest`或`Measure`從其轉譯器取得原生大小的項目。 這兩種方法有參數的寬度和高度*條件約束*。 比方說，`Label`會使用寬度條件約束，來決定如何包裝多行文字。

兩者`GetSizeRequest`並`Measure`傳回型別的值[ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest)，其中包含兩個屬性：

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) 型別 `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) 型別 `Size`

通常這兩個值都相同，而`Minimum`通常可忽略的值。

`VisualElement` 也會定義受保護的方法類似`GetSizeRequest`呼叫從`GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) 傳回`SizeRequest`值

該方法現在是已被取代，並取代為：

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

每個類別衍生自`Layout`或是`Layout<T>`必須覆寫`OnSizeRequest`或`OnMeasure`。 這是其中配置類別會決定其本身的大小，通常根據大小及其子系，它會取得藉由呼叫`GetSizeRequest`或`Measure`的子系。 在呼叫前後`OnSizeRequest`或是`OnMeasure`，`GetSizeRequest`或`Measure`會根據下列屬性的調整：

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)型別的`double`，會影響`Request`屬性 `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 型別的`double`，會影響`Request`屬性 `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) 型別的`double`，會影響`Minimum`屬性 `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) 型別的`double`，會影響`Minimum`屬性 `SizeRequest`

### <a name="infinite-constraints"></a>無限的條件約束

條件約束引數傳遞給`GetSizeRequest`(或`Measure`) 和`OnSizeRequest`(或`OnMeasure`) 可以是無限 (亦即，值`Double.PositiveInfinity`)。 不過，`SizeRequest`傳回從這些方法不能包含無限的維度。

無限的條件約束表示所要求的大小應該會反映此項目的原始大小。 垂直`StackLayout`呼叫`GetSizeRequest`(或`Measure`) 上具有無限的高度限制式及其子系。 水平堆疊版面配置會呼叫`GetSizeRequest`(或`Measure`) 上具有無限的寬度條件約束及其子系。 `AbsoluteLayout`呼叫`GetSizeRequest`(或`Measure`) 上具有無限的寬度和高度條件約束及其子系。

### <a name="peeking-inside-the-process"></a>處理序內查看

[ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)顯示條件約束和大小要求的簡單版面配置資訊。

## <a name="deriving-from-layoutview"></a>衍生自版面配置<View>

自訂版面配置類別衍生自`Layout<View>`。 它有兩個責任：

- 覆寫`OnMeasure`呼叫`Measure`版面配置的所有子系。 傳回要求的大小，本身的版面配置
- 覆寫`LayoutChildren`呼叫`Layout`版面配置的所有子系

`for`或是`foreach`這些覆寫中的迴圈應該略過任何子系其`IsVisible`屬性設定為`false`。

呼叫`OnMeasure`不保證。 `OnMeasure` 將不會呼叫如果配置的父代為控管的配置大小 （例如，用來填滿頁面配置）。 基於這個理由，`LayoutChildren`期間取得的子系大小不能依賴`OnMeasure`呼叫。 通常`LayoutChildren`本身必須呼叫`Measure`版面配置的子系，或者您可以實作某種形式的快取的邏輯 （稍後討論） 的大小。

### <a name="an-easy-example"></a>簡單的範例

[ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含簡化[ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)類別和其使用的示範。

### <a name="vertical-and-horizontal-positioning-simplified"></a>垂直和水平定位簡化

其中一個作業，`VerticalStack`必須執行期間發生`LayoutChildren`覆寫。 此方法會使用子系`HorizontalOptions`屬性來判斷如何放置在其位置內的子系`VerticalStack`。 您可以改為呼叫靜態方法[ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))。 這個方法會呼叫`Measure`上的子系，並使用其`HorizontalOptions`和`VerticalOptions`放置在指定矩形內的子系的屬性。

### <a name="invalidation"></a>失效

通常的項目屬性的變更會影響該項目在版面配置中的顯示方式。 配置必須是以觸發新的版面配置失效。

`VisualElement` 定義受保護的方法[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)，通常稱為屬性變更處理常式的可繫結的任何屬性的變更會影響項目的大小。 `InvalidateMeasure`方法會引發[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件。

`Layout`類別會定義名為類似受保護的方法[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)，`Layout`衍生項目應該呼叫其位置和大小及其子系的方式會影響任何變更。

### <a name="some-rules-for-coding-layouts"></a>一些編碼配置的規則

1. 所定義的屬性`Layout<T>`衍生物件應受到可繫結的屬性和屬性變更處理常式應該呼叫`InvalidateLayout`。

2. A`Layout<T>`定義可繫結的附加的屬性的衍生項目應該覆寫[ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*)將屬性變更處理常式新增至子系並[ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*)移除，處理常式。 這個處理常式應該檢查是否有變更，在這些連結可繫結屬性，並藉由呼叫回應`InvalidateLayout`。

3. A`Layout<T>`實作子系大小的快取的衍生項目應該覆寫`InvalidateLayout`並[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)和呼叫這些方法時清除快取。

### <a name="a-layout-with-properties"></a>版面配置屬性

[ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)假設所有子系是相同的大小，和會子系從一個資料列 （或資料行）下一步。 它會定義`Orientation`的屬性，例如`StackLayout`，和`ColumnSpacing`並`RowSpacing`屬性，例如`Grid`，和快取子系的大小。

[ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例 puts`WrapLayout`在`ScrollView`顯示內建的相片。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許未受限制的維度 ！

[ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫要用來顯示本身內的所有子系。 因此，它無法處理的未受限制的維度，並引發例外狀況，如果其中一個發生。

[ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範例會示範`UniformGridLayout`:

[![三重的螢幕擷取畫面的相片方格](images/ch26fg08-small.png "統一的格線版面配置")](images/ch26fg08-large.png#lightbox "統一的格線版面配置")

### <a name="overlapping-children"></a>重疊的子系

A`Layout<T>`衍生項目可以重疊及其子系。 不過，子系會呈現在其順序`Children`集合，並在其中的順序及其`Layout`方法受到呼叫。

`Layout`類別會定義兩個方法，容許您移動集合內的子系：

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) 若要將子系移至集合的開頭
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) 若要將子系移至集合的結尾

重疊的子系集合結尾的子系以視覺化方式顯示於最上層集合開頭的子系。

[ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫定義附加的屬性以表示轉譯順序，並因此允許其中一個其要顯示在頂端的其他子系。 [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)示範這項範例：

[![三重的螢幕擷取畫面的學生卡檔案方格](images/ch26fg10-small.png "重疊的版面配置子系")](images/ch26fg10-large.png#lightbox "重疊的版面配置子系")

### <a name="more-attached-bindable-properties"></a>多個連結可繫結屬性

[ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫定義附加可繫結的屬性，以指定兩個`Point`值和粗細值及操作`BoxView`類似於行的項目。

[ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)範例所使用的是，若要繪製 3D 立方體。

### <a name="layout-and-layoutto"></a>版面配置和 LayoutTo

A`Layout<T>`衍生項目可以呼叫`LayoutTo`而非`Layout`以動畫顯示版面配置。 [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)類別會執行，而[ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)範例會示範它。



## <a name="related-links"></a>相關連結

- [第 26 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第 26 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂的版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
