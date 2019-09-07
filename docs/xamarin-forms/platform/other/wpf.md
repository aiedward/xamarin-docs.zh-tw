---
title: WPF 平臺設定
description: Xamarin 現已提供 WPF 平臺的預覽支援
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 8fcad4799cd53892106b3e221cff0dfbc737e10d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760044"
---
# <a name="wpf-platform-setup"></a>WPF 平臺設定

![預覽](~/media/shared/preview.png)

Xamarin 現在具有 Windows Presentation Foundation （WPF）的預覽支援。 本文示範如何將 WPF 專案加入至 Xamarin 表單方案。

開始之前，請在 Visual Studio 2019 中建立新的 Xamarin 表單方案，或使用現有的 Xamarin 表單方案，例如[**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)。 您只能將 WPF 應用程式新增至 Windows 中的 [Xamarin] 表單方案。

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>使用 Xamarin 將 WPF 專案新增至 Xamarin. Forms 應用程式

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin. Forms 3.0 WPF 支援影片**

## <a name="adding-a-wpf-app"></a>新增 WPF 應用程式

遵循這些指示來新增將在 Windows 7、8和10桌上型電腦上執行的 WPF 應用程式：

1. 在 Visual Studio 2019 中，以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選擇 [**加入] > [新增專案**]。

2. 在 [**新增專案**] 視窗中，選取左側的 [**視覺效果C#** 和**Windows 傳統桌面**]。 在專案類型清單中，選擇 [ **WPF 應用程式（.NET Framework）** ]。 

3. 以**wpf**擴充功能輸入專案的名稱，例如**BoxViewClock。** 按一下 [**流覽]** 按鈕，選取 [ **BoxViewClock** ] 資料夾，然後按 [**選取資料夾**]。 這會將 WPF 專案放在與方案中的其他專案相同的目錄中。

    ![加入新的 WPF 專案](wpf-images/add-new-project.png "加入新的 WPF 專案")

    按 [確定] 以建立專案。

4. 在 **方案總管**中，以滑鼠右鍵按一下新的**BoxViewClock WPF**專案，然後選取 **管理 NuGet 套件**。 選取 [**流覽**] 索引標籤、按一下 [**包含發行**前版本] 核取方塊，然後搜尋 [ **Xamarin. 表單**]。

    ![選取 NuGet 套件](wpf-images/select-nuget-package.png "選取 NuGet 套件")

    選取該套件，然後按一下 [**安裝**] 按鈕。

5. 現在搜尋 [ **Xamarin** ]，然後再安裝該套件。 請確定套件是來自 Microsoft！

6. 以滑鼠右鍵按一下 **方案總管**中的方案名稱，然後選取 **管理方案的 NuGet 套件**。 選取 [**更新**] 索引標籤和 [ **Xamarin. 表單**] 套件。 選取所有專案，並將其更新為相同的 Xamarin. 表單版本：

    ![更新 NuGet 套件](wpf-images/update-nuget-package.png "更新 NuGet 套件") 

7. 在 WPF 專案中，以滑鼠右鍵按一下 [**參考**]。 在 [**參考管理員**] 對話方塊中，選取左側的 [**專案**]，然後核取 [ **BoxViewClock** ] 專案旁邊的核取方塊：

    ![參考共用專案](wpf-images/reference-shared-project.png "參考共用專案")

8. 編輯 WPF 專案的**mainwindow.xaml。** 在標記中，新增 Xamarin 元件和命名空間的 XML 命名空間宣告： `Window`

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    現在將`Window`標記變更為`wpf:FormsApplicationPage`。 將設定變更為您應用程式的名稱，例如 BoxViewClock。 `Title` 完成的 XAML 檔案看起來應該像這樣：

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. 編輯 WPF 專案的**MainWindow.xaml.cs**檔案。 加入兩個`using`新的指示詞：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    將的基類`MainWindow`從`Window`變更為`FormsApplicationPage`。 在`InitializeComponent`呼叫之後，新增下列兩個語句：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    除了批註和未使用`using`的指示詞之外，完整的**MainWindows.xaml.cs**檔案看起來應該像這樣：

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

10. 以滑鼠右鍵按一下 **方案總管**中的 WPF 專案，然後選取 **設定為啟始專案**。 按 F5 鍵，以在 Windows 桌面上使用 Visual Studio 偵錯工具來執行程式：

    ![WPF BoxView 時鐘](wpf-images/wpf-boxviewclock.png "WPF BoxView 時鐘" )

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以從程式碼或 XAML 判斷您的 Xamarin Forms 應用程式執行所在的平臺。 這可讓您在 WPF 上執行程式特性時，加以變更。 在程式碼中，將的`Device.RuntimePlatform`值`Device.WPF`與常數（等於字串 "WPF"）進行比較。 如果有相符的結果，應用程式就會在 WPF 上執行。

在 XAML 中，您可以使用`OnPlatform`標記來選取平臺特定的屬性值：

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

### <a name="window-size"></a>視窗大小

您可以在 WPF **mainwindow.xaml**中調整視窗的初始大小：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>問題

這是預覽版，因此您應該預期並非所有專案都是生產環境就緒。 並非所有適用于 Xamarin 的 NuGet 套件都已準備好使用 WPF，而且有些功能可能無法完全運作。
