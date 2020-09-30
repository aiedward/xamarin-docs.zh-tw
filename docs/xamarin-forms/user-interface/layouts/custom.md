---
title: 在中建立自訂版面配置 Xamarin.Forms
description: 本文說明如何撰寫自訂的版面配置類別，並示範在頁面上水準排列其子系的 WrapLayout 類別，然後將後續子系的顯示換行至其他資料列。
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 63a939e7093bcbe52f1aed376253c7aa78b078bf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563844"
---
# <a name="create-a-custom-layout-in-no-locxamarinforms"></a>在中建立自訂版面配置 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Forms 定義五個配置類別（StackLayout、AbsoluteLayout、RelativeLayout、Grid 和 FlexLayout），並以不同的方式排列其子系。但是，有時候您必須使用不提供的版面配置來組織頁面內容 Xamarin.Forms 。本文說明如何撰寫自訂的版面配置類別，並示範在頁面上水準排列其子系的 WrapLayout 類別，然後將後續子系的顯示換行至其他資料列。_

在中 Xamarin.Forms ，所有版面配置類別都是衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別，並且會將泛型型別限制為 [`View`](xref:Xamarin.Forms.View) 和其衍生類型。 然後， `Layout<T>` 類別衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別，該類別會提供定位和調整大小子項目的機制。

每個視覺元素都負責判斷自己慣用的大小，也就是所謂的 *要求* 大小。 [`Page`](xref:Xamarin.Forms.Page)、 [`Layout`](xref:Xamarin.Forms.Layout) 和 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 衍生型別負責判斷其子系或子系（相對於本身）的位置和大小。 因此，版面配置包含父子式關聯性，其中父代會決定其子系的大小，但會嘗試容納要求的子系大小。

Xamarin.Forms若要建立自訂版面配置，必須徹底瞭解版面配置和失效週期。 現在將會討論這些週期。

## <a name="layout"></a>配置

版面配置是從具有頁面的視覺化樹狀結構頂端開始，並且會繼續執行視覺化樹狀結構的所有分支，以包含頁面上的每個視覺元素。 其他元素的父系元素會負責調整其子系的大小和位置（相對於本身的位置）。

