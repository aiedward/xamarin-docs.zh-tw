---
title: Microsoft Azure
description: "文件及範例程式碼會下載 azure。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 55213de8d2c93349598bcc9b10232535f09bc3d6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-azure"></a>Microsoft Azure

_文件及範例程式碼會下載 azure。_

[ ![](images/evolve-mikej-azure-sml.png "Azure 應用程式服務功能可輕鬆地加入至 Xamarin 應用程式，包括雲端資料儲存和跨平台推送通知")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[發展 2016年： 開發使用 Azure 和 Xamarin 的已連線應用程式](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>適用於 Mac 連接 Visual Studio 中的服務

新[已連接服務](connected-services.md)適用於 Mac 的 Visual Studio 功能可協助開發人員快速且輕鬆地新增 Azure 功能，從行動裝置應用程式在 IDE 中。 目前可供測試的 Alpha 色板。


## <a name="azure-app-services"></a>Azure 應用程式服務

集合[Azure 行動應用程式文件](~/cross-platform/data-cloud/mobile-apps.md)，引導您完成實作的處理序[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。
Xamarin 也提供 Azure 傳訊 NuGet 封裝的[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)和[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)以協助實作跨平台的推播通知。

設定您的應用程式上[Azure 應用程式服務入口網站](https://portal.azure.com/)存取行動應用程式、 Web 應用程式開發介面，存放裝置，以及執行更多。 深入了解[應用程式服務的不同方式](http://azure.microsoft.com/en-us/updates/whats-new-with-azure-app-service/)和觀賞中[microsoft 這些影片](http://azure.microsoft.com/en-us/campaigns/azure-march-announcement/)。

## <a name="active-directory-authentication"></a>Active Directory 驗證

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md)可用來透過 Xamarin 應用程式中的登入使用者[Xamarin.Auth 元件](https://www.nuget.org/packages/Xamarin.Auth/)。
應用程式可以存取其他如 Office 365 等服務。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 會公開可以輕鬆地取用的 Xamarin 應用程式的其餘部分類似的介面。
您可以輕鬆地向上微調[Azure 網站](https://trywebsites.azurewebsites.net/)和建置 WebAPI 為基礎的應用程式連接到 Xamarin 應用程式。


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)

此教學課程介紹如何整合其餘部分，WCF 與 SOAP web 服務技術搭配 Xamarin 的行動應用程式。 它會檢查服務以各種方式實作，會評估可用的工具和程式庫，以整合，並提供範例模式使用服務資料。 最後，它會提供使用 Xamarin 的行動應用程式建立耗用量的 RESTful web 服務的基本概觀。

## <a name="samples"></a>範例

除了[文件範例](https://github.com/xamarin/mobile-samples/tree/master/Azure)，下列的完整應用程式會示範各種併入 Xamarin 應用程式的 Azure 功能：

- [運動](https://github.com/xamarin/Sport)– 使用資料儲存體與推播通知的好記的運動聯盟追蹤應用程式。
- [稍後](https://github.com/pierceboggan/Moments)– 立即相片共用功能，使用 Azure 儲存體映像。
- [Xamarin CRM](https://github.com/xamarin/app-crm) – 用於後端 Web API。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure 行動應用程式。

- [資格](https://github.com/dotnet-architecture/eShopOnContainers)– 範例如[架構數列](https://www.microsoft.com/net/learn/architecture)有聲。
- [MyDriving](https://azure.microsoft.com/en-us/campaigns/mydriving/) – Azure + IoT 範例建置 2016年。


## <a name="related-links"></a>相關連結

- [Azure 的 PCL 範例 (根據@paulbatum) （範例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 入口網站](http://azure.microsoft.com/)
- [適用於 Xamarin (NuGet) 的行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
