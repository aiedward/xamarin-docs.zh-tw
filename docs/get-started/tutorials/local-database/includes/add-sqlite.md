---
ms.openlocfilehash: 876bfc3b1abe04c61aad025f08178f766195f200
ms.sourcegitcommit: 1550019cd1e858d4d13a4ae6dfb4a5947702f24b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897801"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

若要完成此教學課程，您應該有 Visual Studio 2019 (最新版本)，並已安裝 [使用 .NET 進行行動開發] 工作負載。 此外，您還需要配對的 Mac 才能在 iOS 上建置教學課程應用程式。 如需安裝 Xamarin 平台的相關資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 啟動 Visual Studio，然後建立名為 **LocalDatabaseTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **LocalDatabaseTutorial** 。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 中選取 [LocalDatabaseTutorial] 專案，並以滑鼠右鍵按一下 [管理 NuGet 套件...] 來將其選取：

    ![螢幕擷取畫面：已選取 [管理 NuGet 套件] 功能表項目](../images/vs/add-nuget-packages.png "[新增 NuGet 套件] 功能表項目")

1. 在 [NuGet 套件管理員] 中選取 [瀏覽] 索引標籤，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [安裝] 按鈕，將其新增至專案：

    ![螢幕擷取畫面：NuGet 套件管理員中的 SQLite.NET NuGet 套件](../images/vs/add-package.png "SQLite.NET NuGet 套件")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **擁有者：** praeclarum
    > - **作者：** SQLite-net
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

1. 建置解決方案以確定沒有任何錯誤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教學課程，您應該具備 Visual Studio for Mac (最新版本)，並已安裝 iOS 和 Android 平台支援。 此外，您還需要 Xcode (最新版本)。 如需安裝 Xamarin 平台的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

1. 啟動 Visual Studio for Mac，然後建立名為 **LocalDatabaseTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段會要求將解決方案命名為 **LocalDatabaseTutorial** 。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 中選取 [LocalDatabaseTutorial] 專案，並以滑鼠右鍵按一下 [新增] > [新增 NuGet 套件...] 來將其選取：

    ![螢幕擷取畫面：已選取 [新增 NuGet 套件] 功能表項目](../images/vsmac/add-nuget-packages.png "[新增 NuGet 套件] 功能表項目")

1. 在 [新增套件] 視窗中，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [新增套件] 按鈕，將其新增至專案：

    ![螢幕擷取畫面：NuGet 套件管理員中的 SQLite.NET NuGet 套件](../images/vsmac/add-package.png "SQLite.NET NuGet 套件")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **擁有者：** praeclarum
    > - **作者：** SQLite-net
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

1. 建置解決方案以確定沒有任何錯誤。
