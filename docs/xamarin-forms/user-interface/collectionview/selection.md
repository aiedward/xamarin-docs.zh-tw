---
title: Xamarin.Forms CollectionView 選取專案
description: 預設會停用 CollectionView 選取專案。 不過，您可以啟用單一和多重選取。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fd4ac89b60cdac8509e5c057c698ef79f7e6e969
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375339"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin.Forms CollectionView 選取專案

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義控制專案選取的下列屬性：

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)，屬於類型的 [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) 選取模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)，類型為 `object` ，在清單中選取的專案。 這個屬性的預設系結模式為 `TwoWay` ，而且 `null` 沒有選取任何專案時的值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)，類型為 `IList<object>` ，在清單中選取的專案。 這個屬性的預設系結模式為 `OneWay` ，而且 `null` 沒有選取任何專案時，會有值。
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)，類型為 `ICommand` ，在選取的專案變更時執行。
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)，屬於類型 `object` ，也就是傳遞給的參數 `SelectionChangedCommand` 。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

預設 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會停用選取專案。 不過，您可以藉由將 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性值設定為其中一個列舉成員來變更此行為 [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) ：

- `None` –表示無法選取專案。 這是預設值。
- `Single` –表示可以選取單一專案，並反白顯示選取的專案。
- `Multiple` –表示可以選取多個專案，並反白顯示選取的專案。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 當屬性變更時所引發的事件 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) ，可能是因為使用者從清單中選取專案，或應用程式設定屬性時所引發。 此外，當屬性變更時，也會引發此事件 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 。 [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs)事件隨附的物件 `SelectionChanged` 有兩個屬性，這兩個類型都是 `IReadOnlyList<object>` ：

- `PreviousSelection` –選取專案變更之前選取的專案清單。
- `CurrentSelection` –選取範圍變更之後，所選取的專案清單。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 也有一種 `UpdateSelectedItems` 方法，可 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 使用選取的專案清單來更新屬性，而只會引發單一變更通知。

## <a name="single-selection"></a>單一選取

當 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為時 `Single` ，可以選取中的單一專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 選取專案時， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 屬性會設定為所選取專案的值。 當這個屬性變更時， [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) 會執行 (，並將的值 [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) 傳遞至 `ICommand`) ，並 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 引發事件。

下列 XAML 範例顯示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以回應單一專案選取的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此程式碼範例中，事件 `OnCollectionViewSelectionChanged` 處理常式是在 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件引發時執行，事件處理常式會抓取先前選取的專案，以及目前選取的專案：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)變更屬性時所發生的變更會引發事件 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 。

下列螢幕擷取畫面顯示中的單一專案選取專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

