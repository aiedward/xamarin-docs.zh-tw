---
title: 為 NuGet 建立新的平臺特定程式庫專案
description: 本檔說明如何建立單一 NuGet 套件，其中包含多個平臺的平臺特定程式碼。
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 73f44acad3e30e4301a69e5f2422cd4dd1a3dbf5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766567"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>為 NuGet 建立新的平臺特定程式庫專案

以特定平臺（例如 iOS 和 Android）為目標的多平臺程式庫專案，最適合與共享專案搭配使用。

NuGet 可以包含 iOS 和 Android 特有的程式碼，以及兩者通用的 .NET 程式碼。

建立多個元件，並內建于單一 NuGet 套件中。 NuGet 標準可確保將封裝新增至所有支援的專案類型，例如 Xamarin. iOS 和 Android 專案。

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>建立跨平臺程式庫 NuGet 的步驟

1. 選取 [檔案] **> [新增方案**] （或以滑鼠右鍵按一下現有的方案，然後選擇 [**加入 > 新增專案**]）。

2. 從多**平臺 > 程式庫**區段選擇多**平臺程式庫**：

    [![](platform-specific-images/mulitplatform-library-sml.png "設定單一程式碼基底的多平臺程式庫")](platform-specific-images/multiplatform-library.png#lightbox)

3. 輸入 [**名稱**] 和 [**描述**]，然後選擇 [**平臺特定**]：

    [![](platform-specific-images/specific-configure-sml.png "設定適用于 iOS 和 Android 的平臺特定程式庫")](platform-specific-images/specific-configure.png#lightbox)

4. 完成精靈。 下列專案會新增至方案：

    - **Android 專案**–可以選擇性地將 android 特定程式碼新增至這個專案。
    - **Ios 專案**–可以選擇性地將 ios 特定程式碼新增至這個專案。
    - **NuGet 專案**–此專案中不會加入任何程式碼。 它會參考其他專案，並包含 NuGet 套件輸出的中繼資料設定。
    - **共用專案**-通用程式碼應新增至這個專案，包括編譯器指示詞內`#if`的平臺特定程式碼。

5. 以滑鼠右鍵按一下 NuGet 專案，並選擇 [**選項**]，然後開啟 [ **NuGet 套件 > 中繼資料**] 區段，並輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何選擇性的中繼資料）：

    [![](platform-specific-images/specific-metadata-sml.png "輸入必要的中繼資料")](platform-specific-images/specific-metadata.png#lightbox)

6. 此外，在 [**專案選項**] 視窗中，開啟 [**參考元件**] 區段，並選擇共用程式庫會透過 "bait and SWITCH" 支援的 PCL 設定檔：

    ![](platform-specific-images/specific-reference-assemblies.png "此外，在 [專案選項] 視窗中，開啟 [參考元件] 區段，並選擇共用程式庫會透過 bait 和交換器支援的 PCL 設定檔")

    > [!NOTE]
    > 「Bait 和 switch」表示 PCL 元件只會包含程式庫所公開的 API （它不能包含平臺特定的程式碼）。 將 NuGet 新增至 Xamarin 專案時，會針對 PCL 編譯共用程式庫，但平臺特定元件則包含 iOS 或 Android 專案實際使用的程式碼。

7. 以滑鼠右鍵按一下專案，然後選擇 [**建立 Nuget 套件**] （或 [建立或部署方案]）， **nupkg** NuGet 套件檔案將會儲存在 **/bin/** 資料夾中（視設定而定）。

    ![](platform-specific-images/create-nuget-package.png "NuGet 套件檔案會儲存在 bin 資料夾中，視設定而定（Debug 或 Release）")

## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是 ZIP 檔案，因此可以檢查所產生套件的內部結構。

此螢幕擷取畫面顯示支援 iOS 和 Android 的平臺特定 NuGet 內容，並已選取兩個參考元件：

![](platform-specific-images/nuget-output.png "NuGet 套件中包含的檔案")

## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
