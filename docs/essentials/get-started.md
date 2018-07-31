---
title: 開始使用 Xamarin.Essentials
description: Xamarin.Essentials 提供單一的跨平台 API，適用於任何 iOS、 Android 或 UWP 應用程式，您可以從存取共用程式碼，不論如何建立使用者介面。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c72c1c66a465075770ce739270cb4b1f2c6fba7a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353772"
---
# <a name="get-started-with-xamarinessentials"></a>開始使用 Xamarin.Essentials

![發行前版本的 NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials 提供單一的跨平台 API，適用於任何 iOS、 Android 或 UWP 應用程式，您可以從存取共用程式碼，不論如何建立使用者介面。

## <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平台和作業系統：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |

## <a name="installation"></a>安裝

Xamarin.Essentials 是可以加入至任何現有或新專案使用 Visual Studio 以 NuGet 套件形式提供。

1. 下載並安裝[Visual Studio](http://visualstudio.com)具有[適用於 Xamarin 的 Visual Studio 工具](~/cross-platform/get-started/installation/index.md)。

2. 開啟現有的專案，或建立新的專案使用底下的空白應用程式範本**Visual Studio C#** （Android、 iPhone 和 iPad 或跨平台）。 **重要**： 如果將加入 UWP 專案確定組建 16299 或更新版本已設定專案屬性中。

3. 新增**Xamarin.Essentials**到每個專案的 NuGet 套件：

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    在方案總管 窗格中，以滑鼠右鍵按一下方案名稱，然後選取**管理 NuGet 套件**。 搜尋**Xamarin.Essentials**並安裝此封裝**所有**包括 Android、 iOS、 UWP 和.NET Standard 程式庫的專案。

    > [!TIP]
    > 請檢查**包括發行前版本**方塊時[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)處於預覽狀態。

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    在方案總管 窗格中，以滑鼠右鍵按一下專案名稱，然後選取**新增 > 新增 NuGet 套件...**.搜尋**Xamarin.Essentials**並安裝此封裝**所有**包括 Android、 iOS 和.NET Standard 程式庫的專案。

    > [!TIP]
    > 請檢查**顯示發行前版本套件**方塊時[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)處於預覽狀態。

    -----

4. 若要參考 Api 任何 C# 類別中新增 Xamarin.Essentials 的參考。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要平台專屬的設定：

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials 支援 4.4，API level 19，相對應的最低 Android 版本，但目標 Android 版本編譯的 8.1，必須對應至 API 層級 27。 （在 Visual Studio 中，下列兩個版本是針對 Android 專案的 [Android 資訊清單索引標籤中的 [專案屬性] 對話方塊中設定。在 Visual Studio for Mac 中，設定 Android 應用程式] 索引標籤中的 Android 專案的 [專案選項] 對話方塊中。） 
    
    Xamarin.Essentials 安裝版本 27.0.2.1 它需要的 Xamarin.Android.Support 程式庫。 應用程式所需的任何其他 Xamarin.Android.Support 程式庫也應該更新為版本 27.0.2.1 使用 NuGet 套件管理員。 應用程式所使用的所有 Xamarin.Android.Support 程式庫應該相同，而且應該至少版本 27.0.2.1。 請參閱[疑難排解頁面](troubleshooting.md)如果您有新增 Xamarin.Essentials NuGet 或更新方案中的 Nuget 問題。

    在 Android 專案的`MainLauncher`或任何`Activity`也就是必須在初始化啟動的 Xamarin.Essentials`OnCreate`方法：

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    若要處理在 Android 上的執行階段權限，Xamarin.Essentials 必須收到任何`OnRequestPermissionsResult`。 將下列程式碼新增至所有`Activity`類別：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    不需要其他設定。

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    不需要其他設定。

    -----

6. 請遵循[Xamarin.Essentials 指南](index.md)，可讓您複製並貼上的每個功能的程式碼片段。

## <a name="other-resources"></a>其他資源

我們建議開發人員新手 Xamarin，請造訪[Getting Started with Xamarin 開發](~/cross-platform/getting-started/index.md)。

請瀏覽[Xamarin.Essentials GitHub 存放庫](http://github.com/xamarin/Essentials)若要查看目前的原始碼，未來接下來，執行範例，並關閉存放庫。 社群投稿都是 歡迎使用 ！

瀏覽[API 文件](xref:Xamarin.Essentials)Xamarin.Essentials 的每個功能。
