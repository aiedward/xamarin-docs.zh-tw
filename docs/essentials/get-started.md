---
title: 開始使用 Xamarin.Essentials
description: Xamarin.Essentials 提供單一跨平台 API，能夠搭配任何 iOS、Android 或 UWP 應用程式使用，且無論使用者介面建立的方式為何，都能從共用的程式碼進行存取。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 11/04/2018
ms.openlocfilehash: 5cb53006d7cc965f378f6e971bfc623f0c4cbd60
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899104"
---
# <a name="get-started-with-xamarinessentials"></a>開始使用 Xamarin.Essentials

Xamarin.Essentials 提供單一跨平台 API，能夠搭配任何 iOS、Android 或 UWP 應用程式使用，且無論使用者介面建立的方式為何，都能從共用的程式碼進行存取。

## <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平台與作業系統：

| Platform | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |

## <a name="installation"></a>安裝

Xamarin.Essentials 是以 NuGet 套件形式提供，可讓您使用 Visual Studio 新增到任何現有或新的專案。

1. 下載並安裝具有[適用於 Xamarin 的 Visual Studio 工具](~/cross-platform/get-started/installation/index.md)的 [Visual Studio](http://visualstudio.com)。

2. 開啟現有專案，或建立使用 [Visual Studio C#] (Android、iPhone 與 iPad 或跨平台) 下的空白應用程式範本建立新專案。 **重要**：若新增到 UWP 專案，可確保在專案屬性中設定組建 16299 或更高版本。

3. 將 **Xamarin.Essentials** NuGet 套件新增到每個專案：

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    在 [方案總管] 窗格中，以滑鼠右鍵按一下方案名稱並選取 [管理 NuGet 套件]。 搜尋 **Xamarin.Essentials** 並將套件安裝到 [全部] 專案，包括 Android、iOS、UWP 與 .NET Standard 程式庫。

    > [!TIP]
    > 當 [**Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) 為預覽狀態時，選取 [包括發行前版本] 方塊。

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    在 [方案總管] 窗格中，以滑鼠右鍵按一下方案名稱並選取 [管理 NuGet 套件]。搜尋 **Xamarin.Essentials** 並將套件安裝到 [全部] 專案，包括 Android、iOS 與 .NET Standard 程式庫。

    > [!TIP]
    > 當 [**Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) 為預覽狀態時，選取 [選定發行前版本的套件] 方塊。

    -----

4. 在任何 C# 類別中新增對 Xamarin.Essentials 的參考，以參考 API。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要平台特定設定：

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials 支援最低 Android 版本 4.4，這對應到 API 層級 19，但最高 Android 版本必須是 8.1 才能進行編譯，這對應到 API 層級 27。 (在 Visual Studio 中，這兩個版本是在 [Android 資訊清單] 索引標籤中 Android 專案的 [專案屬性] 中設定的。在 Visual Studio for Mac 中，它們是在 [Android 應用程式] 索引標籤中 [專案選項] 對話方塊中設定的。) 

    Xamarin.Essentials 會安裝它所需的 Xamarin.Android.Support 程式庫 27.0.2.1 版。 您的應用程式所需的任何其他 Xamarin.Android.Support 程式庫都應該使用 NuGet 套件管理員更新到 27.0.2.1 版。 您的應用程式所使用的所有 Xamarin.Android.Support 程式庫都應該相同，而且必須是最新的 27.0.2.1 版。 若您有關於在方案中新增 Xamarin.Essentials NuGet 或更新 NuGet 的問題，請參閱[疑難排解介面](troubleshooting.md)。

    在 Android 專案的 `MainLauncher` 或任何啟動的任何 `Activity` 中，Xamarin.Essentials都必須在 `OnCreate` 方法中初始化：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code
        //...
    ```

    若要處理 Android 上的執行階段權限，Xamarin.Essentials 必須接收任 `OnRequestPermissionsResult`。 將下列程式碼新增到所 `Activity` 類別：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    不需要進行額外設定。

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    不需要進行額外設定。

    -----

6. 依照可讓您為每個功能複製並貼上程式碼片段的 [Xamarin.Essentials 指南](index.md)執行。

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials - 適用於行動應用程式的跨平台 API (影片)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>其他資源

我們建議新的 Xamarin 開發人員瀏覽[開始使用 Xamarin 開發](~/cross-platform/getting-started/index.md)。

瀏覽 [Xamarin.Essentials GitHub 存放庫](http://github.com/xamarin/Essentials)以查看目前的原始程式碼、接下來有什麼新內容、執行範例，以及複製存放庫。 我們非常歡迎社群參與！

瀏覽 [API 文件](xref:Xamarin.Essentials)以了解 Xamarin.Essentials 的每個功能。
