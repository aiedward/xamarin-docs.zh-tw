---
title: 從現有的程式庫專案建立 NuGet
description: 本檔說明如何從現有的程式庫專案建立 NuGet 封裝，讓程式碼可與其他開發人員共用。
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: conceptdev
ms.author: crdun
ms.date: 04/20/2017
ms.openlocfilehash: f9d49fc4bff91939c9924dc42a11ef31ffd87362
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289229"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>從現有的程式庫專案建立 NuGet

您可以透過 [**專案選項**] 視窗，將現有的 PCL 或 .NET Standard 程式庫轉換成 nuget：

1. 以滑鼠右鍵按一下  **Solution Pad**中的 程式庫 專案，然後選擇 **選項**。

2. 移至 [ **NuGet 套件 > 中繼資料**] 區段，並在 [**一般**] 索引標籤中輸入所有[必要的資訊](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)：

   [![](existing-library-images/existing-metadata-sml.png "輸入必要的中繼資料")](existing-library-images/existing-metadata.png#lightbox)

3. （選擇性）在 [**詳細**資料] 索引標籤中[新增其他中繼資料](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)。

4. 設定中繼資料之後，您可以在專案上按一下滑鼠右鍵，然後選擇 [**建立 Nuget 套件**]， **nupkg** NuGet 套件檔案將會儲存在 **/bin/** 資料夾中（視設定而定）（Debug 或 Release）。

   ![](existing-library-images/create-nuget-package.png "從右鍵功能表選擇 [建立 NuGet 套件]")

5. 若要在_每個_組建或部署上建立 nuget 套件，請移至**Nuget 套件 > 組建** 區段，並**在建立專案時**勾選 建立 NuGet 套件：

    [![](existing-library-images/existing-tickbox-sml.png "建立 NuGet 套件的滴答")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 建立 NuGet 套件可能會使組建程式變慢。 如果未核取此方塊，您仍然可以隨時從專案內容功能表（如上述步驟4所示）手動產生 NuGet 套件。

## <a name="verifying-the-output"></a>驗證輸出

NuGet 套件也是 ZIP 檔案，因此可以檢查所產生套件的內部結構。

這個螢幕擷取畫面顯示 PCL 型 NuGet 的內容–只包含單一 PCL 元件：

![](existing-library-images/nuget-output.png "NuGet 套件中包含的檔案")


## <a name="related-links"></a>相關連結

- [中繼資料指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
