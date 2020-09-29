---
title: Microsoft Azure 和 Xamarin
description: 本檔連結到 Visual Studio for Mac、Azure Mobile Apps、Active Directory Authentication 和 WebAPI 中已連線的服務的相關檔。
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 9439b33c0e2c8458fd1308003aa9cd93148b6eac
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457311"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure 和 Xamarin

[![Azure App Services 功能很容易新增至 Xamarin 應用程式，包括雲端資料儲存和跨平臺推播通知](images/evolve-mikej-azure-sml.png)](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[發展2016：使用 Azure 和 Xamarin 開發連線的應用程式](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Visual Studio for Mac 中已連線的服務

Visual Studio for Mac 的新 [已連線的服務](/visualstudio/mac/connected-services) 功能可協助開發人員快速且輕鬆地將 Azure 功能新增至 IDE 內的行動應用程式。 目前可用於 Alpha 通道中的測試。

## <a name="azure-app-services"></a>Azure App Service

有一系列的 [azure Mobile Apps 檔](~/cross-platform/data-cloud/mobile-apps.md) 會引導您完成 [Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)的執行程式。
Xamarin 也提供適用于 [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) 和 [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) 的 Azure 訊息 NuGet 套件，可協助跨平臺執行推播通知。

在 [Azure App Services 入口網站](https://portal.azure.com/) 上設定您的應用程式，以存取 Mobile Apps、Web Api、儲存體等等。 深入瞭解 [應用程式服務的不同](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) 之處，並觀看 [Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/)的影片。

## <a name="active-directory-authentication"></a>Active Directory 驗證

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) 可以用來將使用者登入 Xamarin 應用程式。 然後，應用程式可以存取其他服務，例如 Office 365。

## <a name="webapi"></a>WebAPI

Microsoft 的 Web API 公開了類似 REST 的介面，可供 Xamarin 應用程式輕鬆取用。
您可以輕鬆地啟動 [Azure 網站](https://trywebsites.azurewebsites.net/) ，並建立以 WebAPI 為基礎的應用程式來連接到 Xamarin 應用程式。

### <a name="introduction-to-web-services"></a>[Web 服務簡介](~/cross-platform/data-cloud/web-services/index.md)

本教學課程將介紹如何將 REST、WCF 和 SOAP web 服務技術與 Xamarin 行動應用程式整合。 它會檢查各種服務的執行、評估可用的工具和程式庫以進行整合，並提供取用服務資料的範例模式。 最後，它會提供使用 Xamarin 行動應用程式來建立 RESTful web 服務以供取用的基本總覽。

## <a name="samples"></a>範例

除了 [檔範例](https://github.com/xamarin/mobile-samples/tree/master/Azure)，下列完整的應用程式也會示範整合至 Xamarin 應用程式的各種 Azure 功能：

- [運動](https://github.com/xamarin/Sport) -適合的運動-使用資料儲存體 & 推播通知的聯盟追蹤應用程式。
- [時刻](https://github.com/pierceboggan/Moments) –使用影像 Azure 儲存體的立即相片分享。
- [XAMARIN CRM](https://github.com/xamarin/app-crm) –針對後端使用 Web API。
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure Mobile Apps。

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) –電子書 [架構系列](https://www.microsoft.com/net/learn/architecture) 的範例。
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) –來自組建2016的 Azure + IoT 範例。

## <a name="related-links"></a>相關連結

- [)  (範例 (的 Azure PCL 範例 @paulbatum) ](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 入口網站](https://azure.microsoft.com/)
- [適用于 Xamarin (NuGet) 的行動用戶端 ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)