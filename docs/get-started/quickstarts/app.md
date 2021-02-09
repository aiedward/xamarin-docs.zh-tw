---
title: 建立 Xamarin.Forms 應用程式快速入門
description: 本文說明如何建立 Xamarin.Forms Shell 應用程式，以提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 359F5012-4409-42A7-BEDF-C1DB9AF86DED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d21daa89322021e80a46753c864da3cb671b2d6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974272"
---
# <a name="create-a-xamarinforms-application-quickstart"></a>建立 Xamarin.Forms 應用程式快速入門

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)

在本快速入門中，您將了解如何：

- 建立 Xamarin.Forms Shell 應用程式。
- 使用可延伸應用程式標記語言 (XAML) 來定義頁面的使用者介面，並從程式碼與 XAML 元素互動。
- 藉由子類別化類別，描述 Shell 應用程式的視覺化階層 `Shell` 。

本快速入門會逐步解說如何建立跨平臺 Xamarin.Forms Shell 應用程式，讓您輸入附注並將它保存到裝置儲存體。 最終的應用程式如下所示：

[ ![ 附注應用程式](app-images/screenshots1-sml.png)](app-images/screenshots1.png#lightbox) 
 [ ![ 注意事項頁面](app-images/screenshots2-sml.png)](app-images/screenshots2.png#lightbox)

::: zone pivot="windows"

### <a name="prerequisites"></a>必要條件

- 已安裝 **使用 .NET 進行行動開發** 工作負載的 Visual Studio 2019 (最新版本)。
- C# 知識。
- (選擇性) 在 iOS 上建置應用程式的成對 Mac。

如需這些必要條件的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

## <a name="get-started-with-visual-studio-2019"></a>開始使用 Visual Studio 2019

1. 啟動 Visual Studio 2019，然後在起始視窗中按一下 [建立新專案] 以建立新的專案：

    ![新增方案](app-images/vs/new-solution.png)

2. 在 [ **建立新專案** ] **視窗中，選取 [** **專案類型** ] 下拉式清單中的 [行動裝置]，選取 [行動 **應用程式 (Xamarin.Forms)** ] 範本，然後按 [ **下一步]** 按鈕：

    ![選擇範本](app-images/vs/new-project.png)

3. 在 [設定新專案] 視窗中，將 [方案名稱] 設定為 **Notes**，然後為專案選擇合適的位置，並按一下 [建立] 按鈕：   

    ![設定 Shell 應用程式](app-images/vs/configure-project.png)

    > [!IMPORTANT]
    > 本快速入門中的 C# 和 XAML 程式碼片段要求將解決方案和專案全都命名為 **Notes**。 當您從本快速入門將程式碼複製到專案時，使用不同的名稱會導致建置錯誤。

4. 在 [ **新的行動應用程式** ] 對話方塊中，選取索引標籤 **式** 範本，然後按一下 [ **建立** ] 按鈕：

    ![建立 Shell 應用程式](app-images/vs/create-project.png)

    建立專案之後，請關閉 **GettingStarted.txt** 檔案。

    如需有關所建立之 .NET Standard 程式庫的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門深入探討](deepdive.md) [ Xamarin.Forms shell 應用程式的剖析](deepdive.md#anatomy-of-a-xamarinforms-application)。

5. 在 **方案總管** 的 **Notes** 專案中，刪除下列資料夾 (和其內容) ：

    - **模型**
    - **服務**
    - **ViewModels**
    - **檢視**

6. 在 **方案總管** 的 **Notes** 專案中，刪除 **GettingStarted.txt**。

7. 在 **方案總管** 的 **Notes** 專案中，加入名為 **Views** 的新資料夾。

8. 在 **方案總管** 的 **Notes** 專案中，選取 [ **Views** ] 資料夾，按一下滑鼠右鍵，然後選取 [ **加入 > 新專案**...]。在 [ **加入新專案** ] 對話方塊中，選取 [ **Visual c # 專案] > Xamarin.Forms > 內容] 頁面**，將新的檔案命名為 **>notespage.xaml.cs**，然後按一下 [ **新增** ] 按鈕：

    ![新增 >notespage.xaml.cs](app-images/vs/add-notespage.png)

    這會將名為 **>notespage.xaml.cs** 的新頁面新增至 **Views** 資料夾。 此頁面將會是應用程式中的主頁面。

9. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **>notespage.xaml.cs** ] 將它開啟：

    ![開啟 >notespage.xaml.cs .xaml](app-images/vs/open-notespage-xaml.png)

10. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個物件。 這兩個 `Button` 物件會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ， `Editor` 並在 `Grid` 中垂直配置和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    按 **CTRL + S**，將變更儲存至 **>notespage.xaml.cs。**  

12. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **NotesPage.xaml.cs** ] 將它開啟：

    ![開啟 NotesPage.xaml.cs](app-images/vs/open-notespage-codebehind.png)

12. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    此程式碼定義了 `_fileName` 欄位，這個欄位會參考名為 `notes.txt` 的檔案，以將附註資料儲存在應用程式的本機應用程式資料夾中。 執行頁面的函式時，會讀取檔案（如果存在的話），並顯示在中 [`Editor`](xref:Xamarin.Forms.Editor) 。 當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至檔案。 按下 [刪除] `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並移除 `Editor` 中的所有文字。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)深入探討。

    按 **CTRL + S**，將變更儲存至 **NotesPage.xaml.cs** 。    

13. 在 **方案總管** 的 **Notes** 專案中，選取 [ **Views** ] 資料夾，按一下滑鼠右鍵，然後選取 [ **加入 > 新專案**...]。在 [ **加入新專案** ] 對話方塊中，選取 [ **Visual c # 專案] > Xamarin.Forms > 內容] 頁面**，將新的檔案命名為 **>aboutpage**，然後按一下 [ **新增** ] 按鈕：

    ![新增 >aboutpage](app-images/vs/add-aboutpage.png)

    這會將名為 **>aboutpage** 的新頁面新增至 **Views** 資料夾。

14. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **>aboutpage** ] 將它開啟：

    ![開啟 >aboutpage .xaml](app-images/vs/open-aboutpage-xaml.png)

15. 在 **>aboutpage** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Image`](xref:Xamarin.Forms.Image) 兩個 [`Label`](xref:Xamarin.Forms.Label) 顯示文字的物件，以及 [`Button`](xref:Xamarin.Forms.Button) 。 在中，這兩個 `Label` 物件 `Button` 會垂直配置，並且在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `Image` `StackLayout` 中垂直配置 [`Grid`](xref:Xamarin.Forms.Grid) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    按 **CTRL + S**，將變更儲存至 **>aboutpage。**    

16. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **AboutPage.xaml.cs** ] 將它開啟：

    ![開啟 AboutPage.xaml.cs](app-images/vs/open-aboutpage-codebehind.png)

17. 在 **AboutPage.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    此 `OnButtonClicked` 程式碼會定義事件處理常式，當按下 [ **深入瞭解**] 時，就會執行此事件處理常式 [`Button`](xref:Xamarin.Forms.Button) 。 按下按鈕時，就會啟動網頁瀏覽器，並顯示以 URI 引數代表方法的頁面 `OpenAsync` 。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)深入探討。

    按 **CTRL + S**，將變更儲存至 **AboutPage.xaml.cs** 。

18. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **AppShell** ] 將它開啟：

    ![開啟 AppShell .xaml](app-images/vs/open-appshell-xaml.png)

19. 在 **AppShell** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    這段程式碼會以宣告方式定義應用程式的視覺化階層，其中包含 `TabBar` 兩個 `ShellContent` 物件。 這些物件不代表任何使用者介面元素，而是應用程式視覺化階層的組織。 Shell 將會採用這些物件，並產生內容的使用者介面。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    按 **CTRL + S**，將變更儲存至 **AppShell。**    

20. 在 **方案總管** 的 **Notes** 專案中，展開 [ **AppShell**]，然後按兩下 [ **AppShell.xaml.cs** ] 將它開啟：

    ![開啟 AppShell.xaml.cs](app-images/vs/open-appshell-codebehind.png)

