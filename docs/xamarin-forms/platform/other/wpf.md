---
title: WPF 平台安裝程式
description: Xamarin.Forms 現在提供 WPF 平台的預覽支援
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 2bef13e7f465dd213649f88deb572eb661895250
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245393"
---
# <a name="wpf-platform-setup"></a>WPF 平台安裝程式

![預覽](~/media/shared/preview.png)

Xamarin.Forms 現在提供預覽支援 Windows Presentation Foundation (WPF)。 這篇文章會示範如何將 WPF 專案新增至 Xamarin.Forms 方案。

啟動、 建立新的 Xamarin.Forms 方案在 Visual Studio 2019，或使用現有的 Xamarin.Forms 方案，例如之前[ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)。 您只能新增至 Xamarin.Forms 方案在 Windows 中 WPF 應用程式。

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>使用 Xamarin.University Xamarin.Forms 應用程式中加入 WPF 專案

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF 支援，藉由[Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>新增 WPF 應用程式

請遵循這些指示來新增將在 Windows 7、 8 和 10 桌上型電腦執行的 WPF 應用程式：

1. 在 Visual Studio 2019，以滑鼠右鍵按一下方案名稱，在**方案總管**，然後選擇 **新增 > 新增專案...**.

2. 在 **新的專案**視窗中的，從左邊選取**Visual C#** 並**Windows 傳統桌面**。 在專案類型清單中，選擇**WPF 應用程式 (.NET Framework)**。 

3. 輸入專案名稱**WPF**擴充功能，例如**BoxViewClock.WPF**。 按一下 **瀏覽**按鈕，選取**BoxViewClock**資料夾，然後按**選取資料夾**。 這樣就會加入 WPF 專案與方案中的其他專案相同的目錄中。

    ![加入新的 WPF 專案](wpf-images/add-new-project.png "新增新的 WPF 專案")

    按下 [確定] 以建立專案。

4. 在 **方案總管 中**，直接按一下 新**BoxViewClock.WPF**專案，然後選取**管理 NuGet 套件**。 選取 **瀏覽**索引標籤上，按一下**包含發行前版本**核取方塊，並搜尋**Xamarin.Forms**。

    ![選取 NuGet 套件](wpf-images/select-nuget-package.png "選取 NuGet 套件")

    選取的封裝，然後按一下**安裝** 按鈕。

5. 現在搜尋**Xamarin.Forms.Platform.WPF**封裝，然後一併安裝一個。 請確定封裝是來自 Microsoft ！

6. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**管理方案的 NuGet 套件**。 選取 **更新**索引標籤和**Xamarin.Forms**封裝。 選取所有專案，並更新為相同的 Xamarin.Forms 版本：

    ![更新 NuGet 套件](wpf-images/update-nuget-package.png "的 NuGet 套件更新") 

7. 在 WPF 專案中，以滑鼠右鍵按一下**參考**。 在 **參考管理員**對話方塊中，選取**專案**在左側，並核取方塊旁的核取**BoxViewClock**專案：

    ![參考共用的專案](wpf-images/reference-shared-project.png "參考共用的專案")

8. 編輯**MainWindow.xaml** WPF 專案檔案。 在 `Window`標記中加入 XML 命名空間宣告**Xamarin.Forms.Platform.WPF**組件和命名空間：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    現在請變更`Window`標記至`wpf:FormsApplicationPage`。 變更`Title`設為 應用程式的名稱，例如**BoxViewClock**。 已完成的 XAML 檔案看起來應該像這樣：

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

9. 編輯**MainWindow.xaml.cs** WPF 專案檔案。 新增兩個新`using`指示詞：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    變更的基底類別`MainWindow`從`Window`至`FormsApplicationPage`。 遵循`InitializeComponent`呼叫中，新增下列兩個陳述式：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    除了註解和未使用`using`指示詞，完整**MainWindows.xaml.cs**檔案應該看起來像這樣：

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

10. 中的 WPF 專案上按一下滑鼠右鍵**方案總管**，然後選取**設定為啟始專案**。 按 f5 鍵以執行 Visual Studio 偵錯工具在 Windows 桌面上的程式：

    ![WPF BoxView 時鐘](wpf-images/wpf-boxviewclock.png "WPF BoxView 時鐘" )

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以判斷您的 Xamarin.Forms 應用程式執行的程式碼或 XAML 從何種平台。 這可讓您在 WPF 上執行時，變更程式的特性。 在程式碼中的值相比較的`Device.RuntimePlatform`與`Device.WPF`常數 （等於字串"WPF"）。 如果沒有相符項目，在 WPF 上執行應用程式。

在 XAML 中，您可以使用`OnPlatform`標記來選取平台的特定屬性值：

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

您可以調整視窗中，WPF 中的初始大小**MainWindow.xaml**檔案：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>問題

這會是預覽，因此，您應該預期不是所有項目是用於生產環境。 並非所有的 NuGet 套件，適用於 Xamarin.Forms 準備好進行 WPF 中，而某些功能可能無法完全運作。

