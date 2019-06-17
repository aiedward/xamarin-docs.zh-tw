---
ms.openlocfilehash: 84c4ed5c2e019750db80a6dd930c818dcc9e401c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896648"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，然後建立名為 **LabelTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **LabelTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 的 **LabelTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LabelTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
            <Label Text="Welcome to Xamarin.Forms!"
                   HorizontalOptions="Center" />
       </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Label`](xref:Xamarin.Forms.Label)。 [`Label.Text`](xref:Xamarin.Forms.Button.Text) 屬性會指定顯示的文字，而 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性會指定將文字水平置中。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上置中標籤的螢幕擷取畫面](../images/create-label.png "置中標籤")](../images/create-label-large.png#lightbox "置中標籤")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後建立名為 **LabelTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **LabelTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 的 **LabelTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="LabelTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
            <Label Text="Welcome to Xamarin.Forms!"
                   HorizontalOptions="Center" />
       </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Label`](xref:Xamarin.Forms.Label)。 [`Label.Text`](xref:Xamarin.Forms.Button.Text) 屬性會指定顯示的文字，而 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性會指定將文字水平置中。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上置中標籤的螢幕擷取畫面](../images/create-label.png "置中標籤")](../images/create-label-large.png#lightbox "置中標籤")
