---
title: ''
description: Xamarin.Forms現已提供 WPF 平臺的預覽支援
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bbb9ea950c9f1daa1b5ee4762527d689f3cdd483
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139525"
---
# <a name="wpf-platform-setup"></a>WPF 平臺設定

![預覽](~/media/shared/preview.png)

Xamarin.Forms現在提供 Windows Presentation Foundation （WPF）的預覽支援。 本文示範如何將 WPF 專案加入至 Xamarin.Forms 方案。

> [!IMPORTANT]
> Xamarin.FormsWPF 的支援是由「社區」提供。 如需詳細資訊，請參閱[ Xamarin.Forms 平臺支援](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

開始之前，請先 Xamarin.Forms 在 Visual Studio 2019 中建立新的解決方案，或使用現有的 Xamarin.Forms 解決方案，例如[**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)。 您只能將 WPF 應用程式新增至 Xamarin.Forms Windows 中的方案。

## <a name="add-a-wpf-application"></a>新增 WPF 應用程式

遵循這些指示，新增將在 Windows 7、8和10桌上型電腦上執行的 WPF 應用程式：

1. 在 Visual Studio 2019 中，以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選擇 [**加入] > [新增專案**]。

2. 在 [**加入新專案**] 視窗中，選取 [**語言**] 下拉式下的 [ **c #** ]，在 [**平臺**] 下拉式下選取 [ **Windows** ]，然後選取 [**專案類型**] 下拉式按鈕中的 [**桌面**]。 在專案類型清單中，選擇 [ **WPF 應用程式（.NET Framework）**]：

    ![加入新的 WPF 專案](wpf-images/add-project.png "加入新的 WPF 專案")

    按 [**下一步]** 按鈕。

3. 在 [**設定您的新專案**] 視窗中，以**wpf**擴充功能輸入專案的名稱，例如**BoxViewClock。** 按一下 [**流覽]** 按鈕，選取 [ **BoxViewClock** ] 資料夾，然後按 [**選取資料夾**]，將 WPF 專案放在與方案中的其他專案相同的目錄中：

    ![加入新的 WPF 專案](wpf-images/configure-project.png "加入新的 WPF 專案")

    按 [**建立**] 按鈕以建立專案。

4. 在 [**方案總管**中，以滑鼠右鍵按一下新的**BoxViewClock WPF**專案，然後選取 [**管理 NuGet 套件**...]。選取 [**流覽**] 索引標籤，然後搜尋** Xamarin.Forms 。Platform. WPF**：

    ![選取 NuGet 套件](wpf-images/select-nuget-package.png "選取 NuGet 套件")

    選取套件，然後按一下 [**安裝**] 按鈕。

5. 以滑鼠右鍵按一下 [**方案總管**中的方案名稱，然後選取 [**管理方案的 NuGet 套件**...]。選取 [**更新**] 索引標籤，然後選取 **Xamarin.Forms** 套件。 選取所有專案，並將其更新為相同的 Xamarin.Forms 版本：

    ![更新 NuGet 套件](wpf-images/update-nuget-package.png "更新 NuGet 套件")

6. 在 WPF 專案中，以滑鼠右鍵按一下 [**參考**]，然後選取 [**新增參考 ...**]。在 [**參考管理員**] 對話方塊中，選取左側的 [**專案**]，然後核取 [ **BoxViewClock** ] 專案旁邊的核取方塊：

    ![參考共用專案](wpf-images/reference-shared-project.png "參考共用專案")

    按下 [**確定]** 按鈕。

7. 編輯 WPF 專案的**mainwindow.xaml。** 在 `Window` 標記中，加入的 XML 命名空間宣告** Xamarin.Forms 。Platform. WPF**元件和命名空間：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    現在將 `Window` 標記變更為 `wpf:FormsApplicationPage` 。 將 `Title` 設定變更為您應用程式的名稱，例如**BoxViewClock**。 完成的 XAML 檔案看起來應該像這樣：

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. 編輯 WPF 專案的**MainWindow.xaml.cs**檔案。 加入兩個新的指示詞 `using` ：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    將的基類從變更 `MainWindow` `Window` 為 `FormsApplicationPage` 。 在 `InitializeComponent` 呼叫之後，新增下列兩個語句：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    除了批註和未使用的指示詞之外 `using` ，完整的**MainWindows.xaml.cs**檔案看起來應該像這樣：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

9. 以滑鼠右鍵按一下 [**方案總管**中的 WPF 專案，然後選取 [**設定為啟始專案**]。 按 F5 鍵，以在 Windows 桌面上使用 Visual Studio 偵錯工具來執行程式：

    ![WPF BoxView 時鐘](wpf-images/wpf-boxviewclock.png "WPF BoxView 時鐘" )

## <a name="platform-specifics"></a>平臺細節

您可以 Xamarin.Forms 從程式碼或 XAML 判斷您的應用程式在哪一個平臺上執行。 這可讓您在 WPF 上執行程式特性時，加以變更。 在程式碼中，將的值 `Device.RuntimePlatform` 與 `Device.WPF` 常數（等於字串 "WPF"）進行比較。 如果有相符的結果，應用程式就會在 WPF 上執行。

在 XAML 中，您可以使用 `OnPlatform` 標記來選取平臺特定的屬性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

## <a name="window-size"></a>視窗大小

您可以在 WPF **mainwindow.xaml**中調整視窗的初始大小：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>問題

這是預覽版，因此您應該預期並非所有專案都是生產環境就緒。 並非所有的 NuGet 套件 Xamarin.Forms 都適用于 WPF，而且某些功能可能無法完全正常運作。

## <a name="related-video"></a>相關影片

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms3.0 WPF 支援影片**
