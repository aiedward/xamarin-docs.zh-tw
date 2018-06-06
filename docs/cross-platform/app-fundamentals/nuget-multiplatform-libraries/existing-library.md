---
title: 建立從現有的程式庫專案的 NuGet
description: 本文件說明如何從現有的程式庫專案，允許程式碼可共用給其他開發人員建立的 NuGet 封裝。
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 7f407b22d1793d585ae40aeae8c2d9b7616784e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34779979"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>建立從現有的程式庫專案的 NuGet

現有的 PCL 或.NET 標準程式庫可以轉換成透過 NuGets**專案選項**視窗：

1. 以滑鼠右鍵按一下中的程式庫專案**方案板**選擇**選項**。

2. 移至**NuGet 封裝 > 中繼資料**區段，並輸入所有[必要資訊](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**一般** 索引標籤：

  [![](existing-library-images/existing-metadata-sml.png "輸入必要的中繼資料")](existing-library-images/existing-metadata.png#lightbox)

3. （選擇性）[新增其他中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**詳細資料** 索引標籤。

4. 一旦設定中繼資料，您可以在專案上按一下滑鼠右鍵並選擇**建立 NuGet 封裝**和 **.nupkg** NuGet 封裝檔案會儲存在 **/bin/** 資料夾 （偵錯或發行，組態而定）。

  ![](existing-library-images/create-nuget-package.png "以滑鼠右鍵按一下功能表選擇 建立 NuGet 封裝")

5. 在建立 NuGet 封裝_每_建置或部署，請移至**NuGet 封裝 > 建置**區段和刻度**建置專案時建立 NuGet 封裝**:

    [![](existing-library-images/existing-tickbox-sml.png "若要建立 NuGet 套件的刻度")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 建置 NuGet 封裝會減慢建置程序。 如果此方塊不的話，您可以仍然產生 NuGet 套件以手動方式隨時從 專案內容功能表 （如上述步驟 4 所示）。

## <a name="verifying-the-output"></a>驗證輸出

NuGet 封裝也是套件的 ZIP 檔案，所以有可能檢查產生的內部結構。

這個螢幕擷取畫面顯示的 PCL 為基礎的 NuGet – 內容只有單一 PCL 組件包含：

![](existing-library-images/nuget-output.png "NuGet 封裝中包含的檔案")


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