21. 在 **AppShell.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    按 **CTRL + S**，將變更儲存至 **AppShell.xaml.cs** 。

22. 在 **方案總管** 的 **Notes** 專案中，按兩下 [ **app.xaml** ] 以開啟它：

    ![開啟應用程式 .xaml](app-images/vs/open-app-xaml.png)

23. 在 **app.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    這段程式碼會以宣告方式定義 `App` 類別，此類別負責具現化應用程式。

    按 **CTRL + S**，將變更儲存到 **app.xaml** 。    

24. 在 **方案總管** 的 **Notes** 專案中，展開 **app.xaml**，然後按兩下 **App.xaml.cs** 將它開啟：

    ![開啟 App.xaml.cs](app-images/vs/open-app-codebehind.png)

25. 在 **App.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    此 `App` 程式碼會定義負責具現化應用程式之類別的程式碼後端。 它會將屬性初始化為子類別化的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) `Shell` 物件。

    按 **CTRL + S**，將變更儲存至 **App.xaml.cs** 。    

### <a name="building-the-quickstart"></a>建置快速入門

1. 在 Visual Studio 中，選取 [建置] > [建置方案] 功能表項目 (或按下 F6)。 系統將建置解決方案，且 Visual Studio 狀態列中將會出現成功訊息：

      ![建置成功](app-images/vs/build-successful.png)

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到專案建置成功為止。

2. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 Android Emulator 中的應用程式：

      ![Visual Studio Android 工具列](app-images/vs/android-start.png)

      ![Android 模擬器中的 Notes](app-images/vs/notes1-android.png)

    輸入附註，然後按 [儲存] 按鈕。 然後，關閉應用程式並重新啟動，以確保重載您輸入的附注。

    按 [ **關於** ] 索引標籤圖示，以流覽至 `AboutPage` ：

      ![Android Emulator 中頁面的附注](app-images/vs/notes2-android.png)

    按 [ **深入瞭解** ] 按鈕以啟動 [快速入門] 網頁。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launch-the-application-on-each-platform)深入探討。

    > [!NOTE]
    > 只有在您有符合開發之系統需求的 [配對 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 時，才應該執行下列步驟 Xamarin.Forms 。    

3. 在 Visual Studio 工具列中，以滑鼠右鍵按一下 **Notes.iOS** 專案，然後選取 [設定為啟始專案]。

      ![將記事設定為啟始專案](app-images/vs/set-startup-project-ios.png)

4. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 [iOS 遠端模擬器](~/tools/ios-simulator/index.md)中的應用程式：

      ![Visual Studio iOS 工具列](app-images/vs/ios-start.png)

      [![iOS 模擬器中的 Notes](app-images/vs/notes1-ios.png)](app-images/vs/notes1-ios-large.png#lightbox)

    輸入附註，然後按 [儲存] 按鈕。 然後，關閉應用程式並重新啟動，以確保重載您輸入的附注。

    按 [ **關於** ] 索引標籤圖示，以流覽至 `AboutPage` ：

      [![IOS 模擬器中頁面的相關附注](app-images/vs/notes2-ios.png)](app-images/vs/notes2-ios-large.png#lightbox)

    按 [ **深入瞭解** ] 按鈕以啟動 [快速入門] 網頁。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launch-the-application-on-each-platform)深入探討。

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>必要條件

- 已安裝 iOS 和 Android 平台支援的 Visual Studio for Mac (最新版本)。
- Xcode (最新版本)。
- C# 知識。

如需這些必要條件的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

## <a name="get-started-with-visual-studio-for-mac"></a>Visual Studio for Mac 使用者入門

1. 啟動 Visual Studio for Mac，然後在起始視窗中按一下 [新增] 以建立新專案：

    ![新增方案](app-images/vsmac/new-project.png)

2. 在 [ **為新專案選擇範本** ] 對話方塊中，按一下 [多 **平臺 > 應用程式**]，選取 [ **Shell 表單應用程式** ] 範本，然後按 [ **下一步]** 按鈕：

    ![選擇範本](app-images/vsmac/choose-template.png)

3. 在 [ **設定 Shell Forms 應用程式** ] 對話方塊中，為新 **的應用程式** 命名，然後按 [ **下一步]** 按鈕：    

    ![設定 Shell 應用程式](app-images/vsmac/configure-app.png)

4. 在 [ **設定您的新 Shell 表單應用程式** ] 對話方塊中，將 [方案] 和 [專案名稱] 設定為 [ **備註**]，為專案選擇適當的位置，然後按一下 [ **建立** ] 按鈕以建立專案：

    ![設定 Shell 專案](app-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > 本快速入門中的 C# 和 XAML 程式碼片段要求將解決方案和專案全都命名為 **Notes**。 當您從本快速入門將程式碼複製到專案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立之 .NET Standard 程式庫的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門深入探討](deepdive.md) [ Xamarin.Forms shell 應用程式的剖析](deepdive.md#anatomy-of-a-xamarinforms-application)。

5. 在 **Solution Pad** 的 **Notes** 專案中，刪除下列資料夾 (和其內容) ：

    - **模型**
    - **服務**
    - **ViewModels**
    - **檢視**

6. 在 **Solution Pad** 的 **Notes** 專案中，刪除 **GettingStarted.txt**。

7. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，加入名為 **Views** 的新資料夾。

8. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，選取 [ **Views** ] 資料夾，按一下滑鼠右鍵，然後選取 [ **加入 > 新** 檔案 ...]。在 [ **新增** 檔案] 對話方塊中，選取 **表單 > form ContentPage XAML**、將新檔案命名為 **>notespage.xaml.cs**，然後按一下 [ **新增** ] 按鈕：

    ![新增 >notespage.xaml.cs](app-images/vsmac/add-notespage.png)

    這會將名為 **>notespage.xaml.cs** 的新頁面新增至 **Views** 資料夾。 此頁面將會是應用程式中的主頁面。

9. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，按兩下 [ **>notespage.xaml.cs** ] 將它開啟：

    ![開啟 >notespage.xaml.cs .xaml](app-images/vsmac/open-notespage-xaml.png)

10. 在 **NotesPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Layout children vertically -->
        <StackLayout Margin="20">
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Editor`](xref:Xamarin.Forms.Editor) 用於文字輸入的，以及指示 [`Button`](xref:Xamarin.Forms.Button) 應用程式儲存或刪除檔案的兩個物件。 這兩個 `Button` 物件會以水準方式配置在中 [`Grid`](xref:Xamarin.Forms.Grid) ， `Editor` 並在 `Grid` 中垂直配置和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 >notespage.xaml.cs 的變更 **。**

12. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 [ **NotesPage.xaml.cs** ] 將它開啟：

    ![開啟 NotesPage.xaml.cs](app-images/vsmac/open-notespage-codebehind.png)

12. 在 **NotesPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class NotesPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public NotesPage()
            {
                InitializeComponent();

                // Read the file.
                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                // Save the file.
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                // Delete the file.
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    此程式碼定義了 `_fileName` 欄位，這個欄位會參考名為 `notes.txt` 的檔案，以將附註資料儲存在應用程式的本機應用程式資料夾中。 執行頁面的函式時，會讀取檔案（如果存在的話），並顯示在中 [`Editor`](xref:Xamarin.Forms.Editor) 。 當按下 [ **儲存**] 時 [`Button`](xref:Xamarin.Forms.Button) `OnSaveButtonClicked` ，就會執行事件處理常式，這會將的內容儲存 `Editor` 至檔案。 按下 [刪除] `Button` 即會執行 `OnDeleteButtonClicked` 事件處理常式，這會刪除檔案 (若檔案存在)，並移除 `Editor` 中的所有文字。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)深入探討。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NotesPage.xaml.cs** 的變更。

13. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，選取 [ **Views** ] 資料夾，按一下滑鼠右鍵，然後選取 [ **加入 > 新** 檔案 ...]。在 [ **新增** 檔案] 對話方塊中，選取 **表單 > form ContentPage XAML**、將新檔案命名為 **>aboutpage**，然後按一下 [ **新增** ] 按鈕：

    ![新增 >aboutpage](app-images/vsmac/add-aboutpage.png)

14. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，按兩下 [ **>aboutpage** ] 將它開啟：

    ![開啟 >aboutpage .xaml](app-images/vsmac/open-aboutpage-xaml.png)

    這會將名為 **>aboutpage** 的新頁面新增至 **Views** 資料夾。

15. 在 **>aboutpage** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{OnPlatform iOS=LightSlateGray, Android=#2196F3}"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="20"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    這段程式碼會以宣告方式定義頁面的使用者介面，其中包含 [`Image`](xref:Xamarin.Forms.Image) 兩個 [`Label`](xref:Xamarin.Forms.Label) 顯示文字的物件，以及 [`Button`](xref:Xamarin.Forms.Button) 。 在中，這兩個 `Label` 物件 `Button` 會垂直配置，並且在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `Image` `StackLayout` 中垂直配置 [`Grid`](xref:Xamarin.Forms.Grid) 。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 >aboutpage 的變更 **。**

16. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 [ **AboutPage.xaml.cs** ] 將它開啟：

    ![開啟 AboutPage.xaml.cs](app-images/vsmac/open-aboutpage-codebehind.png)

17. 在 **AboutPage.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using System;
    using Xamarin.Essentials;
    using Xamarin.Forms;

    namespace Notes.Views
    {
        public partial class AboutPage : ContentPage
        {
            public AboutPage()
            {
                InitializeComponent();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                // Launch the specified URL in the system browser.
                await Launcher.OpenAsync("https://aka.ms/xamarin-quickstart");
            }
        }
    }
    ```

    此 `OnButtonClicked` 程式碼會定義事件處理常式，當按下 [ **深入瞭解**] 時，就會執行此事件處理常式 [`Button`](xref:Xamarin.Forms.Button) 。 按下按鈕時，就會啟動網頁瀏覽器，並顯示以 URI 引數代表方法的頁面 `OpenAsync` 。 如需使用者互動的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[回應使用者互動](deepdive.md#responding-to-user-interaction)深入探討。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **AboutPage.xaml.cs** 的變更。

18. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，按兩下 [ **AppShell** ] 將它開啟：

    ![開啟 AppShell .xaml](app-images/vsmac/open-appshell-xaml.png)

19. 在 **AppShell** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">
        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```    

    這段程式碼會以宣告方式定義應用程式的視覺化階層，其中包含 `TabBar` 兩個 `ShellContent` 物件。 這些物件不代表任何使用者介面元素，而是應用程式視覺化階層的組織。 Shell 將會採用這些物件，並產生內容的使用者介面。 如需建立使用者介面的詳細資訊，請參閱[ Xamarin.Forms Shell 快速入門](deepdive.md)中的[使用者介面](deepdive.md#user-interface)深入探討。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 AppShell 的變更 **。**

20. 在 **Solution Pad** 的 **Notes** 專案中，展開 [ **AppShell**]，然後按兩下 [ **AppShell.xaml.cs** ] 將它開啟：

    ![開啟 AppShell.xaml.cs](app-images/vsmac/open-appshell-codebehind.png)

21. 在 **AppShell.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
            }
        }
    }
    ```

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **AppShell.xaml.cs** 的變更。

22. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 [ **app.xaml** ] 以開啟它：

    ![開啟應用程式 .xaml](app-images/vsmac/open-app-xaml.png)

23. 在 **app.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

    </Application>
    ```

    這段程式碼會以宣告方式定義 `App` 類別，此類別負責具現化應用程式。

    選擇 [檔案] **> 儲存** (或按 **&#8984; + S**) ，以將變更儲存到 **app.xaml。**

24. 在 [**附注**] 專案的 [ **Solution Pad**] 中，展開 [ **app.xaml**]，然後按兩下 **App.xaml.cs** 開啟它：

    ![開啟 App.xaml.cs](app-images/vsmac/open-app-codebehind.png)

25. 在 **App.xaml.cs** 中，移除所有範本程式碼，並將它取代為下列程式碼：

    ```csharp
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {

            public App()
            {
                InitializeComponent();
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    此 `App` 程式碼會定義負責具現化應用程式之類別的程式碼後端。 它會將屬性初始化為子類別化的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) `Shell` 物件。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **App.xaml.cs** 的變更。

### <a name="building-the-quickstart"></a>建置快速入門

1. 在 Visual Studio for Mac 中，選取 [建置] > [全部建置] 功能表項目 (或按下 **&#8984; + B**)。 專案將會建立，且 Visual Studio for Mac 工具列中會出現成功訊息：

      ![建置成功](app-images/vsmac/build-successful.png)

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到專案建置成功為止。

2. 在 **Solution Pad** 中選取 **Notes.iOS** 專案，按一下滑鼠右鍵，然後選取 [設定為啟始專案]：

      ![將 iOS 設定為啟始專案](app-images/vsmac/set-startup-project-ios.png)

3. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 iOS 模擬器內的應用程式：

      ![Visual Studio for Mac 工具列](app-images/vsmac/start.png)

      ![iOS 模擬器中的 Notes](app-images/vsmac/notes1-ios.png)

    輸入附註，然後按 [儲存] 按鈕。 然後，關閉應用程式並重新啟動，以確保重載您輸入的附注。

    按 [ **關於** ] 索引標籤圖示，以流覽至 `AboutPage` ：

      ![IOS 模擬器中頁面的相關附注](app-images/vsmac/notes2-ios.png)

    按 [ **深入瞭解** ] 按鈕以啟動 [快速入門] 網頁。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launch-the-application-on-each-platform)深入探討。

4. 在 **Solution Pad** 中選取 **Notes.Droid** 專案，按一下滑鼠右鍵，然後選取 [設定為啟始專案]：

      ![將 Android 設定為啟始專案](app-images/vsmac/set-startup-project-android.png)

5. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選擇 Android Emulator 內的應用程式：

      ![Android 模擬器中的 Notes](app-images/vsmac/notes1-android.png)

    輸入附註，然後按 [儲存] 按鈕。 然後，關閉應用程式並重新啟動，以確保重載您輸入的附注。

    按 [ **關於** ] 索引標籤圖示，以流覽至 `AboutPage` ：

      ![Android Emulator 中頁面的附注](app-images/vsmac/notes2-android.png)

    按 [ **深入瞭解** ] 按鈕以啟動 [快速入門] 網頁。

    如需有關如何在每個平臺上啟動應用程式的詳細資訊，請參閱[ Xamarin.Forms 快速入門](deepdive.md)中的在[每個平臺上啟動應用程式](deepdive.md#launch-the-application-on-each-platform)深入探討。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 建立 Xamarin.Forms Shell 應用程式。
- 使用可延伸應用程式標記語言 (XAML) 來定義頁面的使用者介面，並從程式碼與 XAML 元素互動。
- 藉由子類別化類別，描述 Shell 應用程式的視覺化階層 `Shell` 。

繼續進行下一個快速入門，將其他頁面新增至此 Xamarin.Forms Shell 應用程式。

> [!div class="nextstepaction"]
> [下一步](navigation.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/)
- [Xamarin.Forms Shell 快速入門深入探討](deepdive.md)
