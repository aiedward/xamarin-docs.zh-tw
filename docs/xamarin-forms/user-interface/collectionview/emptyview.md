---
title: Xamarin.FormsCollectionView EmptyView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d35e39e55d66452e47c7a3e3faf86a7a7d6adaca
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136483"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin.FormsCollectionView EmptyView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列屬性，當沒有可顯示的資料時，可以用來提供使用者意見反應：

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)，屬於類型 `object` ，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為時， `null` 或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，將顯示的字串、系結或視圖。 預設值是 `null`。
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)，屬於類型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，這是用來格式化指定的範本 `EmptyView` 。 預設值是 `null`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)當上的篩選作業 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 未產生任何資料，並在從 web 服務抓取資料時顯示使用者意見反應時，設定屬性的主要使用案例會顯示使用者意見反應。

> [!NOTE]
> [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)如有需要，可以將屬性設定為包含互動式內容的視圖。

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>當資料無法使用時顯示字串

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為字串，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示。 下列 XAML 顯示此案例的範例：

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

結果是，因為資料系結集合是，所以 `null` [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 會顯示設定為屬性值的字串：

[![在 iOS 和 Android 上具有文字空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/null-itemssource.png "CollectionView 具有空白視圖的垂直清單")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView 具有空白視圖的垂直清單")

## <a name="display-views-when-data-is-unavailable"></a>資料無法使用時顯示視圖

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為 view，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示。 這可以是單一視圖，或包含多個子視圖的視圖。 下列 XAML 範例顯示將 `EmptyView` 屬性設定為包含多個子視圖的視圖：

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

當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時，所顯示的 `FilterCommand` 集合 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會針對儲存在屬性中的搜尋詞彙進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業未產生任何資料，則 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會顯示 [設定為] [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 屬性值：

[![在 iOS 和 Android 上具有自訂空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/filter-multiple-views.png "具有自訂空白視圖的 CollectionView 垂直清單")](emptyview-images/filter-multiple-views-large.png#lightbox "具有自訂空白視圖的 CollectionView 垂直清單")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>當資料無法使用時，顯示樣板化自訂類型

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為自訂類型，其範本會在屬性為時顯示 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白。 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性可以設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，以定義的外觀 `EmptyView` 。 下列 XAML 顯示此案例的範例：

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

型別會 `FilterData` 定義 `Filter` 屬性，以及對應的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ：

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

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性會設定為 `FilterData` 物件，而屬性資料會系結 `Filter` 至 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 屬性。 當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時，所顯示的 `FilterCommand` 集合 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會針對儲存在屬性中的搜尋詞彙進行篩選 `Filter` 。 如果篩選作業不會產生任何資料，則 [`Label`](xref:Xamarin.Forms.Label) 會顯示在中定義的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) （設定為 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性值）：

[![在 iOS 和 Android 上具有空白視圖範本之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/emptyviewtemplate.png "具有空白視圖範本的 CollectionView 垂直清單")](emptyview-images/emptyviewtemplate-large.png#lightbox "具有空白視圖範本的 CollectionView 垂直清單")

> [!NOTE]
> 當資料無法使用時，顯示樣板化自訂類型時， [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 可以將屬性設定為包含多個子視圖的視圖。

## <a name="choose-an-emptyview-at-runtime"></a>在執行時間選擇 EmptyView

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)當資料無法使用時，將會顯示為的視圖，可以定義為 [`ContentView`](xref:Xamarin.Forms.ContentView) 中的物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 接著，您 `EmptyView` 可以在 `ContentView` 執行時間根據某些商務邏輯，將屬性設定為特定的。 下列 XAML 顯示此案例的範例：

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

此 XAML [`ContentView`](xref:Xamarin.Forms.ContentView) 會在頁面層級中定義兩個物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，其中 [`Switch`](xref:Xamarin.Forms.Switch) 物件會控制要將哪個 `ContentView` 物件設定為 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 屬性值。 當 [`Switch`](xref:Xamarin.Forms.Switch) 切換時， `OnEmptyViewSwitchToggled` 事件處理常式會執行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`ToggleEmptyView`方法會 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 根據屬性的值，將物件的屬性設定 `collectionView` 為 [`ContentView`](xref:Xamarin.Forms.ContentView) 儲存在中的兩個物件的其中一個 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。 當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時，所顯示的 `FilterCommand` 集合 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會針對儲存在屬性中的搜尋詞彙進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業未產生任何資料，則 `ContentView` 會顯示設定為 `EmptyView` 屬性的物件：

[![在 iOS 和 Android 上，已交換空白視圖之 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/swap.png "已交換空白視圖的 CollectionView 垂直清單")](emptyview-images/swap-large.png#lightbox "已交換空白視圖的 CollectionView 垂直清單")

如需資源字典的詳細資訊，請參閱[ Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>在執行時間選擇 EmptyViewTemplate

您 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 可以透過將屬性設定為物件，在執行時間根據其值來選擇的外觀 [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

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

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性會設定為 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 屬性，而 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性會設定為 `SearchTermDataTemplateSelector` 物件。

當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時，所顯示的 `FilterCommand` 集合 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會針對儲存在屬性中的搜尋詞彙進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業未產生任何資料， [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 則物件所選擇的 `SearchTermDataTemplateSelector` 會設定為 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性並顯示。

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

`SearchTermTemplateSelector`類別會定義 `DefaultTemplate` `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate` `DefaultTemplate` 當搜尋查詢不等於 "xamarin" 時，覆寫會傳回，以向使用者顯示訊息。 當搜尋查詢等於 "xamarin" 時，覆寫會傳回 `OnSelectTemplate` `OtherTemplate` ，以向使用者顯示基本訊息：

[![IOS 和 Android 上 CollectionView 執行時間空白視圖範本選取專案的螢幕擷取畫面](emptyview-images/datatemplateselector.png "CollectionView 中的執行時間空白視圖範本選取專案")](emptyview-images/datatemplateselector-large.png#lightbox "CollectionView 中的執行時間空白視圖範本選取專案")

如需資料範本選取器的詳細資訊，請參閱[Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
