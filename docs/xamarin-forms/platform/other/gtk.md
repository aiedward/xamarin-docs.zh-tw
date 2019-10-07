---
title: 'GTK # 平臺設定'
description: 'Xamarin 現在具有 GTK # 平臺的預覽支援'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: d20e27fdbb1f62c2aebb7122d7f0c042b3ac9085
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997184"
---
# <a name="gtk-platform-setup"></a>GTK # 平臺設定

![預覽](~/media/shared/preview.png)

Xamarin 現在具有 GTK # 應用程式的預覽支援。 GTK # 是一種圖形化使用者介面工具組，可連結 GTK + 工具組和各種 GNOME 程式庫，允許使用 Mono 和 .NET 開發完全原生的 GNOME 圖形應用程式。 本文示範如何將 GTK # 專案加入至 Xamarin 表單方案。

> [!IMPORTANT]
> 由社區提供 GTK # 的表單支援。 如需詳細資訊，請參閱[Xamarin. 表單平臺支援](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

開始之前，請先建立新的 Xamarin 表單方案，或使用現有的 Xamarin 表單方案，例如[**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)。

> [!NOTE]
> 雖然本文著重于將 GTK # 應用程式新增至 VS2017 和 Visual Studio for Mac 中的 Xamarin 表單方案，但也可以在適用于 Linux 的[MonoDevelop](http://www.monodevelop.com/)中執行。

## <a name="adding-a-gtk-app"></a>新增 GTK # 應用程式

適用于 macOS 和 Linux 的 GTK # 會安裝為[Mono](https://www.mono-project.com/download/stable/)的一部分。 GTK # for .NET 可以使用[gtk # 安裝程式](https://www.mono-project.com/download/stable/#download-win)安裝在 Windows 上。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

請遵循這些指示來新增將在 Windows 桌面上執行的 GTK # 應用程式：

1. 在 Visual Studio 2019 中，以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選擇 [**加入] > [新增專案**]。

