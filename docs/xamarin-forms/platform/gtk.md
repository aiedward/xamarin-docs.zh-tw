---
title: 'GTK # 平台安裝程式'
description: 'Xamarin.Forms 現在具有預覽 GTK # 平台支援'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 7f68b7c8affc11b50bdb4a2fc9589f8dcbfb45ec
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209186"
---
# <a name="gtk-platform-setup"></a>GTK # 平台安裝程式

![預覽](~/media/shared/preview.png)

Xamarin.Forms 現在具有 GTK # 應用程式的預覽支援。 GTK # 是連結 GTK + 工具組和 GNOME 文件庫中，各種不同的圖形化使用者介面工具組可讓使用單聲道和.NET 原生完全 GNONE 圖形應用程式的開發。 本文示範如何將 GTK # 專案新增至 Xamarin.Forms 方案。

啟動、 建立新的 Xamarin.Forms 方案，或使用現有的 Xamarin.Forms 方案，例如之前[ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)。

> [!NOTE]
> 本文著重於 GTK # 應用程式加入適用於 Mac 的 VS2017 和 Visual Studio 中的 Xamarin.Forms 方案，而它也可以執行在[MonoDevelop](http://www.monodevelop.com/) for Linux。

## <a name="adding-a-gtk-app"></a>加入至 GTK # 應用程式

GTK 的 # macOS 和 Linux 時一併安裝的[Mono](http://www.mono-project.com/download/stable/)。 GTK # for.NET 可安裝在與 Windows [GTK # Installer](http://www.mono-project.com/download/stable/#download-win)。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

請遵循這些指示來新增 GTK # 應用程式將在 Windows 桌面上執行：

1. 在 Visual Studio 2017，以滑鼠右鍵按一下方案名稱在**方案總管 中**選擇**新增 > 新的專案...**.

2. 在**新專案**視窗中的，在左側選取**Visual C#** 和**的傳統 Windows 桌面**。 在專案類型清單中選擇**類別庫 (.NET Framework)**，並確定**Framework**下拉式清單設為.NET Framework 4.7 最少。

3. 輸入的名稱與專案**GTK**擴充功能，例如**GameOfLife.GTK**。 按一下**瀏覽**按鈕、 選取的資料夾包含其他平台專案中，然後按**選取資料夾**。 這樣就會加入 GTK 專案與方案中的其他專案相同的目錄中。

    ![加入新的 GTK 專案](gtk-images/win/add-new-project.png "新增 GTK 專案")

    按**確定**按鈕以建立專案。

4. 在**方案總管 中**，以滑鼠右鍵按一下新 GTK 專案並選取**管理 NuGet 封裝**。 選取**瀏覽**索引標籤，並搜尋**Xamarin.Forms** 3.0 或更新。

    ![選取的 Xamarin.Forms NuGet 套件](gtk-images/win/select-forms-nuget-package.png "選取的 Xamarin.Forms NuGet 套件")

    選取的套件，然後按一下 [**安裝**] 按鈕。

5. 現在搜尋**Xamarin.Forms.Platform.GTK** 3.0 套件或更新版本。

    ![選取 Xamarin.Forms.Platform.GTK NuGet 套件](gtk-images/win/select-forms-platform-nuget-package.png "選取 Xamarin.Forms.Platform.GTK NuGet 封裝")

    選取的套件，然後按一下 [**安裝**] 按鈕。

6. 在**方案總管 中**，以滑鼠右鍵按一下方案名稱，然後選取**管理方案的 NuGet 套件**。 選取**更新** 索引標籤和**Xamarin.Forms**封裝。 選取所有專案，並更新為相同的 Xamarin.Forms 版本所用的 GTK 專案。

7. 在**方案總管 中**，以滑鼠右鍵按一下**參考**GTK 專案中。 在**參考管理員**對話方塊中，選取**專案**在左邊，並核取.NET 標準或共用專案旁邊的核取方塊：

    ![參考共用的專案](gtk-images/win/reference-shared-project.png "參考共用的專案")

