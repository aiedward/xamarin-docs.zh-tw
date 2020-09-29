---
title: 建立單一頁面 Xamarin.Forms 應用程式
description: 本文說明如何建立單頁跨平臺 Xamarin.Forms 應用程式，這可讓您輸入附注，並將其保存到裝置儲存體。
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96b3e6bd055c0bc89ae7bcbb66c8b3f48b21ad17
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436230"
---
# <a name="create-a-single-page-no-locxamarinforms-application"></a>建立單一頁面 Xamarin.Forms 應用程式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

在本快速入門中，您將了解如何：

- 建立跨平臺 Xamarin.Forms 應用程式。
- 使用可延伸應用程式標記語言 (XAML) 定義頁面的使用者介面。
- 從程式碼與 XAML 使用者介面元素互動。

本快速入門會逐步解說如何建立跨平臺 Xamarin.Forms 應用程式，讓您輸入附注，並將其保存到裝置儲存體。 最終的應用程式如下所示：

[![Notes 應用程式](single-page-images/screenshots-sml.png)](single-page-images/screenshots.png#lightbox "Notes 應用程式")

::: zone pivot="windows"

### <a name="prerequisites"></a>必要條件

- 已安裝**使用 .NET 進行行動開發**工作負載的 Visual Studio 2019 (最新版本)。
- C# 知識。
- (選擇性) 在 iOS 上建置應用程式的成對 Mac。

如需這些必要條件的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

## <a name="get-started-with-visual-studio-2019"></a>開始使用 Visual Studio 2019

1. 啟動 Visual Studio 2019，然後在起始視窗中按一下 [建立新專案]**** 以建立新的專案：

    ![新增專案](single-page-images/vs/new-solution-2019.png)

2. 在 [ **建立新專案** ] **視窗中，選取 [** **專案類型** ] 下拉式清單中的 [行動裝置]，選取 [行動 **應用程式 (Xamarin.Forms) ** ] 範本，然後按 [ **下一步]** 按鈕：

    ![跨平台專案範本](single-page-images/vs/new-project-2019.png)

3. 在 [設定新專案]**** 視窗中，將 [方案名稱]**** 設定為 **Notes**，然後為專案選擇合適的位置，並按一下 [建立]**** 按鈕：

    ![設定專案](single-page-images/vs/configure-project.png)

    > [!IMPORTANT]
    > 本快速入門中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **Notes**。 當您從本快速入門將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

4. 在 [New Cross Platform App] \(新增跨平台應用程式\)**** 對話方塊中，按一下 [空白應用程式]****，然後按一下 [確定]**** 按鈕：

    ![新增跨平台應用程式](single-page-images/vs/new-app-2019.png)

    如需有關所建立之 .NET Standard 程式庫的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[ Xamarin.Forms 應用程式剖析](deepdive.md#anatomy-of-a-xamarinforms-application)。

5. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **MainPage.xaml** 將其開啟：

    ![開啟 MainPage.xaml](single-page-images/vs/open-mainpage-xaml-2019.png)

6. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Label`](xref:Xamarin.Forms.Label) 要顯示文字的、 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個實例。 這兩個 `Button` 實例會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ，並在中垂直配置 `Label` 、 `Editor` 和 `Grid` [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)的[使用者介面](deepdive.md#user-interface)。

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

7. 在 [方案總管]**** 的 **Notes** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟：

    ![開啟 MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind-2019.png)

8. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    此程式碼定義了 `_fileName` 欄位，這個欄位會參考名為 `notes.txt` 的檔案，以將附註資料儲存在應用程式的本機應用程式資料夾中。 執行頁面的函式時，會讀取檔案（如果存在的話），並顯示在中 [`Editor`](xref:Xamarin.Forms.Editor) 。 當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至檔案。 按下 [刪除]**** `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並移除 `Editor` 中的所有文字。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)。

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

### <a name="building-the-quickstart"></a>建置快速入門

1. 在 Visual Studio 中，選取 [建置] > [建置方案]**** 功能表項目 (或按下 F6)。 系統將建置解決方案，且 Visual Studio 狀態列中將會出現成功訊息：

      ![建置成功](single-page-images/vs/build-succeeded.png)

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到解決方案建置成功為止。

2. 在 Visual Studio 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 Android Emulator 中的應用程式：

    ![Visual Studio Android 工具列](single-page-images/vs/android-start.png)

    [![Android 模擬器中的 Notes](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Android 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

    > [!NOTE]
    > 只有在您有符合開發之系統需求的 [配對 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 時，才應該執行下列步驟 Xamarin.Forms 。

3. 在 Visual Studio 工具列中，以滑鼠右鍵按一下 **Notes.iOS** 專案，然後選取 [設定為啟始專案]****。

      ![將 iOS 設定為啟始專案](single-page-images/vs/set-as-startup-project-ios.png)

4. 在 Visual Studio 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 [iOS 遠端模擬器](~/tools/ios-simulator/index.md)中的應用程式：

    ![Visual Studio iOS 工具列](single-page-images/vs/ios-start.png)

    [![iOS 模擬器中的 Notes](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "iOS 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>必要條件

- 已安裝**使用 .NET 進行行動開發**工作負載的 Visual Studio 2017。
- C# 知識。
- (選擇性) 在 iOS 上建置應用程式的成對 Mac。

如需這些必要條件的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

## <a name="get-started-with-visual-studio-2017"></a>開始使用 Visual Studio 2017

1. 啟動 Visual Studio 2017，然後在起始頁面上按一下 [建立新專案]**** 以建立新的專案：

    ![新增專案](single-page-images/vs/new-solution.png)

2. 在 [ **新增專案** ] 對話方塊中，按一下 [ **跨平臺**]，選取 [行動 **應用程式] (Xamarin.Forms) ** 範本，將 [名稱] 設定為 [ **附注**]，為專案選擇適當的位置，然後按一下 [ **確定]** 按鈕：

    ![跨平台專案範本](single-page-images/vs/new-project.png)

    > [!IMPORTANT]
    > 本快速入門中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **Notes**。 當您從本快速入門將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

3. 在 [新增跨平台應用程式]**** 對話方塊中，按一下 [空白應用程式]****，並選取 [.NET Standard]**** 作為 [程式碼共用策略]，然後按一下 [確定]**** 按鈕：

    ![新增跨平台應用程式](single-page-images/vs/new-app.png)

    如需有關所建立之 .NET Standard 程式庫的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[ Xamarin.Forms 應用程式剖析](deepdive.md#anatomy-of-a-xamarinforms-application)。

4. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **MainPage.xaml** 將其開啟：

    ![開啟 MainPage.xaml](single-page-images/vs/open-mainpage-xaml.png)

5. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Label`](xref:Xamarin.Forms.Label) 要顯示文字的、 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個實例。 這兩個 `Button` 實例會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ，並在中垂直配置 `Label` 、 `Editor` 和 `Grid` [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)的[使用者介面](deepdive.md#user-interface)。

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

6. 在 [方案總管]**** 的 **Notes** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟：

    ![開啟 MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind.png)

7. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    此程式碼定義了 `_fileName` 欄位，這個欄位會參考名為 `notes.txt` 的檔案，以將附註資料儲存在應用程式的本機應用程式資料夾中。 執行頁面的函式時，會讀取檔案（如果存在的話），並顯示在中 [`Editor`](xref:Xamarin.Forms.Editor) 。 當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至檔案。 按下 [刪除]**** `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並移除 `Editor` 中的所有文字。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)。

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

### <a name="building-the-quickstart"></a>建置快速入門

1. 在 Visual Studio 中，選取 [建置] > [建置方案]**** 功能表項目 (或按下 F6)。 系統將建置解決方案，且 Visual Studio 狀態列中將會出現成功訊息：

      ![建置成功](single-page-images/vs/build-succeeded.png)

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到解決方案建置成功為止。

2. 在 Visual Studio 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 Android Emulator 中的應用程式：

    ![Visual Studio Android 工具列](single-page-images/vs/android-start.png)

    [![Android 模擬器中的 Notes](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Android 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

    > [!NOTE]
    > 只有在您有符合開發之系統需求的 [配對 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 時，才應該執行下列步驟 Xamarin.Forms 。

3. 在 Visual Studio 工具列中，以滑鼠右鍵按一下 **Notes.iOS** 專案，然後選取 [設定為啟始專案]****。

      ![將 iOS 設定為啟始專案](single-page-images/vs/set-as-startup-project-ios.png)

4. 在 Visual Studio 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 [iOS 遠端模擬器](~/tools/ios-simulator/index.md)中的應用程式：

    ![Visual Studio iOS 工具列](single-page-images/vs/ios-start.png)

    [![iOS 模擬器中的 Notes](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "iOS 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>必要條件

- 已安裝 iOS 和 Android 平台支援的 Visual Studio for Mac (最新版本)。
- Xcode (最新版本)。
- C# 知識。

如需這些必要條件的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

## <a name="get-started-with-visual-studio-for-mac"></a>Visual Studio for Mac 使用者入門

1. 啟動 Visual Studio for Mac，然後在起始視窗中按一下 [新增]**** 以建立新專案：

    ![新增方案](single-page-images/vsmac/new-project.png)

2. 在 [為新專案選擇範本]**** 對話方塊中，按一下 [多平台] > [應用程式]****、選取 [空白的 Forms App]**** 範本，然後按 [下一步]**** 按鈕：

    ![選擇範本](single-page-images/vsmac/choose-template.png)

3. 在 [設定空白的表單應用程式]**** 對話方塊中，將新的應用程式命名為 **Notes**、確保已選取 [使用 .NET Standard]**** 選項按鈕，然後按一下 [下一步]**** 按鈕：    

    ![設定 Forms 應用程式](single-page-images/vsmac/configure-app.png)

4. 在 [設定新的空白表單應用程式]**** 對話方塊中，將 [方案名稱] 和 [專案名稱] 設定為 **Notes**、選擇專案的合適位置，然後按一下 [建立]**** 按鈕以建立專案：

    ![設定 Forms 專案](single-page-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > 本快速入門中的 C# 和 XAML 程式碼片段要求將解決方案和專案全都命名為 **Notes**。 當您從本快速入門將程式碼複製到專案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立之 .NET Standard 程式庫的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[ Xamarin.Forms 應用程式剖析](deepdive.md#anatomy-of-a-xamarinforms-application)。

5. 在 [Solution Pad]**** 的 **Notes** 專案中，按兩下 **MainPage.xaml** 將其開啟：

    ![MainPage.xaml](single-page-images/vsmac/mainpage-xaml.png)

6. 在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Label`](xref:Xamarin.Forms.Label) 要顯示文字的、 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個實例。 這兩個 `Button` 實例會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ，並在中垂直配置 `Label` 、 `Editor` 和 `Grid` [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)的[使用者介面](deepdive.md#user-interface)。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

7. 在 [Solution Pad]**** 的 **Notes** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟：

    ![MainPage.xaml.cs](single-page-images/vsmac/mainpage-xaml-cs.png)

8. 在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    此程式碼定義了 `_fileName` 欄位，這個欄位會參考名為 `notes.txt` 的檔案，以將附註資料儲存在應用程式的本機應用程式資料夾中。 執行頁面的函式時，會讀取檔案（如果存在的話），並顯示在中 [`Editor`](xref:Xamarin.Forms.Editor) 。 當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至檔案。 按下 [刪除]**** `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並移除 `Editor` 中的所有文字。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms 快速入門深入探討](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

### <a name="building-the-quickstart"></a>建置快速入門

1. 在 Visual Studio for Mac 中，選取 [建置] > [全部建置]**** 功能表項目 (或按下 **&#8984; + B**)。 系統將建置專案，且 Visual Studio for Mac 工具列中將會出現成功訊息。

      ![建置成功](single-page-images/vsmac/build-successful.png)

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到專案建置成功為止。

2. 在 **Solution Pad** 中選取 **Notes.iOS** 專案，按一下滑鼠右鍵，然後選取 [設定為啟始專案]****：

      ![將 iOS 設定為啟始專案](single-page-images/vsmac/set-startup-project-ios.png)

3. 在 Visual Studio for Mac 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 iOS 模擬器內的應用程式：

      ![Visual Studio for Mac 工具列](single-page-images/vsmac/start.png)

      [![iOS 模擬器中的 Notes](single-page-images/vsmac/notes-ios.png)](single-page-images/vsmac/notes-ios-large.png#lightbox "iOS 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

4. 在 **Solution Pad** 中選取 **Notes.Droid** 專案，按一下滑鼠右鍵，然後選取 [設定為啟始專案]****：

      ![將 Android 設定為啟始專案](single-page-images/vsmac/set-startup-project-android.png)

5. 在 Visual Studio for Mac 工具列中，按下 [啟動]**** 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 Android Emulator 內的應用程式：

      [![Android 模擬器中的 Notes](single-page-images/vsmac/notes-android.png)](single-page-images/vsmac/notes-android-large.png#lightbox "Android 模擬器中的 Notes")

    輸入附註，然後按 [儲存]**** 按鈕。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launching-the-application-on-each-platform)深入探討。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 建立跨平臺 Xamarin.Forms 應用程式。
- 使用可延伸應用程式標記語言 (XAML) 定義頁面的使用者介面。
- 從程式碼與 XAML 使用者介面元素互動。

若要將此單頁應用程式轉換成多頁應用程式，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](multi-page.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)