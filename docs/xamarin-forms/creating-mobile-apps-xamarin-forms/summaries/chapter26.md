---
title： "第26章的摘要。 自訂配置的「描述：」建立 Mobile Apps Xamarin.Forms ：第26章的摘要。 自訂配置 "ms. 生產： xamarin ms. 技術： xamarin assetid： 2B7F4346-414E-49FF-97FB-B85E92D98A21 author： davidbritch ms-chap： dabritch ms. date： 11/07/2017 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-26-custom-layouts"></a>第26章的摘要。 自訂版面配置

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms包含數個衍生自的類別 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ：

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`，及
- `RelativeLayout`.

本章說明如何建立您自己的類別，衍生自 `Layout<View>` 。

## <a name="an-overview-of-layout"></a>版面配置總覽

沒有處理版面配置的集中式系統 Xamarin.Forms 。 每個元素都會負責判斷其本身的大小，以及如何在特定區域內呈現其本身。

### <a name="parents-and-children"></a>父和子系

具有子系的每個元素都會負責將這些子系放在本身內。 它是最終根據其可用大小和子系想要的大小，決定其子系之大小的父系。

### <a name="sizing-and-positioning"></a>調整大小和位置

版面配置會從視覺化樹狀結構的頂端與頁面開始，然後繼續進行所有分支。 版面配置中最重要的公用方法是 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形）） `VisualElement` 。 做為其他專案之父系的每個專案都會針對其子系，為 `Layout` 其子系提供相對於其本身的大小和位置（以值的形式） [`Rectangle`](xref:Xamarin.Forms.Rectangle) 。 這些 `Layout` 呼叫會透過視覺化樹狀結構傳播。

需要呼叫才能 `Layout` 讓元素出現在螢幕上，並會設定下列唯讀屬性。 它們會與 `Rectangle` 傳遞給方法的一致：

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)類型為`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)類型為`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)類型為`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)類型為`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)類型為`double`

在呼叫之前 `Layout` ， `Height` 和的 `Width` mock 值為 &ndash; 1。

呼叫也會 `Layout` 觸發下列受保護方法的呼叫：

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double))，其會呼叫
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double))，可以覆寫。

最後，會引發下列事件：

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`OnSizeAllocated`方法會由和覆 `Page` 寫 `Layout` ，這是中唯一 Xamarin.Forms 可以有子系的兩個類別。 覆寫的方法呼叫

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)針對 `Page` 衍生的 [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) 和 `Layout` 衍生的，其會呼叫
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))適用于 `Page` 衍生的和 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 衍生的 `Layout` 。

`LayoutChildren`然後呼叫 `Layout` 每個專案的子系。 如果至少有一個子系具有新的 `Bounds` 設定，則會引發下列事件：

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)適用于 `Page` 衍生的和 [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) `Layout` 衍生的

### <a name="constraints-and-size-requests"></a>條件約束和大小要求

`LayoutChildren`若要讓 `Layout` 在其所有子系上以智慧方式呼叫，它必須知道子系的*慣用*或*所需*大小。 因此， `Layout` 針對每個子系的呼叫通常會在呼叫之前

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

發行本書之後， `GetSizeRequest` 方法已被取代，並以

- [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags))

`Measure`方法會容納 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性，並包含類型的引數 [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags) ，其中包含兩個成員：

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)不包含邊界

針對許多元素， `GetSizeRequest` 或 `Measure` 從其轉譯器取得元素的原生大小。 這兩種方法都有 width 和 height*條件約束*的參數。 例如， `Label` 會使用 width 條件約束來決定如何包裝多行文字。

和都會傳回 `GetSizeRequest` `Measure` 類型的值 [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) ，其中包含兩個屬性：

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)類型為`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)類型為`Size`

這兩個值通常都相同，而且 `Minimum` 通常可以忽略此值。

`VisualElement`也會定義與 `GetSizeRequest` 從呼叫的受保護方法 `GetSizeRequest` ：

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))傳回 `SizeRequest` 值

該方法現在已被取代，並以取代：

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