2. 在 [**新增專案**] 視窗中，選取左側的 [**視覺效果C#** 和**Windows 傳統桌面**]。 在專案類型清單中，選擇 [**類別庫（.NET Framework）** ]，並確定 [ **Framework** ] 下拉式清單已設定為最小值 .NET Framework 4.7。

3. 輸入具有**GTK**延伸模組之專案的名稱，例如**GameOfLife。** 按一下 [**流覽]** 按鈕，選取包含其他平臺專案的資料夾，然後按 [**選取資料夾**]。 這會將 GTK 專案放在與方案中的其他專案相同的目錄中。

    ![加入新的 gtk 專案](gtk-images/win/add-new-project.png "加入新的 gtk 專案")

    按下 [**確定]** 按鈕以建立專案。

4. 在 **方案總管**中，以滑鼠右鍵按一下新的 GTK 專案，然後選取 **管理 NuGet 套件**。 選取 [**流覽**] 索引標籤，然後搜尋 [ **Xamarin. 表單**3.0] 或更新版本。

    ![選取 [xamarin] nuget 套件](gtk-images/win/select-forms-nuget-package.png "選取 [xamarin] nuget 套件")

    選取套件，然後按一下 [**安裝**] 按鈕。

5. 現在**搜尋 [node.js** 3.0 套件] 或更新版本。

    ![選取 [Node.js nuget 套件]]，(gtk-images/win/select-forms-platform-nuget-package.png "選取 [node.js] nuget 套件")

    選取套件，然後按一下 [**安裝**] 按鈕。

6. 在 **方案總管**中，以滑鼠右鍵按一下方案名稱，然後選取 **管理方案的 NuGet 套件**。 選取 [**更新**] 索引標籤和 [ **Xamarin. 表單**] 套件。 選取所有專案，並將其更新為 GTK 專案所使用的相同 Xamarin. Forms 版本。

7. 在 **方案總管**中，以滑鼠右鍵按一下 GTK 專案中的 **參考**。 在 [**參考管理員**] 對話方塊中，選取左側的 [**專案**]，然後核取 [.NET Standard] 或 [共用] 專案旁的核取方塊：

    ![參考]共用專案(gtk-images/win/reference-shared-project.png "參考共用專案")

8. 在 [**參考管理員**] 對話方塊中，按下 [**流覽]** 按鈕並流覽至 [ **C:\Program Files （x86） \GtkSharp\2.12\lib** ] 資料夾，然後選取 [ **atk-sharp**]、[ **gdk-sharp**]、[ **glade-sharp .dll**]、 **glib-sharp .dll**、 **gtk-dotnet**、 **gtk-sharp .dll**檔案。

    ![引用]gtk # 程式庫(gtk-images/win/reference-gtk-libraries.png "參考 gtk #")程式庫

    按下 [**確定]** 按鈕以新增參考。

9. 在 GTK 專案中，將**Class1.cs**重新命名為**Program.cs**。

10. 在 GTK 專案中，編輯**Program.cs**檔案，使其類似下列程式碼：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此程式碼會初始化 GTK # 和 Xamarin，並建立應用程式視窗，並執行應用程式。

11. 在 **方案總管**中，以滑鼠右鍵按一下 GTK 專案，然後選取 **屬性**。

12. 在 [**屬性**] 視窗中，選取 [**應用程式**] 索引標籤，然後將 [**輸出類型**] 下拉式變更為 [ **Windows 應用程式**]。

    ![變更專案輸出類型](gtk-images/win/change-project-output-type.png "變更專案輸出類型")

13. 在 **方案總管**中，以滑鼠右鍵按一下 GTK 專案，然後選取 **設定為啟始專案**。 按 F5 鍵，以在 Windows 桌面上使用 Visual Studio 偵錯工具來執行程式：

    ![Gtk # 遊戲-生命](gtk-images/win/gtk-gameoflife.png "gtk #")遊戲

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

請遵循這些指示來新增將在 Mac 桌面上執行的 GTK # 應用程式：

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [Xamarin] 方案，然後選擇 [新增] **> [加入新專案**]。

2. 在 **新增專案** 視窗中，選擇 **其他 > .net > Gtk # 2.0 專案**，然後按**下一步**。

3. 輸入具有**GTK**延伸模組的專案名稱，例如**GameOfLife**，然後按 [**建立**]。

4. 在 [ **Solution Pad**中，以滑鼠右鍵按一下 [封裝] > 為 GTK 專案**新增套件 ...** ]，然後新增 [Xamarin 3.0 發行前版本 NuGet 套件] 或更新版本。

    ![選取 [xamarin] nuget 套件](gtk-images/mac/select-forms-nuget-package.png "選取 [xamarin] nuget 套件")

5. 在 [ **Solution Pad**中，以滑鼠右鍵按一下 [封裝] > 為 GTK 專案**新增封裝 ...** ]，然後新增 [node.js 3.0 發行前版本的 NuGet 套件] 或更新版本。

    ![選取 [Node.js nuget 套件]]，(gtk-images/mac/select-forms-platform-nuget-package.png "選取 [node.js] nuget 套件")

6. 更新其他平臺專案，以使用 GTK 專案所使用的相同 Xamarin. Forms 版本。

7. 在 [ **Solution Pad**中，以滑鼠右鍵按一下 GTK 專案 **> 編輯參考 ...** ] 的 [參考]，然後加入 [Xamarin] 專案的參考（[.NET Standard] 或 [共用專案]）。

    ![參考]共用專案(gtk-images/mac/reference-shared-project.png "參考共用專案")

8. 編輯 GTK 專案的**Program.cs**檔，使它類似下列程式碼：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此程式碼會初始化 GTK # 和 Xamarin，並建立應用程式視窗，並執行應用程式。

9. 在  **Solution Pad**中，以滑鼠右鍵按一下 GTK 專案，然後選取 **設定為啟始專案**。

10. 在 [Visual Studio for Mac] 工具列中，按下 [**啟動**] 按鈕（類似于 [播放] 按鈕的三角形按鈕），以啟動應用程式。

    ![Gtk # 遊戲-生命](gtk-images/mac/gtk-gameoflife.png "gtk #")遊戲

-----

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以從 XAML 或程式碼判斷您的 Xamarin Forms 應用程式執行所在的平臺。 這可讓您在 GTK # 上執行程式特性時，加以變更。 在程式碼中，將 `Device.RuntimePlatform` 的值與 `Device.GTK` 常數（等於字串 "GTK"）進行比較。 如果有相符的，應用程式就會在 GTK # 上執行。

在 XAML 中，您可以使用 `OnPlatform` 標記來選取平臺特定的屬性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>應用程式圖示

您可以在啟動時設定應用程式圖示：

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>主題

GTK # 有各種可用的主題，而且可以從 Xamarin 應用程式中使用：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>原生表單

原生表單可讓 Xamarin [@no__t 1](xref:Xamarin.Forms.ContentPage)-衍生的頁面由原生專案使用，包括 GTK # 專案。 這可以藉由使用 `CreateContainer` 擴充方法來建立[@no__t 1](xref:Xamarin.Forms.ContentPage)衍生頁面的實例，並將它轉換成原生 GTK # 類型來完成：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

如需原生表單的詳細資訊，請參閱[原生表單](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>問題

這是預覽版，因此您應該預期並非所有專案都是生產環境就緒。 如需目前的執行狀態，請參閱[狀態](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)，如需目前的已知問題，請參閱[擱置 & 的已知問題](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。
