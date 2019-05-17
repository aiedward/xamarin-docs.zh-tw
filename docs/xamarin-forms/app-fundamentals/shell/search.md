---
title: Xamarin.Forms Shell 搜尋
description: Xamarin.Forms Shell 應用程式可以使用能夠新增至每個頁面頂端的搜尋方塊所提供的整合式搜尋功能。
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: acaa847b61443eff480e2b39e388f5df9de06e42
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054418"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)

Xamarin.Forms Shell 包含 `SearchHandler` 類別所提供的整合式搜尋功能。 將 `Shell.SearchHandler` 附加屬性設定為子類別化的 `SearchHandler` 物件，就可以將搜尋功能新增至網頁。 這會使搜尋方塊新增至頁面頂端：

[![Shell SearchHandler 在 iOS 和 Android 上的螢幕擷取畫面](search-images/searchhandler.png "Shell SearchHandler")](search-images/searchhandler-large.png#lightbox "Shell SearchHandler")

在搜尋方塊輸入查詢時，可以使用資料填入搜尋建議區域：

[![Shell SearchHandler 中的搜尋結果在 iOS 和 Android 上的螢幕擷取畫面](search-images/search-suggestions.png "Shell SearchHandler 搜尋結果")](search-images/search-suggestions-large.png#lightbox "Shell SearchHandler 搜尋結果")

接著，當您選取搜尋結果時，應用程式可以適當地回應，例如，瀏覽至另一個頁面。

## <a name="searchhandler-class"></a>SearchHandler 類別

`SearchHandler` 類別會定義可控制其外觀和行為的下列屬性：

- `ClearIcon`，屬於 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類型，所顯示的圖示可清除搜尋方塊的內容。
- `ClearIconHelpText`，屬於 `string` 類型，用於清除圖示的可存取說明文字。
- `ClearIconName`，屬於 `string` 類型，清除圖示的名稱，可搭配螢幕助讀程式使用。
- `ClearPlaceholderCommand`，屬於 `ICommand` 類型，將在點選 `ClearPlaceholderIcon` 時執行。
- `ClearPlaceholderCommandParameter`，屬於 `object` 類型，這是傳遞至 `ClearPlaceholderCommand` 的參數。
- `ClearPlaceholderEnabled`，屬於 `bool` 類型，用於決定是否可以執行 `ClearPlaceholderCommand`。 預設值為 `true`。
- `ClearPlaceholderHelpText`，屬於 `string` 類型，用於預留位置圖示的可存取說明文字。
- `ClearPlaceholderIcon`，屬於 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類型，搜尋方塊空白時顯示的清除預留位置圖示。
- `ClearPlaceholderName`，屬於 `string` 類型，清除預留位置圖示的名稱，可搭配螢幕助讀程式使用。
- `Command`，屬於 `ICommand` 類型，將在確認搜尋查詢時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `DisplayMemberName`，屬於 `string` 類型，表示針對 `ItemsSource` 集合中每個資料項目所顯示之屬性的名稱或路徑。
- `IsSearchEnabled`，屬於 `bool` 類型，表示搜尋方塊已啟用的狀態。 預設值為 `true`。
- `ItemsSource`，屬於 `IEnumerable` 類型，可指定要在建議區域中顯示之項目的集合，且預設值為 `null`。
- `ItemTemplate`，屬於 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 類型，可在建議區域中要顯示之項目的集合中，指定要套用至每個項目的範本。
- `Placeholder`，屬於 `string` 類型，搜尋方塊空白時顯示的文字。
- `Query`，屬於 `string` 類型，搜尋方塊中使用者所輸入的文字。
- `QueryIcon`，屬於 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類型，向使用者指示可以使用搜尋所使用的圖示。
- `QueryIconHelpText`，屬於 `string` 類型，用於查詢圖示的可存取說明文字。
- `QueryIconName`，屬於 `string` 類型，查詢圖示的名稱，可搭配螢幕助讀程式使用。
- `SearchBoxVisibility`，屬於 `SearchBoxVisibility` 類型，表示是否顯示搜尋方塊。 根據預設，搜尋方塊呈顯示狀態且完全展開。
- `SelectedItem`，屬於 `object` 類型，搜尋結果中選取的項目。 這是唯讀屬性，而且預設值為 `null`。
- `ShowsResults`，屬於 `bool` 類型，表示文字輸入時，搜尋結果是否應該出現在建議區域中。 預設值為 `false`。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

此外，`SearchHandler` 類別會提供下列可覆寫的方法：

- `OnClearPlaceholderClicked`，每當點選 `ClearPlaceholderIcon` 時，就會呼叫該方法。
- `OnItemSelected`，每當使用者選取搜尋結果時，就會呼叫該方法。
- `OnQueryChanged`，當 `Query` 屬性變更時，就會呼叫該方法。
- `OnQueryConfirmed`，每當使用者按下 Enter，或在搜尋方塊中確認其查詢時，就會呼叫該方法。

當使用者在搜尋方塊中輸入查詢時，便會更新 `Query` 屬性，並在每次更新時執行 `OnQueryChanged` 方法。 此方法可以用來更新出現在搜尋方塊下方的建議區域。 當使用者從建議區域選取結果時，就會執行 `OnItemSelected` 方法。

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

當使用者選取搜尋結果時，會執行 `OnItemSelected` 覆寫並設定 `SelectedItem` 屬性。 在此範例中，此方法會導覽至顯示所選 `Animal` 相關資料的另一個頁面。 如需有關導覽的詳細資訊，請參閱 [Xamarin.Forms Shell 導覽](navigation.md)。

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

[![Shell SearchHandler 中的搜尋結果在 iOS 和 Android 上的螢幕擷取畫面](search-images/search-results.png "Shell SearchHandler 搜尋結果")](search-images/search-results-large.png#lightbox "Shell SearchHandler 搜尋結果")

當搜尋查詢變更時，就會更新搜尋建議區域：

[![Shell SearchHandler 中的搜尋結果在 iOS 和 Android 上的螢幕擷取畫面](search-images/search-results-change.png "Shell SearchHandler 搜尋結果")](search-images/search-results-change-large.png#lightbox "Shell SearchHandler 搜尋結果")

選取搜尋結果時，便會導覽至 `MonkeyDetailPage`，並顯示所選猴子的相關資料：

[![猴子詳細資料在 iOS 和 Android 上的螢幕擷取畫面](search-images/detailpage.png "猴子詳細資料")](search-images/detailpage-large.png#lightbox "猴子詳細資料")

## <a name="define-search-results-item-appearance"></a>定義搜尋結果項目外觀

除了顯示搜尋結果中的 `string` 資料之外，將 `SearchHandler.ItemTemplate` 屬性設為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 還可以定義每個搜尋結果項目的外觀：

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

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中指定的元素會定義建議區域中每個項目的外觀。 在此範例中，`DataTemplate` 內的版面配置受 [`Grid`](xref:Xamarin.Forms.Grid) 管理。 `Grid` 包含一個 [`Image`](xref:Xamarin.Forms.Image) 物件和一個 [`Label`](xref:Xamarin.Forms.Label) 物件，這兩個物件都會繫結至每個 `Monkey` 物件的屬性。

下列螢幕擷取畫面顯示建議區域中，每個項目的範本結果：

[![Shell SearchHandler 中的範本搜尋結果在 iOS 和 Android 上的螢幕擷取畫面](search-images/search-results-template.png "Shell SearchHandler 範本搜尋結果")](search-images/search-results-template-large.png#lightbox "Shell SearchHandler 範本搜尋結果")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="searchbox-visibility"></a>SearchBox 可見度

在頁面頂端新增搜尋方塊時，預設搜尋方塊呈顯示狀態且完全展開。 不過，透過將 `SearchHandler.SearchBoxVisibility` 屬性設定為其中一個 `SearchBoxVisibility` 列舉成員，即可變更此行為：

- `Hidden` – 看不到或無法存取搜尋方塊。
- `Collapsible` –在使用者執行動作來顯示搜尋方塊之前，該搜尋方塊是隱藏的。
- `Expanded` – 搜尋方塊呈顯示狀態且完全展開。

下列範例示範如何隱藏搜尋方塊：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)
