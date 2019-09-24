---
title: 在 Xamarin 中建立自訂版面配置
description: 本文說明如何撰寫自訂的版面配置的類別，並示範如何在頁面上，水平排列其子系，然後將包裝至其他資料列的後續子系的顯示方向區分 WrapLayout 類別。
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 9442116bf053768fe22d123d1c7e3d146f9b2834
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198503"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>在 Xamarin 中建立自訂版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin 會定義五個版面配置類別，分別是 StackLayout、AbsoluteLayout、RelativeLayout、Grid 和 FlexLayout，而且每個都以不同的方式排列其子系。不過，有時候就必須以組織使用不提供 Xamarin.Forms 版面配置的頁面內容。本文說明如何撰寫自訂的版面配置的類別，並示範如何在頁面上，水平排列其子系，然後將包裝至其他資料列的後續子系的顯示方向區分 WrapLayout 類別。_

在 Xamarin.Forms 中，所有的版面配置類別衍生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)類別，並將泛型類型，以限制[ `View` ](xref:Xamarin.Forms.View)和其衍生的類型。 依次`Layout<T>`類別衍生自[ `Layout` ](xref:Xamarin.Forms.Layout)類別，可提供機制，用來定位和調整大小的子元素。

每個視覺項目會負責判斷自己慣用的大小，就所謂*要求*大小。 [`Page`](xref:Xamarin.Forms.Page)[ `Layout` ](xref:Xamarin.Forms.Layout)，以及[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)衍生型別會負責判斷及其子系或子系，相對於本身的大小與位置。 因此，版面配置包含父子式關聯性，其中的父代決定其子系的大小應該是什麼，但會嘗試以容納子項目的所要求的大小。

徹底了解 Xamarin.Forms 版面配置和失效循環，才能建立自訂的版面配置。 現在將討論這些循環。

## <a name="layout"></a>配置

版面配置開始與頁面的視覺化樹狀結構頂端，並繼續進行到包含每個頁面上的 visual 元素的視覺化樹狀結構的所有分支。 其他元素的父代的項目負責大小及定位相對於本身及其子系。

[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別會定義[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))方法，可計算配置作業的項目和[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))指定的方法項目會呈現在矩形區域。 當應用程式啟動並顯示第一個頁面，*配置循環*所組成的第一個`Measure`呼叫，然後`Layout`呼叫時，開始上[ `Page` ](xref:Xamarin.Forms.Page)物件：

1. 在版面配置週期中，每個父項目會負責呼叫`Measure`方法及其子系。
1. 每個父項目子系測量之後，會負責呼叫`Layout`方法及其子系。

這個循環可確保每個頁面上的 visual 元素會接收呼叫`Measure`和`Layout`方法。 下圖顯示程序：

![](custom-images/layout-cycle.png "Xamarin.Forms 版面配置循環")

> [!NOTE]
> 請注意，配置循環也可能發生的視覺化樹狀結構子集上某個項目變更影響版面配置。 這包括正在加入或移除，例如，在集合中的項目[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，變更[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)項目或項目的大小變更的屬性。

每個 Xamarin.Forms 類別具有`Content`或是`Children`屬性具有可覆寫[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法。 自訂版面配置的類別衍生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)必須覆寫這個方法，並確保[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))並[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法呼叫所有項目的子系，以提供所需的自訂版面配置。

此外，每個類別衍生自[ `Layout` ](xref:Xamarin.Forms.Layout)或是[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)必須覆寫[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法，這是其中一個配置類別它必須是藉由呼叫的大小會決定[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))其子節點的方法。

> [!NOTE]
> 項目可決定根據其大小*條件約束*，這表示的空間可供內項目的父代的項目。 條件約束會傳遞給[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))並[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法的範圍可以從 0 到`Double.PositiveInfinity`。 元素是*限制*，或*完全限制*收到呼叫時，其[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))方法與非無限引數-項目會受到限制若要在特定的大小。 元素是*未受限制*，或*部分限制*收到呼叫時，其`Measure`具有至少一個引數等於方法`Double.PositiveInfinity`– 可以是無限的條件約束想像成指出自動調整大小。

## <a name="invalidation"></a>失效

