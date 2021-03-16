---
title: Xamarin.Forms CollectionView 滾動
description: 當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 此外，CollectionView 會定義兩個 ScrollTo 方法，以程式設計方式將專案滾動到視圖中。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aa9b43c6a41e48557807270542efa3a1121ed0b5
ms.sourcegitcommit: 9e3ebcd0bd96dd7f7d716e105504e97a7f848c01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557959"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin.Forms CollectionView 滾動

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，這些方法會將專案滾動到視圖中。 其中一個多載會將指定索引處的專案滾動至 view，而另一個多載會將指定的專案滾動至 view。 這兩個多載都有額外的引數，可以指定來指出專案所屬的群組、專案在捲軸之後的確切位置，以及是否要以動畫顯示捲軸。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 其中一個方法時所引發的事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)事件隨附的物件 `ScrollToRequested` 具有許多屬性，包括 `IsAnimated` 、 `Index` 、 `Item` 和 `ScrollToPosition` 。 這些屬性是從方法呼叫中指定的引數所設定 `ScrollTo` 。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView) `Scrolled` 也會定義引發的事件以表示發生滾動。 `ItemsViewScrolledEventArgs`事件隨附的物件 `Scrolled` 具有許多屬性。 如需詳細資訊，請參閱偵測 [滾動](#detect-scrolling)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 也會定義 `ItemsUpdatingScrollMode` 屬性，這個屬性工作表示 `CollectionView` 新增專案時的滾動行為。 如需此屬性的詳細資訊，請參閱 [在新增專案時控制捲軸位置](#control-scroll-position-when-new-items-are-added)。

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 這項功能稱為「貼齊」，因為專案會在滾動停止時貼齊位置。 如需詳細資訊，請參閱 [對齊點](#snap-points)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 也可以在使用者滾動時以累加方式載入資料。 如需詳細資訊，請參閱 [以累加方式載入資料](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>偵測滾動

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義 `Scrolled` 引發的事件以表示發生滾動。 `ItemsViewScrolledEventArgs`類別（代表伴隨事件的物件 `Scrolled` ）會定義下列屬性：

- `HorizontalDelta`型別為的， `double` 代表水準滾動量的變更。 這是向左滾動時為負值，而向右滾動時則為正值。
- `VerticalDelta`型別為的， `double` 代表垂直捲動量的變更。 當向上滾動時，此值為負值，而向下滾動時則為正值。
- `HorizontalOffset`型別 `double` 為的，會定義清單從其來源水準位移的數量。
- `VerticalOffset`型別 `double` 為的，會定義清單從其原點垂直位移的數量。
- `FirstVisibleItemIndex`型別 `int` 為的，是清單中可見的第一個專案的索引。
- `CenterItemIndex`型別為的， `int` 是在清單中顯示之中間專案的索引。
- `LastVisibleItemIndex`型別 `int` 為的，是清單中可見之最後一個專案的索引。

下列 XAML 範例顯示 `CollectionView` 設定事件之事件處理常式的 `Scrolled` ：

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

在此程式碼範例中， `OnCollectionViewScrolled` 事件處理常式會在 `Scrolled` 事件引發時執行：

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    // Custom logic
}
```

> [!IMPORTANT]
> `Scrolled`引發使用者起始滾動的事件，以及以程式設計方式滾動的事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動至 view

第一個方法多載會 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 將指定索引處的專案滾動至 view。 假設有一個 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 名為的物件 `collectionView` ，則下列範例會示範如何將索引12的專案滾動至 view：

```csharp
collectionView.ScrollTo(12);
```

或者，您可以藉由指定專案和群組索引，將群組資料中的專案滾動到視野中。 下列範例顯示如何將第二個群組中的第三個專案滾動至 view：

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="scroll-an-item-into-view"></a>將專案滾動到視圖

第二個方法多載會 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 將指定的專案滾動到視野中。 假設有一個 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 名為的物件 `collectionView` ，則下列範例會示範如何將 Proboscis 的猴子專案滾動至 view：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

或者，您可以藉由指定專案和群組，將群組資料中的專案滾動到視野中。 下列範例顯示如何將猴仔群組中的 Proboscis 猴子專案滾動至 view：

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="disable-scroll-animation"></a>停用捲軸動畫

將專案滾動到視圖時，會顯示滾動動畫。 不過，您可以將方法的引數設定為，以停用此動畫 `animate` `ScrollTo` `false` ：

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

當您將專案滾動到視圖時，可以使用方法的引數來指定完成捲軸之後，專案的確切位置 `position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 此引數接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 列舉成員。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員指出專案應該要先滾動，直到顯示在視圖中為止：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此範例程式碼會產生將專案滾動至視野所需的最小滾動：

[![螢幕擷取畫面：在 iOS 和 Android 上，ScrollToPosition MakeVisible 的 CollectionView 垂直清單](scrolling-images/scrolltoposition-makevisible.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

> [!NOTE]
> [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)如果 `position` 呼叫方法時未指定引數，預設會使用成員 `ScrollTo` 。

### <a name="start"></a>開始

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應滾動至視圖的開頭：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此範例程式碼會導致專案被滾動到視圖的開頭：

[![螢幕擷取畫面：在 iOS 和 Android 上，使用 ScrollToPosition 的 CollectionView 垂直清單](scrolling-images/scrolltoposition-start.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-start-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="center"></a>Center

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員會指出專案應該滾動到視圖的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此範例程式碼會導致專案被滾動到視圖的中心：

[![螢幕擷取畫面：在 iOS 和 Android 上，使用 ScrollToPosition 的 CollectionView 垂直清單](scrolling-images/scrolltoposition-center.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-center-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="end"></a>結束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應滾動至視圖的結尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此範例程式碼會導致專案被滾動到視圖結尾：

[![螢幕擷取畫面： iOS 和 Android 上有 ScrollToPosition 的 CollectionView 垂直清單](scrolling-images/scrolltoposition-end.png "具有滾動專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-end-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

## <a name="control-scroll-position-when-new-items-are-added"></a>在新增專案時控制捲軸位置

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義 `ItemsUpdatingScrollMode` 由可系結屬性所支援的屬性。 這個屬性會取得或設定 `ItemsUpdatingScrollMode` 列舉值，這個值表示 `CollectionView` 新增專案時的滾動行為。 `ItemsUpdatingScrollMode` 列舉會定義下列成員：

- `KeepItemsInView` 在新增專案時，將清單中的第一個專案保留在顯示清單中。
- `KeepScrollOffset` 確保新增專案時，會保留目前的滾動位置。
- `KeepLastItemInView` 調整滾動位移，以在新增專案時，將清單中的最後一個專案保留在顯示清單中。

屬性的預設值 `ItemsUpdatingScrollMode` 為 `KeepItemsInView` 。 因此，當新專案新增至 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 清單中的第一個專案時，仍會顯示。 若要確保在新增專案時顯示清單中的最後一個專案，請將屬性設定 `ItemsUpdatingScrollMode` 為 `KeepLastItemInView` ：

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

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性，這些屬性是由可系結屬性所支援。 這些屬性會取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 代表水準或垂直捲動條何時可見的列舉值。 `ScrollBarVisibility` 列舉會定義下列成員：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) 表示平臺的預設捲軸行為，而且是和屬性的預設值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) 表示捲軸會顯示出來，即使內容符合視野也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) 指出不會顯示捲軸，即使內容無法容納在視圖中也一樣。

## <a name="snap-points"></a>貼齊點

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 這項功能稱為「貼齊」，因為專案會在滾動停止時貼齊位置，並且由類別中的下列屬性控制 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) ：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)型別為的 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) ，指定滾動時對齊點的行為。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)型別為的 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) ，可指定對齊點與專案的對齊方式。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

> [!NOTE]
> 進行貼齊時，會在產生最少量動作的方向中發生。

### <a name="snap-points-type"></a>貼齊點類型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)列舉會定義下列成員：

- `None` 指出滾動沒有貼齊至專案。
- `Mandatory` 指出內容一律會貼齊到最接近的貼齊點，以自然地停止滾動的方向，以及慣性的方向。
- `MandatorySingle` 表示與相同的行為 `Mandatory` ，但一次只能滾動一個專案。

根據預設， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性會設定為 `SnapPointsType.None` ，以確保滾動沒有貼齊專案，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面： iOS 和 Android 上沒有貼齊點的 CollectionView 垂直清單](scrolling-images/snappoints-none.png "CollectionView 不含對齊點的垂直清單")](scrolling-images/snappoints-none-large.png#lightbox "CollectionView 不含對齊點的垂直清單")

### <a name="snap-points-alignment"></a>對齊點對齊

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義 `Start` 、 `Center` 和 `End` 成員。

> [!IMPORTANT]
> [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)只有當 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性設定為或時，才會遵守屬性的值 `Mandatory` `MandatorySingle` 。

#### <a name="start"></a>開始

`SnapPointsAlignment.Start`成員表示對齊點與專案的開頭邊緣對齊。

根據預設， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) 屬性會設定為 `SnapPointsAlignment.Start` 。 不過，基於完整性，下列 XAML 範例會示範如何設定此列舉成員：

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

當使用者撥動起始捲軸時，最上方的專案會對齊視圖的頂端：

[![螢幕擷取畫面： iOS 和 Android 上包含開始貼齊點的 CollectionView 垂直清單](scrolling-images/snappoints-start.png "CollectionView 具有開始貼齊點的垂直清單")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView 具有開始貼齊點的垂直清單")

#### <a name="center"></a>Center

`SnapPointsAlignment.Center`成員表示對齊點與專案的中心對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動起始捲軸時，最上方的專案會在視圖的上方置中對齊：

[![螢幕擷取畫面：在 iOS 和 Android 上，中間對齊點的 CollectionView 垂直清單](scrolling-images/snappoints-center.png "中央對齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-center-large.png#lightbox "中央對齊點的 CollectionView 垂直清單")

#### <a name="end"></a>結束

`SnapPointsAlignment.End`成員表示對齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動起始捲軸時，底部的專案會與視圖底部對齊：

[![螢幕擷取畫面： iOS 和 Android 上具有結束貼齊點的 CollectionView 垂直清單](scrolling-images/snappoints-end.png "CollectionView 具有結束貼齊點的垂直清單")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView 具有結束貼齊點的垂直清單")

## <a name="related-links"></a>相關連結

- [CollectionView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
