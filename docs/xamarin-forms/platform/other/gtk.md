---
title: 'GTK # 平臺設定'
description: 'Xamarin.Forms 現在提供 GTK # 平臺的預覽支援'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: acb3bc77aa19b1d90d73122e1b92ffb1fea953ca
ms.sourcegitcommit: a0de974875f8fa1a29f7abc990137246789ad85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100630224"
---
# <a name="gtk-platform-setup"></a>GTK # 平臺設定

:::image type="icon" source="~/media/shared/preview.png" border="false":::

Xamarin.Forms 現在提供 GTK # 應用程式的預覽支援。 GTK # 是一個圖形化使用者介面工具組，可連結 GTK + 工具組和各種 GNOME 程式庫，讓您可以使用 Mono 和 .NET 來開發完全原生的 GNOME 圖形應用程式。 本文示範如何將 GTK # 專案新增至 Xamarin.Forms 方案。

> [!IMPORTANT]
> Xamarin.Forms 此社區提供 GTK # 的支援。 如需詳細資訊，請參閱[ Xamarin.Forms 平臺支援](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)。

開始之前，請建立新的 Xamarin.Forms 方案，或使用現有的 Xamarin.Forms 解決方案，例如 [**GameOfLife**](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)。

> [!NOTE]
> 雖然本文著重于將 GTK # 應用程式新增至 Xamarin.Forms VS2017 和 Visual Studio for Mac 中的解決方案，但也可以在適用于 Linux 的 [MonoDevelop](https://www.monodevelop.com/) 中執行。

## <a name="adding-a-gtk-app"></a>新增 GTK # 應用程式

適用于 macOS 和 Linux 的 GTK # 會安裝為 [Mono](https://www.mono-project.com/download/stable/)的一部分。 您可以使用 [gtk # 安裝程式](https://www.mono-project.com/download/stable/#download-win)，在 Windows 上安裝適用于 .NET 的 gtk #。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

請遵循下列指示來新增將在 Windows 桌面上執行的 GTK # 應用程式：

1. 在 Visual Studio 2019 中，以滑鼠右鍵按一下 **方案總管** 中的方案名稱，然後選擇 [ **加入 > 新專案 ...**]。

