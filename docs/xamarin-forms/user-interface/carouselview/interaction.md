---
title: Xamarin. Forms CarouselView 互動
description: 您可以透過 [CurrentItem] 和 [位置] 屬性來存取 CarouselView 中目前顯示的專案。
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 150c358346f90a513e1558dc847ad7eb6dd6e6e2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918479"
---
# <a name="xamarinforms-carouselview-interaction"></a>Xamarin. Forms CarouselView 互動

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)會定義可控制使用者互動的下列屬性：

- `CurrentItem`，屬於 `object`類型的，目前正在顯示的專案。 這個屬性具有 `TwoWay`的預設系結模式，而且沒有任何可顯示的資料時，具有 `null` 值。
- `CurrentItemChangedCommand`，屬於 `ICommand`類型，這會在目前的專案變更時執行。
- `CurrentItemChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `CurrentItemChangedCommand` 的參數。
- `IsBounceEnabled`，屬於 `bool`類型，指定 `CarouselView` 是否會在內容界限中彈跳。 預設值是 `true`。
- `IsSwipeEnabled`，屬於 `bool`類型，可決定滑動手勢是否會變更顯示的專案。 預設值是 `true`。
- `Position`，屬於 `int`類型，這是基礎集合中目前專案的索引。 這個屬性具有 `TwoWay`的預設系結模式，而且沒有任何可顯示的資料時，會有0值。
- `PositionChangedCommand`，屬於 `ICommand`類型，會在位置變更時執行。
- `PositionChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `PositionChangedCommand` 的參數。
- `VisibleViews`，屬於 `ObservableCollection<View>`類型，這是一個唯讀屬性，其中包含目前可見專案的物件。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義當 `CurrentItem` 屬性變更時（可能是因為使用者滾動，或是應用程式設定屬性時，所引發的 `CurrentItemChanged` 事件）。 伴隨 `CurrentItemChanged` 事件的 `CurrentItemChangedEventArgs` 物件有兩個屬性，兩種類型都 `object`：

- `PreviousItem` –在屬性變更之後的上一個專案。
- `CurrentItem` –屬性變更之後的目前專案。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義當 `Position` 屬性變更時（可能是因為使用者滾動，或是應用程式設定屬性時，所引發的 `PositionChanged` 事件）。 伴隨 `PositionChanged` 事件的 `PositionChangedEventArgs` 物件有兩個屬性，兩種類型都 `int`：

- `PreviousPosition` –屬性變更之後的先前位置。
- `CurrentPosition` –屬性變更之後的目前位置。

## <a name="respond-to-the-current-item-changing"></a>回應目前的專案變更

當目前顯示的專案變更時，`CurrentItem` 屬性會設定為專案的值。 當這個屬性變更時，會以傳遞至 `ICommand`的 `CurrentItemChangedCommandParameter` 值來執行 `CurrentItemChangedCommand`。 然後會更新 `Position` 屬性，並引發 `CurrentItemChanged` 事件。

> [!IMPORTANT]
> 當 `CurrentItem` 屬性變更時，`Position` 屬性會變更。 這會導致執行中的 `PositionChangedCommand`，並引發 `PositionChanged` 事件。

### <a name="event"></a>事件

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

在此範例中，當 `CurrentItemChanged` 事件引發時，會執行 `OnCurrentItemChanged` 事件處理常式：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

在此範例中，`OnCurrentItemChanged` 事件處理常式會公開先前和目前的專案：

