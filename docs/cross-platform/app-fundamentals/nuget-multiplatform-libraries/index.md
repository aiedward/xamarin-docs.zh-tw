---
title: NuGet 的專案 (Nugetizer 3000)
description: 自動建立 NuGet 套件，以跨平台使用 Nugetizer 3000 共用程式碼 ！
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: 60c78385d0ffd2fbbdd7af5a4ef4af63e30feb9f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="nuget-projects-nugetizer-3000"></a>NuGet 的專案 (Nugetizer 3000)

_自動建立 NuGet 套件，以跨平台使用 Nugetizer 3000 共用程式碼 ！_

可自動建立跨平台使用共用的程式碼的 NuGet 封裝_Nugetizer 3000_。 如此便可利用建立從現有的程式庫專案，或藉由建立新的 NuGet 封裝**多平台程式庫專案**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Nugetizer 3000 隨附於 Visual Studio for Mac 6.2。

[![](images/mulitplatform-library-sml.png "建立新的多平台程式庫視窗")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Nugetizer 3000 Visual Studio 中，請[下載並執行 VSIX 安裝程式](http://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>建置 NuGet 封裝

一旦設定之後，每次建置專案的輸出完整的 NuGet 封裝，可以用來與其他應用程式內部共用程式碼，或上傳至[NuGet.org](https://www.nuget.org)。

有三種案例使用這項功能：

- [現有的程式庫專案](existing-library.md)

  建立從現有的 PCL （或.NET 標準） 專案的 NuGet 封裝。

- [建立新的多平台程式庫專案](single-codebase.md)

  建立新的程式庫共用透過 NuGet，使用 PCL 或.NET 標準的一般程式碼。

- [建立新的平台特定程式庫專案](platform-specific.md)

  建立新的程式庫和，NuGet 可包含適用於 iOS 和 Android 平台專屬程式碼，並使用共用專案以包含通用程式碼和特定平台專案來支援特定 iOS 或 Android 的功能。

請參閱[中繼資料的指南](metadata.md)如必須加入至任何 NuGet 套件的必要和選擇性中繼資料的詳細資訊。


## <a name="further-nuget-information"></a>NuGet 的詳細資訊

深入了解[手動建立 NuGets xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)以及如何[包含應用程式中的 NuGet 封裝](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

Microsoft 的[NuGet 文件](https://docs.microsoft.com/nuget/)包含更詳細的資訊上 **.nupkg**格式和使用 Visual Studio 中的 NuGet 封裝。

NuGet 封裝專案的設計討論內容 （也稱為 NuGetizer 3000) 位於[NuGet GitHub 儲存機制](https://github.com/NuGet/Home/wiki/NuGetizer-3000)。


## <a name="related-links"></a>相關連結

- [NuGetizer 3000 使用案例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [手動建立 xamarin 的 NuGet 封裝](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文件](https://docs.microsoft.com/nuget/)
- [版本資訊](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