衍生自或的每個類別都 `Layout` `Layout<T>` 必須覆寫 `OnSizeRequest` 或 `OnMeasure` 。 這是配置類別判斷其本身大小的位置，通常是根據其子系的大小，藉由在子系上呼叫或來取得 `GetSizeRequest` `Measure` 。 在呼叫或之前和之後 `OnSizeRequest` `OnMeasure` ， `GetSizeRequest` 或 `Measure` 根據下列屬性進行調整：

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)屬於類型的 `double` 會影響的 `Request` 屬性`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬於類型的 `double` 會影響的 `Request` 屬性`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)屬於類型的 `double` 會影響的 `Minimum` 屬性`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)屬於類型的 `double` 會影響的 `Minimum` 屬性`SizeRequest`

### <a name="infinite-constraints"></a>無限條件約束

傳遞至 `GetSizeRequest` （或 `Measure` ）和（或）的條件約束引數 `OnSizeRequest` `OnMeasure` 可以是無限的（也就是的值 `Double.PositiveInfinity` ）。 不過， `SizeRequest` 從這些方法傳回的不能包含無限維度。

無限條件約束表示要求的大小應該反映元素的自然大小。 垂直 `StackLayout` 呼叫 `GetSizeRequest` （或 `Measure` ）在其子系上具有無限高度條件約束。 水準堆疊配置會 `GetSizeRequest` `Measure` 在其子系上呼叫具有無限寬度條件約束的（或）。 `AbsoluteLayout` `GetSizeRequest` 其子系上的呼叫（或 `Measure` ）具有無限的寬度和高度條件約束。

### <a name="peeking-inside-the-process"></a>在進程內查看

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)會顯示簡單版面配置的條件約束和大小要求資訊。

## <a name="deriving-from-layoutview"></a>衍生自版面配置\<View>

自訂版面配置類別衍生自 `Layout<View>` 。 這有兩個責任：

- 覆寫 `OnMeasure` 以呼叫 `Measure` 所有版面配置的子系。 傳回版面配置本身要求的大小
- 覆寫 `LayoutChildren` 以呼叫 `Layout` 所有版面配置的子系

`for` `foreach` 這些覆寫中的或迴圈應該略過屬性設為的任何子系 `IsVisible` `false` 。

不保證對的呼叫 `OnMeasure` 。 `OnMeasure`如果配置的父系負責控管版面配置的大小（例如，填滿頁面的版面配置），則不會呼叫。 基於這個理由， `LayoutChildren` 無法依賴在呼叫期間取得的子大小 `OnMeasure` 。 通常， `LayoutChildren` 本身必須在配置 `Measure` 的子系上呼叫，或者您也可以實作為一種大小的快取邏輯（稍後會討論）。

### <a name="an-easy-example"></a>簡單的範例

[**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)範例包含簡化 [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) 的類別和其使用示範。

### <a name="vertical-and-horizontal-positioning-simplified"></a>簡化垂直和水準定位

必須執行的其中一個作業會在覆 `VerticalStack` 寫期間發生 `LayoutChildren` 。 方法會使用子系的 `HorizontalOptions` 屬性來決定如何在的位置中放置子系 `VerticalStack` 。 您可以改為呼叫靜態方法 [ `Layout.LayoutChildIntoBoundingRect` ] （x： Xamarin.Forms 。LayoutChildIntoBoundingRegion （ Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形））。 這個方法會 `Measure` 在子系上呼叫，並使用其 `HorizontalOptions` 和 `VerticalOptions` 屬性來定位指定矩形內的子系。

### <a name="invalidation"></a>無效

元素的屬性變更通常會影響該元素在配置中的顯示方式。 配置必須失效，才會觸發新的版面配置。

`VisualElement`定義受保護的方法 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) ，通常是由任何可系結屬性的屬性變更處理常式所呼叫，其變更會影響元素的大小。 `InvalidateMeasure`方法會引發 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 事件。

`Layout`類別會定義名為的類似受保護方法 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) ，其 `Layout` 衍生應針對影響其定位和大小其子系的任何變更呼叫。

### <a name="some-rules-for-coding-layouts"></a>編碼版面配置的一些規則

1. 衍生項所定義的屬性 `Layout<T>` 應該由可系結屬性支援，而屬性變更的處理常式應呼叫 `InvalidateLayout` 。

2. 定義附加可系結 `Layout<T>` 屬性的衍生應該覆寫 [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) ，將屬性變更的處理常式加入其子系，並 [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) 移除該處理常式。 處理常式應該檢查這些附加的可系結屬性是否有變更，並藉由呼叫來回應 `InvalidateLayout` 。

3. 實 `Layout<T>` 作為子大小快取的衍生應該覆寫 `InvalidateLayout` 和 [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) ，並在呼叫這些方法時清除快取。

### <a name="a-layout-with-properties"></a>具有屬性的版面配置

[`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的類別會假設其所有子系都是相同的大小，並將子系從一個資料列（或資料行）包裝到下一個。 它會定義 `Orientation` 屬性（例如 `StackLayout` ）、和 `ColumnSpacing` 屬性（ `RowSpacing` 例如 `Grid` ），而且它會快取子大小。

[**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)範例會將放 `WrapLayout` 入中 `ScrollView` ，以顯示股票相片。

### <a name="no-unconstrained-dimensions-allowed"></a>不允許無限制的維度！

[`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的可在其本身內顯示其所有子系。 因此，它無法處理不受限制的維度，而且如果遇到例外狀況，就會引發例外狀況。

[**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)範例會示範 `UniformGridLayout` ：

[![相片方格的三向螢幕擷取畫面](images/ch26fg08-small.png "統一方格版面配置")](images/ch26fg08-large.png#lightbox "統一方格版面配置")

### <a name="overlapping-children"></a>重迭的子系

`Layout<T>`衍生可以重迭其子系。 不過，子系會依照其在集合中的順序轉譯 `Children` ，而不是其方法的 `Layout` 呼叫順序。

`Layout`類別會定義兩個方法，可讓您在集合內移動子系：

- [ `LowerChild` ] （x： Xamarin.Forms 。LowerChild （ Xamarin.Forms 。View）將子系移至集合的開頭
- [ `RaiseChild` ] （x： Xamarin.Forms 。RaiseChild （ Xamarin.Forms 。View）將子系移至集合結尾

對於重迭的子系，集合結尾的子系會以視覺方式顯示在集合開頭的子系上方。

[`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會定義附加屬性，以指出轉譯順序，因而允許其中一個子系顯示在其他專案的上方。 [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)範例會示範這種情況：

[![學生卡片檔案方格的三向螢幕擷取畫面](images/ch26fg10-small.png "重迭版面配置子系")](images/ch26fg10-large.png#lightbox "重迭版面配置子系")

### <a name="more-attached-bindable-properties"></a>更多附加可系結屬性

[`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會定義附加的可系結屬性，以指定兩個 `Point` 值和粗細值，並 `BoxView` 將元素操作成類似的線條。

[**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)範例會使用它來繪製 3d cube。

### <a name="layout-and-layoutto"></a>版面配置和 LayoutTo

`Layout<T>`衍生可以呼叫 `LayoutTo` ，而不是 `Layout` 以動畫顯示版面配置。 [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)類別會執行此工作，而[**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)範例會示範這種情況。

## <a name="related-links"></a>相關連結

- [第26章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [第26章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