[![IOS 和 Android 上具有先前和目前專案之 CarouselView 的螢幕擷取畫面](interaction-images/current-item-events.png "具有目前和前一個專案的 CarouselView")](interaction-images/current-item-events-large.png#lightbox "具有目前和前一個專案的 CarouselView")

### <a name="command"></a>Command

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

當目前顯示的專案變更時，`Position` 屬性會設定為基礎集合中目前專案的索引。 當這個屬性變更時，會以傳遞至 `ICommand`的 `PositionChangedCommandParameter` 值來執行 `PositionChangedCommand`。 接著會引發 `PositionChanged` 事件。 如果 `Position` 屬性已以程式設計方式變更， [`CarouselView`](xref:Xamarin.Forms.CarouselView)將會滾動至對應至 `Position` 值的專案。

> [!NOTE]
> 將 `Position` 屬性設為0，會導致基礎集合中的第一個專案顯示。

### <a name="event"></a>事件

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

在此範例中，當 `PositionChanged` 事件引發時，會執行 `OnPositionChanged` 事件處理常式：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

在此範例中，`OnCurrentItemChanged` 事件處理常式會公開先前和目前的位置：

[![在 iOS 和 Android 上，具有先前和目前位置之 CarouselView 的螢幕擷取畫面](interaction-images/current-position-events.png "具有目前和先前位置的 CarouselView")](interaction-images/current-position-events-large.png#lightbox "具有目前和先前位置的 CarouselView")

### <a name="command"></a>Command

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView)中的目前專案可以藉由將 `CurrentItem` 屬性設定為專案，以程式設計方式設定。 下列 XAML 範例顯示預先選擇目前專案的 `CarouselView`：

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
> `CurrentItem` 屬性具有 `TwoWay`的預設系結模式。

`CarouselView.CurrentItem` 的屬性資料會系結至已連線視圖模型的 `CurrentItem` 屬性，其類型為 `Monkey`。 根據預設，會使用 `TwoWay` 系結，因此，如果使用者變更目前的專案，則 `CurrentItem` 屬性的值將會設定為目前的 `Monkey` 物件。 `CurrentItem` 屬性是在 `MonkeysViewModel` 類別中定義的：

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

在此範例中，`CurrentItem` 屬性會設定為 `Monkeys` 集合中的第四個專案：

[![IOS 和 Android 上具有預設專案之 CarouselView 的螢幕擷取畫面](interaction-images/preset-item.png "具有預設專案的 CarouselView")](interaction-images/preset-item-large.png#lightbox "具有預設專案的 CarouselView")

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
> `Position` 屬性具有 `TwoWay`的預設系結模式。

`CarouselView.Position` 的屬性資料會系結至已連線視圖模型的 `Position` 屬性，其類型為 `int`。 根據預設，會使用 `TwoWay` 系結，因此，如果使用者滾動[`CarouselView`](xref:Xamarin.Forms.CarouselView)，`Position` 屬性的值將會設定為所顯示專案的索引。 `Position` 屬性是在 `MonkeysViewModel` 類別中定義的：

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

在此範例中，`Position` 屬性會設定為 `Monkeys` 集合中的第四個專案：

[![在 iOS 和 Android 上具有預設位置之 CarouselView 的螢幕擷取畫面](interaction-images/preset-position.png "具有預設位置的 CarouselView")](interaction-images/preset-position-large.png#lightbox "具有預設位置的 CarouselView")

## <a name="define-visual-states"></a>定義視覺狀態

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義四種視覺狀態：

- `CurrentItem` 代表目前所顯示專案的視覺狀態。
- `PreviousItem` 代表先前顯示之專案的視覺狀態。
- `NextItem` 代表下一個專案的視覺狀態。
- `DefaultItem` 代表其餘專案的視覺狀態。

這些視覺狀態可以用來起始[`CarouselView`](xref:Xamarin.Forms.CarouselView)所顯示專案的視覺變更。

下列 XAML 範例顯示如何定義 `CurrentItem`、`PreviousItem`、`NextItem`和 `DefaultItem` 的視覺狀態：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

在此範例中，`CurrentItem` 視覺狀態會指定由[`CarouselView`](xref:Xamarin.Forms.CarouselView)所顯示的目前專案，其[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性會從其預設值1變更為1.1。 [`PreviousItem`] 和 [`NextItem` 視覺狀態] 指定目前專案周圍的專案將以0.5 的[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)值顯示。 [`DefaultItem` 視覺狀態] 會指定 `CarouselView` 顯示的其餘專案，其 `Opacity` 值為0.25。

> [!NOTE]
> 或者，您也可以在具有[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性值（也就是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的根項目類型，設定為 `ItemTemplate` 屬性值）的[`Style`](xref:Xamarin.Forms.Style)中定義視覺狀態。

下列螢幕擷取畫面顯示 [`CurrentItem`]、[`PreviousItem`] 和 [`NextItem`] 視覺狀態：

[![在 iOS 和 Android 上使用視覺狀態的 CarouselView 螢幕擷取畫面](interaction-images/visual-states.png "CarouselView 視覺狀態")](interaction-images/visual-states-large.png#lightbox "CarouselView 視覺狀態")

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="clear-the-current-item"></a>清除目前的專案

`CurrentItem` 屬性可以藉由設定它或其所系結的物件來加以清除，以 `null`。

## <a name="disable-bounce"></a>停用跳動

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會在內容界限中彈跳專案。 這可以藉由將 `IsBounceEnabled` 屬性設定為 `false`來停用。

## <a name="disable-swipe-interaction"></a>停用滑動互動

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)可讓使用者使用滑動手勢來移動專案。 將 [`IsSwipeEnabled`] 屬性設定為 [`false`]，即可停用此輕刷互動。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
