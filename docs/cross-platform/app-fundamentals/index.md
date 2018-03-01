---
title: "應用程式基本概念"
description: "應用程式的核心概念"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 9e4e7705e1ca29b6abf716a48ae3fa0e7c1a19ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>應用程式基本概念

此章節提供一些較常見的工作項目或概念，開發人員需要開發行動應用程式時應該注意的指南。

##  <a name="building-cross-platform-applicationscross-platformapp-fundamentalsbuilding-cross-platform-applicationsindexmd"></a>[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)

藉由選擇 Xamarin，並保持記住幾件事您設計及開發您的行動應用程式時，您可以了解跨行動平台共用大量的程式碼、 減少上市時間，運用現有 talent、 符合客戶需求的行動裝置的存取並減少跨平台的複雜性。&nbsp;本文將概述金鑰的指導方針來實現這些公用程式和產能的應用程式的優點。

## <a name="code-sharing-optionscode-sharingmd"></a>[程式碼共用選項](code-sharing.md)

深入了解不同的程式碼共用選項適用於 Xamarin 專案，包括可攜式類別庫 (PCLs)、 共用的專案，以及.NET 標準程式庫。


## <a name="accessibilityaccessibilitymd"></a>[協助工具選項](accessibility.md)

建置可存取的應用程式的提示。


## <a name="localizationlocalizationmd"></a>[當地語系化](localization.md)

讓可以轉譯成多種語言的地區設定感知應用程式的指導方針。


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

##  <a name="cross-platform-data-accessxamarin-formsdata-cloudindexmd"></a>[跨平台的資料存取](~/xamarin-forms/data-cloud/index.md)

大部分的應用程式有某些需求，要將資料儲存在本機裝置上。 除非兩者小型的資料量，所以通常會要求資料庫和資料層應用程式來管理資料庫存取權。 iOS 和 Android 具有 「 內建的 「 SQLite 資料庫引擎，並簡化了 Xamarin 的平台方式來儲存和擷取資料的存取。 [Android 資料存取](~/android/data-cloud/data-access/index.md)， [iOS 資料存取](~/ios/data-cloud/data/index.md)，和[Xamarin.Forms 資料存取](~/xamarin-forms/data-cloud/index.md)指南提供如何在每個平台上存取 SQLite 的範例。


##  <a name="transport-layer-securitytransport-layer-securitymd"></a>[傳輸層安全性](transport-layer-security.md)

於 selectingthe 正確 SSL/TLS 實作以保護您的應用程式的網路連線資訊。


##  <a name="notificationsxamarin-formsdata-cloudpush-notificationsindexmd"></a>[通知](~/xamarin-forms/data-cloud/push-notifications/index.md)

行動應用程式使用通知，告知使用者，某些應用程式特定事件發生不顯眼的方式。 通知通常用來通知使用者在背景中執行應用程式處理序的狀態。 舉例來說，這可能會下載大型檔案。 這個檔案可能需要很長的時間，若要下載，，因此應該會在背景進行這項活動。 下載完成時，會通知使用者事實的通知。
此外，通知阿瑞斯不只限制為本機應用程式。 它也可能會將通知發行給行動應用程式的伺服器應用程式。 本文將討論如何在 Android 和 iOS 上使用通知。
