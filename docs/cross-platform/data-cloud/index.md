---
title: Microsoft Azure 和 Xamarin
description: 本檔連結到有關 Visual Studio for Mac、Azure Mobile Apps、Active Directory Authentication 和 WebAPI 中的已連線的服務檔。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: conceptdev
ms.author: crdun
ms.date: 10/09/2017
ms.openlocfilehash: 0979a0b65cc3d5b4944dadaf67aaa14cf1b3cf73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287592"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure 和 Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services 功能很容易加入 Xamarin 應用程式，包括雲端資料儲存和跨平臺推播通知")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016：使用 Azure 和 Xamarin 開發連線的應用程式](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中已連線的服務

Visual Studio for Mac 的新[已連線的服務](connected-services.md)功能可協助開發人員快速且輕鬆地從 IDE 內將 Azure 功能新增至行動應用程式。 目前可在 Alpha 色板中進行測試。

## <a name="azure-app-services"></a>Azure App 服務

[Azure Mobile Apps 檔](~/cross-platform/data-cloud/mobile-apps.md)的集合會引導您完成[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)的處理常式。
Xamarin 也提供適用于[iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/)和[Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/)的 Azure 訊息 NuGet 套件，以協助跨平臺執行推播通知。

在[Azure App Services 入口網站](https://portal.azure.com/)上設定您的應用程式，以存取 Mobile Apps、Web Api、儲存體及其他更多功能。 深入瞭解[應用程式服務的差異](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/)，並觀看[Microsoft 的這些](https://azure.microsoft.com/campaigns/azure-march-announcement/)影片。

## <a name="active-directory-authentication"></a>Active Directory 驗證

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md)可用來透過[Xamarin. Auth 元件](https://www.nuget.org/packages/Xamarin.Auth/)在 xamarin 應用程式中登入使用者。
然後，應用程式可以存取其他服務，例如 Office 365。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 會公開類似 REST 的介面，可供 Xamarin 應用程式輕鬆使用。
您可以輕鬆地加速[Azure 網站](https://trywebsites.azurewebsites.net/)，並建立以 WebAPI 為基礎的應用程式來連線到 Xamarin 應用程式。


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)

本教學課程介紹如何整合 REST、WCF 和 SOAP web 服務技術與 Xamarin 行動應用程式。 它會檢查各種服務實現、評估可用的工具和程式庫來整合它們，並提供使用服務資料的範例模式。 最後，它提供了建立 RESTful web 服務以供使用 Xamarin 行動應用程式的基本總覽。

## <a name="samples"></a>範例

除了[檔範例](https://github.com/xamarin/mobile-samples/tree/master/Azure)以外，下列完整的應用程式會示範整合到 Xamarin 應用程式中的各種 Azure 功能：

- 適合[運動](https://github.com/xamarin/Sport)的運動-使用資料儲存體 & 推播通知的聯盟應用程式。
- 時間[–使用](https://github.com/pierceboggan/Moments)影像 Azure 儲存體的立即相片分享。
- [XAMARIN CRM](https://github.com/xamarin/app-crm) –使用 Web API 來進行後端。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure Mobile Apps。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) –適用于電子書[架構系列](https://www.microsoft.com/net/learn/architecture)的範例。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) –組建2016中的 Azure + IoT 範例。


## <a name="related-links"></a>相關連結

- [Azure PCL 範例（依據@paulbatum）（範例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 入口網站](https://azure.microsoft.com/)
- [適用于 Xamarin 的行動用戶端（NuGet）](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
