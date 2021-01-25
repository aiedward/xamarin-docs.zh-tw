---
ms.openlocfilehash: 9d96f61b258691715ebdbfe0d4221eed240c1bdf
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634750"
---
[`StackLayout`](xref:Xamarin.Forms.StackLayout) 是一種配置，可將其子系以水平或垂直方式組織成一維堆疊。 根據預設，`StackLayout` 是垂直方向。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

若要完成此教學課程，您應該有 Visual Studio 2019 (最新版本)，並已安裝 [使用 .NET 進行行動開發] 工作負載。 此外，您還需要配對的 Mac 才能在 iOS 上建置教學課程應用程式。 如需安裝 Xamarin 平台的相關資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 啟動 Visual Studio，然後建立名為 **StackLayoutTutorial** 的新空白 Xamarin.Forms 應用程式。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **StackLayoutTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/quickstarts/deepdive.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/quickstarts/deepdive.md#anatomy-of-a-xamarinforms-application)。

1. 在 [方案總管]  的 **StackLayoutTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的三個 [`Label`](xref:Xamarin.Forms.Label) 執行個體。 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會將其子檢視 (`Label` 執行個體) 放在單一資料行中，而預設方向為垂直。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 屬性會指出 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 `StackLayout` 之中的呈現位置。

    > [!NOTE]
    > 除了 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性之外，[`Padding`](xref:Xamarin.Forms.Layout.Padding) 和 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性也可在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 上設定。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性值會指定 `StackLayout` 中各檢視之間的距離，而 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性值會指定 `StackLayout` 中每個子項目之間的空間量。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上 StackLayout 中子檢視的螢幕擷取畫面](../images/create-stacklayout.png "包含標籤執行個體的 StackLayout")](../images/create-stacklayout-large.png#lightbox "包含標籤執行個體的 StackLayout")

    如需有關 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的詳細資訊，請參閱 [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教學課程，您應該具備 Visual Studio for Mac (最新版本)，並已安裝 iOS 和 Android 平台支援。 此外，您還需要 Xcode (最新版本)。 如需安裝 Xamarin 平台的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

1. 啟動 Visual Studio for Mac，然後建立名為 **StackLayoutTutorial** 的新空白 Xamarin.Forms 應用程式。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **StackLayoutTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad]  的 **StackLayoutTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的三個 [`Label`](xref:Xamarin.Forms.Label) 執行個體。 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會將其子檢視 (`Label` 執行個體) 放在單一資料行中，而預設方向為垂直。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 屬性會指出 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 `StackLayout` 之中的呈現位置。

    > [!NOTE]
    > 除了 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性之外，[`Padding`](xref:Xamarin.Forms.Layout.Padding) 和 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性也可在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 上設定。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性值會指定 `StackLayout` 中各檢視之間的距離，而 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性值會指定 `StackLayout` 中每個子項目之間的空間量。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 上 StackLayout 中子檢視的螢幕擷取畫面](../images/create-stacklayout.png "包含標籤執行個體的 StackLayout")](../images/create-stacklayout-large.png#lightbox "包含標籤執行個體的 StackLayout")

    如需有關 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的詳細資訊，請參閱 [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)。
