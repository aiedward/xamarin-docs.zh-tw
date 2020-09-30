---
title: Xamarin.Forms Shell 搜尋
description: Xamarin.Forms Shell 應用程式可以使用搜尋方塊所提供的整合式搜尋功能，此功能可以新增至每個頁面的頂端。
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f0d8766420f876898a3f10c2f23a35a0b0afeb43
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563467"
---
# <a name="no-locxamarinforms-shell-search"></a>Xamarin.Forms Shell 搜尋

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包含類別所提供的整合式搜尋功能 `SearchHandler` 。 將 `Shell.SearchHandler` 附加屬性設定為子類別化的 `SearchHandler` 物件，就可以將搜尋功能新增至網頁。 這會使搜尋方塊新增至頁面頂端：

[![螢幕擷取畫面：在 iOS 和 Android 上的 Shell SearchHandler](search-images/searchhandler.png "Shell SearchHandler")](search-images/searchhandler-large.png#lightbox "Shell SearchHandler")

當在搜尋方塊中輸入查詢時，便會更新 `Query` 屬性，並在每次更新時執行 `OnQueryChanged` 方法。 可以覆寫此方法，以使用資料填入搜尋建議區域：

[![螢幕擷取畫面：在 iOS 和 Android 上，Shell SearchHandler 中的搜尋結果](search-images/search-suggestions.png "Shell SearchHandler 搜尋結果")](search-images/search-suggestions-large.png#lightbox "Shell SearchHandler 搜尋結果")

然後，當從建議區域選取結果時，就會執行 `OnItemSelected` 方法。 可以覆寫此方法以做出適當回應，例如透過導覽至詳細資料頁面。

## <a name="create-a-searchhandler"></a>建立 SearchHandler

將 `SearchHandler` 類別子類別化，並覆寫 `OnQueryChanged` 和 `OnItemSelected` 方法，就可以將搜尋功能新增至 Shell 應用程式：

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

`OnQueryChanged` 覆寫有兩個引數：`oldValue` 中包含先前的搜尋查詢，而 `newValue` 中則包含目前的搜尋查詢。 將 `SearchHandler.ItemsSource` 屬性設為其中包含符合目前搜尋查詢之項目的 `IEnumerable` 集合，就可以更新搜尋建議區域。

當使用者選取搜尋結果時，會執行 `OnItemSelected` 覆寫並設定 `SelectedItem` 屬性。 在此範例中，此方法會導覽至顯示所選 `Animal` 相關資料的另一個頁面。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](navigation.md)。

> [!NOTE]
> 您可以設定其他 `SearchHandler` 屬性來控制搜尋方塊的外觀。

## <a name="consume-a-searchhandler"></a>取用 SearchHandler

將 `Shell.SearchHandler` 附加屬性設為子類別化類型的物件，就可以取用子類別化的 `SearchHandler`：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

`MonkeySearchHandler.OnQueryChanged` 方法會傳回 `Animal` 物件的 `List`。 `DisplayMemberName` 屬性會設定為每個 `Animal` 物件的 `Name` 屬性，因此，建議區域中顯示的資料將會是每個動物名稱。

`ShowsResults` 屬性會設定為 `true`，因此在使用者輸入搜尋查詢時，便會顯示搜尋建議：

[![螢幕擷取畫面：在 iOS 和 Android 上的 Shell SearchHandler 中搜尋結果](search-images/search-results.png "Shell SearchHandler 搜尋結果")](search-images/search-results-large.png#lightbox "Shell SearchHandler 搜尋結果")

當搜尋查詢變更時，就會更新搜尋建議區域：

[![螢幕擷取畫面：在 iOS 和 Android 上的 Shell SearchHandler 中搜尋結果](search-images/search-results-change.png "Shell SearchHandler 搜尋結果")](search-images/search-results-change-large.png#lightbox "Shell SearchHandler 搜尋結果")

選取搜尋結果時，便會導覽至 `MonkeyDetailPage`，並顯示所選猴子的相關資料：

[![在 iOS 和 Android 上的猴子詳細資料螢幕擷取畫面](search-images/detailpage.png "猴子詳細資料")](search-images/detailpage-large.png#lightbox "猴子詳細資料")

## <a name="define-search-results-item-appearance"></a>定義搜尋結果項目外觀

除了在 `string` 搜尋結果中顯示資料之外，還可以藉由將屬性設定為，來定義每個搜尋結果專案的外觀 `SearchHandler.ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

中指定的專案會 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在 [建議] 區域中定義每個專案的外觀。 在此範例中，中的版面配置 `DataTemplate` 是由管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含 [`Image`](xref:Xamarin.Forms.Image) 物件，以及同時系結 [`Label`](xref:Xamarin.Forms.Label) 至每個物件屬性的物件 `Monkey` 。

下列螢幕擷取畫面顯示建議區域中，每個項目的範本結果：

[![螢幕擷取畫面：在 iOS 和 Android 上，Shell SearchHandler 中的樣板化搜尋結果](search-images/search-results-template.png "Shell SearchHandler 樣板化搜尋結果")](search-images/search-results-template-large.png#lightbox "Shell SearchHandler 樣板化搜尋結果")

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="search-box-visibility"></a>搜尋方塊的可見性

在頁面頂端新增 `SearchHandler` 時，預設搜尋方塊呈顯示狀態且完全展開。 不過，透過將 `SearchHandler.SearchBoxVisibility` 屬性設定為其中一個 `SearchBoxVisibility` 列舉成員，即可變更此行為：

- `Hidden` – 看不到或無法存取搜尋方塊。
- `Collapsible` –在使用者執行動作來顯示搜尋方塊之前，該搜尋方塊是隱藏的。 在 iOS 上，您可以藉由垂直跳動頁面內容來顯示搜尋方塊，在 Android 上，您可以藉由點擊問號圖示來顯示搜尋方塊。
- `Expanded` – 搜尋方塊呈顯示狀態且完全展開。 此為 `SearchHandler.SearchBoxVisibility` 屬性的預設值。

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

點選搜尋方塊會叫用螢幕小鍵盤，搜尋方塊會取得輸入焦點。 這也可以透過呼叫 `Focus` 方法以程式設計方式達成，這個方法會嘗試在搜尋方塊中設定輸入焦點，如果成功則傳回 `true`。 當搜尋方塊取得焦點時，會引發 `Focus` 事件並呼叫可覆寫的 `OnFocused` 方法。

當搜尋方塊具有輸入焦點時，點選畫面上的其他位置會關閉螢幕小鍵盤，且搜尋方塊會失去輸入焦點。 這也可以透過呼叫 `Unfocus` 方法以程式設計方式達成。 當搜尋方塊失去焦點時，將引發 `Unfocused` 事件並呼叫可覆寫的 `OnUnfocus` 方法。

可以透過 `IsFocused` 屬性擷取搜尋方塊的焦點狀態，如果 `SearchHandler` 目前具有輸入焦點，則傳回 `true`。

## <a name="searchhandler-appearance"></a>SearchHandler 外觀

`SearchHandler` 類別會定義可影響其外觀的下列屬性：

- 型別為 `Color` 的 `BackgroundColor` 是搜尋方塊文字的背景色彩。
- 型別為 `Color` 的 `CancelButtonColor` 是 [取消] 按鈕的色彩。
- `CharacterSpacing`的類型 `double` 是文字字元之間的間距 `SearchHandler` 。
- 型別為 `FontAttributes` 的 `FontAttributes` 表示搜尋方塊文字是斜體或粗體。
- 型別為 `string` 的 `FontFamily` 是用於搜尋方塊文字的字型家族。
- 型別為 `double` 的 `FontSize` 是搜尋方塊文字的大小。
- 型別為 `TextAlignment` 的 `HorizontalTextAlignment` 是搜尋方塊文字的水平對齊。
- 型別為 `Color` 的 `PlaceholderColor` 是預留位置搜尋方塊文字的色彩。
- 型別為 `Color` 的 `TextColor` 是搜尋方塊文字的色彩。
- `TextTransform`，類型為 `TextTransform` ，決定搜尋方塊文字的大小寫。
- `VerticalTextAlignment`型別為的， `TextAlignment` 是搜尋方塊文字的垂直對齊方式。

## <a name="searchhandler-keyboard"></a>SearchHandler 鍵盤

當使用者與互動時顯示的鍵盤 `SearchHandler` ，可以透過屬性以程式設計的方式設定，以程式設計方式 `Keyboard` 從類別設定為下列其中一個屬性 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

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

## <a name="searchhandler-reference"></a>SearchHandler 參考

`SearchHandler` 類別會定義可控制其外觀和行為的下列屬性：

- 型別為 `Color` 的 `BackgroundColor` 是搜尋方塊文字的背景色彩。
- 型別為 `Color` 的 `CancelButtonColor` 是 [取消] 按鈕的色彩。
- `ClearIcon`型別為的圖示，會 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 顯示以清除搜尋方塊的內容。
- `ClearIconHelpText`，屬於 `string` 類型，用於清除圖示的可存取說明文字。
- `ClearIconName`，屬於 `string` 類型，清除圖示的名稱，可搭配螢幕助讀程式使用。
- `ClearPlaceholderCommand`，屬於 `ICommand` 類型，將在點選 `ClearPlaceholderIcon` 時執行。
- `ClearPlaceholderCommandParameter`，屬於 `object` 類型，這是傳遞至 `ClearPlaceholderCommand` 的參數。
- `ClearPlaceholderEnabled`，屬於 `bool` 類型，用於決定是否可以執行 `ClearPlaceholderCommand`。 預設值是 `true`。
- `ClearPlaceholderHelpText`，屬於 `string` 類型，用於預留位置圖示的可存取說明文字。
- `ClearPlaceholderIcon`，類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，在搜尋方塊空白時顯示的清除預留位置圖示。
- `ClearPlaceholderName`，屬於 `string` 類型，清除預留位置圖示的名稱，可搭配螢幕助讀程式使用。
- `Command`，屬於 `ICommand` 類型，將在確認搜尋查詢時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `DisplayMemberName`，屬於 `string` 類型，表示針對 `ItemsSource` 集合中每個資料項目所顯示之屬性的名稱或路徑。
- 型別為 `FontAttributes` 的 `FontAttributes` 表示搜尋方塊文字是斜體或粗體。
- 型別為 `string` 的 `FontFamily` 是用於搜尋方塊文字的字型家族。
- 型別為 `double` 的 `FontSize` 是搜尋方塊文字的大小。
- 型別為 `TextAlignment` 的 `HorizontalTextAlignment` 是搜尋方塊文字的水平對齊。
- 型別為 `bool` 的 `IsFocused`，表示 `SearchHandler` 目前是否具有輸入焦點。
- `IsSearchEnabled`，屬於 `bool` 類型，表示搜尋方塊已啟用的狀態。 預設值是 `true`。
- `ItemsSource`，屬於 `IEnumerable` 類型，可指定要在建議區域中顯示之項目的集合，且預設值為 `null`。
- `ItemTemplate`，類型為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，指定要套用至建議區域中顯示之專案集合中每個專案的範本。
- 型別為 `Keyboard` 的 `Keyboard` 是 `SearchHandler` 的鍵盤。
- `Placeholder`，屬於 `string` 類型，搜尋方塊空白時顯示的文字。
- 型別為 `Color` 的 `PlaceholderColor` 是預留位置搜尋方塊文字的色彩。
- `Query`，屬於 `string` 類型，搜尋方塊中使用者所輸入的文字。
- `QueryIcon`，類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，用來向使用者指出搜尋可用的圖示。
- `QueryIconHelpText`，屬於 `string` 類型，用於查詢圖示的可存取說明文字。
- `QueryIconName`，屬於 `string` 類型，查詢圖示的名稱，可搭配螢幕助讀程式使用。
- `SearchBoxVisibility`，屬於 `SearchBoxVisibility` 類型，表示是否顯示搜尋方塊。 根據預設，搜尋方塊呈顯示狀態且完全展開。
- `SelectedItem`，屬於 `object` 類型，搜尋結果中選取的項目。 這是唯讀屬性，而且預設值為 `null`。
- `ShowsResults`，屬於 `bool` 類型，表示文字輸入時，搜尋結果是否應該出現在建議區域中。 預設值是 `false`。
- 型別為 `Color` 的 `TextColor` 是搜尋方塊文字的色彩。
- `TextTransform`，類型為 `TextTransform` ，決定搜尋方塊文字的大小寫。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

此外，`SearchHandler` 類別會提供下列可覆寫的方法：

- `OnClearPlaceholderClicked`，每當點選 `ClearPlaceholderIcon` 時，就會呼叫該方法。
- `OnItemSelected`，每當使用者選取搜尋結果時，就會呼叫該方法。
- `OnFocused`，當 `SearchHandler` 取得輸入焦點時呼叫。
- `OnQueryChanged`，當 `Query` 屬性變更時，就會呼叫該方法。
- `OnQueryConfirmed`，每當使用者按下 Enter，或在搜尋方塊中確認其查詢時，就會呼叫該方法。
- `OnUnfocus`，當 `SearchHandler` 失去輸入焦點時呼叫。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)