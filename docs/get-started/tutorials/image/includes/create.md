# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，然後建立名為 **ImageTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **ImageTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 的 **ImageTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Image`](xref:Xamarin.Forms.Image)。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性可指定要透過 URI 顯示的影像。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性的類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource)，其可讓影像源自檔案、URI 或資源。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md#displaying-images)。

    [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Image` 的高度 (採用裝置獨立單位)。

    > [!NOTE]
    > 您不需要在此範例中設定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性。 這是因為根據預設，[`Image`](xref:Xamarin.Forms.Image) 會維持影像的外觀比例。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上影像的螢幕擷取畫面](../images/create-image.png "顯示影像的影像檢視")](../images/create-image-large.png#lightbox "顯示影像的影像檢視")

    > [!NOTE]
    > [`Image`](xref:Xamarin.Forms.Image) 檢視會在 24 小時內自動快取下載的影像。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[下載的影像快取](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後建立名為 **ImageTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **ImageTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 的 **ImageTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Image`](xref:Xamarin.Forms.Image)。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性可指定要透過 URI 顯示的影像。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性的類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource)，其可讓影像源自檔案、URI 或資源。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md#displaying-images)。

    [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Image` 的高度 (採用裝置獨立單位)。

    > [!NOTE]
    > 您不需要在此範例中設定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性。 這是因為根據預設，[`Image`](xref:Xamarin.Forms.Image) 會維持影像的外觀比例。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上影像的螢幕擷取畫面](../images/create-image.png "顯示影像的影像檢視")](../images/create-image-large.png#lightbox "顯示影像的影像檢視")

    > [!NOTE]
    > [`Image`](xref:Xamarin.Forms.Image) 檢視會在 24 小時內自動快取下載的影像。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[下載的影像快取](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。
