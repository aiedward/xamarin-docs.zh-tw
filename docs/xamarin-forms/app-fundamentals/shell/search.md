---
title: Xamarin.Forms Shell 搜尋
description: Xamarin.Forms Shell 應用程式可以使用搜尋方塊所提供的整合式搜尋功能，此功能可以新增至每個頁面的頂端。
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cae7b1e86db6dfceb04c8298c116b6e7de32e5a1
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101758089"
---
# <a name="xamarinforms-shell-search"></a>Xamarin.Forms Shell 搜尋

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包含類別所提供的整合式搜尋功能 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 。 您可以藉由將 [`Shell.SearchHandler`](xref:Xamarin.Forms.SearchHandler) 附加屬性設定為子類別化的物件，將搜尋功能加入至頁面 `SearchHandler` 。 這會使搜尋方塊新增至頁面頂端：

[![螢幕擷取畫面：在 iOS 和 Android 上的 Shell SearchHandler](search-images/searchhandler.png)](search-images/searchhandler-large.png#lightbox)

在 [搜尋] 方塊中輸入查詢時，會 [`Query`](xref:Xamarin.Forms.SearchHandler.Query) 更新屬性，並在每次更新時 [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*) 執行方法。 可以覆寫此方法，以使用資料填入搜尋建議區域：

[![螢幕擷取畫面：在 iOS 和 Android 上，Shell SearchHandler 中的搜尋結果](search-images/search-suggestions.png)](search-images/search-suggestions-large.png#lightbox)

然後，當從搜尋建議區域選取結果時， [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*) 就會執行此方法。 可以覆寫此方法以做出適當回應，例如透過導覽至詳細資料頁面。

## <a name="create-a-searchhandler"></a>建立 SearchHandler

您可以藉由子 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 類別化類別，並覆寫 [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*) 和方法，將搜尋功能新增至 Shell 應用程式 [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*) ：

```csharp
public class AnimalSearchHandler : SearchHandler
{
    public IList<Animal> Animals { get; set; }
    public Type SelectedItemNavigationTarget { get; set; }

    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = Animals
                .Where(animal => animal.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Let the animation complete
        await Task.Delay(1000);

        ShellNavigationState state = (App.Current.MainPage as Shell).CurrentState;
        // The following route works because route names are unique in this application.
        await Shell.Current.GoToAsync($"{GetNavigationTarget()}?name={((Animal)item).Name}");
    }

    string GetNavigationTarget()
    {
        return (Shell.Current as AppShell).Routes.FirstOrDefault(route => route.Value.Equals(SelectedItemNavigationTarget)).Key;
    }
}
```

覆 [`OnQueryChanged`](xref:Xamarin.Forms.SearchHandler.OnQueryChanged*) 寫有兩個引數： `oldValue` （包含先前的搜尋查詢）和 `newValue` （包含目前的搜尋查詢）。 您可以將 [`SearchHandler.ItemsSource`](xref:Xamarin.Forms.SearchHandler.ItemsSource) 屬性設定為 `IEnumerable` 包含符合目前搜尋查詢之專案的集合，以更新搜尋建議區域。

當使用者選取搜尋結果時， [`OnItemSelected`](xref:Xamarin.Forms.SearchHandler.OnItemSelected*) 會執行覆寫並 [`SelectedItem`](xref:Xamarin.Forms.SearchHandler.SelectedItem) 設定屬性。 在此範例中，此方法會導覽至顯示所選 `Animal` 相關資料的另一個頁面。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](navigation.md)。

> [!NOTE]
> 您 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 可以設定其他屬性來控制搜尋方塊的外觀。

## <a name="consume-a-searchhandler"></a>取用 SearchHandler

在 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) [`Shell.SearchHandler`](xref:Xamarin.Forms.SearchHandler) 使用頁面上，將附加屬性設定為子類別化類型的物件，即可使用子類別：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
Shell.SetSearchHandler(this, new AnimalSearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

`AnimalSearchHandler.OnQueryChanged` 方法會傳回 `Animal` 物件的 `List`。 [`DisplayMemberName`](xref:Xamarin.Forms.SearchHandler.DisplayMemberName)屬性會設定為 `Name` 每個物件的屬性 `Animal` ，因此在 [建議] 區域中顯示的資料將會是每個動物名稱。

[`ShowsResults`](xref:Xamarin.Forms.SearchHandler.ShowsResults)屬性設定為 `true` ，以便在使用者輸入搜尋查詢時顯示搜尋建議：

[![在 iOS 和 Android 上 Shell SearchHandler 中搜尋結果的螢幕擷取畫面，其中包含部分字串 M 的結果。](search-images/search-results.png)](search-images/search-results-large.png#lightbox)

當搜尋查詢變更時，就會更新搜尋建議區域：

[![在 iOS 和 Android 上 Shell SearchHandler 中搜尋結果的螢幕擷取畫面，其中包含部分字串 M o n 的結果。](search-images/search-results-change.png)](search-images/search-results-change-large.png#lightbox)

當您選取搜尋結果時， `MonkeyDetailPage` 會流覽至，並顯示有關所選猴子的詳細資料頁面：

[![在 iOS 和 Android 上的猴子詳細資料螢幕擷取畫面](search-images/detailpage.png)](search-images/detailpage-large.png#lightbox)

## <a name="define-search-results-item-appearance"></a>定義搜尋結果項目外觀

除了在 `string` 搜尋結果中顯示資料之外，還可以藉由將屬性設定為，來定義每個搜尋結果專案的外觀 [`SearchHandler.ItemTemplate`](xref:Xamarin.Forms.SearchHandler.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:AnimalSearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10"
                          ColumnDefinitions="0.15*,0.85*">
                        <Image Source="{Binding ImageUrl}"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold"
                               VerticalOptions="Center" />
                    </Grid>
                </DataTemplate>
            </controls:AnimalSearchHandler.ItemTemplate>
       </controls:AnimalSearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
Shell.SetSearchHandler(this, new AnimalSearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold, VerticalOptions = LayoutOptions.Center };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

中指定的專案會 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在 [建議] 區域中定義每個專案的外觀。 在此範例中，中的版面配置 `DataTemplate` 是由管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含 [`Image`](xref:Xamarin.Forms.Image) 物件，以及同時系結 [`Label`](xref:Xamarin.Forms.Label) 至每個物件屬性的物件 `Monkey` 。

下列螢幕擷取畫面顯示建議區域中，每個項目的範本結果：

[![螢幕擷取畫面：在 iOS 和 Android 上，Shell SearchHandler 中的樣板化搜尋結果](search-images/search-results-template.png)](search-images/search-results-template-large.png#lightbox)

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="search-box-visibility"></a>搜尋方塊的可見性

依預設，當 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 新增至頁面頂端時，[搜尋] 方塊會顯示並完全展開。 不過，您可以藉由將 [`SearchHandler.SearchBoxVisibility`](xref:Xamarin.Forms.SearchHandler.SearchBoxVisibility) 屬性設定為其中一個列舉成員來變更此行為 [`SearchBoxVisibility`](xref:Xamarin.Forms.SearchBoxVisibility) ：

- `Hidden` – 看不到或無法存取搜尋方塊。
- `Collapsible` –在使用者執行動作來顯示搜尋方塊之前，該搜尋方塊是隱藏的。 在 iOS 上，您可以藉由垂直跳動頁面內容來顯示搜尋方塊，在 Android 上，您可以藉由點擊問號圖示來顯示搜尋方塊。
- `Expanded` – 搜尋方塊呈顯示狀態且完全展開。 這是屬性的預設值 [`SearchBoxVisibility`](xref:Xamarin.Forms.SearchHandler.SearchBoxVisibility) 。

> [!IMPORTANT]
> 在 iOS 上，可折迭的搜尋方塊需要 iOS 11 或更新版本。

下列範例示範如何隱藏搜尋方塊：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>搜尋方塊焦點

點選搜尋方塊會叫用螢幕小鍵盤，搜尋方塊會取得輸入焦點。 您也可以藉由呼叫方法，以程式設計方式來達成此目的 [`Focus`](xref:Xamarin.Forms.SearchHandler.Focus) ，這會嘗試在搜尋方塊上設定輸入焦點，並在 `true` 成功時傳回。 當搜尋方塊取得焦點時，就 [`Focused`](xref:Xamarin.Forms.SearchHandler.Focused) 會引發事件並呼叫可覆寫的 `OnFocused` 方法。

當搜尋方塊具有輸入焦點時，點選畫面上的其他位置會關閉螢幕小鍵盤，且搜尋方塊會失去輸入焦點。 您也可以藉由呼叫方法，以程式設計的方式來達成此目的 [`Unfocus`](xref:Xamarin.Forms.SearchHandler.Unfocus) 。 當搜尋方塊失去焦點時，就 [`Unfocused`](xref:Xamarin.Forms.SearchHandler.Unfocused) 會引發事件並呼叫可覆寫的 `OnUnfocus` 方法。

搜尋方塊的焦點狀態可透過 [`IsFocused`](xref:Xamarin.Forms.SearchHandler.IsFocused) 屬性抓取， `true` 如果 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 目前有輸入焦點，則會傳回。

## <a name="searchhandler-keyboard"></a>SearchHandler 鍵盤

當使用者與互動時顯示的鍵盤 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) ，可以透過屬性以程式設計的方式設定，以程式設計方式 [`Keyboard`](xref:Xamarin.Forms.SearchHandler.Keyboard) 從類別設定為下列其中一個屬性 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) –用於傳送和表情的位置。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) –預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) –輸入電子郵件地址時使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) –輸入數位時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) –輸入文字時使用，不含任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 指定的。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) –輸入電話號碼時使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) –輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) –用來 & 網址輸入檔案路徑。

執行下列工作即可用 XAML 來達成這點：

```xaml
<SearchHandler Keyboard="Email" />
```

對等的 C# 程式碼為：

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

此 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 類別也有一個 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) factory 方法，可透過指定大小寫、拼字檢查和建議行為來自訂鍵盤。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值會指定為方法的引數，並會傳回自訂的 `Keyboard` 。 `KeyboardFlags` 列舉包含下列值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) –沒有任何功能會新增至鍵盤。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) –表示每個輸入之句子第一個單字的第一個字母會自動大寫。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) –表示將在輸入的文字上執行拼字檢查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) –表示輸入的文字上將會提供單字自動完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) –表示每個單字的第一個字母都會自動大寫。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) -表示每個字元會自動變成大寫。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) –表示不會進行自動大小寫。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) –表示輸入的文字上會出現拼字檢查、word 完成和句子大小寫。

下列 XAML 程式碼範例示範如何自訂預設值 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 以提供文字完成，並讓每個輸入的字元變成大寫：

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

對等的 C# 程式碼為：

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)
