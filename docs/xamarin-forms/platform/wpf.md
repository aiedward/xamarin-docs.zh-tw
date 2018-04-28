---
title: WPF 平台安裝程式
description: Xamarin.Forms 現在具有預覽 WPF 平台支援
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 2e2bbf12cd7b4abab4609349b549fde1bcea09e8
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="wpf-platform-setup"></a>WPF 平台安裝程式

![預覽](~/media/shared/preview.png)

Xamarin.Forms 現在提供預覽支援的 Windows Presentation Foundation (WPF)。 本文示範如何將 WPF 專案新增至 Xamarin.Forms 方案。

啟動、 建立新的 Xamarin.Forms 方案在 Visual Studio 2017，或使用現有的 Xamarin.Forms 方案，例如之前[ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)。 您可以只將 Xamarin.Forms 方案，在 Windows 中 WPF 應用程式中加入。

## <a name="adding-a-wpf-app"></a>加入 WPF 應用程式

請遵循這些指示來新增將在 Windows 7、 8 和 10 桌上型電腦執行的 WPF 應用程式：

1. 在 Visual Studio 2017，以滑鼠右鍵按一下方案名稱在**方案總管 中**選擇**新增 > 新的專案...**.

2. 在**新專案**視窗中的，在左側選取**Visual C#** 和**的傳統 Windows 桌面**。 在專案類型清單中選擇**WPF 應用程式 (.NET Framework)**。 

3. 輸入的名稱與專案**WPF**擴充功能，例如**BoxViewClock.WPF**。 按一下**瀏覽**按鈕，選取**BoxViewClock**資料夾，然後按下**選取資料夾**。 這樣就會加入 WPF 專案與方案中的其他專案相同的目錄中。

    ![加入新的 WPF 專案](wpf-images/add-new-project.png "加入新的 WPF 專案")

    按下 [確定] 建立專案。

4. 在**方案總管] 中**，直接按一下 [新**BoxViewClock.WPF**專案，然後選取**管理 NuGet 封裝**。 選取**瀏覽**索引標籤上，按一下 **包含發行前版本**核取方塊，並搜尋**Xamarin.Forms**。

    ![選取的 NuGet 套件](wpf-images/select-nuget-package.png "選取 NuGet 封裝")

    選取的封裝，然後按一下 [**安裝**] 按鈕。

5. 現在搜尋**Xamarin.Forms.Platform.WPF**封裝，並一併安裝的其中一個。 請確定封裝會從 Microsoft ！

6. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**管理方案的 NuGet 套件**。 選取**更新** 索引標籤和**Xamarin.Forms**封裝。 選取所有專案，並更新為相同的 Xamarin.Forms 版本：

    ![將 NuGet 套件更新](wpf-images/update-nuget-package.png "更新 NuGet 套件") 

7. 在 WPF 專案中，以滑鼠右鍵按一下**參考**。 在**參考管理員**對話方塊中，選取**專案**在左邊，並檢查相鄰的核取方塊**BoxViewClock**專案：

    ![參考共用的專案](wpf-images/reference-shared-project.png "參考共用的專案")

8. 編輯**MainWindow.xaml** WPF 專案檔。 在`Window`標記中，加入的 XML 命名空間宣告**Xamarin.Forms.Platform.WPF**組件和命名空間：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    現在變更`Window`標記至`wpf:FormsApplicationPage`。 變更`Title`設為應用程式的名稱，例如**BoxViewClock**。 已完成的 XAML 檔案看起來應該像這樣：

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

9. 編輯**MainWindow.xaml.cs** WPF 專案檔。 加入兩個新`using`指示詞：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    變更基底類別的`MainWindow`從`Window`至`FormsApplicationPage`。 遵循`InitializeComponent`呼叫中，加入下列兩個陳述式：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    除了註解和未使用`using`指示詞、 完整**MainWindows.xaml.cs**檔案應該看起來像這樣：

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

10. 以滑鼠右鍵按一下中的 WPF 專案**方案總管 中**選取**設定為啟始專案**。 按 f5 鍵，在 Windows 桌面上，Visual Studio 偵錯工具執行程式：

    ![WPF BoxView 時鐘](wpf-images/wpf-boxviewclock.png "WPF BoxView 時鐘" )

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以判斷 Xamarin.Forms 應用程式執行的程式碼或 XAML 何種平台。 這可讓您變更程式的特性，其會在 WPF 上執行。 在程式碼中的值相比較的`Device.RuntimePlatform`與`Device.WPF`常數 （等於字串"WPF"）。 如果沒有相符項目，在 WPF 上執行應用程式。

在 XAML 中，您可以使用`OnPlatform`標記加入至選取的平台專屬的屬性值：

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

您可以調整中 WPF 視窗的初始大小**MainWindow.xaml**檔案：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>問題

這是預覽，因此，您應該預期不是所有項目是生產環境就緒。 並非所有 Xamarin.Forms NuGet 套件 WPF 中，而某些功能可能無法完全運作。