[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別會定義 [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3 方法來測量版面配置作業的元素，而 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。矩形) # A7 方法，這個方法會指定要在其中呈現專案的矩形區域。 當應用程式啟動並顯示第一個頁面時，會在物件上啟動由第一個呼叫所組成的 *版面配置迴圈* ， `Measure` 然後 `Layout` 呼叫 [`Page`](xref:Xamarin.Forms.Page) 。

1. 在版面配置週期中，每個父元素都會負責 `Measure` 在其子系上呼叫方法。
1. 在測量子系之後，每個父元素都會負責呼叫其子系 `Layout` 上的方法。

這個迴圈可確保頁面上的每個視覺元素都會收到 `Measure` 和方法的呼叫 `Layout` 。 程式如下圖所示：

![：：：非 loc (Xamarin. Forms) ：：： Layout 迴圈](custom-images/layout-cycle.png)

> [!NOTE]
> 請注意，如果有變更會影響版面配置，則在視覺化樹狀結構的子集上也可能會發生版面配置迴圈。 這包括要在集合中加入或移除的專案，例如中的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 、專案 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 屬性的變更或元素大小的變更。

Xamarin.Forms具有或屬性的每個類別 `Content` `Children` 都有可覆寫的 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法。 衍生自的自訂版面配置類別 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必須覆寫這個方法，並確定 [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3 和 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。在所有專案的子系上呼叫 # A7 方法) 的矩形，以提供所需的自訂版面配置。

此外，每個衍生自或的類別都 [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 必須覆寫 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法，也就是版面配置類別藉由呼叫 [ `Measure` ] (x：來決定其所需的大小 Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags 其子系的) # A3 方法。

> [!NOTE]
> 專案會根據 *條件約束*來決定其大小，這表示專案父系內的元素有多少空間可供使用。 傳遞至 [ `Measure` ] (x：的條件約束 Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3 和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法的範圍可以從0到 `Double.PositiveInfinity` 。 當元素收到對其 [] (x：的呼叫時，會 *受到限制*或 *完全受限* `Measure` 。 Xamarin.FormsVisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3 方法（具有非無限引數）-元素會限制為特定的大小。 當某個專案接收到其方法的呼叫時，如果專案具有至少一個與等於的引數，則會被視為不受 *限制*或 *部分限制* `Measure` `Double.PositiveInfinity` –可將無限條件約束視為表示自動調整大小。

## <a name="invalidation"></a>失效

[失效] 是頁面上專案的變更觸發新配置週期的進程。 當元素不再具有正確的大小或位置時，會被視為無效。 例如，如果變更的 [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 屬性，則 [`Button`](xref:Xamarin.Forms.Button) `Button` 會被視為無效，因為它將不再具有正確的大小。 調整大小 `Button` 之後，可能會因為頁面其餘部分的版面配置變更而產生 ripple 效果。

專案會藉由叫用方法來使自身失效 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) ，通常是在專案的屬性變更時，可能會導致元素的新大小。 這個方法會引發 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 事件，此事件是元素的父控制碼，用來觸發新的版面配置週期。

[`Layout`](xref:Xamarin.Forms.Layout)類別 [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) 會針對每個加入至其屬性或集合的子系，設定事件的處理常式 `Content` `Children` ，並在移除子系時卸離處理常式。 因此，每個具有子系的視覺化樹狀結構中的專案，只要其中一個子系變更大小，就會收到警示。 下圖說明視覺樹狀結構中元素大小的變更如何造成在樹狀結構中出現的變更：

![視覺化樹狀結構中的失效](custom-images/invalidation.png)

不過，此 `Layout` 類別會嘗試限制在頁面版面配置上變更子大小的影響。 如果配置大小受到限制，則子大小的變更不會影響到比視覺化樹狀結構中的父配置更高的任何值。 不過，配置大小的變更通常會影響版面配置排列其子系的方式。 因此，配置大小的任何變更都會開始配置的版面配置週期，而配置將會接收其 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 和方法的呼叫 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 。

[`Layout`](xref:Xamarin.Forms.Layout)類別也會定義與 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法具有類似用途的方法 [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) 。 `InvalidateLayout`每當進行的變更會影響版面配置位置和大小其子系的方式時，就應該叫用方法。 例如， `Layout` `InvalidateLayout` 每次在配置中加入或移除子系時，類別都會叫用方法。

[`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)可以覆寫來執行快取，以將 [ `Measure` ] (x：的重複調用降至最低 Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags 版面配置子系的) # A3 方法。 覆寫 `InvalidateLayout` 方法會提供在配置中加入或移除子系時的通知。 同樣地， [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 可以覆寫方法，以便在其中一個版面配置的子系變更大小時提供通知。 針對這兩種方法覆寫，自訂版面配置應藉由清除快取來回應。 如需詳細資訊，請參閱 [計算和](#calculate-and-cache-layout-data)快取版面配置資料。

## <a name="create-a-custom-layout"></a>建立自訂版面配置

建立自訂版面配置的程式如下所示：

1. 建立從 `Layout<View>` 類別衍生的類別。 如需詳細資訊，請參閱 [建立 WrapLayout](#create-a-wraplayout)。
1. [*選用*]針對應該在版面配置類別上設定的任何參數，加入可系結屬性支援的屬性。 如需詳細資訊，請參閱加入可系結 [屬性所支援的屬性](#add-properties-backed-by-bindable-properties)。
1. 覆寫 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法以叫用 [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。在所有版面配置的子系上 MeasureFlags) # A3 方法，並傳回配置的要求大小。 如需詳細資訊，請參閱覆 [寫 OnMeasure 方法](#override-the-onmeasure-method)。
1. 覆寫 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法以叫用 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。矩形) # A3 方法在所有版面配置的子系上。 無法叫用 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。矩形) # A3 方法在版面配置中的每個子系上，會導致子系永遠不會收到正確的大小或位置，因此子系不會在頁面上顯示。 如需詳細資訊，請參閱覆 [寫 LayoutChildren 方法](#override-the-layoutchildren-method)。

    > [!NOTE]
    > 列舉和覆寫中的子系時 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) ，請略過其 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 屬性設定為的任何子系 `false` 。 這可確保自訂配置不會留下空間給隱藏的子系。

1. [*選用*]覆寫 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 方法，以在配置中加入或移除子系時收到通知。 如需詳細資訊，請參閱覆 [寫 InvalidateLayout 方法](#override-the-invalidatelayout-method)。
1. [*選用*]覆寫 [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) 方法，以便在其中一個版面配置的子系變更大小時收到通知。 如需詳細資訊，請參閱覆 [寫 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

> [!NOTE]
> 請注意， [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 如果配置的大小是由其父系（而非其子系）所控管，則不會叫用覆寫。 但是，如果其中一個或兩個條件約束都是無限的，或版面配置類別具有非預設值 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 或屬性值，就會叫用覆寫 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 基於這個理由，覆 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 寫不能依賴在方法呼叫期間取得的子大小 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 。 相反地，必須叫用 `LayoutChildren` [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。在叫用 [ `Layout` ] (x：之前，在版面配置的子系上 MeasureFlags) # A3 方法 Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。) # A7 方法的矩形。 或者，您可以快取在覆寫中取得的子系大小 `OnMeasure` ，以避免稍後 `Measure` 在覆寫中叫用 `LayoutChildren` ，但是版面配置類別必須知道何時需要重新取得大小。 如需詳細資訊，請參閱 [計算和](#calculate-and-cache-layout-data)快取版面配置資料。

然後，您可以藉由將它新增至 [`Page`](xref:Xamarin.Forms.Page) ，並藉由將子系加入至配置來取用 layout 類別。 如需詳細資訊，請參閱 [使用 WrapLayout](#consume-the-wraplayout)。

### <a name="create-a-wraplayout"></a>建立 WrapLayout

範例應用程式會示範面向方向的 `WrapLayout` 類別，此類別會在頁面上水準排列其子系，然後將後續子系的顯示換行至其他資料列。

`WrapLayout`類別會根據子系的大小上限，為每個子系配置相同的空間量（稱為資料*格大小*）。 小於資料格大小的子系可以根據其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性值放在資料格中 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。

`WrapLayout`類別定義如下列程式碼範例所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>計算和快取版面配置資料

`LayoutData`結構會在數個屬性中儲存子系集合的相關資料：

- `VisibleChildCount` –版面配置中可見的子係數目。
- `CellSize` –所有子系的大小上限，調整為版面配置的大小。
- `Rows` –資料列的數目。
- `Columns` -資料行的數目。

此 `layoutDataCache` 欄位是用來儲存多個 `LayoutData` 值。 當應用程式啟動時，會將兩個物件快取 `LayoutData` 到 `layoutDataCache` 目前方向的字典中，一個用於覆寫的條件約束引數 `OnMeasure` ，另一個則用於覆寫的 `width` 和 `height` 引數 `LayoutChildren` 。 將裝置旋轉為橫向時， `OnMeasure` 將會再次叫用覆寫和覆 `LayoutChildren` 寫，這會導致將另兩個物件快取 `LayoutData` 到字典中。 不過，將裝置傳回給直向時，不需要進一步的計算，因為 `layoutDataCache` 已經有必要的資料。

下列程式碼範例顯示 `GetLayoutData` 方法，這個方法會 `LayoutData` 根據特定大小來計算結構化的屬性：

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

此 `GetLayoutData` 方法會執行下列作業：

- 它會判斷匯出 `LayoutData` 值是否已在快取中，並在可用時傳回。
- 否則，它會列舉所有的子系，並 `Measure` 在每個具有無限寬度和高度的子系上叫用方法，並決定子大小上限。
- 如果至少有一個可見的子系，則會計算所需的資料列和資料行數目，然後根據的維度計運算元格的資料格大小 `WrapLayout` 。 請注意，資料格大小通常比子大小的最大值稍微寬一點，但如果寬度不足以容納最高的子系，則也可能較小 `WrapLayout` 。
- 它會將新的值儲存在快取 `LayoutData` 中。

#### <a name="add-properties-backed-by-bindable-properties"></a>加入可系結屬性支援的屬性

`WrapLayout`類別會定義 `ColumnSpacing` 和 `RowSpacing` 屬性，其值會用來分隔配置中的資料列和資料行，以及可系結屬性支援的屬性。 可系結屬性會顯示在下列程式碼範例中：

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

每個可系結屬性的屬性變更處理常式會叫 `InvalidateLayout` 用方法覆寫，以在上觸發新的版面配置傳遞 `WrapLayout` 。 如需詳細資訊，請參閱覆 [寫 InvalidateLayout 方法](#override-the-invalidatelayout-method) 和覆 [寫 OnChildMeasureInvalidated 方法](#override-the-onchildmeasureinvalidated-method)。

#### <a name="override-the-onmeasure-method"></a>覆寫 OnMeasure 方法

覆 `OnMeasure` 寫會顯示在下列程式碼範例中：

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

覆寫會叫用 `GetLayoutData` 方法，並 `SizeRequest` 從傳回的資料中建立物件，同時也會考慮 `RowSpacing` 和 `ColumnSpacing` 屬性值。 如需此方法的詳細資訊 `GetLayoutData` ，請參閱 [計算和](#calculate-and-cache-layout-data)快取版面配置資料。

> [!IMPORTANT]
> [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3 和 [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) 方法絕對不應該藉由傳回屬性設定為的值，來要求無限的維度 [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) `Double.PositiveInfinity` 。 但是，至少有一個條件約束引數 `OnMeasure` 可以是 `Double.PositiveInfinity` 。

#### <a name="override-the-layoutchildren-method"></a>覆寫 LayoutChildren 方法

覆 `LayoutChildren` 寫會顯示在下列程式碼範例中：

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

覆寫的開頭是方法的呼叫 `GetLayoutData` ，然後將所有子系列舉為大小，並將它們放置在每個子系的儲存格中。 這可透過叫用 [ `LayoutChildIntoBoundingRegion` ] (x：來達成 Xamarin.Forms 。LayoutChildIntoBoundingRegion (Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形) # A3 方法，這個方法可用來根據其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性值，在矩形內定位子系 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 這相當於呼叫子系的 [ `Layout` ] (x： Xamarin.Forms 。VisualElement (版面配置 Xamarin.Forms 。) # A3 方法的矩形。

> [!NOTE]
> 請注意，傳遞至方法的矩形 `LayoutChildIntoBoundingRegion` 會包括子系可以位於其中的整個區域。

如需此方法的詳細資訊 `GetLayoutData` ，請參閱 [計算和](#calculate-and-cache-layout-data)快取版面配置資料。

#### <a name="override-the-invalidatelayout-method"></a>覆寫 InvalidateLayout 方法

在配置 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) 中加入或移除子系時，或其中一個 `WrapLayout` 屬性變更值時，就會叫用覆寫，如下列程式碼範例所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

覆寫會使配置失效，並捨棄所有快取的版面配置資訊。

> [!NOTE]
> 若要在 [`Layout`](xref:Xamarin.Forms.Layout) 每次在配置中加入或移除子系時，停止叫用方法的類別 [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) ，請覆寫 [ `ShouldInvalidateOnChildAdded` ] (x： Xamarin.Forms 。ShouldInvalidateOnChildAdded (Xamarin.Forms 。View) # A3 和 [ `ShouldInvalidateOnChildRemoved` ] (x： Xamarin.Forms 。ShouldInvalidateOnChildRemoved (Xamarin.Forms 。View) # A7 方法，然後返回 `false` 。 然後，在加入或移除子系時，配置類別可以執行自訂進程。

#### <a name="override-the-onchildmeasureinvalidated-method"></a>覆寫 OnChildMeasureInvalidated 方法

[`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)當其中一個版面配置的子系變更大小時，就會叫用覆寫，如下列程式碼範例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

覆寫會使子配置失效，並捨棄所有快取的版面配置資訊。

### <a name="consume-the-wraplayout"></a>使用 WrapLayout

將 `WrapLayout` 類別放在衍生型別上，即可取用類別 [`Page`](xref:Xamarin.Forms.Page) ，如下列 XAML 程式碼範例所示：

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

然後，您可以視需要將子系新增至 `WrapLayout` 。 下列程式碼範例會顯示 [`Image`](xref:Xamarin.Forms.Image) 新增至的元素 `WrapLayout` ：

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

當包含的頁面 `WrapLayout` 出現時，範例應用程式會以非同步方式存取包含相片清單的遠端 JSON 檔案、建立 [`Image`](xref:Xamarin.Forms.Image) 每個相片的元素，然後將它新增至 `WrapLayout` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

![範例應用程式縱向螢幕擷取畫面](custom-images/portait-screenshots.png)

下列螢幕擷取畫面顯示在 `WrapLayout` 旋轉為橫向方向之後：

![範例 iOS 應用程式環境範例： Android 應用程式橫向範例螢幕擷取畫面 ](custom-images/landscape-ios.png)
 ![ ](custom-images/landscape-android.png)
 ![ 範例 UWP 應用程式橫向螢幕擷取畫面](custom-images/landscape-uwp.png)

每個資料列中的資料行數目取決於相片大小、螢幕寬度和每個裝置獨立單位的圖元數。 這些專案 [`Image`](xref:Xamarin.Forms.Image) 會以非同步方式載入相片，因此 `WrapLayout` 類別會頻繁地呼叫其 [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) 方法，因為每個專案會 `Image` 根據所載入的相片收到新的大小。

## <a name="related-links"></a>相關連結

- [WrapLayout (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [自訂配置](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [ (影片中建立自訂版面配置 Xamarin.Forms) ](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [配置\<T>](xref:Xamarin.Forms.Layout`1)
- [版面配置](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)