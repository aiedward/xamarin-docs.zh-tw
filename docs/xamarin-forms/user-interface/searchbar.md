---
title: Xamarin. Forms 搜尋列
description: 搜尋列是用來起始搜尋的使用者輸入控制項。 搜尋列控制項支援預留位置文字、查詢輸入、執行和取消。 本文說明如何在 XAML 和程式碼中使用搜尋列。
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: a48a91b886cadcbe9dfa73a524b7bfa9fb2cf5fb
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "78292884"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms 搜尋列

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

[Xamarin] [`SearchBar`](xref:Xamarin.Forms.SearchBar)是用來起始搜尋的使用者輸入控制項。 `SearchBar` 控制項支援預留位置文字、查詢輸入、搜尋執行和取消。 下列螢幕擷取畫面顯示在 `ListView`中顯示結果的 `SearchBar` 查詢：

[![IOS 和 Android 上的搜尋列螢幕擷取畫面](searchbar-images/device-searchbars-cropped.png "IOS 和 Android 上的搜尋列")](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的搜尋列")

`SearchBar` 類別會定義下列屬性：

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)是定義 [取消] 按鈕色彩的 `Color`。
* `CharacterSpacing`，屬於 `double`類型，這是 `SearchBar` 文字字元之間的間距。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)是 `FontAttributes` 列舉值，可決定 `SearchBar` 字型為粗體、斜體，或兩者皆非。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)是決定 `SearchBar`所使用之字型系列的 `string`。
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)可以是 `NamedSize` 列舉值，或代表跨平臺之特定字型大小的 `double` 值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)是一個 `TextAlignment` 列舉值，可定義查詢文字的水準對齊方式。
* `VerticalTextAlignment` 是一個 `TextAlignment` 列舉值，可定義查詢文字的垂直對齊方式。
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)是定義預留位置文字的 `string`，例如「搜尋 ...」。
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)是定義預留位置文字色彩的 `Color`。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)是一種 `ICommand`，可讓您將使用者動作（例如手指點按或按一下）系結至 viewmodel 上定義的命令。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)是指定應傳遞至 `SearchCommand`之參數的 `object`。
* [`Text`](xref:Xamarin.Forms.InputView.Text)是包含 `SearchBar`中查詢文字的 `string`。
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)是定義查詢文字色彩的 `Color`。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示 `SearchBar` 可以自訂，而且是資料系結的目標。 在 `SearchBar` 上指定字型屬性，與在其他 Xamarin 上自訂文字是一致的。[表單文字控制項](~/xamarin-forms/user-interface/text/index.md)。 如需詳細資訊，請參閱「 [Xamarin 中](~/xamarin-forms/user-interface/text/fonts.md)的字型」。

## <a name="create-a-searchbar"></a>建立搜尋列

`SearchBar` 可以在 XAML 中具現化。 其選擇性的 `Placeholder` 屬性可以設定為在 [查詢] 輸入方塊中定義提示文字。 `Placeholder` 的預設值為空字串，因此如果未設定，則不會出現任何預留位置。 下列範例顯示如何使用選擇性的 `Placeholder` 屬性集，在 XAML 中具現化 `SearchBar`：

```xaml
<SearchBar Placeholder="Search items..." />
```

您也可以在程式碼中建立 `SearchBar`：

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>搜尋列外觀屬性

`SearchBar` 控制項會定義許多可自訂控制面板的屬性。 下列範例顯示如何在 XAML 中具現化具有多個指定之屬性的 `SearchBar`：

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

在程式碼中建立 `SearchBar` 物件時，也可以指定這些屬性：

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
> 在 iOS 上，`SearchBarRenderer` 類別包含可覆寫的 `UpdateCancelButton` 方法。 這個方法會控制 [取消] 按鈕的顯示時間，並可在自訂轉譯器中加以覆寫。 如需自訂轉譯器的詳細資訊，請參閱[Xamarin. 表單自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="perform-a-search-with-event-handlers"></a>使用事件處理常式執行搜尋

藉由將事件處理常式附加至下列其中一個事件，可以使用 `SearchBar` 控制項來執行搜尋：

* 當使用者按一下 [搜尋] 按鈕或按下 enter 鍵時，會呼叫[`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) 。
* 每當查詢方塊中的文字變更時，就會呼叫[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 。

下列範例顯示附加至 XAML 中 `TextChanged` 事件的事件處理常式，並使用 `ListView` 顯示搜尋結果：

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件處理常式也可以附加至以程式碼建立的 `SearchBar`：

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

不論是透過 XAML 或程式碼建立 `SearchBar`，程式碼後置檔案中的 `TextChanged` 事件處理常式都是相同的：

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

上一個範例意指具有 `GetSearchResults` 方法的 `DataService` 類別，其能夠傳回符合查詢的專案。 `SearchBar` 控制項的 `Text` 屬性值會傳遞至 `GetSearchResults` 方法，而結果會用來更新 `ListView` 控制項的 `ItemsSource` 屬性。 整體效果是搜尋結果會顯示在 [`ListView`] 控制項中。

範例應用程式提供可用來測試搜尋功能的 `DataService` 類別實作為。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 執行搜尋

在不使用事件處理常式的情況下執行搜尋，方法是將 `SearchCommand` 和 `SearchCommandParameter` 屬性系結至 `ICommand` 的部署。 範例專案會使用 ViewModel （MVVM）模式來示範這些執行。 如需有關 MVVM 的資料系結的詳細資訊，請參閱[使用 mvvm 的資料](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)系結。

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
> Viewmodel 會假設有一個 `DataService` 類別能夠執行搜尋。 範例應用程式中提供 `DataService` 類別，包括範例資料。

下列 XAML 示範如何使用顯示搜尋結果的 `ListView` 控制項，將 `SearchBar` 系結至範例 viewmodel：

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
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

這個範例會將 `BindingContext` 設定為 `SearchViewModel` 類別的實例。 它會將 `SearchCommand` 屬性系結至 viewmodel 中的 `PerformSearch` `ICommand`，並將 `SearchBar` `Text` 屬性系結至 `SearchCommandParameter` 屬性。 `ListView.ItemsSource` 屬性會系結至 viewmodel 的 `SearchResults` 屬性。

如需 `ICommand` 介面和系結的詳細資訊，請參閱[Xamarin 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結和[ICommand 介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相關連結

* [搜尋列示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin：表單文字控制項](~/xamarin-forms/user-interface/text/index.md)
* [Xamarin 中的字型](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin. 表單資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
