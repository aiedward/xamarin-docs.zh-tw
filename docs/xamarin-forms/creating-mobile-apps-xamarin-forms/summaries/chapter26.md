---
title: "章 26 的摘要。 自訂配置"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9447f9fb47a3de0f278a89d45d657158be9b70b9
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-26-custom-layouts"></a>章 26 的摘要。 自訂配置

Xamarin.Forms 包含數個類別衍生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout`，及
* `RelativeLayout`.

本章節描述如何建立您自己的類別衍生自`Layout<View>`。

## <a name="an-overview-of-layout"></a>版面配置的概觀

沒有處理 Xamarin.Forms 配置沒有任何集中式的系統。 每個項目是負責決定其本身的大小應該是什麼，以及如何在特定區域中呈現其本身。

### <a name="parents-and-children"></a>父代和子系

每一個子系的項目會負責定位在本身內的子系。 它是最終決定大小為何其子系的父系，應該根據大小可供使用，而是要子系的大小。

### <a name="sizing-and-positioning"></a>調整大小和位置

版面配置頁的視覺化樹狀結構頂端開始，並繼續進行到所有分支。 版面配置中的最重要的公用方法是[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)所定義`VisualElement`。 每個項目，是為其他項目呼叫父`Layout`其大小和位置相對於本身的形式提供子系的子系的每個[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)值。 這些`Layout`呼叫傳播到整個視覺化樹狀結構。

呼叫`Layout`需要元素出現在畫面上，而導致設定下列唯讀屬性。 它們是一致的`Rectangle`傳遞至方法：

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) 型別 `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) 型別 `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) 型別 `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) 型別 `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) 型別 `double`

之前`Layout`呼叫，`Height`和`Width`有模擬值 & #x 2013; 1。

呼叫`Layout`也會觸發下列受保護的方法呼叫：

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/)而它會呼叫
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/)它會覆寫。

最後，會引發下列事件：

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

`OnSizeAllocated`方法會覆寫`Page`和`Layout`，這些是可以有子系的 Xamarin.Forms 中只有兩個類別。 覆寫的方法呼叫

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) 如`Page`衍生項目和[ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/)如`Layout`衍生項目，呼叫
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) 如`Page`衍生項目和[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)如`Layout`衍生項目。

`LayoutChildren` 然後呼叫`Layout`每個項目的子系。 如果至少一個子系有新`Bounds`設定，然後在引發下列事件：

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) 如`Page`衍生項目和[ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/)如`Layout`衍生項目

### <a name="constraints-and-size-requests"></a>條件約束和大小的要求

如`LayoutChildren`以聰明的方式呼叫`Layout`上所有子系，必須知道*慣用*或*所需*大小的子系。 因此在呼叫`Layout`子系的每個通常前面加上呼叫

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

發行活頁簿之後，`GetSizeRequest`方法已被取代，而且取代

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

`Measure`方法適合[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)屬性，並包含類型的引數[ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/)，其中包含兩個成員：

- [`IncludeMargins`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.IncludeMargins/)
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.None/) 不包含邊界

許多項目，如`GetSizeRequest`或`Measure`取得原生大小的項目從其轉譯器。 這兩種方法有參數的寬度和高度*條件約束*。 例如，`Label`會使用以判斷如何在多個文字換行的寬度限制。

同時`GetSizeRequest`和`Measure`傳回型別的值[ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/)，其中有兩個屬性：

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) 型別 `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) 型別 `Size`

這兩個值通常是相同的而`Minimum`通常可忽略的值。

`VisualElement` 也會定義受保護的方法類似於`GetSizeRequest`，而呼叫從`GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) 傳回`SizeRequest`值

該方法現在是已被取代，並取代：

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

每個類別衍生自`Layout`或`Layout<T>`必須覆寫`OnSizeRequest`或`OnMeasure`。 這是其中配置類別會決定其本身的大小，通常為基礎的子系，它會取得藉由呼叫大小`GetSizeRequest`或`Measure`的子系。 在呼叫前後`OnSizeRequest`或`OnMeasure`，`GetSizeRequest`或`Measure`會調整根據下列屬性：

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)型別的`double`，會影響`Request`屬性 `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) 型別的`double`，會影響`Request`屬性 `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) 型別的`double`，會影響`Minimum`屬性 `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) 型別的`double`，會影響`Minimum`屬性 `SizeRequest`

### <a name="infinite-constraints"></a>無限的條件約束

條件約束引數傳遞至`GetSizeRequest`(或`Measure`) 和`OnSizeRequest`(或`OnMeasure`) 可以是無限 (也就是值`Double.PositiveInfinity`)。 不過，`SizeRequest`傳回從這些方法不能包含無限的維度。

無限的條件約束表示所要求的大小應該會反映項目的原始大小。 垂直`StackLayout`呼叫`GetSizeRequest`(或`Measure`) 上具有無限高度條件約束的子系。 水平堆疊配置呼叫`GetSizeRequest`(或`Measure`) 上具有無限的寬度條件約束的子系。 `AbsoluteLayout`呼叫`GetSizeRequest`(或`Measure`) 上具有無限的寬度和高度條件約束的子系。

