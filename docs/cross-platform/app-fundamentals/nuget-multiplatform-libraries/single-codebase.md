---
title: 建立新的多平台程式庫的 NuGet
description: 本文件說明如何使用 NuGet 建立多平台的程式庫，才能使用。 這項技術很適合商務邏輯和可以完全在.NET 基底類別庫中表示，因此不需要平台特定程式碼的所有目標平台上執行的演算法而定。
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6371c2af15eab9c5124212eefd9cf70d07b945d4
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864724"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>建立新的多平台程式庫的 NuGet

建立多平台程式庫專案使用 PCL 或.NET Standard 表示產生的 NuGet，可以新增至任何支援的目標設定檔，包括 ASP.NET 專案或使用 WinForms、 WPF 或 UWP 的桌面應用程式的.NET 專案。

程式庫只可包含所選取的 PCL 或.NET Standard 的設定檔，以及任何其他的 Nuget，會加入支援的程式碼。
這是適用於商務邏輯和可以完全以.NET 基底類別程式庫表示的演算法。

建立單一組件，並內建於 NuGet 套件。

如果您稍後需要平台專屬功能[平台專屬專案可以加入](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>若要建立多平台程式庫 NuGet 的步驟

1. 選取 **檔案 > 新的解決方案**(或以滑鼠右鍵按一下現有的方案，然後選擇**新增 > 新增專案**)。

2. 選擇**多平台程式庫**從**多平台 > 程式庫**區段：

   [![](single-codebase-images/mulitplatform-library-sml.png "設定單一程式碼基底的多平台程式庫")](single-codebase-images/mulitplatform-library.png#lightbox)

3. 輸入**名稱**並**描述**，然後選擇**單一適用於所有平台**:

   [![](single-codebase-images/single-configure-sml.png "設定單一程式碼基底的多平台程式庫")](single-codebase-images/single-configure.png#lightbox)

4. 完成精靈。 在方案中建立單一程式庫專案。

5. 以滑鼠右鍵按一下新的程式庫專案，然後選取**選項**。 **建置 > 一般** 區段可讓**目標 Framework**設定 – 選擇.NET 可攜式 PCL 設定檔或.NET Standard 版本：

   [![](single-codebase-images/single-choose-type-sml.png "選擇 PCL 或.NET Standard 程式庫類型")](single-codebase-images/single-choose-type.png#lightbox)

6. 此外，在**專案選項**視窗中，開啟**NuGet 封裝 > 中繼資料**區段，然後輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何選擇性的中繼資料）：

   [![](single-codebase-images/single-metadata-sml.png "輸入必要的中繼資料")](single-codebase-images/single-metadata.png#lightbox)

7. 以滑鼠右鍵按一下程式庫專案，然後選擇 **建立 NuGet 套件**（或建置或部署解決方案） 和 **.nupkg** NuGet 套件檔案會儲存在 **/bin/** 資料夾 （偵錯或發行，視設定而定）：

   ![](single-codebase-images/create-nuget-package.png "NuGet 套件檔案會儲存在 bin 資料夾偵錯或發行版本視組態而定")


## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是套件的 ZIP 檔案，因此可以檢查產生的內部結構。

這個螢幕擷取畫面顯示的 PCL 型 NuGet – 內容只是單一 PCL 的組件就會包含：

![](single-codebase-images/nuget-output.png "NuGet 封裝中包含的檔案")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>加入平台特定程式碼

PCL 專案和.NET Standard 為基礎的專案不能包含特定平台 （例如 iOS 或 Android 的功能） 的參考。

如果現有的 PCL 專案或.NET Standard 專案需要擴展成包含平台特定程式碼，做法是在專案上按一下滑鼠右鍵，然後選取**新增 > 新增平台實作...** :

[![](single-codebase-images/add-later-sml.png "新增平台實作功能表")](single-codebase-images/add-later.png#lightbox)

一或多個平台專案可以加入至方案，與現有的 PCL 或.NET Standard 程式庫可以選擇性地轉換成共用的專案：

[![](single-codebase-images/add-later-platforms-sml.png "新增平台的選項，例如 iOS、 Android 及共用專案")](single-codebase-images/add-later-platforms-sml.png#lightbox)

之後將轉換為共用的專案，請瀏覽**專案選項 > NuGet 封裝 > 參考組件**
[一節](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)，並確定所有必要的設定檔已選取 (以便NuGet 會繼續與先前已在中使用的專案相容）。


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
