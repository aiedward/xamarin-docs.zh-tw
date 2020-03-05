---
title: Xamarin. 表單 CarouselView 滾動
description: 當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外，CarouselView 會定義兩個 ScrollTo 方法，以程式設計方式將專案滾動到 view。
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 735a572f4aadfc224e545e371525b96f29c9552e
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292185"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Xamarin. 表單 CarouselView 滾動

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義下列捲軸相關屬性：

- `HorizontalScrollBarVisibility`，屬於 `ScrollBarVisibility`類型，可指定水準捲軸何時可見。
- `IsDragging`，屬於 `bool`類型，表示 `CarouselView` 是否正在滾動。 這是唯讀屬性，其預設值為 `false`。
- `IsScrollAnimated`，屬於 `bool`類型，可指定在滾動 `CarouselView`時是否會發生動畫。 預設值是 `true`。
- `ItemsUpdatingScrollMode`，屬於 `ItemsUpdatingScrollMode`類型，代表將新專案新增至其中時，`CarouselView` 的滾動行為。
- `VerticalScrollBarVisibility`，屬於 `ScrollBarVisibility`類型，可指定垂直捲動條何時可見。

所有這些屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義兩個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。 這兩個多載都有額外的引數，可指定以指出專案在捲軸完成後的確切位置，以及是否要以動畫顯示捲軸。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義當叫用其中一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，所引發的[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。 伴隨 `ScrollToRequested` 事件的[`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs)物件有許多屬性，包括 `IsAnimated`、`Index`、`Item`和 `ScrollToPosition`。 這些屬性是從 `ScrollTo` 方法呼叫中指定的引數所設定。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義引發的 `Scrolled` 事件，以指出滾動發生。 伴隨 `Scrolled` 事件的 `ItemsViewScrolledEventArgs` 物件有許多屬性。 如需詳細資訊，請參閱偵測[滾動](#detect-scrolling)。

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 這項功能稱為「貼齊」，因為當滾動停止時，專案會貼齊位置。 如需詳細資訊，請參閱[貼齊點](#snap-points)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也可以在使用者滾動時，以累加方式載入資料。 如需詳細資訊，請參閱[以累加方式載入資料](populate-data.md#load-data-incrementally)。

## <a name="detect-scrolling"></a>偵測滾動

您可以檢查 `IsDragging` 屬性，以判斷[`CarouselView`](xref:Xamarin.Forms.CarouselView)目前是否正在透過專案進行滾動。

此外， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義引發的 `Scrolled` 事件，以指出滾動發生。 當需要 scroll 的相關資料時，應該使用此事件。

下列 XAML 範例顯示設定 `Scrolled` 事件之事件處理常式的 `CarouselView`：

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

在此程式碼範例中，當 `Scrolled` 事件引發時，會執行 `OnCarouselViewScrolled` 事件處理常式：

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

在此範例中，`OnCarouselViewScrolled` 事件處理常式會輸出事件隨附之 `ItemsViewScrolledEventArgs` 物件的值。

> [!IMPORTANT]
> 針對使用者起始的滾動和以程式設計方式滾動時，會引發 `Scrolled` 事件。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動到 view

第一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將位於指定索引處的專案滾動到 view。 假設有一個名為 `carouselView`的[`CarouselView`](xref:Xamarin.Forms.CarouselView)物件，下列範例會示範如何將位於索引6的專案滾動到 view：

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> 叫用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，就會引發[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="scroll-an-item-into-view"></a>將專案滾動到視野中

第二個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將指定的專案滾動到 view。 假設有一個名為 `carouselView`的[`CarouselView`](xref:Xamarin.Forms.CarouselView)物件，下列範例會示範如何將 Proboscis 的猴子專案滾動到 view：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> 叫用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時，就會引發[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)事件。

## <a name="disable-scroll-animation"></a>停用捲軸動畫

在[`CarouselView`](xref:Xamarin.Forms.CarouselView)中的專案之間移動時，會顯示滾動動畫。 此動畫會針對使用者起始的滾動進行，並以程式設計方式進行滾動。 將 `IsScrollAnimated` 屬性設定為 `false` 將會停用這兩個滾動類別的動畫。

或者，您可以將 `ScrollTo` 方法的 `animate` 引數設定為 `false`，以在程式設計的滾動時停用滾動動畫：

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

將專案滾動到 view 時，可以使用[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的 `position` 引數來指定專案在完成滾動之後的確切位置。 這個引數會接受[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)列舉成員。

### <a name="makevisible"></a>MakeVisible

[ [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員] 表示專案應該滾動，直到在視圖中顯示為止：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

這個範例程式碼會產生將專案滾動到視野所需的最小滾動。

> [!NOTE]
> 如果呼叫 `ScrollTo` 方法時未指定 `position` 引數，預設會使用[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員。

### <a name="start"></a>Start

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的開頭：

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

這個範例程式碼會導致專案滾動到視圖的開頭。

### <a name="center"></a>中心

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員表示該專案應該滾動到視圖的中心：

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義 `ItemsUpdatingScrollMode` 屬性，其受可系結屬性支援。 這個屬性會取得或設定 `ItemsUpdatingScrollMode` 列舉值，表示在新專案新增至其中時，`CarouselView` 的滾動行為。 `ItemsUpdatingScrollMode` 列舉會定義下列成員：

- `KeepItemsInView` 調整捲軸位移，以在新增新專案時保持顯示第一個可見的專案。
- 新增新專案時，`KeepScrollOffset` 會維護相對於清單開頭的捲軸位移。
- `KeepLastItemInView` 調整捲軸位移，以便在新增新專案時，讓最後一個專案保持可見。

`ItemsUpdatingScrollMode` 屬性的預設值為 `KeepItemsInView`。 因此，當新專案新增至[`CarouselView`](xref:Xamarin.Forms.CarouselView)時，清單中的第一個可見專案將會保持顯示狀態。 為確保新加入的專案一律會顯示在清單底部，`ItemsUpdatingScrollMode` 屬性應該設定為 `KeepLastItemInView`：

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義可系結屬性所支援的 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性。 這些屬性會取得或設定[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)列舉值，以表示水準或垂直捲動條何時可見。 `ScrollBarVisibility` 列舉會定義下列成員：

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

根據預設，在[`CarouselView`](xref:Xamarin.Forms.CarouselView)上， [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)屬性會設定為 [`SnapPointsType.MandatorySingle`]，以確保滾動一次只會滾動一個專案。

下列螢幕擷取畫面顯示貼齊已關閉的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

[![IOS 和 Android 上沒有貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-none.png "不含貼齊點的 CarouselView")](scrolling-images/snappoints-none-large.png#lightbox "不含貼齊點的 CarouselView")

### <a name="snap-points-alignment"></a>貼齊點對齊

[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義 `Start`、`Center`和 `End` 成員。

> [!IMPORTANT]
> 只有當 [ [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) ] 屬性設定為 [`Mandatory`] 或 [`MandatorySingle`] 時，才會遵守[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性的值。

#### <a name="start"></a>Start

`SnapPointsAlignment.Start` 成員表示貼齊點與專案的前置邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動起始水準滾動[`CarouselView`](xref:Xamarin.Forms.CarouselView)的滾動時，左側專案會與視圖的左邊對齊：

[![在 iOS 和 Android 上具有開始貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-start.png "具有開始貼齊點的 CarouselView")](scrolling-images/snappoints-start-large.png#lightbox "具有開始貼齊點的 CarouselView")

#### <a name="center"></a>中心

`SnapPointsAlignment.Center` 成員表示貼齊點對齊專案的中心。

根據預設，在[`CarouselView`](xref:Xamarin.Forms.CarouselView)上， [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性會設定為 [`Center`]。 不過，基於完整性，下列 XAML 範例示範如何設定這個列舉成員：

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

當使用者撥動起始水準滾動[`CarouselView`](xref:Xamarin.Forms.CarouselView)的滾動時，中心專案會對齊視圖的中心：

[![在 iOS 和 Android 上具有中心貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-center.png "具有中心貼齊點的 CarouselView")](scrolling-images/snappoints-center-large.png#lightbox "具有中心貼齊點的 CarouselView")

#### <a name="end"></a>結束

`SnapPointsAlignment.End` 成員表示貼齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員：

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

當使用者撥動起始水準滾動[`CarouselView`](xref:Xamarin.Forms.CarouselView)的滾動時，右邊的專案會對齊視圖的右邊。

[![在 iOS 和 Android 上具有結束貼齊點的 CarouselView 螢幕擷取畫面](scrolling-images/snappoints-end.png "具有結束貼齊點的 CarouselView")](scrolling-images/snappoints-end-large.png#lightbox "具有結束貼齊點的 CarouselView")

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