失效是由此頁面上的項目中的變更會觸發新的配置循環程序。 當他們不再擁有正確的大小或位置時，項目都視為無效。 例如，如果[ `FontSize` ](xref:Xamarin.Forms.Button.FontSize)屬性[ `Button` ](xref:Xamarin.Forms.Button)的變更，`Button`稱為無效，因為它將不會再有正確的大小。 調整大小`Button`可能也會有透過頁面的其餘部分的配置中的變更產生漣漪效果。

項目則會叫用失效本身[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)方法，通常當屬性的項目變更時，可能會導致新的大小的項目。 這個方法，就會引發[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)項目的父代控制代碼對應到新的配置循環觸發程序的事件。

[ `Layout` ](xref:Xamarin.Forms.Layout)類別設定的處理常式[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)新增至每一個子系上的事件其`Content`屬性或`Children`集合，並中斷連結處理常式時移除子系。 因此，只要其中一個子系變更大小，會通知具有子系的視覺化樹狀結構中的每個項目。 下圖說明如何視覺化樹狀結構中的項目大小的變更可能會導致 ripple 樹狀結構的變更：

![](custom-images/invalidation.png "視覺化樹狀結構中的無效判定")

不過，`Layout`類別會嘗試限制的頁面配置的子系的大小變更的影響。 如果配置大小限制，則子系的大小變更不會影響高於父版面配置，視覺化樹狀結構中的任何項目。 不過，通常在配置的大小變更會影響版面配置方式排列其子系。 因此中的配置大小的任何變更將會啟動版面配置循環配置，而且版面配置將會收到呼叫其[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))並[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法。

[ `Layout` ](xref:Xamarin.Forms.Layout)類別也會定義[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)方法具有類似的目的，若要[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)方法。 `InvalidateLayout`有所變更時，會影響版面配置位置的方式，和大小及其子系時，應叫用方法。 例如，`Layout`類別叫用`InvalidateLayout`每當加入或從配置中移除子系的方法。

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)可以實作快取以減少重複的引動過程的覆寫[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))版面配置的子系的方法。 覆寫`InvalidateLayout`方法會提供當加入或從配置中移除子系的通知。 同樣地， [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)可以覆寫方法，以提供的通知，當其中一個版面配置的子系變更大小。 針對這兩個方法覆寫中，自訂的版面配置回應時應該清除快取。 如需詳細資訊，請參閱 <<c0> [ 計算和快取資料](#caching)。

## <a name="create-a-custom-layout"></a>建立自訂版面配置

建立自訂的版面配置的程序如下所示：

1. 建立從 `Layout<View>` 類別衍生的類別。 如需詳細資訊，請參閱 <<c0> [ 建立 WrapLayout](#creating)。
1. [*選擇性*] 新增屬性，由可繫結的屬性，應配置類別設定任何參數的支援。 如需詳細資訊，請參閱 <<c0> [ 由可繫結的屬性新增屬性支援](#adding_properties)。
1. 覆寫[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法來叫用[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))方法配置的配置的所有子系，並傳回要求的大小。 如需詳細資訊，請參閱 <<c0> [ 覆寫 OnMeasure 方法](#onmeasure)。
1. 覆寫[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法來叫用[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))版面配置的所有子系的方法。 叫用失敗[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))上每個子系的版面配置中的方法將會導致永遠不會收到正確的大小或位置的子系，並因此子系將不會顯示在頁面上。 如需詳細資訊，請參閱 <<c0> [ 覆寫 LayoutChildren 方法](#layoutchildren)。

    > [!NOTE]
    > 列舉中的子系時[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))並[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))覆寫，請略過任何子系其[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)屬性設定為 `false`. 這可確保自訂的版面配置不會保留空間，不可見的子系。

1. [*選擇性*] 覆寫[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)子系加入或從配置中移除時收到通知的方法。 如需詳細資訊，請參閱 <<c0> [ 覆寫 InvalidateLayout 方法](#invalidatelayout)。
1. [*選擇性*] 覆寫[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)其中一個版面配置的子系的大小變更時接獲通知的方法。 如需詳細資訊，請參閱 <<c0> [ 覆寫 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

> [!NOTE]
> 請注意， [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))不會叫用覆寫，如果配置的大小由其父代，而不是子系。 不過，覆寫會叫用一或多個條件約束都是無限的則配置類別具有非預設[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或是[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性值。 基於這個理由， [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))覆寫不能依賴期間取得的子系大小[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法呼叫。 相反地，`LayoutChildren`必須叫用[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))版面配置的子系，然後再叫用方法[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。 中的子系的大小或者，取得`OnMeasure`覆寫可快取以避免日後發生`Measure`中的引動過程`LayoutChildren`覆寫，但配置類別需要知道何時需要再次取得大小。 如需詳細資訊，請參閱 <<c0> [ 計算和快取配置資料](#caching)。

版面配置類別然後可供將它加入至[ `Page` ](xref:Xamarin.Forms.Page)，並可加入至版面配置的子系。 如需詳細資訊，請參閱 <<c0> [ 耗用 WrapLayout](#consuming)。

<a name="creating" />

### <a name="create-a-wraplayout"></a>建立 WrapLayout

範例應用程式示範方向區分`WrapLayout`類別在頁面上，水平排列其子系，然後將包裝的後續的子系，額外的資料列顯示。

`WrapLayout`類別會為每個子系，又稱為配置相同數量的空間*的儲存格大小*，這取決於子系的大小上限。 小於儲存格大小可以位於資料格內的子系將會根據其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性值。

`WrapLayout`類別定義以下列程式碼範例所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculate-and-cache-layout-data"></a>計算和快取版面配置資料

`LayoutData`結構將相關的子系集合的資料儲存在數個屬性：

- `VisibleChildCount` – 會出現在版面配置的子系數目。
- `CellSize` – 所有的子系，調整大小的版面配置的大小上限。
- `Rows` -資料列數目。
- `Columns` – 資料行數目。

`layoutDataCache`欄位用來儲存多個`LayoutData`值。 應用程式啟動時，兩個`LayoutData`會快取到的物件`layoutDataCache`目前的方向 – 一個用於條件約束引數的字典`OnMeasure`覆寫，且另一個用於`width`和`height`引數若要`LayoutChildren`覆寫。 旋轉橫印到的裝置時`OnMeasure`覆寫，`LayoutChildren`覆寫會再次叫用，從而導致其他兩個`LayoutData`所快取到字典的物件。 不過，當裝置回到直式方向，任何進一步的計算所需因為`layoutDataCache`已經具有必要的資料。

下列程式碼範例所示`GetLayoutData`方法，計算的屬性`LayoutData`結構化根據特定的大小：

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

- 它會判斷是否計算`LayoutData`值已經在快取，並傳回它，如果有的話。
- 否則，它會列舉所有子系，叫用`Measure`方法上使用無限的寬度和高度，每個子系，並判斷最大的子系的大小。
- 前提是沒有至少一個可見的子系，它會計算資料列數和所需，資料行，然後計算的維度為基礎的子系的 儲存格大小`WrapLayout`。 儲存格大小通常是稍微超出最大的子系的大小，但是，它也可能是較小的附註如果`WrapLayout`寬度不足最寬子系或夠高的最高的子系。
- 它會儲存新`LayoutData`快取中的值。

<a name="adding_properties" />

#### <a name="add-properties-backed-by-bindable-properties"></a>新增可系結屬性所支援的屬性

`WrapLayout`類別會定義`ColumnSpacing`和`RowSpacing`屬性，其值用來分隔的資料列和資料行，在配置中，且其享有的支援可繫結屬性。 可繫結的屬性是以下列程式碼範例所示：

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

每個可繫結屬性的屬性變更處理常式會叫用`InvalidateLayout`方法覆寫，以觸發新的版面配置傳遞`WrapLayout`。 如需詳細資訊，請參閱 <<c0> [ 覆寫 InvalidateLayout 方法](#invalidatelayout)並[覆寫 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

<a name="onmeasure" />

#### <a name="override-the-onmeasure-method"></a>覆寫 OnMeasure 方法

`OnMeasure`覆寫以下列程式碼範例所示：

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

覆寫會叫用`GetLayoutData`方法和建構`SizeRequest`物件傳回的資料，同時也納入考量`RowSpacing`和`ColumnSpacing`屬性值。 如需詳細資訊`GetLayoutData`方法，請參閱 <<c2> [ 計算和快取資料](#caching)。

> [!IMPORTANT]
> [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))並[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法應該永遠不會要求無限的維度傳回[ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest)屬性設為值`Double.PositiveInfinity`. 不過，至少其中一個條件約束引數`OnMeasure`可以是`Double.PositiveInfinity`。

<a name="layoutchildren" />

#### <a name="override-the-layoutchildren-method"></a>覆寫 LayoutChildren 方法

`LayoutChildren`覆寫以下列程式碼範例所示：

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

覆寫開始呼叫`GetLayoutData`方法，然後列舉所有的調整大小，然後放置在每個子系的資料格內的子系。 這藉由叫用來達成[ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))方法，用來定位為基礎的矩形內的子系其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性值。 這相當於呼叫的子[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。

> [!NOTE]
> 請注意，矩形會傳遞至`LayoutChildIntoBoundingRegion`方法包含所在的子系可以整個區域。

如需詳細資訊`GetLayoutData`方法，請參閱 <<c2> [ 計算和快取資料](#caching)。

<a name="invalidatelayout" />

#### <a name="overridethe-invalidatelayout-method"></a>Overridethe InvalidateLayout 方法

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)來新增或移除在版面配置，或當一子系時，會叫用覆寫的`WrapLayout`屬性變更值，如下列程式碼範例所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

覆寫讓版面配置失效，並捨棄所有的快取的配置資訊。

> [!NOTE]
> 若要停止[ `Layout` ](xref:Xamarin.Forms.Layout)類別叫用[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)每當加入或移除從配置中，子系的方法覆寫[ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View))並[ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View))方法，並傳回`false`。 新增或移除子系時，配置類別然後可以實作自訂處理序。

<a name="onchildmeasureinvalidated" />

#### <a name="override-the-onchildmeasureinvalidated-method"></a>覆寫 OnChildMeasureInvalidated 方法

[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)覆寫會叫用時配置的子系的其中一個變更大小，以及下列的程式碼範例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

覆寫讓子系版面配置失效，並捨棄所有的快取的配置資訊。

<a name="consuming" />

### <a name="consume-the-wraplayout"></a>使用 WrapLayout

`WrapLayout`類別可供將它放入[ `Page` ](xref:Xamarin.Forms.Page)衍生型別，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

對等的 C# 程式碼如下所示：

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

子系可以再加入`WrapLayout`視需要而定。 下列程式碼範例所示[ `Image` ](xref:Xamarin.Forms.Image)項目新增至`WrapLayout`:

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

當頁面包含`WrapLayout`出現時，範例應用程式以非同步方式存取遠端的 JSON 檔案，其中包含相片的清單，然後建立[ `Image` ](xref:Xamarin.Forms.Image)每個項目相片，並將它新增至`WrapLayout`. 這會導致下列的螢幕擷取畫面所示的外觀：

![](custom-images/portait-screenshots.png "範例應用程式縱向螢幕擷取畫面")

下列螢幕擷取畫面顯示`WrapLayout`經過旋轉成橫式方向之後：

![](custom-images/landscape-ios.png "範例 iOS 應用程式橫向螢幕擷取畫面")
![](custom-images/landscape-android.png "範例 Android 應用程式橫向螢幕擷取畫面")
![](custom-images/landscape-uwp.png "範例 UWP 應用程式橫向螢幕擷取畫面")

每個資料列中的資料行的數目取決於相片大小、 螢幕的寬度和每個裝置獨立單位的像素數目。 [ `Image` ](xref:Xamarin.Forms.Image)項目以非同步方式載入的相片，因此`WrapLayout`類別將會收到經常呼叫其[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法的每個`Image`項目收到新的大小，以載入的相片。

## <a name="related-links"></a>相關連結

- [WrapLayout （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [自訂版面配置](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [建立在 Xamarin.Forms 中的自訂配置 （影片）](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [版面\<配置 T >](xref:Xamarin.Forms.Layout`1)
- [版面配置](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
