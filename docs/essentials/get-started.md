---
title: 開始使用Xamarin.Essentials
description: Xamarin.Essentials提供單一跨平臺 API，可搭配任何 iOS、Android 或 UWP 應用程式使用，無論使用者介面建立的方式為何，都能從共用的程式碼存取。
ms.assetid: ''
author: ''
ms.author: ''
ms.custom: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c0052b35475522ffb3634ebe22f69f66fe3b22b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129307"
---
# <a name="get-started-with-xamarinessentials"></a>開始使用Xamarin.Essentials

Xamarin.Essentials提供單一跨平臺 API，可搭配任何 iOS、Android 或 UWP 應用程式使用，無論使用者介面建立的方式為何，都能從共用的程式碼存取。 如需有關支援之作業系統的詳細資訊，請參閱[平台與功能支援指南](platform-feature-support.md)。

## <a name="installation"></a>安裝

Xamarin.Essentials是以 NuGet 套件的形式提供，並包含在 Visual Studio 中的每個新專案中。 您也可以使用下列步驟，將它新增至任何現有的 Visual Studio。

1. 下載並安裝具有[適用於 Xamarin 的 Visual Studio 工具](~/get-started/installation/index.md)的 [Visual Studio](https://visualstudio.microsoft.com/)。

2. 開啟現有專案，或建立使用 [Visual Studio C#]**** (Android、iPhone 與 iPad 或跨平台) 下的空白應用程式範本建立新專案。

    > [!IMPORTANT]
    > 若新增到 UWP 專案，請確保在專案屬性中設定組建 16299 或更高版本。

3. 將 [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) NuGet 套件新增至每個專案：

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    在 [方案總管] 窗格中，以滑鼠右鍵按一下方案名稱並選取 [管理 NuGet 套件]****。 搜尋 **Xamarin.Essentials** 套件並將其安裝至**所有**專案，包括 Android、iOS、UWP 和 .NET Standard 程式庫。

    # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

    在 [方案總管] 面板中，以滑鼠右鍵按一下專案名稱，然後選取 [**新增] > 新增 NuGet 套件**...]。搜尋 **Xamarin.Essentials** 套件並將其安裝至**所有**專案，包括 Android、iOS 和 .NET Standard 程式庫。

    -----

4. Xamarin.Essentials在任何 c # 類別中新增的參考，以參考 api。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials需要平臺特定安裝程式：

    # <a name="android"></a>[Android](#tab/android)

    Xamarin.Essentials支援4.4 的最低 Android 版本（對應至 API 層級19），但要編譯的目標 Android 版本必須是9.0 或10.0，其對應于 API 層級28和層級29。 （在 Visual Studio 中，這兩個版本是在 android 專案的 [專案屬性] 對話方塊中，于 [Android 資訊清單] 索引標籤中設定。在 Visual Studio for Mac 中，它們是在 Android 專案的 [專案選項] 對話方塊中，于 [Android 應用程式] 索引標籤中設定。）

    針對 Android 9.0 進行編譯時， Xamarin.Essentials 會安裝所需的支援程式庫版本28.0.0.3。 您的應用程式所需的任何其他支援程式庫，也應該更新為使用 NuGet 套件管理員的版本28.0.0.3。 您的應用程式所使用的所有 Xamarin. 支援程式庫都應該相同，而且至少應為版本28.0.0.3。 如果您在解決方案中新增 NuGet 或更新 Nuget 時遇到問題，請參閱[疑難排解頁面](troubleshooting.md) Xamarin.Essentials 。

    從版本1.5.0 開始針對 Android 10.0 進行編譯時，請 Xamarin.Essentials 安裝所需的 AndroidX 支援程式庫。 如果您尚未進行轉換，請閱讀[AndroidX 檔](https://docs.microsoft.com/xamarin/android/platform/androidx)。

    在 Android 專案的 `MainLauncher` 或任何 `Activity` 已啟動的中， Xamarin.Essentials 必須在方法中初始化 `OnCreate` ：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    若要在 Android 上處理執行時間許可權， Xamarin.Essentials 必須接收任何 `OnRequestPermissionsResult` 。 將下列程式碼新增到所 `Activity` 類別：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    不需要進行額外設定。

    # <a name="uwp"></a>[UWP](#tab/uwp)

    不需要進行額外設定。

    -----

6. 遵循可讓您針對每項功能複製及貼上程式碼片段的[ Xamarin.Essentials 指南](index.md)。

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials-適用于 Mobile Apps 的跨平臺 Api （影片）

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>其他資源

我們建議新的 Xamarin 開發人員流覽[開始使用 xamarin 開發](~/cross-platform/getting-started/index.md)。

請造訪[ Xamarin.Essentials GitHub 存放庫](https://github.com/xamarin/Essentials)以查看目前的原始程式碼、接下來的程式碼、執行範例，以及複製存放庫。 我們非常歡迎社群參與！

流覽[API 檔](xref:Xamarin.Essentials)以瞭解的每項功能 Xamarin.Essentials 。
