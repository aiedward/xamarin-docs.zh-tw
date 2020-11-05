---
title: Xamarin.Forms SearchBar
description: Xamarin.FormsSearchBar 是用來初始化搜尋的使用者輸入控制項。 SearchBar 控制項支援預留位置文字、查詢輸入、執行和取消。 本文說明如何在 XAML 和程式碼中使用 SearchBar。
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 66ffbe0f45754517610a2fc2858a00a6185e1d45
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369125"
---
# <a name="no-locxamarinforms-searchbar"></a>Xamarin.Forms SearchBar

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin.Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) 是用來起始搜尋的使用者輸入控制項。 `SearchBar`控制項支援預留位置文字、查詢輸入、搜尋執行和取消。 下列螢幕擷取畫面顯示 `SearchBar` 在中顯示結果的查詢 `ListView` ：

[![SearchBar 在 iOS 和 Android 上的螢幕擷取畫面](searchbar-images/device-searchbars-cropped.png "IOS 和 Android 上的 SearchBar")](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的 SearchBar")

`SearchBar`類別會定義下列屬性：

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) 是 `Color` ，定義 [取消] 按鈕的色彩。
* `CharacterSpacing`的類型 `double` 是文字字元之間的間距 `SearchBar` 。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) 這是一個 `FontAttributes` 列舉值，可決定 `SearchBar` 字型為粗體、斜體，或兩者皆非。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) 是 `string` ，它會決定所使用的字型系列 `SearchBar` 。
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) 可以是 `NamedSize` 列舉值或 `double` 代表跨平臺之特定字型大小的值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) 這是 `TextAlignment` 定義查詢文字水準對齊的列舉值。
* `VerticalTextAlignment` 這是 `TextAlignment` 定義查詢文字垂直對齊的列舉值。
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 是 `string` 定義預留位置文字的，例如「搜尋 ...」。
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) 是 `Color` ，定義預留位置文字的色彩。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) 是允許系結 `ICommand` 使用者動作（例如，點按或按一下）至 viewmodel 上所定義之命令的。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) 是 `object` ，指定應傳遞至的參數 `SearchCommand` 。
* [`Text`](xref:Xamarin.Forms.InputView.Text) 這是 `string` ，其中包含中的查詢文字 `SearchBar` 。
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 這是 `Color` 定義查詢文字色彩的。
* `TextTransform` 這是 `TextTransform` 決定文字大小寫的值 `SearchBar` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `SearchBar` 可以自訂，也可以是資料系結的目標。 在上指定字型屬性 `SearchBar` 與自訂其他[ Xamarin.Forms 文字控制項](~/xamarin-forms/user-interface/text/index.md)上的文字是一致的。 如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/text/fonts.md)的字型。

## <a name="create-a-searchbar"></a>建立 SearchBar

`SearchBar`可以在 XAML 中具現化。 您 `Placeholder` 可以設定選擇性屬性，在 [查詢輸入] 方塊中定義提示文字。 的預設值 `Placeholder` 為空字串，因此如果未設定，則不會出現預留位置。 下列範例示範如何 `SearchBar` 在 XAML 中具現化具有選擇性 `Placeholder` 屬性集的 a：

```xaml
<SearchBar Placeholder="Search items..." />
```

您 `SearchBar` 也可以在程式碼中建立：

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>SearchBar 外觀屬性

`SearchBar`控制項定義許多自訂控制面板的屬性。 下列範例顯示如何 `SearchBar` 在 XAML 中具現化具有多個指定屬性的：

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           TextTransform="Lowercase"
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
    TextTransform = TextTransform.Lowercase,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

下列螢幕擷取畫面顯示產生的 `SearchBar` 控制項：

[![IOS 和 Android 上自訂 SearchBar 的螢幕擷取畫面](searchbar-images/device-searchbars-styled-cropped.png "IOS 和 Android 上的自訂 SearchBar")](searchbar-images/device-searchbars-styled.png#lightbox "IOS 和 Android 上的自訂 SearchBar")

> [!NOTE]
> 在 iOS 上， `SearchBarRenderer` 類別包含可覆寫的 `UpdateCancelButton` 方法。 這個方法會控制 [取消] 按鈕的出現時間，而且可以在自訂轉譯器中覆寫。 如需自訂轉譯器的詳細資訊，請參閱[ Xamarin.Forms 自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="perform-a-search-with-event-handlers"></a>使用事件處理常式執行搜尋

您可以使用控制項來執行搜尋，方法是將 `SearchBar` 事件處理常式附加至下列其中一個事件：

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) 當使用者按一下 [搜尋] 按鈕或按下 enter 鍵時，就會呼叫。
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 每當查詢方塊中的文字變更時，就會呼叫。

下列範例顯示在 XAML 中附加至事件的事件處理常式 `TextChanged` ，並使用 `ListView` 來顯示搜尋結果：

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件處理常式也可以附加至在程式 `SearchBar` 代碼中建立的：

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

`TextChanged`無論 `SearchBar` 是透過 XAML 或程式碼建立，程式碼後端檔案中的事件處理常式都相同：

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

上述範例意指 `DataService` 具有 `GetSearchResults` 可傳回符合查詢之專案之方法的類別存在。 `SearchBar`控制項的 `Text` 屬性值會傳遞至 `GetSearchResults` 方法，並使用結果來更新 `ListView` 控制項的 `ItemsSource` 屬性。 整體效果是，搜尋結果會顯示在控制項中 `ListView` 。

範例應用程式提供 `DataService` 可用來測試搜尋功能的類別實作為。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 執行搜尋

您可以藉由將和屬性系結至執行，在沒有事件處理常式的情況下執行搜尋 `SearchCommand` `SearchCommandParameter` `ICommand` 。 範例專案會使用模型 ViewModel (MVVM) 模式來示範這些執行。 如需使用 MVVM 資料系結的詳細資訊，請參閱 [使用 mvvm 的資料](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)系結。

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
> Viewmodel 假設有一個 `DataService` 能夠執行搜尋的類別。 `DataService`範例應用程式中提供類別（包括範例資料）。

下列 XAML 顯示如何 `SearchBar` 使用顯示搜尋結果的控制項，將 a 系結至範例 viewmodel `ListView` ：

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

如需有關介面和系結的詳細資訊 `ICommand` ，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結和[ICommand 介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相關連結

* [SearchBar 示範](/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin.Forms 文字控制項](~/xamarin-forms/user-interface/text/index.md)
* [中的字型 Xamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin.Forms 資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)