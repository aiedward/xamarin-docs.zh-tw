---
title: CollectionView 選取專案的表單
description: 預設會停用 CollectionView 選項。 不過，可以啟用單一和多重選取。
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304468"
---
# <a name="xamarinforms-collectionview-selection"></a>CollectionView 選取專案的表單

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義控制專案選取的下列屬性：

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)，屬於[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)類型的選取模式。
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)，屬於 `object`的類型，這是清單中選取的專案。 此屬性具有 `TwoWay`的預設系結模式，而且未選取任何專案時，會具有 `null` 值。
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)，屬於 `IList<object>`類型，這是清單中選取的專案。 這個屬性具有 `OneWay`的預設系結模式，而且未選取任何專案時，會有 `null` 值。
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand)，屬於 `ICommand`類型，這會在選取的專案變更時執行。
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)，屬於 `object`類型，這是傳遞至 `SelectionChangedCommand`的參數。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

預設會停用[`CollectionView`](xref:Xamarin.Forms.CollectionView)選取專案。 不過，將[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)屬性值設定為其中一個[`SelectionMode`](xref:Xamarin.Forms.SelectionMode)列舉成員，即可變更此行為：

- `None` –表示無法選取專案。 這是預設值。
- `Single` –表示可選取單一專案，並反白顯示選取的專案。
- `Multiple` –表示可以選取多個專案，並反白顯示選取的專案。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性變更時所引發的[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件，可能是因為使用者從清單中選取某個專案，或是應用程式設定了屬性。 此外，當[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性變更時，也會引發此事件。 伴隨 `SelectionChanged` 事件的[`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs)物件有兩個屬性，兩種類型都 `IReadOnlyList<object>`：

- `PreviousSelection` –選取範圍變更之前所選取的專案清單。
- `CurrentSelection` –選取範圍變更之後所選取的專案清單。

此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有 `UpdateSelectedItems` 方法，會以選取的專案清單更新[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性，而只會引發單一變更通知。

## <a name="single-selection"></a>單一選取

當 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`Single`] 時，就可以選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的單一專案。 選取專案時， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性會設定為所選取專案的值。 當這個屬性變更時，會執行[`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) （具有傳遞給 `ICommand`的[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)值），並引發[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

下列 XAML 範例顯示可以回應單一專案選取的[`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

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

在此程式碼範例中，當[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件引發時，會執行 `OnCollectionViewSelectionChanged` 事件處理常式，事件處理常式會抓取先前選取的專案，以及目前選取的專案：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可以由變更[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)屬性所造成的變更引發。

下列螢幕擷取畫面顯示[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的單一專案選擇：

[![在 iOS 和 Android 上具有單一選取專案之 CollectionView 垂直清單的螢幕擷取畫面](selection-images/single-selection.png "具有單一選取專案的 CollectionView 垂直清單")](selection-images/single-selection-large.png#lightbox "具有單一選取專案的 CollectionView 垂直清單")

## <a name="multiple-selection"></a>多重選取

當 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`Multiple`] 時，就可以選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多個專案。 選取專案時， [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性會設定為選取的專案。 當這個屬性變更時，會執行[`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) （具有傳遞給 `ICommand`的[`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter)值），並引發[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

下列 XAML 範例顯示可以回應多個專案選取的[`CollectionView`](xref:Xamarin.Forms.CollectionView) ：

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

在此程式碼範例中，當[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件引發時，會執行 `OnCollectionViewSelectionChanged` 事件處理常式，事件處理常式會抓取先前選取的專案，以及目前選取的專案：

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件可以由變更[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)屬性所造成的變更引發。

下列螢幕擷取畫面顯示[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多個專案選擇：

[![在 iOS 和 Android 上具有多個選取範圍之 CollectionView 垂直清單的螢幕擷取畫面](selection-images/multiple-selection.png "具有多重選取範圍的 CollectionView 垂直清單")](selection-images/multiple-selection-large.png#lightbox "具有多重選取範圍的 CollectionView 垂直清單")

## <a name="single-pre-selection"></a>單一預先選取

當 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`Single`] 時，可以藉由將[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性設定為專案，預先選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的單一專案。 下列 XAML 範例顯示預先選取單一專案的 `CollectionView`：

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
> [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性具有 `TwoWay`的預設系結模式。

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)的屬性資料會系結至已連線視圖模型的 `SelectedMonkey` 屬性，其類型為 `Monkey`。 根據預設，會使用 `TwoWay` 系結，因此，如果使用者變更選取的專案，則 `SelectedMonkey` 屬性的值將會設定為選取的 `Monkey` 物件。 `SelectedMonkey` 屬性是在 `MonkeysViewModel` 類別中定義，而且會設定為 `Monkeys` 集合的第四個專案：

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

因此，當[`CollectionView`](xref:Xamarin.Forms.CollectionView)出現時，會預先選取清單中的第四個專案：

[![在 iOS 和 Android 上具有單一預先選取專案之 CollectionView 垂直清單的螢幕擷取畫面](selection-images/single-pre-selection.png "具有單一預先選取專案的 CollectionView 垂直清單")](selection-images/single-pre-selection-large.png#lightbox "具有單一預先選取專案的 CollectionView 垂直清單")

## <a name="multiple-pre-selection"></a>多次預先選取

當 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`Multiple`] 時，就可以預先選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的多個專案。 下列 XAML 範例顯示將啟用多個專案之預先選取的 `CollectionView`：

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
> [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性具有 `OneWay`的預設系結模式。

[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)的屬性資料會系結至已連線視圖模型的 `SelectedMonkeys` 屬性，其類型為 `ObservableCollection<object>`。 `SelectedMonkeys` 屬性是在 `MonkeysViewModel` 類別中定義，而且會設定為 `Monkeys` 集合中的第二、第四和第五個專案：

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

因此，當[`CollectionView`](xref:Xamarin.Forms.CollectionView)出現時，會預先選取清單中的第二個、第四和第五個專案：

[![在 iOS 和 Android 上具有多個預先選取專案之 CollectionView 垂直清單的螢幕擷取畫面](selection-images/multiple-pre-selection.png "具有多個預先選取專案的 CollectionView 垂直清單")](selection-images/multiple-pre-selection-large.png#lightbox "具有多個預先選取專案的 CollectionView 垂直清單")

## <a name="clear-selections"></a>清除選取專案

[`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)和[`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems)屬性可以藉由設定它們或其系結至 `null`的物件來加以清除。

## <a name="change-selected-item-color"></a>變更選取的專案色彩

[`CollectionView`](xref:Xamarin.Forms.CollectionView)具有 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用來起始對 `CollectionView`中所選取專案的視覺效果變更。 此 `VisualState` 的常見使用案例是變更所選項目的背景色彩，如下列 XAML 範例所示：

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
> 包含 `Selected` `VisualState` 的[`Style`](xref:Xamarin.Forms.Style)必須具有[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性值，這是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的根項目類型，會設定為 `ItemTemplate` 屬性值。

在此範例中， [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性值設定為 `Grid`，因為[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)的根項目是[`Grid`](xref:Xamarin.Forms.Grid)。 `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState)指定當選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的專案時，專案的[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)將會設定為 `LightSkyBlue`：

[![在 iOS 和 Android 上具有自訂單一選取色彩之 CollectionView 垂直清單的螢幕擷取畫面](selection-images/single-selection-color.png "具有自訂單一選取色彩的 CollectionView 垂直清單")](selection-images/single-selection-color-large.png#lightbox "具有自訂單一選取色彩的 CollectionView 垂直清單")

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-selection"></a>停用選取範圍

預設會停用[`CollectionView`](xref:Xamarin.Forms.CollectionView)選取專案。 不過，如果 `CollectionView` 選取 [已啟用]，則可以藉由將 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`None`] 來停用它：

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

當 [ [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ] 屬性設定為 [`None`] 時，就無法選取[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的專案， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性仍會 `null`，而且不會引發[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件。

> [!NOTE]
> 當選取專案且[`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode)屬性從 `Single` 變更為 `None`時， [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem)屬性會設定為 `null`，而且[`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged)事件將會以空的 `CurrentSelection` 屬性來引發。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
