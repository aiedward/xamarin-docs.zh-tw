---
title: 建立新的平台專屬程式庫專案的 NuGet
description: 本文件說明如何建立單一的 NuGet 套件包含多個平台的平台特定程式碼。
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 4be010448d963462ccf06c263ddfad7ba1d9feae
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832030"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>建立新的平台專屬程式庫專案的 NuGet

多平台特定的平台，例如 iOS 和 Android 為目標的程式庫專案最適合使用共用專案。

IOS 和 Android 特定程式碼，以及同時的.NET 程式碼，可以包含 NuGet。

建立多個組件，並內建於單一 NuGet 套件。 NuGet 標準能夠確保封裝，可以加入所有支援的專案類型，例如，Xamarin.iOS 和 Android 專案。

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>若要建立跨平台程式庫 NuGet 的步驟

1. 選取 **檔案 > 新的解決方案**(或以滑鼠右鍵按一下現有的方案，然後選擇**新增 > 新增專案**)。

2. 選擇**多平台程式庫**從**多平台 > 程式庫**區段：

    [![](platform-specific-images/mulitplatform-library-sml.png "設定單一程式碼基底的多平台程式庫")](platform-specific-images/multiplatform-library.png#lightbox)

3. 請輸入**名稱**並**描述**，然後選擇 **特定平台**:

    [![](platform-specific-images/specific-configure-sml.png "設定適用於 iOS 和 Android 平台專屬程式庫")](platform-specific-images/specific-configure.png#lightbox)

4. 完成精靈。 下列專案加入方案：

    - **Android 專案**– （選擇性） 可以 Android 特定程式碼加入至這個專案。
    - **iOS 專案**– iOS 特定程式碼可以選擇性地加入至這個專案。
    - **NuGet 專案**– 沒有任何程式碼加入至這個專案。 它會參考其他專案，並包含 NuGet 封裝輸出的中繼資料組態。
    - **共用專案**– 常見的程式碼應該加入至這個專案，包括平台特定程式碼內`#if`編譯器指示詞。

5. NuGet 專案上按一下滑鼠右鍵，然後選擇 **選項**，然後開啟**NuGet 封裝 > 中繼資料**區段，然後輸入[必要的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（為以及任何選擇性中繼資料）：

    [![](platform-specific-images/specific-metadata-sml.png "輸入必要的中繼資料")](platform-specific-images/specific-metadata.png#lightbox)

6. 此外，在**專案選項**視窗中，開啟**參考組件**區段，然後選擇 共用程式庫將支援透過 「 誘導轉向 」 PCL 設定檔：

    ![](platform-specific-images/specific-reference-assemblies.png "也在 [專案選項] 視窗中，開啟 [參考組件] 區段，然後選擇 共用程式庫將支援透過誘導轉向的 PCL 設定檔")

    > [!NOTE]
    > 「 誘導轉向 」 表示 PCL 組件只會包含程式庫 （它不能包含平台特定程式碼） 所公開的 API。 當 NuGet 新增至 Xamarin 專案時，共用程式庫會針對 PCL 中，編譯，但平台特有的組件包含實際採用的 iOS 或 Android 專案的程式碼。

7. 以滑鼠右鍵按一下專案，然後選擇 **建立 NuGet 套件**（或建置或部署解決方案） 和 **.nupkg** NuGet 套件檔案會儲存在 **/bin/** 資料夾 (偵錯或發行，視設定而定）。

    ![](platform-specific-images/create-nuget-package.png "NuGet 套件檔案會儲存在 bin 資料夾偵錯] 或 [版本中，視設定而定")


## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是套件的 ZIP 檔案，因此可以檢查產生的內部結構。

選取此螢幕擷取畫面顯示以特定平台的 NuGet 支援 iOS 和 Android，有兩個參考組件的內容：

![](platform-specific-images/nuget-output.png "NuGet 封裝中包含的檔案")


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
