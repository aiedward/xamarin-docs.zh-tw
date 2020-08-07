---
title: Xamarin.FormsCarouselView 滾動
description: 當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外，CarouselView 會定義兩個 ScrollTo 方法，以程式設計方式將專案滾動到 view。
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5c15760919c2511ee24485ab7539463ec252d999
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918135"
---
# <a name="no-locxamarinforms-carouselview-scrolling"></a>Xamarin.FormsCarouselView 滾動

![發行前版本 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義下列捲軸相關屬性：

- `HorizontalScrollBarVisibility`，屬於類型 `ScrollBarVisibility` ，可指定水準捲軸何時可見。
- `IsDragging`，屬於類型 `bool` ，表示是否 `CarouselView` 正在滾動。 這是唯讀屬性，其預設值為 `false` 。
- `IsScrollAnimated`，屬於類型 `bool` ，指定滾動時是否會發生動畫 `CarouselView` 。 預設值是 `true`。
- `ItemsUpdatingScrollMode`，屬於類型 `ItemsUpdatingScrollMode` ，表示將 `CarouselView` 新專案加入其中時的滾動行為。
- `VerticalScrollBarVisibility`，屬於類型 `ScrollBarVisibility` ，可指定垂直捲動條何時可見。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。 這兩個多載都有額外的引數，可指定以指出專案在捲軸完成後的確切位置，以及是否要以動畫顯示捲軸。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 其中一個方法時所引發的事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)事件隨附的物件 `ScrollToRequested` 有許多屬性，包括 `IsAnimated` 、 `Index` 、 `Item` 和 `ScrollToPosition` 。 這些屬性是由方法呼叫中指定的引數所設定 `ScrollTo` 。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView) `Scrolled` 會定義引發的事件，以指出發生滾動。 `ItemsViewScrolledEventArgs`事件隨附的物件 `Scrolled` 有許多屬性。 如需詳細資訊，請參閱偵測[滾動](#detect-scrolling)。

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 這項功能稱為「貼齊」，因為當滾動停止時，專案會貼齊位置。 如需詳細資訊，請參閱[貼齊點](#snap-points)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也可以在使用者滾動時，以累加方式載入資料。 如需詳細資訊，請參閱[以累加方式載入資料](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>偵測滾動

您 `IsDragging` 可以檢查屬性，以判斷目前是否 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 正在透過專案進行滾動。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView) `Scrolled` 會定義引發的事件，以指出發生滾動。 當需要 scroll 的相關資料時，應該使用此事件。

下列 XAML 範例顯示 `CarouselView` ，它會設定事件的事件處理常式 `Scrolled` ：

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

在此程式碼範例中，事件 `OnCarouselViewScrolled` 處理常式會在 `Scrolled` 事件引發時執行：

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

在此範例中， `OnCarouselViewScrolled` 事件處理常式 `ItemsViewScrolledEventArgs` 會輸出事件隨附之物件的值。

> [!IMPORTANT]
> `Scrolled`引發使用者起始滾動的事件，並以程式設計方式進行滾動。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動到 view

第一個方法多載會將 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 指定索引處的專案滾動到 view。 假設有一個 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名為的物件 `carouselView` ，下列範例示範如何將位於索引6的專案滾動到 view：

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="scroll-an-item-into-view"></a>將專案滾動到視野中

第二個方法多載會 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 將指定的專案滾動到 view。 假設有一個 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 名為的物件 `carouselView` ，下列範例示範如何將 Proboscis 的猴子專案滾動到 view：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> 叫用 [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) 方法時，就會引發事件 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。

## <a name="disable-scroll-animation"></a>停用捲軸動畫

在中的專案之間移動時，會顯示滾動動畫 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 此動畫會針對使用者起始的滾動進行，並以程式設計方式進行滾動。 `IsScrollAnimated`將屬性設定為， `false` 將會停用這兩個滾動類別的動畫。

或者，您 `animate` 可以將方法的引數 `ScrollTo` 設定為，以停用以程式 `false` 設計方式滾動的滾動動畫：

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

將專案滾動到 view 時，可以使用方法的引數來指定專案在捲軸之後的確切位置 `position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 。 這個引數會接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 列舉成員。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到它在視圖中可見為止：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

這個範例程式碼會產生將專案滾動到視野所需的最小滾動。

> [!NOTE]
> [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)如果在 `position` 呼叫方法時未指定引數，則預設會使用此成員 `ScrollTo` 。

### <a name="start"></a>開始

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的開頭：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

這個範例程式碼會導致專案滾動到視圖的開頭。

### <a name="center"></a>Center

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的中心：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

這個範例程式碼會將專案滾動到視圖的中央。

### <a name="end"></a>結束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的結尾：

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

這個範例程式碼會導致專案滾動到視圖的結尾。

## <a name="control-scroll-position-when-new-items-are-added"></a>新增新專案時的控制項捲軸位置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義 `ItemsUpdatingScrollMode` 屬性，它是由可系結屬性所支援。 這個屬性會取得或設定 `ItemsUpdatingScrollMode` 列舉值，表示將 `CarouselView` 新專案加入其中時的滾動行為。 `ItemsUpdatingScrollMode` 列舉會定義下列成員：

- `KeepItemsInView`調整捲軸位移，以在新增新專案時保持顯示第一個可見的專案。
- `KeepScrollOffset`當加入新專案時，會維護相對於清單開頭的捲軸位移。
- `KeepLastItemInView`調整捲軸位移，以在新增新專案時讓最後一個專案保持可見。

屬性的預設值 `ItemsUpdatingScrollMode` 為 `KeepItemsInView` 。 因此，將新的專案加入至 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 清單中的第一個可見專案時，將會維持顯示狀態。 若要確保新增的專案一律會顯示在清單底部， `ItemsUpdatingScrollMode` 屬性應該設定為 `KeepLastItemInView` ：

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性，並由可系結屬性支援。 這些屬性會取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 列舉值，表示水準或垂直捲動條何時可見。 `ScrollBarVisibility` 列舉會定義下列成員：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)表示平臺的預設捲軸行為，而是和屬性的預設值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)表示捲軸會顯示出來，即使內容適合在視圖中也一樣。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)表示即使內容無法放在視圖中，也不會顯示捲軸。

## <a name="snap-points"></a>貼齊點

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 這項功能稱為「貼齊」，因為當滾動停止時，專案會貼齊至位置，而且是由類別的下列屬性所控制 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) ：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)，屬於類型 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) ，會在滾動時指定貼齊點的行為。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)，屬於類型 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) ，指定對齊點與專案對齊的方式。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

> [!NOTE]
> 發生貼齊時，會以產生最少動作的方向進行。

### <a name="snap-points-type"></a>貼齊點類型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)列舉會定義下列成員：

- `None`表示滾動不會貼齊專案。
- `Mandatory`指出內容一律會貼齊到最接近的貼齊點，也就是慣性的方向。
- `MandatorySingle`表示與相同的行為 `Mandatory` ，但一次只會滾動一個專案。

根據預設，在上 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性會設定為 `SnapPointsType.MandatorySingle` ，以確保滾動一次只會滾動一個專案。

下列螢幕擷取畫面顯示 [已關閉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 貼齊]：

[![IOS 和 Android 上沒有貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-none.png "不含貼齊點的 CarouselView")](scrolling-images/snappoints-none-large.png#lightbox "不含貼齊點的 CarouselView")

### <a name="snap-points-alignment"></a>貼齊點對齊

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義 `Start` 、 `Center` 和 `End` 成員。

> [!IMPORTANT]
> [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)只有當 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) 屬性設定為或時，才會遵守屬性的值 `Mandatory` `MandatorySingle` 。

#### <a name="start"></a>開始

`SnapPointsAlignment.Start`成員表示貼齊點與專案的前置邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動以水準捲軸起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，左側專案會與視圖的左邊對齊：

[![在 iOS 和 Android 上具有開始貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-start.png "具有開始貼齊點的 CarouselView")](scrolling-images/snappoints-start-large.png#lightbox "具有開始貼齊點的 CarouselView")

#### <a name="center"></a>Center

`SnapPointsAlignment.Center`成員表示貼齊點對齊專案的中心。

根據預設，在上 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) 屬性會設定為 `Center` 。 不過，基於完整性，下列 XAML 範例示範如何設定這個列舉成員：

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

當使用者撥動在水準捲軸中起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，中心專案會對齊視圖的中心：

[![在 iOS 和 Android 上具有中心貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-center.png "具有中心貼齊點的 CarouselView")](scrolling-images/snappoints-center-large.png#lightbox "具有中心貼齊點的 CarouselView")

#### <a name="end"></a>結束

`SnapPointsAlignment.End`成員表示貼齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動以水準捲軸起始滾動時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，右邊的專案會對齊視圖的右邊。

[![在 iOS 和 Android 上具有結束貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-end.png "具有結束貼齊點的 CarouselView")](scrolling-images/snappoints-end-large.png#lightbox "具有結束貼齊點的 CarouselView")

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
