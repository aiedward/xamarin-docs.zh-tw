# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，然後建立名為 **GridTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **GridTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 的 **GridTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Label`](xref:Xamarin.Forms.Label)。 根據預設，`Grid` 會將其子檢視放在單一位置中。 因此，包含多個子系的 `Grid` 應該指定資料行和資料列，下一個練習會有相關說明。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 屬性會指出 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 `Grid` 之中的呈現位置。

    > [!NOTE]
    > 除了 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性之外，[`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性也可在 [`Grid`](xref:Xamarin.Forms.Grid) 上設定。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性值會在 `Grid` 中指定檢視之間的距離。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 遠端模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上格線中的標籤](../images/create-grid.png "包含標籤的格線")](../images/create-grid-large.png#lightbox "包含標籤的格線s")

    如需有關 [`Grid`](xref:Xamarin.Forms.Grid) 的詳細資訊，請參閱 [Xamarin.Forms 格線](~/xamarin-forms/user-interface/layouts/grid.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後建立名為 **GridTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **GridTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 的 **GridTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Label`](xref:Xamarin.Forms.Label)。 根據預設，`Grid` 會將其子檢視放在單一位置中。 因此，包含多個子系的 `Grid` 應該指定資料行和資料列，下一個練習會有相關說明。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 屬性會指出 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 `Grid` 之中的呈現位置。

    > [!NOTE]
    > 除了 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性之外，[`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性也可在 [`Grid`](xref:Xamarin.Forms.Grid) 上設定。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性值會在 `Grid` 中指定檢視之間的距離。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上格線中的標籤](../images/create-grid.png "包含標籤的格線")](../images/create-grid-large.png#lightbox "包含標籤的格線s")

    如需有關 [`Grid`](xref:Xamarin.Forms.Grid) 的詳細資訊，請參閱 [Xamarin.Forms 格線](~/xamarin-forms/user-interface/layouts/grid.md)。