[![螢幕擷取畫面：在 iOS 和 Android 上選取單一選項的 CollectionView 垂直清單](selection-images/single-selection.png "具有單一選取專案的 CollectionView 垂直清單")](selection-images/single-selection-large.png#lightbox "具有單一選取專案的 CollectionView 垂直清單")

## <a name="multiple-selection"></a>多重選取

當 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為時 `Multiple` ，可以選取中的多個專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 選取專案時， [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 屬性會設定為選取的專案。 當這個屬性變更時， [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) 會執行 (，並將的值 [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) 傳遞至 `ICommand`) ，並 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 引發事件。

下列 XAML 範例顯示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可回應多個專案選取的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

在此程式碼範例中，事件 `OnCollectionViewSelectionChanged` 處理常式是在 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 事件引發時執行，事件處理常式會抓取先前選取的專案，以及目前選取的專案：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)變更屬性時所發生的變更會引發事件 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 。

下列螢幕擷取畫面顯示中的多個專案選取 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

[![螢幕擷取畫面： iOS 和 Android 上有多重選取的 CollectionView 垂直清單](selection-images/multiple-selection.png "CollectionView 具有多個選取範圍的垂直清單")](selection-images/multiple-selection-large.png#lightbox "CollectionView 具有多個選取範圍的垂直清單")

## <a name="single-pre-selection"></a>單一預先選取

當 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為時 `Single` ，可以將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 屬性設定為專案，以預先選取中的單一專案 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 。 下列 XAML 範例顯示 `CollectionView` 預先選取單一專案的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> 屬性的預設系結 [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 模式為 `TwoWay` 。

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性資料系結至 `SelectedMonkey` 連接視圖模型的屬性，其類型為 `Monkey` 。 根據預設，會使用系結， `TwoWay` 因此，如果使用者變更選取的專案，屬性的值 `SelectedMonkey` 將會設定為選取的 `Monkey` 物件。 `SelectedMonkey`屬性定義于 `MonkeysViewModel` 類別中，並設定為集合的第四個專案 `Monkeys` ：

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

因此，當 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 出現時，會預先選取清單中的第四個專案：

[![螢幕擷取畫面： iOS 和 Android 上具有單一預先選取專案的 CollectionView 垂直清單](selection-images/single-pre-selection.png "具有單一預先選取專案的 CollectionView 垂直清單")](selection-images/single-pre-selection-large.png#lightbox "具有單一預先選取專案的 CollectionView 垂直清單")

## <a name="multiple-pre-selection"></a>多重預先選取

當 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為時 `Multiple` ，中的多個專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以預先選取。 下列 XAML 範例顯示可 `CollectionView` 讓您預先選取多個專案的：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> 屬性的預設系結 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 模式為 `OneWay` 。

[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性資料系結至 `SelectedMonkeys` 連接視圖模型的屬性，其類型為 `ObservableCollection<object>` 。 `SelectedMonkeys`屬性定義于 `MonkeysViewModel` 類別中，而且會設定為集合中的第二個、第四個和第五個專案 `Monkeys` ：

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

因此，當 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 出現時，會預先選取清單中的第二個、第四個和第五個專案：

[![螢幕擷取畫面： iOS 和 Android 上有多個預先選取的 CollectionView 垂直清單](selection-images/multiple-pre-selection.png "CollectionView 具有多個預先選取專案的垂直清單")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView 具有多個預先選取專案的垂直清單")

## <a name="clear-selections"></a>清除選取專案

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)和 [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) 屬性可以藉由將和屬性設定為，或它們所系結的物件來進行清除 `null` 。

## <a name="change-selected-item-color"></a>變更選取的專案色彩

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用來起始中所選取專案的視覺變更 `CollectionView` 。 常見的使用案例 `VisualState` 是變更所選取專案的背景色彩，如下列 XAML 範例所示：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> [`Style`](xref:Xamarin.Forms.Style)包含的 `Selected` `VisualState` 屬性值必須是的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 根項目型別 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，其設定為 `ItemTemplate` 屬性值。

在此範例中， [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性值設定為， `Grid` 因為的根項目 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 是 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定當選取中的專案時 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ， [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 專案的會設定為 `LightSkyBlue` ：

[![螢幕擷取畫面：在 iOS 和 Android 上具有自訂單一選取色彩的 CollectionView 垂直清單](selection-images/single-selection-color.png "CollectionView 具有自訂單一選取色彩的垂直清單")](selection-images/single-selection-color-large.png#lightbox "CollectionView 具有自訂單一選取色彩的垂直清單")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>停用選取範圍

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 預設會停用選取專案。 但是，如果 `CollectionView` 選取了 [已啟用]，則可以將屬性設定為，以停用它 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) `None` ：

```xaml
<CollectionView ...
                SelectionMode="None" />
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

當 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性設定為時 `None` ，無法選取中的專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 屬性將會保留 `null` ，且 [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 不會引發事件。

> [!NOTE]
> 當選取專案且 [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) 屬性從變更 `Single` 為時 `None` ， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) 屬性會設定為，而且會以 `null` [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) 空白屬性來引發事件 `CurrentSelection` 。

## <a name="related-links"></a>相關連結

- [CollectionView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)