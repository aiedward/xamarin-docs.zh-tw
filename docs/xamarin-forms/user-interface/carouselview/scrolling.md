---
title: Xamarin.Forms CarouselView 滾動
description: 當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 此外，CarouselView 會定義兩個 ScrollTo 方法，以程式設計方式將專案滾動到視圖中。
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4c05e6fef969e002befe2afadb8e7db9e477aaba
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940508"
---
# <a name="no-locxamarinforms-carouselview-scrolling"></a>Xamarin.Forms CarouselView 滾動

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義下列捲軸相關屬性：

- `HorizontalScrollBarVisibility`，類型為 `ScrollBarVisibility` ，指定何時可以看到水準捲軸。
- `IsDragging`，表示是否 `bool` 正在滾動的型別 `CarouselView` 。 這是唯讀屬性，其預設值為 `false` 。
- `IsScrollAnimated`，類型為 `bool` ，指定滾動時是否會發生動畫 `CarouselView` 。 預設值是 `true`。
- `ItemsUpdatingScrollMode`型別 `ItemsUpdatingScrollMode` ，代表 `CarouselView` 新專案加入時的滾動行為。
- `VerticalScrollBarVisibility`，類型為 `ScrollBarVisibility` ，指定垂直捲動條何時可見。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 也會定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，這些方法會將專案滾動到視圖中。 其中一個多載會將指定索引處的專案滾動至 view，而另一個多載會將指定的專案滾動至 view。 這兩個多載都有額外的引數，可以指定來指出專案在捲軸完成之後的確切位置，以及是否要以動畫顯示捲軸。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 其中一個方法時所引發的事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)事件隨附的物件 `ScrollToRequested` 具有許多屬性，包括 `IsAnimated` 、 `Index` 、 `Item` 和 `ScrollToPosition` 。 這些屬性是從方法呼叫中指定的引數所設定 `ScrollTo` 。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView) `Scrolled` 也會定義引發的事件以表示發生滾動。 `ItemsViewScrolledEventArgs`事件隨附的物件 `Scrolled` 具有許多屬性。 如需詳細資訊，請參閱偵測 [滾動](#detect-scrolling)。

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，使專案完全顯示。 這項功能稱為「貼齊」，因為專案會在滾動停止時貼齊位置。 如需詳細資訊，請參閱 [對齊點](#snap-points)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 也可以在使用者滾動時以累加方式載入資料。 如需詳細資訊，請參閱 [以累加方式載入資料](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>偵測滾動

您 `IsDragging` 可以檢查屬性，以判斷目前是否 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 正在透過專案進行滾動。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView) `Scrolled` 也會定義引發的事件以表示發生滾動。 當需要捲軸的相關資料時，應該使用此事件。

下列 XAML 範例顯示 `CarouselView` 設定事件之事件處理常式的 `Scrolled` ：

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

在此程式碼範例中， `OnCarouselViewScrolled` 事件處理常式會在 `Scrolled` 事件引發時執行：

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
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

在此範例中， `OnCarouselViewScrolled` 事件處理常式 `ItemsViewScrolledEventArgs` 會輸出伴隨于事件的物件值。

> [!IMPORTANT]
> `Scrolled`引發使用者起始滾動的事件，以及以程式設計方式滾動的事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動至 view

第一個方法多載會 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 將指定索引處的專案滾動至 view。 假設有一個 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名為的物件 `carouselView` ，則下列範例會示範如何在索引6上將專案滾動至 view：

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="scroll-an-item-into-view"></a>將專案滾動到視圖

第二個方法多載會 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 將指定的專案滾動到視野中。 假設有一個 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名為的物件 `carouselView` ，則下列範例會示範如何將 Proboscis 的猴子專案滾動至 view：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="disable-scroll-animation"></a>停用捲軸動畫

在中的專案之間移動時，會顯示滾動動畫 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 這種動畫會針對使用者起始的滾動，以及以程式設計方式滾動進行。 將 `IsScrollAnimated` 屬性設定為， `false` 將會停用兩個滾動分類的動畫。

或者， `animate` 方法的引數 `ScrollTo` 可以設定為， `false` 以停用程式設計滾動時的滾動動畫：

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

當您將專案滾動到視圖時，可以使用方法的引數來指定完成捲軸之後，專案的確切位置 `position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 此引數接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 列舉成員。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員指出專案應該要先滾動，直到顯示在視圖中為止：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此範例程式碼會產生將專案滾動至視野所需的最小捲軸。

> [!NOTE]
> [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)如果 `position` 呼叫方法時未指定引數，預設會使用成員 `ScrollTo` 。

### <a name="start"></a>開始

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應滾動至視圖的開頭：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此範例程式碼會導致專案滾動到視圖的開頭。

### <a name="center"></a>Center

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員會指出專案應該滾動到視圖的中心：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此範例程式碼會導致專案被滾動到視圖的中心。

### <a name="end"></a>結束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應滾動至視圖的結尾：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此範例程式碼會導致專案被滾動到視圖結尾。

## <a name="control-scroll-position-when-new-items-are-added"></a>在新增專案時控制捲軸位置

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義 `ItemsUpdatingScrollMode` 由可系結屬性所支援的屬性。 這個屬性會取得或設定 `ItemsUpdatingScrollMode` 列舉值，這個值表示 `CarouselView` 新增專案時的滾動行為。 `ItemsUpdatingScrollMode` 列舉會定義下列成員：

- `KeepItemsInView` 在新增專案時，將清單中的第一個專案保留在顯示清單中。
- `KeepScrollOffset` 確保新增專案時，會保留目前的滾動位置。
- `KeepLastItemInView` 調整滾動位移，以在新增專案時，將清單中的最後一個專案保留在顯示清單中。

屬性的預設值 `ItemsUpdatingScrollMode` 為 `KeepItemsInView` 。 因此，當新專案新增至 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 清單中的第一個專案時，仍會顯示。 若要確保在新增專案時顯示清單中的最後一個專案，請將屬性設定 `ItemsUpdatingScrollMode` 為 `KeepLastItemInView` ：

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>捲軸可見度

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性，這些屬性是由可系結屬性所支援。 這些屬性會取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 代表水準或垂直捲動條何時可見的列舉值。 `ScrollBarVisibility` 列舉會定義下列成員：

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

根據預設，在上 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性會設定為 `SnapPointsType.MandatorySingle` ，以確保每次只能滾動一個專案。

下列螢幕擷取畫面顯示已關閉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 貼齊功能的：

[![螢幕擷取畫面： iOS 和 Android 上沒有貼齊點的 CarouselView](scrolling-images/snappoints-none.png "沒有貼齊點的 CarouselView")](scrolling-images/snappoints-none-large.png#lightbox "沒有貼齊點的 CarouselView")

### <a name="snap-points-alignment"></a>對齊點對齊

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義 `Start` 、 `Center` 和 `End` 成員。

> [!IMPORTANT]
> [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)只有當 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性設定為或時，才會遵守屬性的值 `Mandatory` `MandatorySingle` 。

#### <a name="start"></a>開始

`SnapPointsAlignment.Start`成員表示對齊點與專案的開頭邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

當使用者撥動以水準滾動的方式起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，左邊的專案會對齊視圖的左邊：

[![螢幕擷取畫面： iOS 和 Android 上的 CarouselView 與開始貼齊點](scrolling-images/snappoints-start.png "具有開始貼齊點的 CarouselView")](scrolling-images/snappoints-start-large.png#lightbox "具有開始貼齊點的 CarouselView")

#### <a name="center"></a>Center

`SnapPointsAlignment.Center`成員表示對齊點與專案的中心對齊。

根據預設 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) 屬性會設定為 `Center` 。 不過，基於完整性，下列 XAML 範例會示範如何設定此列舉成員：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

當使用者撥動以水準滾動的方式起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，中央專案將會與視圖的中心對齊：

[![螢幕擷取畫面： iOS 和 Android 上的中央嵌入式管理點 CarouselView](scrolling-images/snappoints-center.png "具有中間對齊點的 CarouselView")](scrolling-images/snappoints-center-large.png#lightbox "具有中間對齊點的 CarouselView")

#### <a name="end"></a>結束

`SnapPointsAlignment.End`成員表示對齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

當使用者撥動以水準滾動的方式起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，右邊的專案將會與視圖的右邊對齊。

[![螢幕擷取畫面： iOS 和 Android 上的結束貼齊點 CarouselView](scrolling-images/snappoints-end.png "具有結束貼齊點的 CarouselView")](scrolling-images/snappoints-end-large.png#lightbox "具有結束貼齊點的 CarouselView")

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
