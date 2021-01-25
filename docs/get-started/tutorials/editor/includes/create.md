---
ms.openlocfilehash: 76eab5a08f5ccc53a4db5370bf996aed75c142e7
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689880"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

若要完成此教學課程，您應該有 Visual Studio 2019 (最新版本)，並已安裝 [使用 .NET 進行行動開發] 工作負載。 此外，您還需要配對的 Mac 才能在 iOS 上建置教學課程應用程式。 如需安裝 Xamarin 平台的相關資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 啟動 Visual Studio，然後建立名為 **EditorTutorial** 的新空白 Xamarin.Forms 應用程式。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **EditorTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 的 **EditorTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Editor`](xref:Xamarin.Forms.Editor)。 [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性會指定 `Editor` 第一次顯示時呈現的預留位置文字。 此外，[`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Editor` 的高度 (採用與裝置無關的單位)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上的 Editor](../images/create-editor.png "包含預留位置文字的 Editor")](../images/create-editor-large.png#lightbox "包含預留位置文字的 Editor")

    > [!NOTE]
    > Android 會指示 [`Editor`](xref:Xamarin.Forms.Editor) 的高度，但 iOS 不會。

    在 Visual Studio 中，停止應用程式。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教學課程，您應該具備 Visual Studio for Mac (最新版本)，並已安裝 iOS 和 Android 平台支援。 此外，您還需要 Xcode (最新版本)。 如需安裝 Xamarin 平台的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

1. 啟動 Visual Studio for Mac，然後建立名為 **EditorTutorial** 的新空白 Xamarin.Forms 應用程式。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **EditorTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 的 **EditorTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Editor`](xref:Xamarin.Forms.Editor)。 [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性會指定 `Editor` 第一次顯示時呈現的預留位置文字。 此外，[`HeightRequest`](xref:Xamarin.Forms.VisualElement) 屬性可指定 `Editor` 的高度 (採用與裝置無關的單位)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上的 Editor](../images/create-editor.png "包含預留位置文字的 Editor")](../images/create-editor-large.png#lightbox "包含預留位置文字的 Editor")

    > [!NOTE]
    > Android 會指示 [`Editor`](xref:Xamarin.Forms.Editor) 的高度，但 iOS 不會。

    在 Visual Studio for Mac 中，停止應用程式。
