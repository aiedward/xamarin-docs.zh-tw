---
title: 建立新的平台特定程式庫專案的 NuGet
description: 本文件說明如何建立單一的 NuGet 封裝包含多個平台的平台專屬程式碼。
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 00a02973d6016ad63e4317279515acc2b4e2e81b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780639"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>建立新的平台特定程式庫專案的 NuGet

IOS 和 Android 等特定的平台為目標的多平台程式庫專案最適合使用共用專案中。

NuGet 可包含特定 iOS 及 Android 程式碼，以及同時.NET 程式碼。

建立多個組件和內建於單一的 NuGet 封裝。 NuGet 標準能夠確保封裝，可以加入所有支援的專案類型，例如 Xamarin.iOS 和 Android 專案。

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>若要建立跨平台程式庫 NuGet 的步驟

1. 選取**檔案 > 新的方案**(或以滑鼠右鍵按一下現有的方案，並選擇 **新增 > 新的專案**)。

2. 選擇**多平台程式庫**從**多平台 > 程式庫**> 一節：

  [![](platform-specific-images/mulitplatform-library-sml.png "設定單一程式碼基底的多平台程式庫")](platform-specific-images/multiplatform-library.png#lightbox)

3. 輸入**名稱**和**描述**，然後選擇 **特定平台**:

  [![](platform-specific-images/specific-configure-sml.png "設定適用於 iOS 和 Android 平台特定程式庫")](platform-specific-images/specific-configure.png#lightbox)

4. 完成精靈。 下列專案會加入至方案：

  - **Android 專案**– Android 專屬的程式碼可以選擇性地加入至這個專案。
  - **iOS 專案**– iOS 專屬的程式碼可以選擇性地加入至這個專案。
  - **NuGet 專案**– 此專案中加入任何程式碼。 它會參考其他專案，並包含 NuGet 封裝輸出的中繼資料設定。
  - **共用專案**– 常見的程式碼應該加入至這個專案，包括平台專屬程式碼內`#if`編譯器指示詞。

5. NuGet 的專案上按一下滑鼠右鍵，然後選擇 **選項**，然後開啟**NuGet 封裝 > 中繼資料**區段，並輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（為以及任何選擇性中繼資料）：

  [![](platform-specific-images/specific-metadata-sml.png "輸入必要的中繼資料")](platform-specific-images/specific-metadata.png#lightbox)

6. 此外，在**專案選項**視窗中，開啟**參考組件**區段，然後選擇 共用的程式庫將透過上鉤 and 的交換器 」 支援哪些 PCL 設定檔：

  ![](platform-specific-images/specific-reference-assemblies.png "也在專案選項] 視窗中，開啟 [參考組件] 區段，然後選擇 [PCL 共用程式庫將透過上鉤和交換器支援的設定檔")

  > [!NOTE]
> 上鉤 and 的交換器 」 表示 PCL 組件只會包含程式庫 （不能包含平台專屬的程式碼） 所公開的 API。 當 NuGet 加入 Xamarin 專案中時，將編譯共用程式庫，針對此 PCL，但這些特定平台組件包含實際使用 iOS 或 Android 專案的程式碼。

7. 以滑鼠右鍵按一下專案，然後選擇 **建立 NuGet 封裝**（或建置或部署解決方案） 和 **.nupkg** NuGet 封裝檔案會儲存在 **/bin/** 資料夾 (偵錯或發行，視組態而定）。

  ![](platform-specific-images/create-nuget-package.png "NuGet 封裝檔案會儲存在 bin 資料夾中偵錯或發行，視組態而定")


## <a name="verifying-the-output"></a>驗證輸出

NuGet 封裝也是套件的 ZIP 檔案，所以有可能檢查產生的內部結構。

選取此螢幕擷取畫面顯示特定平台的 NuGet 支援 iOS 和 Android，且有兩個參考組件的內容：

![](platform-specific-images/nuget-output.png "NuGet 封裝中包含的檔案")


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
