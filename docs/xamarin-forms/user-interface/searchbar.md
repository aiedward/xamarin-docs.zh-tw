---
title: Xamarin.Forms搜尋列
description: Xamarin.Forms搜尋列是用來起始搜尋的使用者輸入控制項。 搜尋列控制項支援預留位置文字、查詢輸入、執行和取消。 本文說明如何在 XAML 和程式碼中使用搜尋列。
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8ceb139b1b9cd77aa922f98c80884d5c3e1a474
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127539"
---
# <a name="xamarinforms-searchbar"></a>Xamarin.Forms搜尋列

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin.Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) 是用來起始搜尋的使用者輸入控制項。 `SearchBar`控制項支援預留位置文字、查詢輸入、搜尋執行和取消。 下列螢幕擷取畫面顯示 `SearchBar` 查詢，其中顯示的結果 `ListView` 如下：

[![IOS 和 Android 上的搜尋列螢幕擷取畫面](searchbar-images/device-searchbars-cropped.png "IOS 和 Android 上的搜尋列")](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的搜尋列")

`SearchBar`類別會定義下列屬性：

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)是 `Color` ，定義 [取消] 按鈕的色彩。
* `CharacterSpacing`，屬於類型 `double` ，這是文字字元之間的間距 `SearchBar` 。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)這是一個 `FontAttributes` 列舉值，可決定 `SearchBar` 字型為粗體、斜體，或兩者皆非。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)是 `string` ，可決定所使用的字型系列 `SearchBar` 。
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)可以是 `NamedSize` 列舉值或 `double` 代表跨平臺之特定字型大小的值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)這是一個 `TextAlignment` 列舉值，可定義查詢文字的水準對齊方式。
* `VerticalTextAlignment`這是一個 `TextAlignment` 列舉值，可定義查詢文字的垂直對齊方式。
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)是 `string` ，可定義預留位置文字，例如「搜尋 ...」。
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)是 `Color` ，可定義預留位置文字的色彩。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)是， `ICommand` 允許將使用者動作（例如滑鼠點按或按一下）系結至 viewmodel 上定義的命令。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)是 `object` ，它會指定應傳遞至的參數 `SearchCommand` 。
* [`Text`](xref:Xamarin.Forms.InputView.Text)是， `string` 其中包含中的查詢文字 `SearchBar` 。
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)是 `Color` 定義查詢文字色彩的。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `SearchBar` 可以自訂，並作為資料系結的目標。 在上指定字型屬性 `SearchBar` ，與在其他[ Xamarin.Forms 文字控制項](~/xamarin-forms/user-interface/text/index.md)上自訂文字是一致的。 如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/text/fonts.md)的字型。

## <a name="create-a-searchbar"></a>建立搜尋列

`SearchBar`可以在 XAML 中具現化。 您 `Placeholder` 可以設定它的選擇性屬性，以定義 [查詢輸入] 方塊中的提示文字。 的預設值 `Placeholder` 為空字串，因此如果未設定預留位置，則不會出現任何預留位置。 下列範例顯示如何 `SearchBar` 在 XAML 中使用選擇性屬性集來具現化 `Placeholder` ：

```xaml
<SearchBar Placeholder="Search items..." />
```

`SearchBar`也可以在程式碼中建立：

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>搜尋列外觀屬性

`SearchBar`控制項會定義許多可自訂控制面板的屬性。 下列範例顯示如何 `SearchBar` 使用指定的多個屬性，在 XAML 中具現化：

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

在程式碼中建立物件時，也可以指定這些屬性 `SearchBar` ：

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

下列螢幕擷取畫面顯示產生的 `SearchBar` 控制項：

[![IOS 和 Android 上自訂搜尋列的螢幕擷取畫面](searchbar-images/device-searchbars-styled-cropped.png "IOS 和 Android 上的自訂搜尋列")](searchbar-images/device-searchbars-styled.png#lightbox "IOS 和 Android 上的自訂搜尋列")

> [!NOTE]
> 在 iOS 上， `SearchBarRenderer` 類別包含可覆寫的 `UpdateCancelButton` 方法。 這個方法會控制 [取消] 按鈕的顯示時間，並可在自訂轉譯器中加以覆寫。 如需自訂轉譯器的詳細資訊，請參閱[ Xamarin.Forms 自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="perform-a-search-with-event-handlers"></a>使用事件處理常式執行搜尋

您可以使用控制項來執行搜尋，方法是將 `SearchBar` 事件處理常式附加至下列其中一個事件：

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)當使用者按一下 [搜尋] 按鈕或按下 enter 鍵時，會呼叫。
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)每當查詢方塊中的文字變更時，就會呼叫。

下列範例顯示在 XAML 中附加至事件的事件處理常式 `TextChanged` ，並使用 `ListView` 來顯示搜尋結果：

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件處理常式也可以附加至以程式 `SearchBar` 代碼建立的：

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

`TextChanged`程式碼後置檔案中的事件處理常式相同，不論是透過 `SearchBar` XAML 或程式碼所建立：

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

上一個範例意指 `DataService` 具有 `GetSearchResults` 能夠傳回符合查詢之專案的方法存在的類別。 `SearchBar`控制項的 `Text` 屬性值會傳遞至 `GetSearchResults` 方法，而結果會用來更新 `ListView` 控制項的 `ItemsSource` 屬性。 整體效果是搜尋結果會顯示在 `ListView` 控制項中。

範例應用程式提供 `DataService` 可用於測試搜尋功能的類別執行。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 執行搜尋

在不使用事件處理常式的情況下，您可以將和屬性系結至實作為執行搜尋 `SearchCommand` `SearchCommandParameter` `ICommand` 。 範例專案會使用 ViewModel （MVVM）模式來示範這些執行。 如需有關 MVVM 的資料系結的詳細資訊，請參閱[使用 mvvm 的資料](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)系結。

範例應用程式中的 viewmodel 包含下列程式碼：

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> Viewmodel 假設有一個 `DataService` 類別能夠執行搜尋。 `DataService`範例應用程式中提供類別，包括範例資料。

下列 XAML 顯示如何將系結 `SearchBar` 至範例 viewmodel，以及 `ListView` 顯示搜尋結果的控制項：

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults}" />
    </StackLayout>
</ContentPage>
```

這個範例會將設定 `BindingContext` 為類別的實例 `SearchViewModel` 。 它會將 `SearchCommand` 屬性系結至 `PerformSearch` `ICommand` viewmodel 中的，並將屬性系結 `SearchBar` `Text` 至 `SearchCommandParameter` 屬性。 `ListView.ItemsSource`屬性會系結至 `SearchResults` viewmodel 的屬性。

如需介面和系結的詳細資訊 `ICommand` ，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結和[ICommand 介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相關連結

* [搜尋列示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin.Forms文字控制項](~/xamarin-forms/user-interface/text/index.md)
* [中的字型Xamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin.Forms資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
