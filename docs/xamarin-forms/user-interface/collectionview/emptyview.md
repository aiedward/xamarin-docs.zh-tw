---
title: Xamarin. Forms CollectionView EmptyView
description: 在 CollectionView 中，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 9302b9a353173b0445d1d696849b4956134e555d
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696901"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin. Forms CollectionView EmptyView

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列屬性，可在沒有資料可顯示時，用來提供使用者意見反應：

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)，屬於 `object` 類型的字串、系結或視圖， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性 `null` 時，或 `ItemsSource` 屬性所指定的集合 `null` 或空白時，將會顯示。 預設值是 `null`。
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型，這是用來格式化指定 `EmptyView` 的範本。 預設值是 `null`。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

設定[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性的主要使用案例是在[`CollectionView`](xref:Xamarin.Forms.CollectionView)的篩選作業未產生任何資料，以及在從 web 服務抓取資料時顯示使用者意見反應時，顯示使用者意見反應。

> [!NOTE]
> 如有需要，可以將[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性設定為包含互動式內容的視圖。

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>當資料無法使用時顯示字串

[@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為字串，當[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性 `null` 時，或 `ItemsSource` 屬性所指定的集合 `null` 或空白時，就會顯示此字串。 下列 XAML 顯示此案例的範例：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

結果是，因為資料系結集合是 `null` 的，所以會顯示設定為[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性值的字串：

[![在 iOS 和 Android 上具有文字空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/null-itemssource.png "CollectionView 具有空白視圖的垂直清單")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView 具有空白視圖的垂直清單")

## <a name="display-views-when-data-is-unavailable"></a>資料無法使用時顯示視圖

[@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為 view，這會在[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性 `null` 時，或 `ItemsSource` 屬性所指定的集合 `null` 或空白時顯示。 這可以是單一視圖，或包含多個子視圖的視圖。 下列 XAML 範例顯示設定為包含多個子視圖之視圖的 `EmptyView` 屬性：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

當[`SearchBar`](xref:Xamarin.Forms.SearchBar)執行 `FilterCommand` 時， [`CollectionView`](xref:Xamarin.Forms.CollectionView)所顯示的集合會針對儲存在[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)屬性中的搜尋詞彙進行篩選。 如果篩選作業未產生任何資料，則會顯示[`StackLayout`](xref:Xamarin.Forms.StackLayout)設定為[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性值：

[![在 iOS 和 Android 上具有自訂空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/filter-multiple-views.png "具有自訂空白視圖的 CollectionView 垂直清單")](emptyview-images/filter-multiple-views-large.png#lightbox "具有自訂空白視圖的 CollectionView 垂直清單")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>當資料無法使用時，顯示樣板化自訂類型

[@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為自訂類型，當[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性 `null` 時，或 `ItemsSource` 屬性所指定的集合 `null` 或空白時，就會顯示其範本。 [@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性可以設定為定義 `EmptyView` 外觀的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 下列 XAML 顯示此案例的範例：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

@No__t_0 類型會定義 `Filter` 屬性和對應的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)：

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

[@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyView)屬性會設定為 `FilterData` 物件，而 `Filter` 屬性資料會系結至[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)屬性。 當[`SearchBar`](xref:Xamarin.Forms.SearchBar)執行 `FilterCommand` 時， [`CollectionView`](xref:Xamarin.Forms.CollectionView)所顯示的集合會針對儲存在 `Filter` 屬性中的搜尋詞彙進行篩選。 如果篩選作業未產生任何資料，則會顯示[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中所定義的[`Label`](xref:Xamarin.Forms.Label) ，該屬性會設定為[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)內容值）：

[![在 iOS 和 Android 上具有空白視圖範本之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/emptyviewtemplate.png "具有空白視圖範本的 CollectionView 垂直清單")](emptyview-images/emptyviewtemplate-large.png#lightbox "具有空白視圖範本的 CollectionView 垂直清單")

> [!NOTE]
> 當資料無法使用時，顯示樣板化自訂類型時，可以將[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性設定為包含多個子視圖的視圖。

## <a name="choose-an-emptyview-at-runtime"></a>在執行時間選擇 EmptyView

當資料無法使用時，將會顯示為[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)的視圖，可以定義為[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中[`ContentView`](xref:Xamarin.Forms.ContentView)物件。 然後，`EmptyView` 屬性可以根據某些商務邏輯，在執行時間設定為特定 `ContentView`。 下列 XAML 範例顯示此案例的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

此 XAML 會在頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義兩個[`ContentView`](xref:Xamarin.Forms.ContentView)物件，而[`Switch`](xref:Xamarin.Forms.Switch)物件控制哪一個 `ContentView` 物件將會設定為[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性值。 切換[`Switch`](xref:Xamarin.Forms.Switch)時，`OnEmptyViewSwitchToggled` 事件處理常式會執行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

@No__t_0 方法會根據[`ResourceDictionary`](xref:Xamarin.Forms.Switch.IsToggled)屬性的值，將 `collectionView` 物件的[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性設定為儲存在[`Switch.IsToggled`](xref:Xamarin.Forms.ResourceDictionary)中的兩個[`ContentView`](xref:Xamarin.Forms.ContentView)物件之一。 當[`SearchBar`](xref:Xamarin.Forms.SearchBar)執行 `FilterCommand` 時， [`CollectionView`](xref:Xamarin.Forms.CollectionView)所顯示的集合會針對儲存在[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)屬性中的搜尋詞彙進行篩選。 如果篩選作業未產生任何資料，則會顯示 `ContentView` 物件設定為 `EmptyView` 屬性：

[![在 iOS 和 Android 上，已交換空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/swap.png "已交換空白視圖的 CollectionView 垂直清單")](emptyview-images/swap-large.png#lightbox "已交換空白視圖的 CollectionView 垂直清單")

如需資源字典的詳細資訊，請參閱[Xamarin. Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>在執行時間選擇 EmptyViewTemplate

藉由將[`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性設定為[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)物件，您可以在執行時間根據其值來選擇[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)的外觀：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[ [@No__t_1](xref:Xamarin.Forms.ItemsView.EmptyView) ] 屬性會設定為 [ [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) ] 屬性，而 [ [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) ] 屬性會設定為 `SearchTermDataTemplateSelector` 物件。

當[`SearchBar`](xref:Xamarin.Forms.SearchBar)執行 `FilterCommand` 時， [`CollectionView`](xref:Xamarin.Forms.CollectionView)所顯示的集合會針對儲存在[`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text)屬性中的搜尋詞彙進行篩選。 如果篩選作業未產生任何資料，則 `SearchTermDataTemplateSelector` 物件所選擇的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會設定為[`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性並顯示。

下列範例顯示 `SearchTermDataTemplateSelector` 類別：

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

@No__t_0 類別會定義設定為不同資料範本的 `DefaultTemplate` 和 `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 當搜尋查詢不等於 "xamarin" 時，`OnSelectTemplate` 覆寫會傳回 `DefaultTemplate`，它會向使用者顯示訊息。 當搜尋查詢等於 "xamarin" 時，`OnSelectTemplate` 覆寫會傳回 `OtherTemplate`，以向使用者顯示基本訊息：

[![IOS 和 Android 上 CollectionView 執行時間空白視圖範本選取專案的螢幕擷取畫面](emptyview-images/datatemplateselector.png "CollectionView 中的執行時間空白視圖範本選取專案")](emptyview-images/datatemplateselector-large.png#lightbox "CollectionView 中的執行時間空白視圖範本選取專案")

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. 表單資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin. 表單資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
