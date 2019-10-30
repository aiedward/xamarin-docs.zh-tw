---
title: 在多個平臺上共用程式碼
description: 本檔連結到描述共用程式碼技術的各種指南，包括可移植的類別庫、共用的專案、.NET Standard 和 NuGet。
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016820"
---
# <a name="sharing-code-on-multiple-platforms"></a>在多個平臺上共用程式碼

這些文章說明可跨平臺共用程式碼的不同選項，包括 Windows、Android、iOS 等。

## <a name="code-sharing-overviewcode-sharingmd"></a>[程式碼共用總覽](code-sharing.md)

瞭解 Xamarin 專案可用的不同程式碼共用選項，包括 .NET Standard 程式庫和共用專案。 也支援可移植的類別庫，不過，它們會被視為已被取代而改用 .NET Standard。

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard 是跨平臺共用程式碼的慣用選項。 程式碼是針對特定版本所建立（2.0 提供與現有 .NET Framework 程式碼的最佳 API 相容性），並可供其他支援該層級或更高等級的專案使用。 Visual Studio 2019 和 Visual Studio 2019 for Mac 都支援 .NET Standard 專案。

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)

共用的專案可讓您撰寫一些不同應用程式專案所參考的通用程式碼。 程式碼會編譯為每個參考專案的一部分，而且可以包含編譯器指示詞，以協助將平臺特定功能併入共用程式碼基底中。 本文討論共用專案的工作方式，以及如何建立和使用 Xamarin 專案。

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)

可移植的類別庫專案可讓您建立和散發包含共用程式碼的元件，以便在多個平臺上執行。 若要建立可移植的類別庫（或「PCL」），您必須先選取要設為目標的平臺，然後針對這些平臺定義的設定檔中可用的 .NET Framework 子集合撰寫程式碼。 Pcl 在最新版本的 Visual Studio 中被視為已被取代;建議開發人員改用 .NET Standard 2.0。

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 專案：程式碼共用的多平臺程式庫](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

NuGet 套件可以從 PCL 或 .NET standard 專案自動產生;和共用的專案可以使用個別的 NuGet 專案類型，封裝成「bait 和切換」 NuGet 套件。 本節說明如何為每個程式碼共用案例建立 NuGet 套件。

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[手動建立適用于 Xamarin 的 NuGet 套件](~/cross-platform/app-fundamentals/nuget-manual.md)

建立可與 Xamarin 平臺搭配使用之 NuGet 套件的秘訣。

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[在跨平臺C++ Xamarin 專案中使用 C/程式庫](~/cross-platform/cpp/index.md)

這項技術可讓您分離 C/C++程式庫的演進、 C# NuGet 中的系結，以及 Xamarin 應用程式。 功能是由原生平臺 C/C++程式庫所提供，但所有平臺特定的程式碼都與最終的 Xamarin 應用程式隔離，允許最高的效能，而不會重複程式碼。 