### <a name="peeking-inside-the-process"></a>處理序內其內

[ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)顯示條件約束和大小要求簡單配置資訊。

## <a name="deriving-from-layoutview"></a>衍生自版面配置<View>

自訂版面配置類別衍生自`Layout<View>`。 它具有兩個責任：

- 覆寫`OnMeasure`呼叫`Measure`版面配置的所有子系。 傳回本身的配置要求的大小
- 覆寫`LayoutChildren`呼叫`Layout`版面配置的所有子系

`for`或`foreach`這些覆寫中的迴圈應該略過任何子系的`IsVisible`屬性設定為`false`。

呼叫`OnMeasure`不保證。 `OnMeasure` 將不會呼叫如果父代的配置控管的配置大小 （例如，用來填滿頁面配置）。 基於這個理由，`LayoutChildren`期間取得的子系大小不能依賴`OnMeasure`呼叫。 通常，`LayoutChildren`本身必須呼叫`Measure`版面配置的子系，或您可以實作某種快取邏輯 （稍後會討論） 的大小。

### <a name="an-easy-example"></a>簡單的範例

[ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含的精簡型[ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)類別和其使用的示範。

### <a name="vertical-and-horizontal-positioning-simplified"></a>垂直和水平定位簡化

其中一個作業，`VerticalStack`必須執行期間發生`LayoutChildren`覆寫。 此方法會使用子系的`HorizontalOptions`屬性來決定如何放置在其位置內的子系`VerticalStack`。 您可以改為呼叫靜態方法[ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/)。 這個方法會呼叫`Measure`上子系，並使用其`HorizontalOptions`和`VerticalOptions`放置在指定的矩形內的子屬性。

### <a name="invalidation"></a>失效

通常的項目屬性的變更會影響該項目在版面配置中的顯示方式。 配置必須是以觸發新的版面配置失效。

`VisualElement` 定義受保護的方法[ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/)，通常稱為屬性變更處理常式，任何可繫結屬性的變更影響的項目大小。 `InvalidateMeasure`方法引發[ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/)事件。

`Layout`類別會定義名為類似的受保護的方法[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)、 哪些`Layout`衍生項目應該呼叫會影響如何定位和調整大小及其子系的任何變更。

### <a name="some-rules-for-coding-layouts"></a>一些編碼配置的規則

1. 所定義的屬性`Layout<T>`衍生項目應該要備份的可繫結的屬性和屬性變更處理常式應該呼叫`InvalidateLayout`。

2. A`Layout<T>`定義附加的繫結屬性的衍生項目應該覆寫[ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/)將屬性變更處理常式新增至其子系和[ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/)移除，處理常式。 此處理常式應該檢查是否有變更，在這些連接可繫結屬性和回應的呼叫`InvalidateLayout`。

3. A`Layout<T>`實作子系大小的快取的衍生項目應該覆寫`InvalidateLayout`和[ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/)和呼叫這些方法時清除快取。

### <a name="a-layout-with-properties"></a>版面配置與屬性

[ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)假設所有子系是相同的大小，和會子系從一個資料列 （或資料行）下一步。 它會定義`Orientation`屬性視為`StackLayout`，和`ColumnSpacing`和`RowSpacing`屬性，例如`Grid`，和它快取子系的大小。

[ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例置於`WrapLayout`中`ScrollView`顯示內建的相片。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許未受限制的維度 ！

[ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫要用來顯示本身內的所有子系。 因此，它無法處理的未受限制的維度，並引發例外狀況，如果發生其中一個。

[ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範例將示範如何`UniformGridLayout`:

[![相片方格的三個螢幕擷取畫面](images/ch26fg08-small.png "統一格線版面配置")](images/ch26fg08-large.png#lightbox "統一格線版面配置")

### <a name="overlapping-children"></a>重疊的子系

A`Layout<T>`衍生項目可以重疊的子系。 但是，在順序中轉譯子系`Children`集合，並在其中的順序及其`Layout`呼叫的方法。

`Layout`類別會定義兩個方法可讓您將集合內的子系：

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) 將子系移至集合的開頭
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) 將子系移至集合的結尾

重疊的子系的集合結尾的子系以視覺化方式顯示在集合開頭的子系上方。

[ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫會定義表示轉譯順序，並因此允許其中一個附加的屬性其要顯示在頂端，其他的子系。 [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)範例為其示範：

[![學生卡檔案格線的三個螢幕擷取畫面](images/ch26fg10-small.png "重疊配置子系")](images/ch26fg10-large.png#lightbox "重疊配置子系")

### <a name="more-attached-bindable-properties"></a>連接可繫結屬性

[ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫定義附加可繫結的屬性，以指定兩個`Point`值和粗細值及操作`BoxView`類似於行項目。

[ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)範例會使用該繪製 3D 立方體。

### <a name="layout-and-layoutto"></a>版面配置和 LayoutTo

A`Layout<T>`衍生項目可以呼叫`LayoutTo`而不是`Layout`以動畫方式顯示版面配置。 [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)類別會執行，而[ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)範例會示範它。



## <a name="related-links"></a>相關連結

- [章 26 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [章 26 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
