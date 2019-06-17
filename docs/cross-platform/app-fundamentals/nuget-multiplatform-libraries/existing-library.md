---
title: 建立從現有的程式庫專案的 NuGet
description: 本文件說明如何從現有的程式庫專案，讓其他開發人員與共用程式碼建立 NuGet 套件。
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 7f407b22d1793d585ae40aeae8c2d9b7616784e6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267789"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>建立從現有的程式庫專案的 NuGet

現有的 PCL 或.NET Standard 程式庫可以轉換成透過 Nuget**專案選項**視窗：

1. 中的程式庫專案上按一下滑鼠右鍵**Solution Pad** ，然後選擇**選項**。

2. 移至**NuGet 封裝 > 中繼資料**區段，然後輸入所有[必要資訊](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**一般** 索引標籤：

  [![](existing-library-images/existing-metadata-sml.png "輸入必要的中繼資料")](existing-library-images/existing-metadata.png#lightbox)

3. （選擇性）[新增額外的中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**詳細資料** 索引標籤。

4. 一旦設定中繼資料，您可以以滑鼠右鍵按一下專案，並選擇**建立 NuGet 套件**並 **.nupkg** NuGet 套件檔案會儲存在 **/bin/** 資料夾 （偵錯或發行，取決於組態）。

  ![](existing-library-images/create-nuget-package.png "從快顯功能表中選擇 建立 NuGet 套件")

5. 上建立 NuGet 套件_每隔_建置或部署，請前往**NuGet 套件 > 建置**一節和刻度**建置專案時建立 NuGet 套件**:

    [![](existing-library-images/existing-tickbox-sml.png "若要建立 NuGet 套件的刻度")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 建立 NuGet 封裝變慢建置程序。 如果不勾選此方塊，您可以仍然產生 NuGet 套件以手動方式隨時從專案操作功能表 （如上述步驟 4 所示）。

## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是套件的 ZIP 檔案，因此可以檢查產生的內部結構。

這個螢幕擷取畫面顯示的 PCL 型 NuGet – 內容只是單一 PCL 的組件就會包含：

![](existing-library-images/nuget-output.png "NuGet 封裝中包含的檔案")


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
