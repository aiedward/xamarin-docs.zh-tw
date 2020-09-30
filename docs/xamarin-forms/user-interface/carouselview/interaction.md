---
title: Xamarin.Forms CarouselView 互動
description: CarouselView 中目前顯示的專案可以透過 CurrentItem 和 Position 屬性來存取。
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43f2e32a240b74fde50659033096c8a7246ea60c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564052"
---
# <a name="no-locxamarinforms-carouselview-interaction"></a>Xamarin.Forms CarouselView 互動

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義控制使用者互動的下列屬性：

- `CurrentItem`，類型為 `object` 目前顯示的專案。 這個屬性的預設系結模式為 `TwoWay` ， `null` 如果沒有任何要顯示的資料，則具有值。
- `CurrentItemChangedCommand`，類型 `ICommand` 為，當目前專案變更時，就會執行此型別。
- `CurrentItemChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `CurrentItemChangedCommand` 的參數。
- `IsBounceEnabled`，類型為 `bool` ，指定是否 `CarouselView` 會在內容界限上彈跳。 預設值是 `true`。
- `IsSwipeEnabled`，類型為 `bool` ，決定滑動手勢是否會變更顯示的專案。 預設值是 `true`。
- `Position`，型別為 `int` 基礎集合中目前專案的索引。 這個屬性的預設系結模式為 `TwoWay` ，如果沒有任何要顯示的資料，則具有0值。
- `PositionChangedCommand`，類型 `ICommand` 為，其會在位置變更時執行。
- `PositionChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `PositionChangedCommand` 的參數。
- `VisibleViews`，屬於類型 `ObservableCollection<View>` ，這是唯讀屬性，其中包含目前可見專案的物件。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義 `CurrentItemChanged` 當屬性變更時所引發的事件 `CurrentItem` ，可能是因為使用者滾動，或應用程式設定屬性時所引發的事件。 `CurrentItemChangedEventArgs`事件隨附的物件 `CurrentItemChanged` 有兩個屬性，這兩個類型都是 `object` ：

- `PreviousItem` –屬性變更之後的前一個專案。
- `CurrentItem` –屬性變更之後的目前專案。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 也會定義 `PositionChanged` 屬性變更時所引發的事件 `Position` ，可能是因為使用者滾動，或應用程式設定屬性時所引發。 `PositionChangedEventArgs`事件隨附的物件 `PositionChanged` 有兩個屬性，這兩個類型都是 `int` ：

- `PreviousPosition` –屬性變更之後的先前位置。
- `CurrentPosition` –屬性變更之後的目前位置。

## <a name="respond-to-the-current-item-changing"></a>回應目前專案的變更

當目前顯示的專案變更時， `CurrentItem` 屬性會設定為專案的值。 當這個屬性變更時， `CurrentItemChangedCommand` 會以 `CurrentItemChangedCommandParameter` 傳遞至的值來執行 `ICommand` 。 `Position`然後，屬性會更新，並 `CurrentItemChanged` 引發事件。

> [!IMPORTANT]
> 屬性變更 `Position` 時，屬性會變更 `CurrentItem` 。 這會導致執行中 `PositionChangedCommand` ，並 `PositionChanged` 引發事件。

### <a name="event"></a>事件

下列 XAML 範例 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會示範，它會使用事件處理常式來回應目前專案的變更：

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

在此範例中， `OnCurrentItemChanged` 事件處理常式會在 `CurrentItemChanged` 事件引發時執行：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

在此範例中， `OnCurrentItemChanged` 事件處理常式會公開先前和目前的專案：

