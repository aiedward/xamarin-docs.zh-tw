---
title: NuGet 多平台程式庫專案 (也稱為 Nugetizer 3000)
description: 本文件說明如何使用 Nugetizer 3000 工具會自動建立 NuGet 套件，以跨平台共用程式碼。
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6d3f7b316e397705ecb9bd404007dcd9ef5aa183
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266778"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>NuGet 多平台程式庫專案 (Nugetizer 3000)

_自動建立 NuGet 套件，以使用 Nugetizer 3000 的平台共用程式碼 ！_

可自動建立跨平台共用程式碼的 NuGet 套件_Nugetizer 3000_。 如此一來，可建立從現有的程式庫專案，或藉由建立新的 NuGet 套件**多平台程式庫專案**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Nugetizer 3000 是隨附於 Visual Studio for Mac&ndash;尋求**文件庫 > Mulitplatform 程式庫**專案中的型別**檔案 > 新增**視窗：

[![](images/mulitplatform-library-sml.png "建立新的多平台程式庫視窗")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中使用 Nugetizer 3000，請[下載並執行 VSIX 安裝程式](http://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>建置 NuGet 套件

一旦設定之後，每次建置專案的輸出完整的 NuGet 套件，可以用來與其他應用程式內部共用程式碼，或上傳至[NuGet.org](https://www.nuget.org)。

有三種案例，使用這項功能：

- [現有的程式庫專案](existing-library.md)

  建立從現有的 PCL （或.NET Standard） 專案的 NuGet 套件。

- [建立新的多平台程式庫專案](single-codebase.md)

  建立新的程式庫共用透過 NuGet，使用 PCL 或.NET Standard 的一般程式碼。

- [建立新的平台專屬程式庫專案](platform-specific.md)

  建立新的程式庫和包含適用於 iOS 和 Android 平台特定程式碼，並在共用的專案中包含的通用程式碼和以支援特定 iOS 或 Android 功能的平台特定專案的 NuGet。

請參閱[中繼資料指南](metadata.md)如必須新增至任何 NuGet 套件的必要和選擇性中繼資料的詳細資訊。

## <a name="further-nuget-information"></a>NuGet 的進一步資訊

深入了解[手動建立適用於 Xamarin 的 Nuget](~/cross-platform/app-fundamentals/nuget-manual.md) ，以及如何[應用程式中包含 NuGet 套件](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

Microsoft 的[NuGet 文件](https://docs.microsoft.com/nuget/)包含更詳細的資訊上 **.nupkg**格式，並在 Visual Studio 使用 NuGet 套件。

NuGet 封裝專案的設計討論 （也稱為 NuGetizer 3000) 位於[NuGet GitHub 存放庫](https://github.com/NuGet/Home/wiki/NuGetizer-3000)。

## <a name="related-links"></a>相關連結

- [NuGetizer 3000 使用案例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [手動建立適用於 Xamarin 的 NuGet 套件](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文件](https://docs.microsoft.com/nuget/)
