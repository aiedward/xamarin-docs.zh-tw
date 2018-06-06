---
title: 開始使用 Xamarin.Essentials
description: Xamarin.Essentials 提供了單一跨平台 API，可搭配任何 iOS、 Android 或 UWP 應用程式可存取的共用程式碼，不論如何建立使用者介面。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f0f6eebbd12041a7be2d8e2dc00a9146b40d675f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783070"
---
# <a name="get-started-with-xamarinessentials"></a>開始使用 Xamarin.Essentials

![發行前版本的 NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials 提供了單一跨平台 API，可搭配任何 iOS、 Android 或 UWP 應用程式可存取的共用程式碼，不論如何建立使用者介面。

## <a name="platform-support"></a>平台支援

Xamarin.Essentials 支援下列平台和作業系統：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (應用程式開發介面 19) 或更高版本 |
| iOS |10.0 或更高 |
| UWP | 10.0.16299.0 或更高版本 |

## <a name="installation"></a>安裝

Xamarin.Essentials 是可以加入任何現有的或新專案，使用 Visual Studio 的 NuGet 封裝。

1. 下載並安裝[Visual Studio](http://visualstudio.com)與[for Xamarin Visual Studio tools](~/cross-platform/get-started/installation/index.md)。

2. 開啟現有的專案，或建立新的專案使用底下的空白應用程式範本**Visual Studio C#** （Android、 iPhone 和 iPad 或跨平台）。 **重要**： 如果 UWP 專案中加入請 16299 或更高版本的組建設定專案屬性中。

3. 新增**Xamarin.Essentials** NuGet 封裝加入每個專案：

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    在 [方案總管] 面板中，以滑鼠右鍵按一下方案名稱，然後選取**管理 NuGet 封裝**。 搜尋**Xamarin.Essentials**和安裝套件的**所有**包括 Android、 iOS、 UWP，和.NET 標準程式庫的專案。

    > [!TIP]
    > 請檢查**包含發行前版本**方塊時[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)處於預覽狀態。

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    在 [方案總管] 面板中，以滑鼠右鍵按一下專案名稱，然後選取**新增 > 新增 NuGet 封裝...**.搜尋**Xamarin.Essentials**和安裝套件的**所有**包括 Android、 iOS 和.NET 標準程式庫的專案。

    > [!TIP]
    > 請檢查**顯示發行前版本的封裝**方塊時[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)處於預覽狀態。

    -----

4. 在任何 C# 類別，參考的應用程式開發介面中加入 Xamarin.Essentials 的參考。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要平台專屬的設定：

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    在 Android 專案的`MainLauncher`或任何`Activity`也就是必須在初始化啟動的 Xamarin.Essentials`OnCreate`方法：

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    若要處理在 Android 上的執行階段權限，Xamarin.Essentials 必須接收任何`OnRequestPermissionsResult`。 將下列程式碼加入至所有`Activity`類別：

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

6. 請遵循[Xamarin.Essentials 指南](index.md)可讓您複製和貼上程式碼片段，針對每項功能。

## <a name="other-resources"></a>其他資源

我們建議開發人員接觸 Xamarin，請造訪[開始使用 Xamarin 開發](~/cross-platform/getting-started/index.md)。

請瀏覽[Xamarin.Essentials GitHub 儲存機制](http://github.com/xamarin/Essentials)若要查看目前的原始碼，未來接下來，執行範例，並關閉儲存機制。 社群投稿在 歡迎使用 ！

瀏覽[API 文件](xref:Xamarin.Essentials)Xamarin.Essentials 的每個功能。
