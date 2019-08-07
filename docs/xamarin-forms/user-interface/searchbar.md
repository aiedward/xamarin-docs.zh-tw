---
title: Xamarin. Forms 搜尋列
description: 搜尋列是用來起始搜尋的使用者輸入控制項。 搜尋列控制項支援預留位置文字、查詢輸入、執行和取消。 本文說明如何在 XAML 和程式碼中使用搜尋列。
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 391820cf2e94c1131f4082798ee9efa05d8489b8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739392"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms 搜尋列

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

[Xamarin [`SearchBar`](xref:Xamarin.Forms.SearchBar) ] 是用來起始搜尋的使用者輸入控制項。 `SearchBar`控制項支援預留位置文字、查詢輸入、搜尋執行和取消。 下列螢幕擷取畫面顯示`SearchBar`查詢, 其中顯示`ListView`的結果如下:

Ios 和[(searchbar-images/device-searchbars-cropped.png "android 上")![的 ios 和 Android 搜尋列上的搜尋列螢幕擷取畫面]](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的搜尋列")

會`SearchBar`定義下列屬性:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)`Color`是, 定義 [取消] 按鈕的色彩。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)這是`FontAttributes`一個列舉值, 可決定`SearchBar`字型為粗體、斜體, 或兩者皆非。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)是, 可決定所`SearchBar`使用的字型系列。 `string`
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)可以是`NamedSize`列舉值`double`或代表跨平臺之特定字型大小的值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)這是`TextAlignment`一個列舉值, 可定義查詢文字的水準對齊方式。
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)`string`是, 可定義預留位置文字, 例如「搜尋 ...」。
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)`Color`是, 可定義預留位置文字的色彩。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)`ICommand`是, 允許將使用者動作 (例如滑鼠點按或按一下) 系結至 viewmodel 上定義的命令。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)是, 它會指定應傳遞至的`SearchCommand`參數。 `object`
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)是, `SearchBar`其中包含中的查詢文字。 `string`
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)`Color`是定義查詢文字色彩的。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這`SearchBar`表示可以自訂, 並作為資料系結的目標。 在上`SearchBar`指定字型屬性, 與在其他 Xamarin 上自訂文字是一致的。[表單文字控制項](~/xamarin-forms/user-interface/text/index.md)。 如需詳細資訊, 請參閱「 [Xamarin 中](~/xamarin-forms/user-interface/text/fonts.md)的字型」。

## <a name="create-a-searchbar"></a>建立搜尋列

`SearchBar`可以在 XAML 中具現化。 您可以`Placeholder`設定它的選擇性屬性, 以定義 [查詢輸入] 方塊中的提示文字。 的預設值`Placeholder`為空字串, 因此如果未設定預留位置, 則不會出現任何預留位置。 下列範例顯示如何`SearchBar`在 XAML 中使用選擇性`Placeholder`屬性集來具現化:

```xaml
<SearchBar Placeholder="Search items..." />
```

也`SearchBar`可以在程式碼中建立:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>搜尋列外觀屬性

`SearchBar`控制項會定義許多可自訂控制面板的屬性。 下列範例顯示如何使用指定的多`SearchBar`個屬性, 在 XAML 中具現化:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

`SearchBar`在程式碼中建立時, 您也可以指定這些屬性:

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

下列螢幕擷取畫面顯示所產生`SearchBar`的:

Ios 和 Android 上[![自訂搜尋列的螢幕擷取畫面 Ios 和 android 上的]自(searchbar-images/device-searchbars-styled-cropped.png "定義搜尋列")](searchbar-images/device-searchbars-styled.png#lightbox "IOS 和 Android 上的自訂搜尋列")

## <a name="perform-a-search-with-event-handlers"></a>使用事件處理常式執行搜尋

您可以使用`SearchBar`控制項來執行搜尋, 方法是將事件處理常式附加至下列其中一個事件:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)當使用者按一下 [搜尋] 按鈕或按下 enter 鍵時, 會呼叫。
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)每當查詢方塊中的文字變更時, 就會呼叫。

下列範例顯示在 XAML 中附加至事件的`TextChanged`事件處理常式, 並`ListView`使用來顯示搜尋結果:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件處理常式也可以附加至以程式`SearchBar`代碼建立的:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

程式碼後置檔案中的`SearchBar` 事件處理常式相同,不論是透過XAML或程式碼所建立:`TextChanged`

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

上一個範例意指`DataService` `GetSearchResults`具有能夠傳回符合查詢之專案的方法存在的類別。 `ItemsSource` `ListView`控制項的`Text` 屬性`GetSearchResults`值會傳遞至方法, 而結果會用來更新控制項的屬性。 `SearchBar` 整體效果是搜尋結果會顯示在`ListView`控制項中。

範例應用程式提供`DataService`可用於測試搜尋功能的類別執行。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 執行搜尋

在不使用事件處理常式的情況`SearchCommand`下, 您可以將和`ICommand` `SearchCommandParameter`屬性系結至實作為執行搜尋。 範例專案會使用 ViewModel (MVVM) 模式來示範這些執行。 如需有關 MVVM 的資料系結的詳細資訊, 請參閱[使用 mvvm 的資料](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)系結。

範例應用程式中的 viewmodel 包含下列程式碼:

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
> Viewmodel 假設有一個`DataService`類別能夠執行搜尋。 範例應用程式中提供類別,包括範例資料。`DataService`

下列 XAML 顯示如何`SearchBar`將系結至範例 viewmodel, 以及`ListView`顯示搜尋結果的控制項:

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

這個範例會將`BindingContext`設定為`SearchViewModel`類別的實例。 `Text` `ICommand` `PerformSearch` 它會`SearchCommandParameter`將`SearchBar`屬性系結至 viewmodel 中的, 並將屬性系結至屬性。 `SearchCommand` 屬性會系結`SearchResults`至 viewmodel 的屬性。 `ListView.ItemsSource`

如需有關`ICommand`介面和系結的詳細資訊, 請參閱[Xamarin. 表單資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結和[ICommand 介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相關連結

* [搜尋列示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin: 表單文字控制項](~/xamarin-forms/user-interface/text/index.md)
* [Xamarin 中的字型](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin. 表單資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