8. 在**參考管理員**對話方塊中，按下**瀏覽**按鈕並瀏覽至**C:\Program Files (x86)\GtkSharp\2.12\lib**資料夾，然後選取**atk sharp.dll**， **gdk sharp.dll**，**叢林 sharp.dll**， **glib sharp.dll**， **gtk dotnet.dll**， **gtk sharp.dll**檔案。

    ![參考程式庫 GTK #](gtk-images/win/reference-gtk-libraries.png "參考 GTK # 程式庫")

    按**確定**來新增參考 按鈕。

9. 在 GTK 專案中，重新命名**Class1.cs**至**Program.cs**。

10. 在 GTK 專案中，編輯**Program.cs**檔案，讓它類似下列程式碼：

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

    此程式碼初始化 GTK # 和 Xamarin.Forms，建立應用程式視窗，並執行應用程式。

11. 在**方案總管 中**，以滑鼠右鍵按一下 GTK 專案並選取**屬性**。

12. 在**屬性**視窗中，選取**應用程式**索引標籤，然後變更**輸出類型**下拉式清單可**Windows 應用程式**。

    ![變更專案的輸出類型](gtk-images/win/change-project-output-type.png "變更專案的輸出類型")

13. 在**方案總管 中**，WPF 專案上按一下滑鼠右鍵，然後選取**設定為啟始專案**。 按 f5 鍵，在 Windows 桌面上，Visual Studio 偵錯工具執行程式：

    ![GTK # 遊戲的使用壽命到期](gtk-images/win/gtk-gameoflife.png "GTK # 遊戲的使用壽命到期")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

請遵循這些指示來新增將執行 Mac 桌面上 GTK # 應用程式：

1. 在 Visual Studio for Mac Xamarin.Forms 方案上按一下滑鼠右鍵，然後選擇 **新增 > 加入新的專案...**.

2. 在**新專案**視窗選擇**其他 >.NET > Gtk # 2.0 專案**按**下一步**。

3. 輸入的名稱與專案**GTK**擴充功能，例如**GameOfLife.GTK**，然後按**建立**。

4. 在**方案板**，以滑鼠右鍵按一下**封裝 > 新增套件...** GTK 的專案，然後將 Xamarin.Forms 3.0 發行前版本的 NuGet 套件加入或更新版本。

    ![選取的 Xamarin.Forms NuGet 套件](gtk-images/mac/select-forms-nuget-package.png "選取的 Xamarin.Forms NuGet 套件")

5. 在**方案板**，以滑鼠右鍵按一下**封裝 > 新增套件...** GTK 的專案，然後將 Xamarin.Forms.Platform.GTK 3.0 發行前版本的 NuGet 套件加入或更新版本。

    ![選取 Xamarin.Forms.Platform.GTK NuGet 套件](gtk-images/mac/select-forms-platform-nuget-package.png "選取 Xamarin.Forms.Platform.GTK NuGet 封裝")

6. 更新其他平台專案，以使用 Xamarin.Forms 版本相同，所用的 GTK 專案。

7. 在**方案板**，以滑鼠右鍵按一下**參考 > 編輯參考...** GTK 的專案，然後將參考加入 Xamarin.Forms 專案 （.NET 標準或共用專案）。

    ![參考共用的專案](gtk-images/mac/reference-shared-project.png "參考共用的專案")

8. 編輯**Program.cs** GTK 專案，讓它類似下列的程式碼檔案：

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

    此程式碼初始化 GTK # 和 Xamarin.Forms，建立應用程式視窗，並執行應用程式。

9. 在**方案板**，GTK 專案上按一下滑鼠右鍵，然後選取**設定為啟始專案**。

10. 在 Visual Studio for Mac 工具列中，按**啟動**按鈕 （類似於 [開始] 按鈕的三角形按鈕） 以啟動應用程式。

    ![GTK # 遊戲的使用壽命到期](gtk-images/mac/gtk-gameoflife.png "GTK # 遊戲的使用壽命到期")

-----

## <a name="next-steps"></a>後續步驟

### <a name="platform-specifics"></a>平台特性

您可以判斷何種平台執行 Xamarin.Forms 應用程式從 XAML 或程式碼。 這可讓您變更程式特性 GTK # 上執行時。 在程式碼中的值相比較的`Device.RuntimePlatform`與`Device.GTK`常數 （等於字串"GTK"）。 如果沒有相符項目，GTK # 上執行應用程式。

在 XAML 中，您可以使用`OnPlatform`標記加入至選取的平台專屬的屬性值：

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

您可以在啟動時設定的應用程式圖示：

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>主題

有各種不同的主題可提供 GTK # 中，而且它們可以用於從 Xamarin.Forms 應用程式：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>原生格式

原生格式可讓 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生原生專案，包括 GTK # 專案中使用的頁面。 這可藉由建立的執行個體[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，並將它轉換成原生 GTK # 型別使用`CreateContainer`擴充方法：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

如需有關原生格式的詳細資訊，請參閱[原生格式](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>問題

這是預覽，因此，您應該預期不是所有項目是生產環境就緒。 如需目前的實作狀態，請參閱[狀態](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)，以及目前的已知問題，請參閱[暫止和已知問題](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。
