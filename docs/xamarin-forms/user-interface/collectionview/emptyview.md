---
title: 當資料無法使用時顯示 EmptyView
description: 在 CollectionView，空的檢視可以指定，將提供意見反應給使用者，當沒有資料可供顯示。 字串、 一個檢視或多個檢視，可以是空的檢視。
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/19/2019
ms.openlocfilehash: a430387bba83887045e5687c99d9295d4be373e4
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329851"
---
# <a name="display-an-emptyview-when-data-is-unavailable"></a>當資料無法使用時顯示 EmptyView

![預覽](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> `CollectionView`目前為預覽狀態，且缺少其中一些規劃功能。 此外，實作完成時，可能會變更的 API。

`CollectionView` 定義可用來提供使用者意見反應，要顯示的資料時的下列屬性：

- `EmptyView`型別的`object`，字串、 繫結或檢視將會顯示何時`ItemsSource`屬性是`null`，或集合指定時`ItemsSource`屬性是`null`或空白。 預設值為 `null`。
- `EmptyViewTemplate`型別的[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，用來格式化指定的範本`EmptyView`。 預設值為 `null`。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。

設定的主要使用案例`EmptyView`屬性會顯示在篩選作業時的使用者意見反應`CollectionView`從 web 服務擷取資料時，產生任何資料，以及顯示的使用者意見反應。

> [!NOTE]
> `EmptyView`屬性可以設定為包含互動式內容，如有必要的檢視。

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>資料無法使用時，顯示字串

`EmptyView`屬性可以設定為字串，將會顯示何時`ItemsSource`屬性是`null`，或集合指定時`ItemsSource`屬性是`null`或空白。 下列 XAML 顯示此案例的範例：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

結果是，由於資料繫結集合`null`，此字串設為`EmptyView`屬性值會顯示：

[![文字的空白檢視，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/null-itemssource.png "CollectionView 垂直文字空的檢視清單")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView 空白文字的垂直清單檢視")

## <a name="display-views-when-data-is-unavailable"></a>無法使用資料時所顯示檢視

`EmptyView`屬性可以設定為檢視中，將會顯示何時`ItemsSource`屬性是`null`，或當所指定集合`ItemsSource`屬性是`null`或空白。 這可以是單一檢視或包含多個子檢視的檢視。 下列 XAML 範例所示`EmptyView`屬性設定為包含多個子檢視的檢視：

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

對等的 C# 程式碼是：

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

當[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行`FilterCommand`，所顯示的集合`CollectionView`中儲存的搜尋字詞會篩選[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)屬性。 如果篩選的作業會不產生任何資料， [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)設定為`EmptyView`屬性值會顯示：

[![使用自訂的空白檢視，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/filter-multiple-views.png "CollectionView 垂直清單與自訂的空白檢視")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView 垂直清單使用的自訂空的檢視")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>資料無法使用時，顯示樣板化的自訂型別

`EmptyView`屬性可以設定為自訂類型，其範本時`ItemsSource`屬性是`null`，或當所指定集合`ItemsSource`屬性是`null`或空白。 `EmptyViewTemplate`屬性可以設定為[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)所定義的外觀`EmptyView`。 下列 XAML 顯示此案例的範例：

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

對等的 C# 程式碼是：

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

`FilterData`型別會定義`Filter`屬性，以及對應[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty):

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

`EmptyView`屬性設定為`FilterData`物件，而`Filter`屬性的資料繫結至[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)屬性。 當[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行`FilterCommand`，所顯示的集合`CollectionView`已篩選的搜尋字詞會儲存在`Filter`屬性。 如果篩選的作業會不產生任何資料， [ `Label` ](xref:Xamarin.Forms.Label)中定義[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，，已設定為`EmptyViewTemplate`屬性值，會顯示：

[![使用空的檢視範本，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/emptyviewtemplate.png "CollectionView 使用空的檢視範本的垂直清單")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView 垂直清單空的檢視範本")

> [!NOTE]
> 無法使用，資料時，顯示樣板化的自訂型別時`EmptyViewTemplate`屬性可以設定為包含多個子檢視的檢視。

## <a name="choose-an-emptyview-at-runtime"></a>選擇在執行階段 EmptyView

檢視會顯示成`EmptyView`無法使用資料時，可以定義為[ `ContentView` ](xref:Xamarin.Forms.ContentView)中的物件[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 `EmptyView`屬性可以設定在特定`ContentView`，這取決於一些商務邏輯，在執行階段。 下列範例中，XAML 會顯示此案例的範例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
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

此 XAML 會定義兩個[ `ContentView` ](xref:Xamarin.Forms.ContentView)中的頁面層級物件[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，使用[ `Switch` ](xref:Xamarin.Forms.Switch)物件控制的`ContentView`物件會被設定為`EmptyView`屬性值。 當[ `Switch` ](xref:Xamarin.Forms.Switch)已切換`OnEmptyViewSwitchToggled`事件處理常式執行`ToggleEmptyView`方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

`ToggleEmptyView`方法會設定`EmptyView`屬性`collectionView`這兩個的其中一個物件[ `ContentView` ](xref:Xamarin.Forms.ContentView)物件儲存在[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，根據的值[ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled)屬性。 當[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)執行`FilterCommand`，所顯示的集合`CollectionView`中儲存的搜尋字詞會篩選[ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text)屬性。 如果篩選的作業會不產生任何資料，`ContentView`物件設定為`EmptyView`屬性會顯示：

[![已交換的空白檢視，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](emptyview-images/swap.png "CollectionView 垂直清單與交換的空白檢視")](emptyview-images/swap-large.png#lightbox "CollectionView 垂直清單交換空白檢視")

如需有關資源字典的詳細資訊，請參閱[Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.Forms 的資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
