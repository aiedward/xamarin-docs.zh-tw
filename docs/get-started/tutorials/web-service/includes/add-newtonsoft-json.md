---
ms.openlocfilehash: aee7c7eb494af76bb450038453c19ee1ed83f2d3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388823"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio，然後建立名為 **WebServiceTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **WebServiceTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管] 中選取 **WebServiceTutorial** 專案，並以滑鼠右鍵按一下 [管理 NuGet 套件...] 來將其選取：

    ![選取新增 NuGet 套件功能表項目的螢幕擷取畫面](../images/vs/add-nuget-packages.png "新增 NuGet 套件功能表項目")

1. 在 [NuGet 套件管理員] 中選取 [瀏覽] 索引標籤，搜尋 **Newtonsoft.Json** NuGet 套件並加以選取，然後按一下 [安裝] 按鈕，將其新增至專案：

    ![NuGet 套件管理員中 Newtonsoft.Json NuGet 套件的螢幕擷取畫面](../images/vs/add-package.png "Newtonsoft.Json NuGet 套件")

    此套件用來將 JSON 還原序列化併入應用程式。

1. 建置解決方案以確定沒有任何錯誤。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac，然後建立名為 **WebServiceTutorial** 的全新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **WebServiceTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad] 中選取 **WebServiceTutorial** 專案，並以滑鼠右鍵按一下 [新增] > [新增 NuGet 套件...] 來將其選取：

    ![選取新增 NuGet 套件功能表項目的螢幕擷取畫面](../images/vsmac/add-nuget-packages.png "新增 NuGet 套件功能表項目")

1. 在 [新增套件] 視窗中，搜尋 **Newtonsoft.Json** NuGet 套件並加以選取，然後按一下 [新增套件] 按鈕，將其新增至專案：

    ![NuGet 套件管理員中 Newtonsoft.Json NuGet 套件的螢幕擷取畫面](../images/vsmac/add-package.png "Newtonsoft.Json NuGet 套件")

    此套件用來將 JSON 還原序列化併入應用程式。

1. 建置解決方案以確定沒有任何錯誤。
