---
ms.openlocfilehash: 855ca4a56915578fdd2560110bc3231f122d0dc9
ms.sourcegitcommit: 99aa05bd9b5e3f66d134066b860f41b54fa2d850
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109836"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

若要完成此教學課程，您應該有 Visual Studio 2019 (最新版本)，並已安裝 [使用 .NET 進行行動開發]  工作負載。 此外，您還需要配對的 Mac 才能在 iOS 上建置教學課程應用程式。 如需安裝 Xamarin 平台的相關資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 啟動 Visual Studio，然後建立名為 **ImageTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **ImageTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管]  的 **ImageTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Image`](xref:Xamarin.Forms.Image)。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性可指定要透過 URI 顯示的影像。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性的類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource)，其可讓影像源自檔案、URI 或資源。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md#display-images)。

    [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Image` 的高度 (採用裝置獨立單位)。

    > [!NOTE]
    > 您不需要在此範例中設定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性。 這是因為根據預設，[`Image`](xref:Xamarin.Forms.Image) 會維持影像的外觀比例。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![IOS 和 Android 影像的螢幕擷取畫面](../images/create-image.png "顯示影像的影像檢視")](../images/create-image-large.png#lightbox "顯示影像的影像檢視")

    > [!NOTE]
    > [`Image`](xref:Xamarin.Forms.Image) 檢視會在 24 小時內自動快取下載的影像。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[下載的影像快取](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教學課程，您應該具備 Visual Studio for Mac (最新版本)，並已安裝 iOS 和 Android 平台支援。 此外，您還需要 Xcode (最新版本)。 如需安裝 Xamarin 平台的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

1. 啟動 Visual Studio for Mac，然後建立名為 **ImageTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **ImageTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad]  的 **ImageTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Image`](xref:Xamarin.Forms.Image)。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性可指定要透過 URI 顯示的影像。 [`Image.Source`](xref:Xamarin.Forms.Image.Source) 屬性的類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource)，其可讓影像源自檔案、URI 或資源。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md#display-images)。

    [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Image` 的高度 (採用裝置獨立單位)。

    > [!NOTE]
    > 您不需要在此範例中設定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性。 這是因為根據預設，[`Image`](xref:Xamarin.Forms.Image) 會維持影像的外觀比例。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![IOS 和 Android 影像的螢幕擷取畫面](../images/create-image.png "顯示影像的影像檢視")](../images/create-image-large.png#lightbox "顯示影像的影像檢視")

    > [!NOTE]
    > [`Image`](xref:Xamarin.Forms.Image) 檢視會在 24 小時內自動快取下載的影像。 如需詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)指南中的[下載的影像快取](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。
