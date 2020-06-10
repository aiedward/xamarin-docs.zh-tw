---
標題：「 Xamarin.Forms CarouselView 互動」描述：「CarouselView 中目前顯示的專案可以透過 CurrentItem 和 Position 屬性來存取」。
assetid： 854D97E5-D119-4BE2-AE7C-BD428792C992 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/11/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview-interaction"></a>Xamarin.FormsCarouselView 互動

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義可控制使用者互動的下列屬性：

- `CurrentItem`，屬於類型 `object` ，這是目前正在顯示的專案。 這個屬性具有的預設系結模式 `TwoWay` ，而且沒有 `null` 任何可顯示的資料時，會有值。
- `CurrentItemChangedCommand`，屬於類型 `ICommand` ，這是在目前專案變更時執行的。
- `CurrentItemChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `CurrentItemChangedCommand` 的參數。
- `IsBounceEnabled`，屬於類型 `bool` ，指定是否 `CarouselView` 會在內容界限中彈跳。 預設值是 `true`。
- `IsSwipeEnabled`，屬於類型 `bool` ，可決定滑動手勢是否會變更顯示的專案。 預設值是 `true`。
- `Position`，屬於類型 `int` ，這是基礎集合中目前專案的索引。 這個屬性具有的預設系結模式 `TwoWay` ，而且沒有任何可顯示的資料時，其值為0。
- `PositionChangedCommand`，屬於類型 `ICommand` ，會在位置變更時執行。
- `PositionChangedCommandParameter`，屬於 `object` 類型，這是傳遞至 `PositionChangedCommand` 的參數。
- `VisibleViews`，屬於類型 `ObservableCollection<View>` ，這是唯讀屬性，其中包含目前可見專案的物件。

所有這些屬性都是以物件為後盾，也就是說，這些屬性 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 可以是資料系結的目標。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義 `CurrentItemChanged` 當 `CurrentItem` 屬性變更時（可能是因為使用者滾動），或應用程式設定屬性時，所引發的事件。 `CurrentItemChangedEventArgs`事件隨附的物件 `CurrentItemChanged` 有兩個屬性，兩種類型都是 `object` ：

- `PreviousItem`–在屬性變更之後的上一個專案。
- `CurrentItem`–在屬性變更之後的目前專案。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義 `PositionChanged` 當 `Position` 屬性變更時（可能是因為使用者滾動），或應用程式設定屬性時，所引發的事件。 `PositionChangedEventArgs`事件隨附的物件 `PositionChanged` 有兩個屬性，兩種類型都是 `int` ：

- `PreviousPosition`–在屬性變更之後的先前位置。
- `CurrentPosition`–在屬性變更之後的目前位置。

## <a name="respond-to-the-current-item-changing"></a>回應目前的專案變更

當目前顯示的專案變更時， `CurrentItem` 屬性將會設定為專案的值。 當這個屬性變更時， `CurrentItemChangedCommand` 會以 `CurrentItemChangedCommandParameter` 傳遞至的值來執行 `ICommand` 。 `Position`然後會更新屬性，並 `CurrentItemChanged` 引發事件。

> [!IMPORTANT]
> `Position`當屬性變更時，屬性會變更 `CurrentItem` 。 這會導致 `PositionChangedCommand` 執行，並 `PositionChanged` 引發事件。

### <a name="event"></a>事件

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它會使用事件處理常式來回應目前的專案變更：

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

在此範例中，事件 `OnCurrentItemChanged` 處理常式會在 `CurrentItemChanged` 事件引發時執行：

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

在此範例中， `OnCurrentItemChanged` 事件處理常式會公開先前和目前的專案：

[![IOS 和 Android 上具有先前和目前專案之 CarouselView 的螢幕擷取畫面](interaction-images/current-item-events.png "具有目前和前一個專案的 CarouselView")](interaction-images/current-item-events-large.png#lightbox "具有目前和前一個專案的 CarouselView")

### <a name="command"></a>Command

下列 XAML 範例顯示，其 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 使用命令回應目前的專案變更：

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

在此範例中， `CurrentItemChangedCommand` 屬性會系結至 `ItemChangedCommand` 屬性，並將 `CurrentItem` 屬性值當做引數傳遞給它。 `ItemChangedCommand`然後，可以視需要回應目前的專案變更：

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

在此範例中， `ItemChangedCommand` 會更新儲存先前和目前專案的物件。

## <a name="respond-to-the-position-changing"></a>回應位置變更

當目前顯示的專案變更時， `Position` 屬性將會設定為基礎集合中目前專案的索引。 當這個屬性變更時， `PositionChangedCommand` 會以 `PositionChangedCommandParameter` 傳遞至的值來執行 `ICommand` 。 `PositionChanged`接著會引發事件。 如果屬性已以程式設計 `Position` 方式變更， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將會滾動至對應至值的專案 `Position` 。

> [!NOTE]
> `Position`將屬性設定為0會導致基礎集合中的第一個專案顯示。

### <a name="event"></a>事件

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它會使用事件處理常式來回應屬性的 `Position` 變更：

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

在此範例中，事件 `OnPositionChanged` 處理常式會在 `PositionChanged` 事件引發時執行：

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

在此範例中， `OnCurrentItemChanged` 事件處理常式會公開先前和目前的位置：

[![在 iOS 和 Android 上，具有先前和目前位置之 CarouselView 的螢幕擷取畫面](interaction-images/current-position-events.png "具有目前和先前位置的 CarouselView")](interaction-images/current-position-events-large.png#lightbox "具有目前和先前位置的 CarouselView")

### <a name="command"></a>Command

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它會使用命令來回應 `Position` 屬性的變更：

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

在此範例中， `PositionChangedCommand` 屬性會系結至 `PositionChangedCommand` 屬性，並將 `Position` 屬性值當做引數傳遞給它。 `PositionChangedCommand`然後，可以視需要回應變更的位置：

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

在此範例中， `PositionChangedCommand` 會更新儲存先前和目前位置的物件。

## <a name="preset-the-current-item"></a>預先設定目前的專案

藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將屬性設定為專案，可以透過程式設計方式設定中的目前專案 `CurrentItem` 。 下列 XAML 範例顯示 `CarouselView` 預先選擇目前專案的：

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

`CarouselView.CurrentItem`屬性資料會系結至已 `CurrentItem` 連接視圖模型的屬性，其類型為 `Monkey` 。 根據預設，會使用系結， `TwoWay` 因此，如果使用者變更目前的專案，則屬性的值 `CurrentItem` 將會設定為目前的 `Monkey` 物件。 `CurrentItem`屬性定義于 `MonkeysViewModel` 類別中：

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

[![IOS 和 Android 上具有預設專案之 CarouselView 的螢幕擷取畫面](interaction-images/preset-item.png "具有預設專案的 CarouselView")](interaction-images/preset-item-large.png#lightbox "具有預設專案的 CarouselView")

## <a name="preset-the-position"></a>設定位置的預設值

藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將 `Position` 屬性設定為基礎集合中專案的索引，可以透過程式設計方式設定所顯示的專案。 下列 XAML 範例顯示 `CarouselView` 設定所顯示專案的：

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

`CarouselView.Position`屬性資料會系結至已 `Position` 連接視圖模型的屬性，其類型為 `int` 。 根據預設，會使用系結， `TwoWay` 因此，如果使用者滾動到 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，則屬性的值 `Position` 將會設定為所顯示專案的索引。 `Position`屬性定義于 `MonkeysViewModel` 類別中：

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

[![在 iOS 和 Android 上具有預設位置之 CarouselView 的螢幕擷取畫面](interaction-images/preset-position.png "具有預設位置的 CarouselView")](interaction-images/preset-position-large.png#lightbox "具有預設位置的 CarouselView")

## <a name="define-visual-states"></a>定義視覺狀態

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義四種視覺狀態：

- `CurrentItem`表示目前所顯示專案的視覺狀態。
- `PreviousItem`表示先前顯示之專案的視覺狀態。
- `NextItem`表示下一個專案的視覺狀態。
- `DefaultItem`表示其餘專案的視覺狀態。

這些視覺狀態可以用來對所顯示的專案起始視覺效果變更 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。

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

在此範例中， `CurrentItem` 視覺狀態會指定所顯示的目前專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會將其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性從預設值1變更為1.1。 `PreviousItem`和 `NextItem` 視覺狀態會指定顯示目前專案周圍的專案，其 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 值為0.5。 `DefaultItem`視覺狀態會指定顯示的其餘專案 `CarouselView` 會顯示 `Opacity` 值為0.25。

> [!NOTE]
> 或者，您也可以在 [`Style`](xref:Xamarin.Forms.Style) 具有屬性值（其為的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 根項目類型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，設定為 `ItemTemplate` 屬性值）的中定義視覺狀態。

下列螢幕擷取畫面顯示 `CurrentItem` 、 `PreviousItem` 和 `NextItem` 視覺狀態：

[![在 iOS 和 Android 上使用視覺狀態的 CarouselView 螢幕擷取畫面](interaction-images/visual-states.png "CarouselView 視覺狀態")](interaction-images/visual-states-large.png#lightbox "CarouselView 視覺狀態")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="clear-the-current-item"></a>清除目前的專案

藉 `CurrentItem` 由將屬性（property）或其系結至的物件加以設定，即可清除該屬性 `null` 。

## <a name="disable-bounce"></a>停用跳動

根據預設，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 在內容界限中彈跳專案。 將屬性設定為，即可停用這項功能 `IsBounceEnabled` `false` 。

## <a name="disable-swipe-interaction"></a>停用滑動互動

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可讓使用者使用滑動手勢在專案間移動。 您可以藉由將屬性設定為來停用此輕刷互動 `IsSwipeEnabled` `false` 。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
