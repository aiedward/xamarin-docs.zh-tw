---
title: Xamarin. Forms CarouselView 互動
description: 您可以透過 [CurrentItem] 和 [位置] 屬性來存取 CarouselView 中目前顯示的專案。
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: fd85876b38c21c7ff1ea85d7a61c1449395d56f5
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749794"
---
# <a name="xamarinforms-carouselview-interaction"></a>Xamarin. Forms CarouselView 互動

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義可控制使用者互動的下列屬性：

- `CurrentItem`，屬於 `object` 類型的，目前正在顯示的專案。 這個屬性具有 `TwoWay` 的預設系結模式，而且沒有任何可顯示的資料時，具有 `null` 值。
- `CurrentItemChangedCommand`，屬於 `ICommand` 類型，這會在目前的專案變更時執行。
- `CurrentItemChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `CurrentItemChangedCommand` 的參數。
- `IsBounceEnabled`，屬於 `bool` 類型，指定 `CarouselView` 是否會在內容界限中彈跳。 預設值是 `true`。
- `IsSwipeEnabled`，屬於 `bool` 類型，可決定滑動手勢是否會變更顯示的專案。 預設值是 `true`。
- `Position`，屬於 `int` 類型，這是基礎集合中目前專案的索引。 這個屬性具有 `TwoWay` 的預設系結模式，而且沒有任何可顯示的資料時，會有0值。
- `PositionChangedCommand`，屬於 `ICommand` 類型，會在位置變更時執行。
- `PositionChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `PositionChangedCommand` 的參數。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義當 `CurrentItem` 屬性變更時（可能是因為使用者滾動，或是應用程式設定屬性時，所引發的 `CurrentItemChanged` 事件）。 伴隨 `CurrentItemChanged` 事件的 `CurrentItemChangedEventArgs` 物件有兩個屬性，兩種類型都 `object`：

- `PreviousItem` –在屬性變更之後的上一個專案。
- `CurrentItem` –屬性變更之後的目前專案。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義當 `Position` 屬性變更時（可能是因為使用者滾動，或是應用程式設定屬性時，所引發的 `PositionChanged` 事件）。 伴隨 `PositionChanged` 事件的 `PositionChangedEventArgs` 物件有兩個屬性，兩種類型都 `int`：

- `PreviousPosition` –屬性變更之後的先前位置。
- `CurrentPosition` –屬性變更之後的目前位置。

## <a name="respond-to-the-current-item-changing"></a>回應目前的專案變更

當目前顯示的專案變更時，`CurrentItem` 屬性會設定為專案的值。 當這個屬性變更時，會以傳遞至 `ICommand` 的 `CurrentItemChangedCommandParameter` 值來執行 `CurrentItemChangedCommand`。 然後會更新 `Position` 屬性，並引發 `CurrentItemChanged` 事件。

> [!IMPORTANT]
> 當 `CurrentItem` 屬性變更時，`Position` 屬性會變更。 這會導致執行中的 `PositionChangedCommand`，並引發 `PositionChanged` 事件。

### <a name="event"></a>Event - 事件

下列 XAML 範例顯示使用事件處理常式來回應目前專案變更的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

在此範例中，當 `CurrentItemChanged` 事件引發時，會執行 `OnCurrentItemChanged` 事件處理常式，事件處理常式會公開先前和目前的專案：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

### <a name="command"></a>命令

下列 XAML 範例顯示使用命令來回應目前專案變更的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

在此範例中，`CurrentItemChangedCommand` 屬性會系結至 `ItemChangedCommand` 屬性，並將 `CurrentItem` 屬性值當做引數傳遞給它。 然後，`ItemChangedCommand` 可以視需要回應目前變更的專案：

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

在此範例中，`ItemChangedCommand` 會更新儲存先前和目前專案的物件。

## <a name="respond-to-the-position-changing"></a>回應位置變更

當目前顯示的專案變更時，`Position` 屬性會設定為基礎集合中目前專案的索引。 當這個屬性變更時，會以傳遞至 `ICommand` 的 `PositionChangedCommandParameter` 值來執行 `PositionChangedCommand`。 接著會引發 `PositionChanged` 事件。 如果 `Position` 屬性已以程式設計方式變更， [`CarouselView`](xref:Xamarin.Forms.CarouselView)將會滾動至對應至 `Position` 值的專案。

> [!NOTE]
> 將 `Position` 屬性設為0，會導致基礎集合中的第一個專案顯示。

### <a name="event"></a>Event - 事件

下列 XAML 範例顯示使用事件處理常式來回應 `Position` 屬性變更的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

在此範例中，當 `PositionChanged` 事件引發時，會執行 `OnPositionChanged` 事件處理常式，事件處理常式會公開先前和目前的位置：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

### <a name="command"></a>命令

下列 XAML 範例顯示使用命令來回應 `Position` 屬性變更的[`CarouselView`](xref:Xamarin.Forms.CarouselView) ：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

在此範例中，`PositionChangedCommand` 屬性會系結至 `PositionChangedCommand` 屬性，並將 `Position` 屬性值當做引數傳遞給它。 然後，`PositionChangedCommand` 可以視需要回應變更的位置：

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

在此範例中，`PositionChangedCommand` 會更新儲存先前和目前位置的物件。

## <a name="preset-the-current-item"></a>預先設定目前的專案

[@No__t_1](xref:Xamarin.Forms.CarouselView)中的目前專案可以藉由將 `CurrentItem` 屬性設定為專案，以程式設計方式設定。 下列 XAML 範例顯示預先選擇目前專案的 `CarouselView`：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> @No__t_0 屬性具有 `TwoWay` 的預設系結模式。

@No__t_0 的屬性資料會系結至已連線視圖模型的 `CurrentItem` 屬性，其類型為 `Monkey`。 根據預設，會使用 `TwoWay` 系結，因此，如果使用者變更目前的專案，則 `CurrentItem` 屬性的值將會設定為目前的 `Monkey` 物件。 @No__t_0 屬性是在 `MonkeysViewModel` 類別中定義，而且會設定為 `Monkeys` 集合中的第四個專案：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

## <a name="preset-the-position"></a>設定位置的預設值

藉由將 `Position` 屬性設定為基礎集合中專案的索引，可以透過程式設計方式設定所顯示的專案[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 下列 XAML 範例顯示設定所顯示專案的 `CarouselView`：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> @No__t_0 屬性具有 `TwoWay` 的預設系結模式。

@No__t_0 的屬性資料會系結至已連線視圖模型的 `Position` 屬性，其類型為 `int`。 根據預設，會使用 `TwoWay` 系結，因此，如果使用者滾動[`CarouselView`](xref:Xamarin.Forms.CarouselView)，`Position` 屬性的值將會設定為所顯示專案的索引。 @No__t_0 屬性是在 `MonkeysViewModel` 類別中定義，而且會設定為 `Monkeys` 集合中的第四個專案：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

## <a name="clear-the-current-item"></a>清除目前的專案

@No__t_0 屬性可以藉由設定它或其所系結的物件來加以清除，以 `null`。

## <a name="disable-bounce"></a>停用跳動

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會在內容界限中彈跳專案。 這可以藉由將 `IsBounceEnabled` 屬性設定為 `false` 來停用。

## <a name="disable-swipe-interaction"></a>停用滑動互動

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)可讓使用者使用滑動手勢來移動專案。 將 [`IsSwipeEnabled`] 屬性設定為 [`false`]，即可停用此輕刷互動。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
