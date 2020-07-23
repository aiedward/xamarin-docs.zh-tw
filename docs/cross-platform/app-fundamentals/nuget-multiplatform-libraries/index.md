---
title: NuGet 多平臺程式庫專案（也稱為 Nugetizer 3000）
description: 本檔說明如何使用 Nugetizer 3000 工具來自動建立 NuGet 套件，以跨平臺共用程式碼。
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: ac8e4b2eef0905ba50cb24b9eb035d50a05dd84a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932235"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>NuGet 多平臺程式庫專案（Nugetizer 3000）

_使用 ' Nugetizer 3000 ' 自動建立 NuGet 套件，以跨平臺共用程式碼！_

您可以使用_Nugetizer 3000_，自動建立 NuGet 套件以跨平臺共用程式碼。 這可讓您從現有的程式庫專案建立 NuGet 封裝，或建立新的多**平臺程式庫專案**。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Nugetizer 3000 隨附于 Visual Studio for Mac 在檔案 &ndash; **> 新**視窗中尋找程式庫 **> Mulitplatform 程式庫**專案類型：

[![建立新的多平臺程式庫視窗](images/mulitplatform-library-sml.png)](images/mulitplatform-library.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中使用 Nugetizer 3000，請[下載並執行 VSIX 安裝程式](https://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>建立 NuGet 套件

一旦設定之後，專案的每個組建都會輸出完整的 NuGet 套件，可用來在內部與其他應用程式共用程式碼，或上傳至[NuGet.org](https://www.nuget.org)。

使用這項功能的案例有三種：

- [現有的程式庫專案](existing-library.md)

  從現有的 PCL （或 .NET Standard）專案建立 NuGet 套件。

- [建立新的多平臺程式庫專案](single-codebase.md)

  使用 PCL 或 .NET Standard 建立新的程式庫，以透過 NuGet 共用通用程式碼。

- [建立新的平臺特定程式庫專案](platform-specific.md)

  建立新的程式庫和 NuGet，其中包含適用于 iOS 和 Android 的平臺專屬程式碼，並使用共用專案來包含通用程式碼和平臺特定專案，以支援 iOS 或 Android 特有的功能。

請參閱[中繼資料指南](metadata.md)，以取得必須新增至任何 NuGet 封裝之必要和選擇性中繼資料的詳細資訊。

## <a name="further-nuget-information"></a>進一步的 NuGet 資訊

深入瞭解如何[手動建立 Xamarin 的 nuget](~/cross-platform/app-fundamentals/nuget-manual.md) ，以及如何[在應用程式中包含 NuGet 套件](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

Microsoft 的[NuGet 檔](https://docs.microsoft.com/nuget/)包含**nupkg**格式的詳細資訊，以及在 Visual Studio 中使用 NuGet 套件。

NuGet 套件專案的設計討論（也稱為 NuGetizer 3000）可在[NuGet GitHub 存放庫](https://github.com/NuGet/Home/wiki/NuGetizer-3000)中取得。

## <a name="related-links"></a>相關連結

- [NuGetizer-3000 使用案例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [手動建立適用于 Xamarin 的 NuGet 套件](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文件](https://docs.microsoft.com/nuget/)
