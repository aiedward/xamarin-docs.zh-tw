---
標題：「在下列描述中建立自訂版面配置：」本文 Xamarin.Forms 說明如何撰寫自訂的版面配置類別，並示範在頁面上水準排列其子系的面向方向 WrapLayout 類別，然後將後續子系的顯示內容包裝到其他資料列。」
assetid： B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/29/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="create-a-custom-layout-in-xamarinforms"></a>在中建立自訂版面配置Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin 會定義五個版面配置類別，分別是 StackLayout、AbsoluteLayout、RelativeLayout、Grid 和 FlexLayout，而且每個都以不同的方式排列其子系。不過，有時候您必須使用不是由提供的版面配置來組織頁面內容 Xamarin.Forms 。本文說明如何撰寫自訂的版面配置類別，並示範面向方向的 WrapLayout 類別，它會在頁面上水準排列其子系，然後將後續子系的顯示包裝到其他資料列。_

在中 Xamarin.Forms ，所有版面配置類別都是衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別，並將泛型型別限制為 [`View`](xref:Xamarin.Forms.View) 和其衍生類型。 接著， `Layout<T>` 類別衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別，它會提供定位和調整大小子項目的機制。

每個視覺元素都會負責判斷自己偏好的大小，也就是所*要求*的大小。 [`Page`](xref:Xamarin.Forms.Page)、 [`Layout`](xref:Xamarin.Forms.Layout) 和衍生型別 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 負責判斷其子系的位置和大小，或子系相對於本身的位置。 因此，版面配置牽涉到父子式關聯性，其中父代會決定其子系的大小，但會嘗試配合要求的子系大小。

Xamarin.Forms若要建立自訂版面配置，必須徹底瞭解版面配置和失效週期。 現在將會討論這些週期。

## <a name="layout"></a>Layout

版面配置會從視覺化樹狀結構的頂端開始，並具有頁面，並會繼續執行視覺化樹狀結構的所有分支，以包含頁面上的每個視覺元素。 身為其他專案之父系的元素會負責調整大小，並將其子系相對於其本身。

