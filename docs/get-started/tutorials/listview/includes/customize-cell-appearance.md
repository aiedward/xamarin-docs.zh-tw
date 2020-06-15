---
ms.openlocfilehash: 04d2a244482c92a615c3eec2130cec1df8aaaf1f
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389989"
---
先前，已使用資料繫結在 [`ListView`](xref:Xamarin.Forms.ListView) 中填入資料。 不過，儘管將資料繫結至集合，而集合中的每個物件已定義多個資料項目，但每個物件只會顯示單一資料項目 (`Monkey` 物件的 `Name` 屬性)。

在此練習中，您將修改 **ListViewTutorial** 專案，讓 [`ListView`](xref:Xamarin.Forms.ListView) 在每個資料列中顯示多個資料項目。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.ListView) 宣告，以自訂每個資料列的外觀：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    此程式碼會將 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以在 [`ListView`](xref:Xamarin.Forms.ListView) 中定義每個資料列的外觀。 `DataTemplate` 的子系必須是 [`Cell`](xref:Xamarin.Forms.Cell) 類型或衍生自該類型。 此程式碼會使用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) (其衍生自 `Cell`)，以針對每個 `ListView` 資料列建立自訂版面配置。 `ViewCell` 內的版面配置在此由 [`Grid`](xref:Xamarin.Forms.Grid) 管理，其中包含一個 [`Image`](xref:Xamarin.Forms.Image) 物件和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Image` 物件資料會將其 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性繫結至每個`Monkey` 物件的 `ImageUrl` 屬性，而 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至每個 `Monkey` 物件的 `Name` 和 `Location` 屬性。

    根據預設，[`ListView`](xref:Xamarin.Forms.ListView) 中所有資料列的高度相同。 不過，此程式碼會將 [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) 屬性設定為 `true`，使資料列高度可就其內容而調整。 這適用於每個 `Monkey` 物件具有可變長度字串的 `Name` 和 `Location` 屬性。

    如需有關 [`ListView`](xref:Xamarin.Forms.ListView) 儲存格外觀的詳細資訊，請參閱[自訂 ListView 資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。 如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：項目以資料範本樣板化的 ListView](../images/customize-cell-appearance.png "顯示樣板化資料的 ListView")](../images/customize-cell-appearance-large.png#lightbox "顯示樣板化資料的 ListView")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`ListView`](xref:Xamarin.Forms.ListView) 宣告，以自訂每個資料列的外觀：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    此程式碼會將 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以在 [`ListView`](xref:Xamarin.Forms.ListView) 中定義每個資料列的外觀。 `DataTemplate` 的子系必須是 [`Cell`](xref:Xamarin.Forms.Cell) 類型或衍生自該類型。 此程式碼會使用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) (其衍生自 `Cell`)，以針對每個 `ListView` 資料列建立自訂版面配置。 `ViewCell` 內的版面配置在此由 [`Grid`](xref:Xamarin.Forms.Grid) 管理，其中包含一個 [`Image`](xref:Xamarin.Forms.Image) 物件和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Image` 物件資料會將其 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性繫結至每個`Monkey` 物件的 `ImageUrl` 屬性，而 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至每個 `Monkey` 物件的 `Name` 和 `Location` 屬性。

    根據預設，[`ListView`](xref:Xamarin.Forms.ListView) 中所有資料列的高度相同。 不過，此程式碼會將 [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) 屬性設定為 `true`，使資料列高度可就其內容而調整。 這適用於每個 `Monkey` 物件具有可變長度字串的 `Name` 和 `Location` 屬性。

    如需有關 [`ListView`](xref:Xamarin.Forms.ListView) 儲存格外觀的詳細資訊，請參閱[自訂 ListView 資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。 如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：項目以資料範本樣板化的 ListView](../images/customize-cell-appearance.png "顯示樣板化資料的 ListView")](../images/customize-cell-appearance-large.png#lightbox "顯示樣板化資料的 ListView")
