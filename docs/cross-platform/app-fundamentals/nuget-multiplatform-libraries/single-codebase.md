---
title: Nuget 在建立新的多平台程式庫
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 85403ee2ab8b5c1433e0e070b3af22fa42cd5efa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Nuget 在建立新的多平台程式庫

建立的多平台程式庫專案使用 PCL 或.NET 標準表示產生 NuGet 可以將新增到任何支援的目標設定檔，包括 ASP.NET 專案或使用 WinForms、 WPF 或 UWP 的桌面應用程式的.NET 專案。

程式庫只能包含所選取的 PCL 或標準.NET 設定檔，因為會加入任何其他 NuGets 支援的程式碼。
這適用於商務邏輯和可以完全在.NET 基底類別程式庫表示的演算法。

建立單一組件，並內建的 NuGet 封裝。

如果您稍後需要特定平台功能[平台專屬專案才能加入](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>若要建立多平台程式庫 NuGet 的步驟

1. 選取**檔案 > 新的方案**(或以滑鼠右鍵按一下現有的方案，並選擇 **新增 > 新的專案**)。

2. 選擇**多平台程式庫**從**多平台 > 程式庫**> 一節：

  [![](single-codebase-images/mulitplatform-library-sml.png "設定單一程式碼基底的多平台程式庫")](single-codebase-images/mulitplatform-library.png#lightbox)

3. 輸入**名稱**和**描述**，然後選擇 **所有平台的單一**:

  [![](single-codebase-images/single-configure-sml.png "設定單一程式碼基底的多平台程式庫")](single-codebase-images/single-configure.png#lightbox)

4. 完成精靈。 在方案中建立單一程式庫專案。

5. 以滑鼠右鍵按一下新的程式庫專案，然後選取**選項**。 **建置 > 一般** 區段可讓**目標 Framework**設定 – 選擇.NET 可攜式 PCL 設定檔或標準的.NET 版本：

  [![](single-codebase-images/single-choose-type-sml.png "選擇 PCL 或.NET 標準程式庫類型")](single-codebase-images/single-choose-type.png#lightbox)

6. 此外，在**專案選項**視窗中，開啟**NuGet 封裝 > 中繼資料**區段，並輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何選擇性的中繼資料）：

  [![](single-codebase-images/single-metadata-sml.png "輸入必要的中繼資料")](single-codebase-images/single-metadata.png#lightbox)

7. 以滑鼠右鍵按一下程式庫專案，然後選擇 **建立 NuGet 封裝**（或建置或部署解決方案） 和**.nupkg** NuGet 封裝檔案會儲存在**/bin/**資料夾 （偵錯或發行，視組態而定）：

  ![](single-codebase-images/create-nuget-package.png "NuGet 封裝檔案會儲存在 bin 資料夾中偵錯或發行，視組態而定")


## <a name="verifying-the-output"></a>驗證輸出

NuGet 封裝也是套件的 ZIP 檔案，所以有可能檢查產生的內部結構。

這個螢幕擷取畫面顯示的 PCL 為基礎的 NuGet – 內容只有單一 PCL 組件包含：

![](single-codebase-images/nuget-output.png "NuGet 封裝中包含的檔案")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>加入平台專屬程式碼

PCL 為基礎的專案和.NET 標準為基礎的專案不能包含特定平台 （例如 iOS 或 android 本身功能） 的參考。

如果現有的 PCL 專案或標準.NET 專案需要擴展成包含特定平台程式碼，即可啟用此專案上按一下滑鼠右鍵，然後選取**新增 > 加入平台實作...**:

[![](single-codebase-images/add-later-sml.png "加入平台實作功能表")](single-codebase-images/add-later.png#lightbox)

一或多個平台專案中可以加入至方案中，與現有的 PCL 或.NET 標準程式庫可以選擇性地轉換成共用的專案：

[![](single-codebase-images/add-later-platforms-sml.png "加入平台的選項，例如 iOS、 Android 和共用專案")](single-codebase-images/add-later-platforms-sml.png#lightbox)

轉換為共用的專案之後, 請瀏覽**專案選項 > NuGet 封裝 > 參考組件**
[區段](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)，並確定所有必要的設定檔已選取 (以便NuGet 會繼續與先前已在中使用的專案）。


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