[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別會定義 [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法來測量版面配置作業的元素，而 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法，指定要在其中轉譯元素的矩形區域。 當應用程式啟動並顯示第一頁時，由第一個呼叫所組成的*版面配置週期* `Measure` ，接著會 `Layout` 呼叫，並在物件上啟動 [`Page`](xref:Xamarin.Forms.Page) ：

1. 在版面配置週期期間，每個父項目都會負責 `Measure` 在其子系上呼叫方法。
1. 在測量子系之後，每個父項目都會負責 `Layout` 在其子系上呼叫方法。

這個迴圈可確保頁面上的每個視覺專案都會收到 `Measure` 和方法的呼叫 `Layout` 。 下圖顯示此程式：

![](custom-images/layout-cycle.png "Xamarin.Forms Layout Cycle")

> [!NOTE]
> 請注意，如果變更會影響版面配置，版面配置週期也可能出現在視覺化樹狀結構的子集上。 這包括新增或移除集合中的專案，例如中的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 、 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 元素的屬性變更，或元素大小的變更。

Xamarin.Forms具有或屬性的每個類別 `Content` `Children` 都有可覆寫的 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法。 衍生自的自訂版面配置類別 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必須覆寫此方法，並確定 [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））和 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法會在所有專案的子系上呼叫，以提供所需的自訂版面配置。

此外，衍生自或的每個類別都 [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必須覆寫 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法，這是配置類別透過呼叫 [ `Measure` ] （x：）來判斷其所需的大小 Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法的子系。

> [!NOTE]
> 元素會根據*條件約束*來決定其大小，這表示元素的父系中的專案有多少可用空間。 傳遞給 [ `Measure` ] （x：）的條件約束 Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法的範圍可以從0到 `Double.PositiveInfinity` 。 當元素收到對其 [] （x：的呼叫時，會*受到限制*或*完全條件約束* `Measure` Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法，其中包含非無限的引數-元素會限制為特定的大小。 當元素收到其方法的呼叫，且其中至少有一個引數等於時，該專案會受到*限制*或*部分條件約束* `Measure` `Double.PositiveInfinity` –可以將無限條件約束視為表示自動調整大小。

## <a name="invalidation"></a>無效

「失效」是頁面上專案中的變更會觸發新版面配置週期的進程。 當元素不再具有正確的大小或位置時，會將其視為無效。 例如，如果的 [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 屬性 [`Button`](xref:Xamarin.Forms.Button) 變更，則會被視為 `Button` 無效，因為它將不再具有正確的大小。 調整大小 `Button` 之後，可能會在頁面的其餘部分變更版面配置的 ripple 效果。

元素會藉由叫用方法來使其本身失效 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) ，通常是當元素的屬性變更時，可能會導致元素的新大小。 這個方法會引發 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 事件，以觸發新的版面配置週期，此專案的父代會進行處理。

[`Layout`](xref:Xamarin.Forms.Layout)類別 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 會針對加入其屬性或集合的每個子節點設定事件的處理常式 `Content` `Children` ，並在移除子系時卸離處理常式。 因此，視覺樹狀結構中具有子系的每個專案都會在其中一個子系變更大小時收到警示。 下圖說明在視覺化樹狀結構中，元素大小的變更會如何導致在樹狀結構上出現變更：

![](custom-images/invalidation.png "Invalidation in the Visual Tree")

不過， `Layout` 類別會嘗試限制子系大小在頁面版面配置上的變更影響。 如果配置大小受到限制，則子大小變更不會影響任何高於視覺化樹狀結構中父配置的專案。 不過，配置大小的變更通常會影響版面配置如何排列其子系。 因此，配置大小的任何變更都會啟動配置的版面配置週期，而配置會接收其 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 和方法的呼叫 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 。

[`Layout`](xref:Xamarin.Forms.Layout)類別也會定義與 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法具有類似用途的方法 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) 。 `InvalidateLayout`每當變更時，就應該叫用方法，這會影響版面配置位置和其子系的大小。 例如， `Layout` `InvalidateLayout` 每次在配置中加入或移除子系時，類別就會叫用方法。

[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)可以覆寫以執行快取，以將 [ `Measure` ] （x：的重複調用降至最低 Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法，其為版面配置的子系。 覆寫 `InvalidateLayout` 方法會提供在配置中加入或移除子系的通知。 同樣地， [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 可以覆寫方法，以便在其中一個版面配置的子系變更大小時提供通知。 對於這兩種方法覆寫，自訂配置應該藉由清除快取來回應。 如需詳細資訊，請參閱[計算和](#calculate-and-cache-layout-data)快取版面配置資料。

## <a name="create-a-custom-layout"></a>建立自訂版面配置

建立自訂版面配置的流程如下所示：

1. 建立從 `Layout<View>` 類別衍生的類別。 如需詳細資訊，請參閱[Create a WrapLayout](#create-a-wraplayout)。
1. [*選用*]針對應在版面配置類別上設定的任何參數，加入可系結屬性所支援的屬性。 如需詳細資訊，請參閱加入可系結[屬性所支援的屬性](#add-properties-backed-by-bindable-properties)。
1. 覆寫 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法以叫用 [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法對所有版面配置的子系，並傳回所要求的版面配置大小。 如需詳細資訊，請參閱覆[寫 OnMeasure 方法](#override-the-onmeasure-method)。
1. 覆寫 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法以叫用 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法來呈現所有版面配置的子系。 無法叫用 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法，在版面配置中的每個子系上，子系永遠不會收到正確的大小或位置，因此子系不會在頁面上顯示。 如需詳細資訊，請參閱覆[寫 LayoutChildren 方法](#override-the-layoutchildren-method)。

    > [!NOTE]
    > 列舉和覆寫中的子系時 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) ，略過 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 屬性設定為的任何子系 `false` 。 這可確保自訂配置不會為不可見的子系留下空間。

1. [*選用*]覆寫 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法，以在子系加入至配置或從配置中移除時收到通知。 如需詳細資訊，請參閱覆[寫 InvalidateLayout 方法](#override-the-invalidatelayout-method)。
1. [*選用*]覆寫 [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 方法，以在其中一個版面配置的子系變更大小時收到通知。 如需詳細資訊，請參閱覆[寫 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

> [!NOTE]
> 請注意， [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 如果配置的大小是由其父系（而非其子系）所控制，則不會叫用覆寫。 不過，如果其中一個或兩個條件約束都是無限的，或如果版面配置類別具有非預設 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 或屬性值，則會叫用覆寫 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 基於這個理由，覆 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 寫不能依賴在方法呼叫期間取得的子大小 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 。 相反地，必須叫用 `LayoutChildren` [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））方法在配置的子系上，再叫用 [ `Layout` ] （x： Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法。 或者，您可以快取在覆寫中取得的子系大小， `OnMeasure` 以避免稍後 `Measure` 在覆寫中叫用 `LayoutChildren` ，但配置類別必須知道何時需要重新取得大小。 如需詳細資訊，請參閱[計算和](#calculate-and-cache-layout-data)快取版面配置資料。

然後，您可以將配置類別加入至 [`Page`](xref:Xamarin.Forms.Page) ，並將子系加入至配置中，藉此取用該版面配置。 如需詳細資訊，請參閱[使用 WrapLayout](#consume-the-wraplayout)。

### <a name="create-a-wraplayout"></a>建立 WrapLayout

範例應用程式會示範面向方向 `WrapLayout` 的類別，它會在頁面上水準排列其子系，然後將後續子系的顯示包裝到其他資料列。

`WrapLayout`類別會根據子系的大小上限，為每個子系（也稱為資料*格大小*）配置相同的空間量。 小於資料格大小的子系可以根據其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性值放置在資料格中 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。

`WrapLayout`類別定義如下列程式碼範例所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>計算和快取版面配置資料

結構會將 `LayoutData` 子系集合的相關資料儲存在數個屬性中：

- `VisibleChildCount`–在版面配置中可見的子係數目。
- `CellSize`–所有子系的大小上限，已調整為版面配置的大小。
- `Rows`–資料列的數目。
- `Columns`–資料行的數目。

`layoutDataCache`欄位是用來儲存多個 `LayoutData` 值。 當應用程式啟動時， `LayoutData` 會將兩個物件快取到 `layoutDataCache` 目前方向的字典中–一個用於覆寫的條件約束引數 `OnMeasure` ，另一個用於覆 `width` 寫的和 `height` 引數 `LayoutChildren` 。 將裝置旋轉為橫向時，會再次叫用覆 `OnMeasure` 寫和覆 `LayoutChildren` 寫，這會導致將另兩個 `LayoutData` 物件快取到字典中。 不過，將裝置傳回直向方向時，不需要進一步的計算，因為 `layoutDataCache` 已經有必要的資料。

下列程式碼範例示範 `GetLayoutData` 方法，其會 `LayoutData` 根據特定大小來計算結構化的屬性：

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

`GetLayoutData`方法會執行下列作業：

- 它會判斷計算 `LayoutData` 值是否已在快取中，並在有提供時傳回。
- 否則，它會列舉所有子系， `Measure` 在每個子系上叫用具有無限寬度和高度的方法，並決定子大小的最大值。
- 如果至少有一個可見的子系，則會計算所需的資料列和資料行數目，然後根據的維度來計運算元系的資料格大小 `WrapLayout` 。 請注意，資料格大小通常會比子大小的最大值稍微寬一點，但如果寬度不足，或夠高的子系，則可能也會較小 `WrapLayout` 。
- 它會將新的值儲存在快取 `LayoutData` 中。

#### <a name="add-properties-backed-by-bindable-properties"></a>新增可系結屬性所支援的屬性

`WrapLayout`類別會定義 `ColumnSpacing` 和 `RowSpacing` 屬性，其值會用來分隔配置中的資料列和資料行，並由可系結屬性支援。 可系結屬性如下列程式碼範例所示：

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

每個可系結屬性的屬性變更處理常式都會叫 `InvalidateLayout` 用方法覆寫，以觸發上的新版面配置傳遞 `WrapLayout` 。 如需詳細資訊，請參閱覆[寫 InvalidateLayout 方法](#override-the-invalidatelayout-method)和覆[寫 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

#### <a name="override-the-onmeasure-method"></a>覆寫 OnMeasure 方法

`OnMeasure`下列程式碼範例會顯示覆寫：

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

覆寫會叫 `GetLayoutData` 用方法，並 `SizeRequest` 從傳回的資料中建立物件，同時也將 `RowSpacing` 和 `ColumnSpacing` 屬性值納入考慮。 如需方法的詳細資訊 `GetLayoutData` ，請參閱[計算和](#calculate-and-cache-layout-data)快取版面配置資料。

> [!IMPORTANT]
> [ `Measure` ] （X： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags））和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法絕不能藉由傳回屬性設為的值，來要求無限維度 [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) `Double.PositiveInfinity` 。 不過，的條件約束引數中至少有一個 `OnMeasure` 可以是 `Double.PositiveInfinity` 。

#### <a name="override-the-layoutchildren-method"></a>覆寫 LayoutChildren 方法

`LayoutChildren`下列程式碼範例會顯示覆寫：

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

覆寫會從方法的呼叫開始 `GetLayoutData` ，然後列舉所有子系的大小，並將它們放置在每個子系的資料格中。 這是藉由叫用 [ `LayoutChildIntoBoundingRegion` ] （x：）來達成 Xamarin.Forms 。LayoutChildIntoBoundingRegion （ Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形））方法，其會根據其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性值，在矩形內放置子系 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 這相當於對子系的 [ `Layout` ] （x：）進行呼叫 Xamarin.Forms 。VisualElement。版面配置（ Xamarin.Forms 。矩形））方法。

> [!NOTE]
> 請注意，傳遞至方法的矩形 `LayoutChildIntoBoundingRegion` 包含子系可以位於的整個區域。

如需方法的詳細資訊 `GetLayoutData` ，請參閱[計算和](#calculate-and-cache-layout-data)快取版面配置資料。

#### <a name="override-the-invalidatelayout-method"></a>覆寫 InvalidateLayout 方法

在配置中 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 加入或移除子系時，或其中一個 `WrapLayout` 屬性變更值時，就會叫用覆寫，如下列程式碼範例所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

覆寫會使配置失效，並捨棄所有快取的版面配置資訊。

> [!NOTE]
> 若要在 [`Layout`](xref:Xamarin.Forms.Layout) 每次在配置中 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 加入或移除子系時，停止叫用方法的類別，請覆寫 [ `ShouldInvalidateOnChildAdded` ] （x： Xamarin.Forms 。ShouldInvalidateOnChildAdded （ Xamarin.Forms 。View））和 [ `ShouldInvalidateOnChildRemoved` ] （x： Xamarin.Forms 。ShouldInvalidateOnChildRemoved （ Xamarin.Forms 。View））方法，並傳回 `false` 。 然後，當加入或移除子系時，配置類別就可以執行自訂程式。

#### <a name="override-the-onchildmeasureinvalidated-method"></a>覆寫 OnChildMeasureInvalidated 方法

[`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)當其中一個版面配置的子系變更大小時，會叫用覆寫，如下列程式碼範例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

覆寫會使子版面配置失效，並捨棄所有快取的版面配置資訊。

### <a name="consume-the-wraplayout"></a>使用 WrapLayout

藉 `WrapLayout` 由將類別放在衍生型別上，即可加以取用 [`Page`](xref:Xamarin.Forms.Page) ，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

對等的 c # 程式碼如下所示：

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

然後，可以視需要將子系新增至 `WrapLayout` 。 下列程式碼範例顯示 [`Image`](xref:Xamarin.Forms.Image) 要新增至的專案 `WrapLayout` ：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

當包含的頁面 `WrapLayout` 出現時，範例應用程式會以非同步方式存取包含相片清單的遠端 JSON 檔案、 [`Image`](xref:Xamarin.Forms.Image) 為每張相片建立一個專案，並將其新增至 `WrapLayout` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

![](custom-images/portait-screenshots.png "Sample Application Portrait Screenshots")

下列螢幕擷取畫面顯示 `WrapLayout` 已旋轉為橫向方向之後：

![](custom-images/landscape-ios.png "Sample iOS Application Landscape Screenshot")
![](custom-images/landscape-android.png "Sample Android Application Landscape Screenshot")
![](custom-images/landscape-uwp.png "Sample UWP Application Landscape Screenshot")

每個資料列中的資料行數目取決於相片大小、螢幕寬度，以及每個裝置獨立單位的圖元數目。 專案 [`Image`](xref:Xamarin.Forms.Image) 會以非同步方式載入相片，因此， `WrapLayout` 類別會經常收到其方法的呼叫， [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 因為每個 `Image` 元素都會根據已載入的相片接收新的大小。

## <a name="related-links"></a>相關連結

- [WrapLayout （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [自訂配置](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [在中建立自訂版面配置 Xamarin.Forms （影片）](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [Layout\<T>](xref:Xamarin.Forms.Layout`1)
- [配置](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
