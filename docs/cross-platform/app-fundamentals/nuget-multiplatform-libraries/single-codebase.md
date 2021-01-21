---
title: 為 NuGet 建立新的多平臺程式庫
description: 本檔說明如何建立可搭配 NuGet 使用的多平臺程式庫。 這項技術適用于可完全以 .NET 基類庫表示的商務邏輯和演算法，因此不需要平臺特定程式碼，就能在所有目標平臺上執行。
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: ca874149874c420801c839fcde7afac11f46c5e1
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628913"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>為 NuGet 建立新的多平臺程式庫

建立使用 PCL 或 .NET Standard 的多平臺程式庫專案，表示產生的 NuGet 可以新增至任何支援目標設定檔的 .NET 專案，包括 ASP.NET 專案或使用 WinForms、WPF 或 UWP 的桌面應用程式。

程式庫只能包含所選 PCL 或 .NET Standard 設定檔所支援的程式碼，以及任何其他已加入的 Nuget。
這適用于可完全以 .NET 基類庫表示的商務邏輯和演算法。

建立單一元件並內建于 NuGet 套件中。

如果您稍後需要平臺特定功能，則 [可以新增平臺專屬專案](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>建立多平臺程式庫 NuGet 的步驟

1. 選取 [檔案] **> [新增方案** ] (或以滑鼠右鍵按一下現有的方案，然後選擇 [ **加入 > 新專案** ]) 。

2. 從 [多 **平臺 > 程式庫**] 區段選擇 [多 **平臺程式庫**]：

   [![螢幕擷取畫面顯示 [選擇已選取多平臺程式庫的範本]。](single-codebase-images/mulitplatform-library-sml.png)](single-codebase-images/mulitplatform-library.png#lightbox)

3. 輸入 **名稱** 和 **描述**，然後 **為所有平臺** 選擇 [單一]：

   [![螢幕擷取畫面：顯示為 [名稱]、[描述] 和 [執行] 輸入的值。](single-codebase-images/single-configure-sml.png)](single-codebase-images/single-configure.png#lightbox)

4. 完成精靈。 方案中會建立單一程式庫專案。

5. 以滑鼠右鍵按一下新的程式庫專案，然後選取 [ **選項**]。 **組建 > 一般** 區段可讓您設定 **目標 Framework** -選擇 .net 便攜 PCL 設定檔或 .NET Standard 版本：

   [![選擇適用于程式庫類型的 PCL 或 .NET Standard](single-codebase-images/single-choose-type-sml.png)](single-codebase-images/single-choose-type.png#lightbox)

6. 此外，在 [ **專案選項** ] 視窗中，開啟 [ **NuGet 封裝 > 中繼資料** ] 區段，並輸入 [必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (以及任何選用的中繼資料) ：

   [![輸入必要的中繼資料](single-codebase-images/single-metadata-sml.png)](single-codebase-images/single-metadata.png#lightbox)

7. 以滑鼠右鍵按一下 [程式庫] 專案，然後選擇 [ **建立 NuGet 套件** (] 或 [建立或部署方案) ]， **nupkg** NuGet 套件檔案將會儲存在 **/bin/** 資料夾中， ([Debug] 或 [發行]，視 configuration) 而定：

   ![NuGet 套件檔案將會儲存在 [bin] 資料夾中，視設定而定](single-codebase-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是 ZIP 檔案，因此可以檢查所產生封裝的內部結構。

此螢幕擷取畫面顯示以 PCL 為基礎之 NuGet 的內容-只包含單一 PCL 元件：

![NuGet 套件中包含的檔案](single-codebase-images/nuget-output.png)

<a name="add-platforms"></a>

## <a name="adding-platform-specific-code"></a>新增 Platform-Specific 程式碼

以 PCL 為基礎的專案和以 .NET Standard 為基礎的專案不能包含特定平臺的參考 (例如 iOS 或 Android 功能) 。

如果現有的 PCL 專案或 .NET Standard 專案必須展開以包含平臺特定程式碼，則可以用滑鼠右鍵按一下專案，然後選取 [ **新增 > 新增平臺**]，來完成此動作：

[![新增平臺執行功能表](single-codebase-images/add-later-sml.png)](single-codebase-images/add-later.png#lightbox)

您可以將一或多個平臺專案加入至方案，而且可以選擇性地將現有的 PCL 或 .NET Standard 程式庫轉換成共用的專案：

[![新增平臺選項，例如 iOS、Android 和共用專案](single-codebase-images/add-later-platforms-sml.png)](single-codebase-images/add-later-platforms-sml.png#lightbox)

轉換成共用的專案之後，請流覽 **專案選項 > NuGet 套件 > 參考元件**] 
 [區段](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)，並確定已選取任何所需的設定檔 (讓 NuGet 繼續與先前在) 中使用的專案相容。

## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
