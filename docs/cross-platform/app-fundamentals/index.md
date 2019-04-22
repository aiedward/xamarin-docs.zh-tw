---
title: 在多個平台上的共用程式碼
description: 本文件所連結到各種描述技術來共用程式碼，包括可攜式類別庫、 共用的專案、.NET Standard 和 NuGet 的輔助線。
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 35c210cc75033f2ad2c83ed6f5196f71d26d000f
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854180"
---
# <a name="sharing-code-on-multiple-platforms"></a>共用多個平台上的程式碼

這些文章會說明適用於跨平台，包括 Windows、 Android、 iOS 和多個共用程式碼的不同選項。

## <a name="code-sharing-overviewcode-sharingmd"></a>[程式碼共用概觀](code-sharing.md)

深入了解不同的程式碼共用選項適用於 Xamarin 專案，包括.NET 標準程式庫及共用專案。 也支援可攜式類別庫，但是會將它們視為取代為.NET Standard。

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard 是跨平台共用程式碼的慣用的選項。 程式碼建置針對特定版本 （2.0 會提供最佳的 API 相容性與現有的.NET Framework 程式碼），您可以使用其他支援的層級的專案或更高版本。 在 Visual Studio 2019 和 Visual Studio 2019 支援.NET standard 專案 for mac。

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)

共用的專案可讓您撰寫數個不同的應用程式專案所參考的通用程式碼。 程式碼編譯為一部分的每個參考的專案，而且可以包含編譯器指示詞，可幫助這些共用的程式碼基底平台專屬功能。 這篇文章討論共用專案的運作方式，以及如何建立及使用 Xamarin 專案。

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)

可攜式類別庫專案可讓您建置及散發包含共用程式碼，以在多個平台上執行的組件。 若要建立您第一次選取哪一個平台為目標，然後撰寫程式碼的可攜式類別庫 （或 「 PCL 」），針對.NET Framework 所提供的平台所定義的設定檔中的子集。 Pcl 會被視為在 Visual Studio; 的最新版本中被取代開發人員都要改為使用.NET Standard 2.0。

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 專案：適用於共用程式碼的多平台程式庫](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

NuGet 套件可以自動產生的 PCL 或.NET standard 專案;和共用專案可以封裝到 「 誘導轉向 」 使用個別的 NuGet 專案類型的 NuGet 套件。 本節說明如何建立 NuGet 套件，針對每個程式碼共用的案例。

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[以手動方式為 Xamarin 建立 NuGet 套件](~/cross-platform/app-fundamentals/nuget-manual.md)

建立使用 Xamarin 平台的 NuGet 套件的秘訣。

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[使用 C /C++跨平台 Xamarin 專案中的程式庫](~/cross-platform/cpp/index.md)

這項技術可讓您將分離的發展您的 C /C++程式庫C#中的 NuGet 和您的 Xamarin 應用程式的繫結。 功能會由平台的原生 C /C++程式庫，但所有平台特定程式碼會與最終的 Xamarin 應用程式，可讓任何程式碼重複使用的最高可能效能隔離。 
