---
ms.openlocfilehash: 6118d4ee8d2fa56a3b14d3d280db4991a36874fc
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690027"
---
先前，已使用資料繫結在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中填入資料。 不過，儘管將資料繫結至集合，而集合中的每個物件已定義多個資料項目，但每個物件只會顯示單一資料項目 (`Monkey` 物件的 `Name` 屬性)。

在此練習中，您將修改 **CollectionViewTutorial** 專案，讓 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 在每個資料列中顯示多個資料項目。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 宣告，以自訂每個資料項目的外觀：

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    此程式碼會將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以定義 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中每個項目的外觀。 `DataTemplate` 的子系是 [`Grid`](xref:Xamarin.Forms.Grid)，其中包含 [`Image`](xref:Xamarin.Forms.Image) 物件和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Image` 物件資料會將其 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性繫結至每個`Monkey` 物件的 `ImageUrl` 屬性，而 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至每個 `Monkey` 物件的 `Name` 和 `Location` 屬性。

    如需 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 項目外觀的詳細資訊，請參閱[定義項目外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。 如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![以資料範本將其項目樣板化的 CollectionView 螢幕擷取畫面](../images/customize-item-appearance.png "顯示樣板化資料的 CollectionView")](../images/customize-item-appearance-large.png#lightbox "顯示樣板化資料的 CollectionView")

    在 Visual Studio 中，停止應用程式。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 宣告，以自訂每個資料項目的外觀：

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    此程式碼會將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以定義 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中每個項目的外觀。 `DataTemplate` 的子系是 [`Grid`](xref:Xamarin.Forms.Grid)，其中包含 [`Image`](xref:Xamarin.Forms.Image) 物件和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Image` 物件資料會將其 [`Source`](xref:Xamarin.Forms.Image.Source) 屬性繫結至每個`Monkey` 物件的 `ImageUrl` 屬性，而 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至每個 `Monkey` 物件的 `Name` 和 `Location` 屬性。

    如需 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 項目外觀的詳細資訊，請參閱[定義項目外觀](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。 如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![以資料範本將其項目樣板化的 CollectionView 螢幕擷取畫面](../images/customize-item-appearance.png "顯示樣板化資料的 CollectionView")](../images/customize-item-appearance-large.png#lightbox "顯示樣板化資料的 CollectionView")

    在 Visual Studio for Mac 中，停止應用程式。
