---
title: 開始 Xamarin.Essentials
description: 開始使用 Xamarin.Essentials ，其提供可搭配任何 iOS、Android 或 UWP 應用程式使用的單一跨平臺 API。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 05/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8bf7b03cb7bb211d0e25e0f75e527ad4c5c1dbd2
ms.sourcegitcommit: e7a5d1ec9e50a09b3b24f4c57850a4763c3406d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101087306"
---
# <a name="get-started-with-xamarinessentials"></a>開始 Xamarin.Essentials

Xamarin.Essentials 提供單一跨平臺 API，適用于任何可從共用程式碼存取的 iOS、Android 或 UWP 應用程式，無論使用者介面的建立方式為何。 如需有關支援之作業系統的詳細資訊，請參閱[平台與功能支援指南](platform-feature-support.md)。

## <a name="installation"></a>安裝

Xamarin.Essentials 會以 NuGet 套件的形式提供，並包含在 Visual Studio 中的每個新專案。 您也可以透過下列步驟，使用 Visual Studio 將它新增至任何現有的專案。

1. 下載並安裝具有[適用於 Xamarin 的 Visual Studio 工具](~/get-started/installation/index.md)的 [Visual Studio](https://visualstudio.microsoft.com/)。

2. 開啟現有專案，或建立使用 [Visual Studio C#] (Android、iPhone 與 iPad 或跨平台) 下的空白應用程式範本建立新專案。

    > [!IMPORTANT]
    > 若新增到 UWP 專案，請確保在專案屬性中設定組建 16299 或更高版本。

3. 將 [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) NuGet 套件新增到每個專案：

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    在 [方案總管] 窗格中，以滑鼠右鍵按一下方案名稱並選取 [管理 NuGet 套件]。 搜尋 **Xamarin.Essentials** 套件，並將其安裝到 **所有** 專案中，包括 Android、iOS、UWP 和 .NET Standard 程式庫。

    # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

    在方案總管面板中，以滑鼠右鍵按一下專案名稱，然後選取 [ **新增] > 新增 NuGet 套件**...]。搜尋 **Xamarin.Essentials** 套件，並將其安裝到 **所有** 專案中，包括 Android、iOS 和 .NET Standard 程式庫。

    -----

4. Xamarin.Essentials在任何 c # 類別中新增參考，以參考 api。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要平臺特定的設定：

    # <a name="android"></a>[Android](#tab/android)

    Xamarin.Essentials 支援最低 Android 版本4.4 （對應至 API 層級19），但要編譯的目標 Android 版本必須是9.0 或10.0，對應至 API 層級28和層級29。  (在 Visual Studio 中，這兩個版本是在 android 專案的 [專案屬性] 對話方塊的 [Android 資訊清單] 索引標籤中設定。在 Visual Studio for Mac 中，它們是在 android 專案的 [專案選項] 對話方塊中，于 [Android 應用程式] 索引標籤中設定。 ) 

    針對 Android 9.0 進行編譯時， Xamarin.Essentials 會安裝所需的28.0.0.3 版支援程式庫版本。 您應用程式所需的任何其他 Xamarin 支援程式庫，也應使用 NuGet 套件管理員更新為版本28.0.0.3 版。 所有的 Xamarin. Android. 您的應用程式所使用的支援程式庫都應該相同，而且至少要有版本28.0.0.3 版。 如果您在解決方案中新增 NuGet 或更新 Nuget 時發生問題，請參閱 [ [疑難排解] 頁面](troubleshooting.md) Xamarin.Essentials 。

    在針對 Android 10.0 進行編譯時，從1.5.0 版開始，請 Xamarin.Essentials 安裝所需的 AndroidX 支援程式庫。 如果您尚未進行轉換，請參閱 [AndroidX 檔](../android/platform/androidx.md) 。

    在 Android 專案 `MainLauncher` 或任何 `Activity` 已啟動的中， Xamarin.Essentials 必須在方法中初始化 `OnCreate` ：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    若要處理 Android 上的執行時間許可權， Xamarin.Essentials 必須接收任何 `OnRequestPermissionsResult` 。 將下列程式碼新增到所 `Activity` 類別：

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

6. 遵循可讓您針對每項功能複製並貼上程式碼片段的[ Xamarin.Essentials 指南](index.md)。

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials -適用于 Mobile Apps (影片的跨平臺 Api) 

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>其他資源

我們建議剛開始使用 Xamarin 的開發人員造訪 [xamarin 開發入門](~/cross-platform/getting-started/index.md)。

請造訪[ Xamarin.Essentials GitHub 存放庫](https://github.com/xamarin/Essentials)，以查看目前的原始程式碼、接下來的內容、執行範例，以及複製存放庫。 我們非常歡迎社群參與！

流覽 [API 檔](xref:Xamarin.Essentials) 以瞭解的每項功能 Xamarin.Essentials 。
