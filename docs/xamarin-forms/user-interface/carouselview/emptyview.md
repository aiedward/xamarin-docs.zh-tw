---
title: Xamarin.Forms CarouselView EmptyView
description: 在 CarouselView 中，您可以指定空的視圖，在沒有資料可供顯示時，提供意見反應給使用者。 空白視圖可以是字串、視圖或多個視圖。
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 948a0194ef6b6d672f04737698991c835f1181fc
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374117"
---
# <a name="no-locxamarinforms-carouselview-emptyview"></a>Xamarin.Forms CarouselView EmptyView

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義下列屬性，可用來在沒有可顯示的資料時提供使用者意見反應：

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)、型別 `object` 、屬性為時所顯示的字串、系結或視圖， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `null` 或是屬性指定的集合 `ItemsSource` 為 `null` 或空白時所顯示的。 預設值是 `null`。
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)型別為的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 範本，用來格式化指定的 `EmptyView` 。 預設值是 `null`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

設定屬性的主要使用案例 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) ，是在上的篩選作業未產生任何資料時顯示使用者意見反應 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，並在從 web 服務取得資料時顯示使用者意見反應。

> [!NOTE]
> [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)如有需要，可以將屬性設定為包含互動式內容的視圖。

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>當資料無法使用時顯示字串

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為字串，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為 `null` 或屬性指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示此字串。 下列 XAML 顯示此案例的範例：

```xaml
<CarouselView ItemsSource="{Binding EmptyMonkeys}"
              EmptyView="No items to display." />
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    EmptyView = "No items to display."
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

結果是，由於資料系結集合是，因此 `null` [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 會顯示設定為屬性值的字串。

## <a name="display-views-when-data-is-unavailable"></a>當資料無法使用時顯示視圖

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為視圖，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為 `null` 或屬性指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示此屬性。 這可以是單一視圖，也可以是包含多個子視圖的視圖。 下列 XAML 範例顯示 `EmptyView` 屬性設定為包含多個子視圖的視圖：

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
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
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
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
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時 `FilterCommand` ，所顯示的集合 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會針對儲存在屬性中的搜尋字詞進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業沒有產生任何資料，則 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會顯示該設定的 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 屬性值。

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>當資料無法使用時顯示樣板化自訂類型

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性可以設定為自訂類型，當 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性為時 `null` ，或當屬性指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示其範本。 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate)屬性可以設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，它會定義的外觀 `EmptyView` 。 下列 XAML 顯示此案例的範例：

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <controls:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CarouselView.EmptyView>
        <CarouselView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CarouselView.EmptyViewTemplate>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

此 `FilterData` 類型會定義 `Filter` 屬性和對應的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ：

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

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)屬性設定為 `FilterData` 物件，且屬性資料系結 `Filter` 至 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 屬性。 當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時 `FilterCommand` ，所顯示的集合 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會針對儲存在屬性中的搜尋字詞進行篩選 `Filter` 。 如果篩選作業沒有產生任何資料，則 [`Label`](xref:Xamarin.Forms.Label) 會顯示在中定義 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 為 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性值的。

> [!NOTE]
> 當資料無法使用時顯示樣板化自訂型別時， [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 可以將屬性設定為包含多個子視圖的視圖。

## <a name="choose-an-emptyview-at-runtime"></a>在執行時間選擇 EmptyView

[`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)當資料無法使用時，將會顯示為的視圖，可以定義為 [`ContentView`](xref:Xamarin.Forms.ContentView) 中的物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 然後，您 `EmptyView` 可以在 `ContentView` 執行時間，根據某些商務邏輯，將屬性設定為特定的。 下列 XAML 範例顯示此案例的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:viewmodels="clr-namespace:CarouselViewDemos.ViewModels"
             x:Class="CarouselViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.BindingContext>
        <viewmodels:MonkeysViewModel />
    </ContentPage.BindingContext>
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
        <SearchBar SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CarouselView x:Name="carouselView"
                      ItemsSource="{Binding Monkeys}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

這個 XAML [`ContentView`](xref:Xamarin.Forms.ContentView) 會在頁面層級中定義兩個物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，而物件則控制要將 [`Switch`](xref:Xamarin.Forms.Switch) 哪個 `ContentView` 物件設定為 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 屬性值。 當 [`Switch`](xref:Xamarin.Forms.Switch) 切換時， `OnEmptyViewSwitchToggled` 事件處理常式會執行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`ToggleEmptyView`方法會 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 根據屬性的值，將物件的屬性設 `carouselView` 為 [`ContentView`](xref:Xamarin.Forms.ContentView) 儲存在中的兩個物件的其中一個 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。 當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時 `FilterCommand` ，所顯示的集合 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會針對儲存在屬性中的搜尋字詞進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業沒有產生任何資料，則 `ContentView` 會顯示設定為 `EmptyView` 屬性的物件。

如需資源字典的詳細資訊，請參閱[ Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>在執行時間選擇 EmptyViewTemplate

您 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 可以將屬性設定為物件，在執行時間根據其值來選擇的外觀 [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls">
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
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <CarouselView ItemsSource="{Binding Monkeys}"
                      EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                      EmptyViewTemplate="{StaticResource SearchSelector}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView()
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

屬性 [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) 設定為 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 屬性，而且 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性設定為 `SearchTermDataTemplateSelector` 物件。

當 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 執行時 `FilterCommand` ，所顯示的集合 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會針對儲存在屬性中的搜尋字詞進行篩選 [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) 。 如果篩選作業沒有產生任何資料，則 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 物件所選擇的 `SearchTermDataTemplateSelector` 會設定為 [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) 屬性，並顯示。

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

`SearchTermTemplateSelector`類別會定義 `DefaultTemplate` `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate` `DefaultTemplate` 當搜尋查詢不等於 "xamarin" 時，覆寫會傳回，以顯示訊息給使用者。 當搜尋查詢等於 "xamarin" 時，覆寫會傳回 `OnSelectTemplate` `OtherTemplate` ，以顯示基本訊息給使用者。

如需資料範本選取器的詳細資訊，請參閱 [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)