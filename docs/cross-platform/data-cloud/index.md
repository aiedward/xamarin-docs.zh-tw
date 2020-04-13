---
title: 微軟 Azure 和 Xamarin
description: 本文件連結到有關 Mac、Azure 行動應用程式、活動目錄身份驗證和 WebAPI 視覺化工作室中已連接服務的文件。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 2b6dfeb0de0fac59556280609dbf870c23a9298b
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388521"
---
# <a name="microsoft-azure-and-xamarin"></a>微軟 Azure 和 Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services features are easy to add to Xamarin apps, including cloud data storage and cross-platform push notifications")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[2016 年發展:使用 Azure 和 Xamarin 開發互聯應用](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中已連線的服務

適用於 Mac 的 Visual Studio 新的[互聯服務](connected-services.md)功能可協助開發人員從 IDE 中快速輕鬆地將 Azure 功能添加到行動應用程式。 目前可在 Alpha 通道中進行測試。

## <a name="azure-app-services"></a>Azure App Service

有一組[Azure 行動應用程式文件](~/cross-platform/data-cloud/mobile-apps.md),用於指導您完成實現[Azure 行動客戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)的過程。
Xamarin 還為[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)和[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)提供了 Azure 訊息傳遞 NuGet 包,以説明跨平台實現推送通知。

在[Azure 應用服務門戶](https://portal.azure.com/)上配置應用以存取行動應用、Web API、儲存等。 瞭解[應用程式服務是如何不同的](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/),並觀看[這些影片從微軟](https://azure.microsoft.com/campaigns/azure-march-announcement/)。

## <a name="active-directory-authentication"></a>Active Directory 驗證

[Azure 活動目錄](~/cross-platform/data-cloud/active-directory/index.md)可用於將使用者登錄到 Xamarin 應用。 然後,這些應用可以訪問 Office 365 等其他服務。

## <a name="webapi"></a>WebAPI

微軟的 Web API 公開了一個類似 REST 的介面,Xamarin 應用程式可以輕鬆使用該介面。
您可以輕鬆地啟動[Azure 網站](https://trywebsites.azurewebsites.net/)並建構基於 WebAPI 的應用以連接到 Xamarin 應用。

### <a name="introduction-to-web-services"></a>[網路服務簡介](~/cross-platform/data-cloud/web-services/index.md)

本教學介紹如何將 REST、WCF 和 SOAP Web 服務技術與 Xamarin 行動應用程式整合。 它檢查各種服務實現,評估用於集成它們的可用工具和庫,並提供用於使用服務數據的範例模式。 最後,它提供了使用 Xamarin 行動應用程式創建用於消費的 RESTful Web 服務的基本概述。

## <a name="samples"></a>範例

除了[文件範例](https://github.com/xamarin/mobile-samples/tree/master/Azure)之外,以下完整應用程式展示了整合到 Xamarin 應用程式中的各種 Azure 功能:

- [體育](https://github.com/xamarin/Sport)– 友好的體育聯賽跟蹤應用程式,使用數據存儲&推送通知。
- [時刻](https://github.com/pierceboggan/Moments)– 使用 Azure 儲存的圖像的即時照片共用。
- [Xamarin CRM](https://github.com/xamarin/app-crm) = 使用 Web API 作為後端介面。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) - Azure 行動應用。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) –[架構結構系列](https://www.microsoft.com/net/learn/architecture)電子書的範例。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) = Azure + IoT 示例來自 2016 年生成。

## <a name="related-links"></a>相關連結

- [Azure PCL@paulbatum範例 (按 ) (範例)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 入口網站](https://azure.microsoft.com/)
- [Xamarin 的行動客戶端 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
