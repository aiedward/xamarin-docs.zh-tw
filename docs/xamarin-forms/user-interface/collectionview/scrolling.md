---
title: Xamarin. 表單 CollectionView 滾動
description: 當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外，CollectionView 會定義兩個 ScrollTo 方法，以程式設計方式將專案滾動到 view。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 7aef14cbb854d89a2088a450353b943402f76a86
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913113"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin. 表單 CollectionView 滾動

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義兩個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。 這兩個多載都有額外的引數，可指定以指出專案所屬的群組、在完成捲軸之後，專案的確切位置，以及是否要以動畫顯示捲軸。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義當叫用其中一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，所引發的[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。 伴隨 `ScrollToRequested` 事件的[`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)物件有許多屬性，包括 `IsAnimated`、`Index`、`Item`和 `ScrollToPosition`。 這些屬性是從 `ScrollTo` 方法呼叫中指定的引數所設定。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義引發的 `Scrolled` 事件，以指出滾動發生。 伴隨 `Scrolled` 事件的 `ItemsViewScrolledEventArgs` 物件有許多屬性。 如需詳細資訊，請參閱偵測[滾動](#detect-scrolling)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)也會定義 `ItemsUpdatingScrollMode` 屬性，表示在新專案新增至其中時，`CollectionView` 的滾動行為。 如需這個屬性的詳細資訊，請參閱[在新增新專案時控制捲軸位置](#control-scroll-position-when-new-items-are-added)。

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 這項功能稱為「貼齊」，因為當滾動停止時，專案會貼齊位置。 如需詳細資訊，請參閱[貼齊點](#snap-points)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)也可以在使用者滾動時，以累加方式載入資料。 如需詳細資訊，請參閱[以累加方式載入資料](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>偵測滾動

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義引發的 `Scrolled` 事件，以指出發生滾動。 下列 XAML 範例顯示設定 `Scrolled` 事件之事件處理常式的 `CollectionView`：

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

在此程式碼範例中，當 `Scrolled` 事件引發時，會執行 `OnCollectionViewScrolled` 事件處理常式：

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

在此範例中，`OnCollectionViewScrolled` 事件處理常式會輸出事件隨附之 `ItemsViewScrolledEventArgs` 物件的值。

> [!IMPORTANT]
> 針對使用者起始的滾動和以程式設計方式滾動時，會引發 `Scrolled` 事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動到 view

第一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將位於指定索引處的專案滾動到 view。 假設有一個名為 `collectionView`的[`CollectionView`](xref:Xamarin.Forms.CollectionView)物件，下列範例會示範如何將索引12處的專案滾動到 view：

```csharp
collectionView.ScrollTo(12);
```

或者，您可以藉由指定專案和群組索引，將群組資料中的專案滾動到 view。 下列範例顯示如何將第二個群組中的第三個專案，滾動到 view：

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> 叫用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，就會引發[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="scroll-an-item-into-view"></a>將專案滾動到視野中

第二個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將指定的專案滾動到 view。 假設有一個名為 `collectionView`的[`CollectionView`](xref:Xamarin.Forms.CollectionView)物件，下列範例會示範如何將 Proboscis 的猴子專案滾動到 view：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

或者，您可以藉由指定專案和群組，將群組資料中的專案滾動到 view。 下列範例顯示如何將猴仔群組中的 Proboscis 猴子專案，滾動到 view：

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> 叫用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，就會引發[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="disable-scroll-animation"></a>停用捲軸動畫

將專案滾動到 view 時，會顯示滾動動畫。 不過，您可以將 `ScrollTo` 方法的 `animate` 引數設定為 `false`，藉此停用這個動畫：

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

將專案滾動到 view 時，可以使用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的 `position` 引數來指定專案在完成滾動之後的確切位置。 這個引數會接受[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)列舉成員。

### <a name="makevisible"></a>MakeVisible

[ [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員] 表示專案應該滾動，直到在視圖中顯示為止：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

這個範例程式碼會產生將專案滾動到視野所需的最小滾動：

[![CollectionView 垂直清單的螢幕擷取畫面，其中的專案會在 iOS 和 Android 上滾動到 view](scrolling-images/scrolltoposition-makevisible.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

> [!NOTE]
> 如果呼叫 `ScrollTo` 方法時未指定 `position` 引數，預設會使用[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員。

### <a name="start"></a>Start

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的開頭：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

這個範例程式碼會導致專案滾動到視圖的開頭：

[![CollectionView 垂直清單的螢幕擷取畫面，其中的專案會在 iOS 和 Android 上滾動到 view](scrolling-images/scrolltoposition-start.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-start-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="center"></a>中心

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員表示該專案應該滾動到視圖的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此範例程式碼會將專案滾動到視圖的中心：

[![CollectionView 垂直清單的螢幕擷取畫面，其中的專案會在 iOS 和 Android 上滾動到 view](scrolling-images/scrolltoposition-center.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-center-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="end"></a>結束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的結尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

這個範例程式碼會導致專案滾動到視圖的結尾：

[![CollectionView 垂直清單的螢幕擷取畫面，其中的專案會在 iOS 和 Android 上滾動到 view](scrolling-images/scrolltoposition-end.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-end-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

## <a name="control-scroll-position-when-new-items-are-added"></a>新增新專案時的控制項捲軸位置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義 `ItemsUpdatingScrollMode` 屬性，其受可系結屬性支援。 這個屬性會取得或設定 `ItemsUpdatingScrollMode` 列舉值，表示在新專案新增至其中時，`CollectionView` 的滾動行為。 `ItemsUpdatingScrollMode` 列舉會定義下列成員：

- `KeepItemsInView` 調整捲軸位移，以在新增新專案時保持顯示第一個可見的專案。
- 新增新專案時，`KeepScrollOffset` 會維護相對於清單開頭的捲軸位移。
- `KeepLastItemInView` 調整捲軸位移，以便在新增新專案時，讓最後一個專案保持可見。

`ItemsUpdatingScrollMode` 屬性的預設值為 `KeepItemsInView`。 因此，當新專案新增至[`CollectionView`](xref:Xamarin.Forms.CollectionView)時，清單中的第一個可見專案將會保持顯示狀態。 為確保新加入的專案一律會顯示在清單底部，`ItemsUpdatingScrollMode` 屬性應該設定為 `KeepLastItemInView`：

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>捲軸可見度

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義可系結屬性所支援的 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性。 這些屬性會取得或設定[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)列舉值，以表示水準或垂直捲動條何時可見。 `ScrollBarVisibility` 列舉會定義下列成員：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)表示平臺的預設捲軸行為，而是 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性的預設值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)表示捲軸會顯示出來，即使內容適合在視圖中也一樣。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)表示不會顯示捲軸，即使該內容無法放入視圖中也一樣。

## <a name="snap-points"></a>貼齊點

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 這項功能稱為「貼齊」，因為當滾動停止時，專案會貼齊位置，而且是由[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別中的下列屬性所控制：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)，屬於[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)類型，會在滾動時指定貼齊點的行為。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)，屬於[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)類型，可指定對齊點與專案對齊的方式。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

> [!NOTE]
> 發生貼齊時，會以產生最少動作的方向進行。

### <a name="snap-points-type"></a>貼齊點類型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)列舉會定義下列成員：

- `None` 表示滾動不會貼齊至專案。
- `Mandatory` 指出內容一律會貼齊到最接近的貼齊點，也就是慣性的方向。
- `MandatorySingle` 表示與 `Mandatory`相同的行為，但一次只會滾動一個專案。

根據預設， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)屬性會設定為 [`SnapPointsType.None`]，以確保滾動不會貼齊專案，如下列螢幕擷取畫面所示：

[![在 iOS 和 Android 上的 CollectionView 垂直清單（沒有貼齊點）的螢幕擷取畫面](scrolling-images/snappoints-none.png "不含貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-none-large.png#lightbox "不含貼齊點的 CollectionView 垂直清單")

### <a name="snap-points-alignment"></a>貼齊點對齊

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義 `Start`、`Center`和 `End` 成員。

> [!IMPORTANT]
> 只有當 [ [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) ] 屬性設定為 [`Mandatory`] 或 [`MandatorySingle`] 時，才會遵守[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性的值。

#### <a name="start"></a>Start

`SnapPointsAlignment.Start` 成員表示貼齊點與專案的前置邊緣對齊。

根據預設， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性會設定為 [`SnapPointsAlignment.Start`]。 不過，基於完整性，下列 XAML 範例示範如何設定這個列舉成員：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

當使用者撥動起始捲軸時，最上方的專案會與視圖的頂端對齊：

[![在 iOS 和 Android 上具有開始貼齊點之 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-start.png "具有開始貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-start-large.png#lightbox "具有開始貼齊點的 CollectionView 垂直清單")

#### <a name="center"></a>中心

`SnapPointsAlignment.Center` 成員表示貼齊點對齊專案的中心。 下列 XAML 範例顯示如何設定此列舉成員：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

當使用者撥動起始捲軸時，最上方的專案會置中對齊視圖的頂端：

[![在 iOS 和 Android 上具有中心貼齊點之 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-center.png "具有中心貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-center-large.png#lightbox "具有中心貼齊點的 CollectionView 垂直清單")

#### <a name="end"></a>結束

`SnapPointsAlignment.End` 成員表示貼齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

當使用者撥動起始捲軸時，底部專案會對齊視圖的底部：

[![在 iOS 和 Android 上具有結束貼齊點之 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-end.png "具有結束貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-end-large.png#lightbox "具有結束貼齊點的 CollectionView 垂直清單")

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
