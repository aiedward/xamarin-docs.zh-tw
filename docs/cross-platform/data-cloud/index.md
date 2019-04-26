---
title: Microsoft Azure 及 Xamarin
description: 關於已連線的服務，在 Visual Studio 中的文件，適用於 Mac、 Azure Mobile Apps、 Active Directory 驗證和 WebAPI 這個文件連結。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 25eec247a0cb664897541a0e6e818a77018fda43
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187765"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure 及 Xamarin

[ ![](images/evolve-mikej-azure-sml.png "Azure App Service 功能可輕鬆地加入至 Xamarin 應用程式，包括雲端資料儲存體和跨平台推播通知")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016：開發使用 Azure 及 Xamarin 的連線應用程式](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中已連線的服務

新[已連接服務](connected-services.md)Visual Studio for Mac 功能可協助開發人員快速且輕鬆地將 Azure 的功能，從行動裝置應用程式在 IDE 中。 目前僅在 Alpha 通道中進行測試。

## <a name="azure-app-services"></a>Azure App Services

集合[Azure Mobile Apps 文件](~/cross-platform/data-cloud/mobile-apps.md)，會引導您完成實作的程序[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。
Xamarin 也提供 Azure 傳訊 NuGet 封裝[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)並[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)來協助實作跨平台的推播通知。

在 設定您的應用程式[Azure App Service 入口網站](https://portal.azure.com/)來存取行動應用程式、 Web Api、 儲存體及更多。 深入了解[應用程式服務有何不同](http://azure.microsoft.com/updates/whats-new-with-azure-app-service/)，並在觀察[這些影片來自 Microsoft](http://azure.microsoft.com/campaigns/azure-march-announcement/)。

## <a name="active-directory-authentication"></a>Active Directory 驗證

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md)可用來透過 Xamarin 應用程式中的登入使用者[Xamarin.Auth 元件](https://www.nuget.org/packages/Xamarin.Auth/)。
應用程式就可以存取 Office 365 等其他服務。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 會公開可以輕鬆地取用的 Xamarin 應用程式的 REST 型介面。
您可以輕鬆地向上微調[Azure 網站](https://trywebsites.azurewebsites.net/)和建置 WebAPI 型應用程式連接到 Xamarin 應用程式。


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Web 服務的簡介](~/cross-platform/data-cloud/web-services/index.md)

本教學課程介紹如何整合 REST，WCF 和 SOAP web 服務技術與 Xamarin 行動應用程式。 它會檢查各種服務實作、 評估可用的工具和程式庫來整合，並提供範例模式使用服務的資料。 最後，它會提供建立 RESTful web 服務，以便與 Xamarin 行動應用程式的基本概觀。

## <a name="samples"></a>範例

除了[文件範例](https://github.com/xamarin/mobile-samples/tree/master/Azure)，下列完整的應用程式示範併入 Xamarin 應用程式的各種 Azure 功能：

- [Sport](https://github.com/xamarin/Sport) – 會使用資料儲存體和推播通知的好記的體育聯盟追蹤應用程式。
- [時間](https://github.com/pierceboggan/Moments)– 立即的相片分享，使用 Azure 儲存體的映像。
- [Xamarin CRM](https://github.com/xamarin/app-crm) – 使用 Web API 後端進行。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure 行動應用程式。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – 範例，以[架構系列](https://www.microsoft.com/net/learn/architecture)電子書。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT 的 Build 2016 的範例。


## <a name="related-links"></a>相關連結

- [Azure 的 PCL 範例 (由@paulbatum) （範例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 入口網站](http://azure.microsoft.com/)
- [適用於 Xamarin (NuGet) 的行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
