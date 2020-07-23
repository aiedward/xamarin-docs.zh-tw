---
title: 建立適用于 NuGet 的新多平臺程式庫
description: 本檔說明如何建立可搭配 NuGet 使用的多平臺程式庫。 這項技術適用于可完全以 .NET 基類庫表示的商務邏輯和演算法，因此會在所有目標平臺上執行，而不需要平臺特定程式碼。
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 3226820dddbd6ecb83b87b29ef1991d19104b2a6
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936977"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>建立適用于 NuGet 的新多平臺程式庫

建立使用 PCL 或 .NET Standard 的多平臺程式庫專案，表示可以將產生的 NuGet 新增至任何支援目標設定檔的 .NET 專案，包括 ASP.NET 專案，或使用 WinForms、WPF 或 UWP 的桌面應用程式。

程式庫只能包含所選 PCL 或 .NET Standard 設定檔所支援的程式碼，以及所新增的任何其他 Nuget。
這適用于可完全在 .NET 基類庫中表示的商務邏輯和演算法。

建立單一元件，並內建在 NuGet 套件中。

如果您稍後需要平臺特定的功能，[可以加入平臺特定的專案](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>建立多平臺程式庫 NuGet 的步驟

1. 選取 [檔案] **> [新增方案**] （或以滑鼠右鍵按一下現有的方案，然後選擇 [**加入 > 新增專案**]）。

2. 從多**平臺 > 程式庫**區段選擇多**平臺程式庫**：

   [![設定單一程式碼基底的多平臺程式庫](single-codebase-images/mulitplatform-library-sml.png)](single-codebase-images/mulitplatform-library.png#lightbox)

3. 輸入 [**名稱**] 和 [**描述**]，然後**針對 [所有平臺**] 選擇 [單一]：

   [![設定單一程式碼基底的多平臺程式庫](single-codebase-images/single-configure-sml.png)](single-codebase-images/single-configure.png#lightbox)

4. 完成精靈。 解決方案中會建立單一程式庫專案。

5. 以滑鼠右鍵按一下新的程式庫專案，然後選取 [**選項**]。 [**組建 > 一般**] 區段可讓您設定**目標 Framework** –選擇 .net 便攜 PCL 設定檔或 .NET Standard 版本：

   [![選擇 [PCL] 或 [.NET Standard] 作為 [程式庫類型]](single-codebase-images/single-choose-type-sml.png)](single-codebase-images/single-choose-type.png#lightbox)

6. 此外，在 [**專案選項**] 視窗中，開啟 [ **NuGet 封裝 > 中繼資料**] 區段，並輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何選擇性的中繼資料）：

   [![輸入必要的中繼資料](single-codebase-images/single-metadata-sml.png)](single-codebase-images/single-metadata.png#lightbox)

7. 以滑鼠右鍵按一下程式庫專案，然後選擇 [**建立 NuGet 套件**] （或 [建立或部署方案]）， **nupkg** NuGet 套件檔案將會儲存在 **/bin/** 資料夾中（視設定而定，[Debug] 或 [Release]）：

   ![NuGet 套件檔案會儲存在 bin 資料夾中，視設定而定（Debug 或 Release）](single-codebase-images/create-nuget-package.png)

## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是 ZIP 檔案，因此可以檢查所產生套件的內部結構。

這個螢幕擷取畫面顯示 PCL 型 NuGet 的內容–只包含單一 PCL 元件：

![NuGet 套件中包含的檔案](single-codebase-images/nuget-output.png)

<a name="add-platforms"></a>

## <a name="adding-platform-specific-code"></a>加入平臺特定程式碼

以 PCL 為基礎的專案和以 .NET Standard 為基礎的專案不能包含平臺特定的參考（例如 iOS 或 Android 功能）。

如果現有的 PCL 專案或 .NET Standard 專案必須擴充以包含平臺特定的程式碼，您可以在專案上按一下滑鼠右鍵，然後選取 [新增] [ **> 新增平臺**]，來完成此動作：

[![[新增平臺執行] 功能表](single-codebase-images/add-later-sml.png)](single-codebase-images/add-later.png#lightbox)

一或多個平臺專案可以新增至方案，而現有的 PCL 或 .NET Standard 程式庫則可以選擇性地轉換成共用的專案：

[![新增 iOS、Android 和共用專案等平臺選項](single-codebase-images/add-later-platforms-sml.png)](single-codebase-images/add-later-platforms-sml.png#lightbox)

轉換為共用專案之後，請造訪 **> NuGet 套件 > 參考元件**] 區段中的專案選項， 
 [section](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)並確定已選取所有必要的設定檔（讓 NuGet 繼續與先前用於的專案相容）。

## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
