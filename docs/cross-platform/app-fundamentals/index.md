---
title: 共用程式碼
description: 應用程式的核心概念
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 01116a35dca80cd92ea16232a2abb127f60d9f0a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="sharing-code"></a>共用程式碼

此章節提供一些較常見的工作項目或概念，開發人員需要開發行動應用程式時應該注意的指南。

## <a name="code-sharing-overviewcode-sharingmd"></a>[程式碼共用概觀](code-sharing.md)

深入了解不同的程式碼共用選項適用於 Xamarin 專案，包括可攜式類別庫 (PCLs)、 共用的專案，以及.NET 標準程式庫。


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)

可攜式類別庫專案可讓您建置和發佈包含共用程式碼，在多個平台上執行的組件。 針對.NET Framework 所提供的平台所定義的設定檔中的子集，建立您第一次選取哪一個平台為目標，然後撰寫程式碼的可攜式類別庫 （或 「 PCL"）。 本文件說明如何建立及使用 PCLs 來搭配 Xamarin。

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)

共用的專案可讓您撰寫正由許多不同的應用程式專案的通用程式碼。 程式碼會編譯為組件的每個參考的專案，而且可包含編譯器指示詞，可幫助這些特定平台共用程式碼基底中的功能。 這篇文章會討論共用專案的運作方式，以及如何建立並與 Xamarin 專案搭配使用。

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

標準.NET 是跨平台共用程式碼的新選項。 它適用於可攜式類別庫; 類似的方式程式碼針對特定的版本 (目前透過 1.6 1.0) 所建立，並接著可以使用其他支援的層級的專案或更高版本。 標準.NET 支援專案 Xamarin Studio 6.2、 Visual Studio 針對 Windows 和 Visual Studio for mac。

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 的專案： 多平台程式庫共用的程式碼](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

NuGet 封裝可以自動產生從 PCL 或.NET 標準專案的專案。和共用的專案可以封裝到使用個別的 NuGet 專案型別。 上鉤 and 的交換器 」 NuGet 封裝。 本節說明如何建立每個程式碼共用案例的 NuGet 封裝。

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[手動建立 Xamarin NuGet 套件](~/cross-platform/app-fundamentals/nuget-manual.md)

建立搭配 Xamarin 平台的 NuGet 封裝的提示。