[![螢幕擷取畫面： iOS 和 Android 上的先前和目前專案的 CarouselView](interaction-images/current-item-events.png "具有目前和先前專案的 CarouselView")](interaction-images/current-item-events-large.png#lightbox "具有目前和先前專案的 CarouselView")

### <a name="command"></a>Command

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 使用命令來回應目前專案變更的：

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

在此範例中， `CurrentItemChangedCommand` 屬性會系結至 `ItemChangedCommand` 屬性，並將 `CurrentItem` 屬性值做為引數傳遞給它。 然後，您 `ItemChangedCommand` 可以視需要回應目前專案的變更：

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

在此範例中， `ItemChangedCommand` 會更新儲存先前和目前專案的物件。

## <a name="respond-to-the-position-changing"></a>回應位置變更

當目前顯示的專案變更時， `Position` 屬性會設定為基礎集合中目前專案的索引。 當這個屬性變更時， `PositionChangedCommand` 會以 `PositionChangedCommandParameter` 傳遞至的值來執行 `ICommand` 。 `PositionChanged`接著會引發事件。 如果屬性已以程式設計 `Position` 方式變更， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將會滾動至對應至值的專案 `Position` 。

> [!NOTE]
> 將 `Position` 屬性設定為0會導致基礎集合中的第一個專案顯示。

### <a name="event"></a>事件

下列 XAML 範例示範如何 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 使用事件處理常式來回應 `Position` 屬性變更：

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

在此範例中， `OnPositionChanged` 事件處理常式會在 `PositionChanged` 事件引發時執行：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

在此範例中， `OnCurrentItemChanged` 事件處理常式會公開先前和目前的位置：

[![螢幕擷取畫面： iOS 和 Android 上的 CarouselView 與先前和目前的位置](interaction-images/current-position-events.png "具有目前和先前位置的 CarouselView")](interaction-images/current-position-events-large.png#lightbox "具有目前和先前位置的 CarouselView")

### <a name="command"></a>Command

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 使用命令來回應 `Position` 屬性變更的：

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

在此範例中， `PositionChangedCommand` 屬性會系結至 `PositionChangedCommand` 屬性，並將 `Position` 屬性值做為引數傳遞給它。 `PositionChangedCommand`接著可以視需要回應位置變更：

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

在此範例中， `PositionChangedCommand` 會更新儲存先前和目前位置的物件。

## <a name="preset-the-current-item"></a>預設值為目前的專案

您 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以藉由將屬性設定為專案，以程式設計方式設定中的目前專案 `CurrentItem` 。 下列 XAML 範例顯示 `CarouselView` 預先選擇目前專案的：

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
> 屬性的預設系結 `CurrentItem` 模式為 `TwoWay` 。

`CarouselView.CurrentItem`屬性資料系結至 `CurrentItem` 連接視圖模型的屬性，其類型為 `Monkey` 。 根據預設，會使用系結， `TwoWay` 因此，如果使用者變更目前的專案，屬性的值 `CurrentItem` 將會設定為目前的 `Monkey` 物件。 `CurrentItem`屬性定義于 `MonkeysViewModel` 類別中：

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

在此範例中， `CurrentItem` 屬性會設定為集合中的第四個專案 `Monkeys` ：

[![螢幕擷取畫面： iOS 和 Android 上具有預設專案的 CarouselView](interaction-images/preset-item.png "具有預設專案的 CarouselView")](interaction-images/preset-item-large.png#lightbox "具有預設專案的 CarouselView")

## <a name="preset-the-position"></a>預設位置

您可以藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將 `Position` 屬性設定為基礎集合中專案的索引，以程式設計方式設定顯示的專案。 下列 XAML 範例顯示的 `CarouselView` 會設定顯示的專案：

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
> 屬性的預設系結 `Position` 模式為 `TwoWay` 。

`CarouselView.Position`屬性資料系結至 `Position` 連接視圖模型的屬性，其類型為 `int` 。 根據預設，會使用系結，如此一來， `TwoWay` 如果使用者滾動 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，屬性的值 `Position` 就會設定為顯示專案的索引。 `Position`屬性定義于 `MonkeysViewModel` 類別中：

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

在此範例中， `Position` 屬性會設定為集合中的第四個專案 `Monkeys` ：

[![螢幕擷取畫面： iOS 和 Android 上具有預設位置的 CarouselView](interaction-images/preset-position.png "具有預設位置的 CarouselView")](interaction-images/preset-position-large.png#lightbox "具有預設位置的 CarouselView")

## <a name="define-visual-states"></a>定義視覺狀態

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義四種視覺狀態：

- `CurrentItem` 表示目前顯示之專案的視覺狀態。
- `PreviousItem` 表示先前顯示之專案的視覺狀態。
- `NextItem` 表示下一個專案的視覺狀態。
- `DefaultItem` 表示其餘專案的視覺狀態。

這些視覺狀態可以用來對所顯示的專案起始視覺變更 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。

下列 XAML 範例顯示如何定義 `CurrentItem` 、 `PreviousItem` 、 `NextItem` 和 `DefaultItem` 視覺狀態：

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

在此範例中， `CurrentItem` 視覺狀態會指定所顯示的目前專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性從預設值1變更為1.1。 `PreviousItem`和 `NextItem` 視覺狀態指定目前專案周圍的專案將會顯示為 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 0.5 的值。 `DefaultItem`視覺狀態會指定所顯示之專案的其餘部分會 `CarouselView` 顯示為0.25 的 `Opacity` 值。

> [!NOTE]
> 或者，您可以在中定義視覺狀態，其 [`Style`](xref:Xamarin.Forms.Style) [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性值是的根項目類型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，會設定為 `ItemTemplate` 屬性值。

下列螢幕擷取畫面顯示 `CurrentItem` 、 `PreviousItem` 和 `NextItem` 視覺狀態：

[![螢幕擷取畫面：在 iOS 和 Android 上使用視覺狀態的 CarouselView](interaction-images/visual-states.png "CarouselView 視覺狀態")](interaction-images/visual-states-large.png#lightbox "CarouselView 視覺狀態")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="clear-the-current-item"></a>清除目前的專案

您 `CurrentItem` 可以將屬性（property）或它所系結的物件設為，以將其清除 `null` 。

## <a name="disable-bounce"></a>停用彈跳

依預設，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 在內容界限上彈跳專案。 您可以藉由將屬性設定為來停用此功能 `IsBounceEnabled` `false` 。

## <a name="disable-swipe-interaction"></a>停用滑動互動

依預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可讓使用者使用滑動手勢來移動專案。 您可以藉由將屬性設定為，來停用這個滑動互動 `IsSwipeEnabled` `false` 。

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)