2. 在 [ **新增專案** ] 視窗中，從左邊選取 [ **Visual c #** ] 和 [ **Windows 傳統桌面**]。 在專案類型清單中，選擇 [ **類別庫 ( .NET Framework)**，並確定 [ **Framework** ] 下拉式清單設定為最小值 .NET Framework 4.7。

3. 輸入具有 **GTK** 副檔名之專案的名稱，例如 **GameOfLife。** 按一下 [ **流覽]** 按鈕，選取包含其他平臺專案的資料夾，然後按 [ **選取資料夾**]。 這會將 GTK 專案放置於方案中的其他專案所在的相同目錄中。

    ![加入新的 GTK 專案](gtk-images/win/add-new-project.png "加入新的 GTK 專案")

    按下 [ **確定]** 按鈕以建立專案。

4. 在 **方案總管** 中，以滑鼠右鍵按一下新的 GTK 專案，然後選取 [ **管理 NuGet 套件**]。 選取 [ **流覽** ] 索引標籤，然後搜尋 **Xamarin.Forms** 3.0 或更新版本。

    ![選取：：：非 loc (Xamarin. Forms) ：：： NuGet 套件](gtk-images/win/select-forms-nuget-package.png "選取：：：非 loc (Xamarin. Forms) ：：： NuGet 套件")

    選取封裝，然後按一下 [ **安裝** ] 按鈕。

5. 現在搜尋 **Xamarin.Forms 。平臺 GTK** 3.0 封裝或更高版本。

    ![選取：：：非 loc (Xamarin. Forms) ：：：。Platform （GTK） NuGet 套件](gtk-images/win/select-forms-platform-nuget-package.png "選取：：：非 loc (Xamarin. Forms) ：：：。Platform （GTK） NuGet 套件")

    選取封裝，然後按一下 [ **安裝** ] 按鈕。

6. 在 **方案總管** 中，以滑鼠右鍵按一下方案名稱，然後選取 [ **管理方案的 NuGet 套件**]。 選取 [ **更新** ] 索引標籤和 **Xamarin.Forms** 套件。 選取所有專案，並將其更新為 Xamarin.Forms GTK 專案所使用的相同版本。

7. 在 **方案總管** 中，以滑鼠右鍵按一下 GTK 專案中的 **參考** 。 在 [ **參考管理員** ] 對話方塊中，選取左側的 [ **專案** ]，然後核取 .NET Standard 或共用專案旁邊的核取方塊：

    ![參考共用專案](gtk-images/win/reference-shared-project.png "參考共用專案")

8. 在 [ **參考管理員** ] 對話方塊中，按下 [ **流覽]** 按鈕，並流覽至 **C:\Program 檔案 (x86) \gtksharp\2.12\lib** 資料夾，然後選取 **atk-sharp.dll**、 **gdk-sharp.dll**、 **glade-sharp.dll**、 **glib-sharp.dll**、 **gtk-dotnet.dll** **gtk-sharp.dll檔** 。

    ![參考 GTK # 程式庫](gtk-images/win/reference-gtk-libraries.png "參考 GTK # 程式庫")

    按下 [ **確定]** 按鈕以新增參考。

9. 在 GTK 專案中，將 **Class1.cs** 重新命名為 **Program.cs**。

10. 在 GTK 專案中，編輯 **Program.cs** 檔案，使其類似于下列程式碼：

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

    此程式碼會初始化 GTK #，並 Xamarin.Forms 建立應用程式視窗，然後執行應用程式。

11. 在 **方案總管** 中，以滑鼠右鍵按一下 GTK 專案，然後選取 [ **屬性**]。

12. 在 [ **屬性** ] 視窗中選取 [ **應用程式** ] 索引標籤，並將 [ **輸出類型** ] 下拉式清單變更為 [ **Windows 應用程式**]。

    ![變更專案輸出類型](gtk-images/win/change-project-output-type.png "變更專案輸出類型")

13. 在 **方案總管** 中，以滑鼠右鍵按一下 GTK 專案，然後選取 [ **設定為啟始專案**]。 按 F5 以在 Windows 桌面上使用 Visual Studio 偵錯工具來執行程式：

    ![GTK # 遊戲生命](gtk-images/win/gtk-gameoflife.png "GTK # 遊戲生命")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

請遵循下列指示來新增將在 Mac 桌面上執行的 GTK # 應用程式：

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 Xamarin.Forms 方案，然後選擇 [ **加入] > [加入新專案**]。

2. 在 [**新增專案**] 視窗中，選擇 [**其他 > .net > Gtk # 2.0] 專案**，然後按 **[下一步]**

3. 輸入具有 **GTK** 副檔名之專案的名稱，例如 **GameOfLife**，然後按 [ **建立**]。

4. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下 [ **封裝] > [新增封裝** ]，然後新增 Xamarin.Forms 3.0 發行前版 NuGet 封裝或更高版本。

    ![選取：：：非 loc (Xamarin. Forms) ：：： NuGet 套件](gtk-images/mac/select-forms-nuget-package.png "選取：：：非 loc (Xamarin. Forms) ：：： NuGet 套件")

5. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下 [ **封裝] > [新增封裝** ]，然後加入 Xamarin.Forms 。平臺 GTK 3.0 預先發行 NuGet 套件或更高版本。

    ![選取：：：非 loc (Xamarin. Forms) ：：：。Platform （GTK） NuGet 套件](gtk-images/mac/select-forms-platform-nuget-package.png "選取：：：非 loc (Xamarin. Forms) ：：：。Platform （GTK） NuGet 套件")

6. 更新其他平臺專案，以使用 Xamarin.Forms GTK 專案所使用的相同版本。

7. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下 [ **參考] > [編輯參考** ] 的參考，然後加入專案的參考 Xamarin.Forms (.NET Standard 或共用專案) 。

    ![參考共用專案](gtk-images/mac/reference-shared-project.png "參考共用專案")

8. 編輯 GTK 專案的 **Program.cs** 檔，使其類似于下列程式碼：

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

    此程式碼會初始化 GTK #，並 Xamarin.Forms 建立應用程式視窗，然後執行應用程式。

9. 在 **Solution Pad** 中，以滑鼠右鍵按一下 GTK 專案，然後選取 [ **設定為啟始專案**]。

10. 在 [Visual Studio for Mac] 工具列中，按下 [ **開始** ] 按鈕 (類似于 [播放] 按鈕的三角形按鈕) 啟動應用程式。

    ![GTK # 遊戲生命](gtk-images/mac/gtk-gameoflife.png "GTK # 遊戲生命")

-----

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以 Xamarin.Forms 從 XAML 或程式碼判斷您的應用程式執行所在的平臺。 這可讓您在 GTK # 上執行時變更程式特性。 在程式碼中，將的值 `Device.RuntimePlatform` 與 `Device.GTK` 常數 (（等於字串 "GTK" ) ）進行比較。 如果有相符的，應用程式就會在 GTK # 上執行。

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

### <a name="themes"></a>佈景主題

GTK # 有各種可用的主題，而且可以從 Xamarin.Forms 應用程式使用：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>原生表單

原生表單允許 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 原生專案使用衍生的頁面，包括 GTK # 專案。 這可以藉由建立 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生頁面的實例，並使用擴充方法將它轉換成原生 GTK # 型別來達成 `CreateContainer` ：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

如需原生表單的詳細資訊，請參閱 [原生表單](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>問題

這是預覽功能，因此您應該預期並非所有專案都已就緒。 如需目前的執行狀態，請參閱 [狀態](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)，若要瞭解目前的已知問題，請參閱暫止的 [& 已知問題](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。