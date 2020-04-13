---
title: WPF 平台設定
description: Xamarin.Forms 現在對 WPF 平臺具有預覽支援
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: c9ec9fec2391d7d7a24f97f2ec20208a7d69dbc1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992308"
---
# <a name="wpf-platform-setup"></a>WPF 平台設定

![預覽](~/media/shared/preview.png)

Xamarin.Forms 現在具有對 Windows 演示基礎 (WPF) 的預覽支援。 本文演示如何將 WPF 專案添加到 Xamarin.Forms 解決方案。

> [!IMPORTANT]
> Xamarin.表格支援WPF由社區提供。 有關詳細資訊,請參閱[Xamarin.窗體平台支援](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

在開始之前,在 Visual Studio 2019 建立新的 Xamarin.Forms 解決方案,或使用現有的 Xamarin.Forms 解決方案,例如[**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)。 您只能將 WPF 應用添加到 Windows 中的 Xamarin.Forms 解決方案。

## <a name="add-a-wpf-application"></a>新增 WPF 應用程式

依以下說明新增將在 Windows 7、8 和 10 桌面執行的 WPF 應用程式:

1. 在 Visual Studio 2019 中,右鍵單擊**解決方案資源管理器**中的解決方案名稱,然後選擇"**添加>新專案..."**

2. 在「**新增新項目**」 視窗中,在 **「語言**」下拉清單中選擇**C#,** 在 **「平臺**」下拉清單中選擇**Windows,** 然後選擇 **「項目類型**」 選單中的**桌面**。 在項目型態清單中,選擇**WPF 應用 (.NET 框架):**

    ![新增 WPF 專案](wpf-images/add-project.png "新增 WPF 專案")

    按 **「下一步」** 按鈕。

3. 在 **「設定新項目**」 視窗中,鍵入具有**WPF**副檔名的項目名稱,例如**BoxViewClock.WPF**。 點選 **「瀏覽」** 按鈕,選擇**BoxViewClock**資料夾,然後按 **「選擇資料夾」** 將 WPF 專案與解決方案中的其他專案放在同一目錄中:

    ![新增 WPF 專案](wpf-images/configure-project.png "新增 WPF 專案")

    按 **「創建**」按鈕以建立專案。

4. 在**解決方案資源管理器**中,右鍵單擊新的**BoxViewClock.WPF**專案,然後選擇 **「管理 NuGet 包..."。** 選擇 **「瀏覽**」選項卡,然後搜尋**Xamarin.Forms.Platform.WPF**:

    ![選擇 NuGet 套件](wpf-images/select-nuget-package.png "選擇 NuGet 套件")

    選擇包,然後按下 **「安裝**」按鈕。

5. 右鍵單擊**解決方案資源管理器**中的解決方案名稱,然後選擇 **「管理解決方案的 NuGet 包..."。** 選擇「**更新**」選項卡,然後選擇**Xamarin.窗體**套件。 選擇所有項目並將其更新為相同的 Xamarin.Forms 版本:

    ![更新 NuGet 套件](wpf-images/update-nuget-package.png "更新 NuGet 套件")

6. 在 WPF 專案中,右鍵單擊 **"引用"** 並選擇"**添加參考..."** 在 **"參考管理員'****對話框中,** 選擇左側的項目,然後選中與**BoxViewClock**專案相鄰的複選框:

    ![參考分享項目](wpf-images/reference-shared-project.png "參考分享項目")

    按 **「確定」** 按鈕。

7. 編輯 WPF 專案的**MainWindow.xaml**檔。 在標記`Window`中,為**Xamarin.Forms.Platform.WPF**程式集和命名空間新增 XML 命名空間聲明:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    現在將`Window`標記變更`wpf:FormsApplicationPage`為 。 將`Title`設定變更為應用程式的名稱,例如**BoxViewClock**。 已完成的 XAML 檔應如下所示:

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

8. 編輯 WPF 專案的**MainWindow.xaml.cs**檔。 新增兩個新`using`指令:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    將`MainWindow`的基數`Window``FormsApplicationPage`以變更為 。 呼叫`InitializeComponent`後,加入以下兩個語句:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    除了註解和未使用的`using`指令外,完整的**MainWindows.xaml.cs**檔應如下所示:

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

9. 右鍵單擊**解決方案資源管理器**中的 WPF 專案,然後選擇 **「設置為啟動專案**」。 以 F5 在 Windows 桌面上使用 Visual Studio 除錯器執行程式:

    ![WPF 盒檢視時鐘](wpf-images/wpf-boxviewclock.png "WPF 盒檢視時鐘" )

## <a name="platform-specifics"></a>平台詳情

您可以確定 Xamarin.Forms 應用程式從代碼或 XAML 運行的平臺。 這允許您在 WPF 上執行程式特徵時更改其特徵。 在代碼中,將的值`Device.RuntimePlatform``Device.WPF`與常量(等於字串"WPF")進行比較。 如果存在匹配項,則應用程式在 WPF 上運行。

在 XAML`OnPlatform`中, 可以使用標記選擇特定於平台的屬性值:

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

您可以在 WPF **MainWindow.xaml**檔案中調整視窗的初始大小:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>問題

這是預覽,因此您應該期望並非所有產品都已準備就緒。 並非所有 Xamarin.Forms 的 NuGet 包都已準備好用於 WPF,並且某些功能可能未完全工作。

## <a name="related-video"></a>相關影片

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.表格 3.0 WPF 支援視訊**
