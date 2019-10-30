---
title: Microsoft Azure Mobile Apps
description: 本檔連結的指南會說明如何建立連線到 Azure 的 Xamarin 應用程式。 討論如何使用 Xamarin Azure 元件、使用者和推播通知。
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: davidortinau
ms.author: daortin
ms.date: 04/02/2017
ms.openlocfilehash: 84517e4961dc3ad728b6cc352e9fb992d9e8b5bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016585"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者，並使用**資料**服務來保存和同步雲端中的應用程式資料。
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊[App Center](https://appcenter.ms/signup?utm_source=XamarinDocs&utm_medium=Azure&utm_campaign=docs) 。

_Azure 入口網站檔的範例和程式碼下載。_

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer xamarin com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started https://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data https://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push https://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication https://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs https://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data  https://go.microsoft.com/fwlink/p/?LinkId=331330
-->

這些連結適用于[Azure Mobile Apps](https://docs.microsoft.com/azure/app-service-mobile/)網站上提供的 Xamarin 檔。
藉由下載[Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)，將 azure 功能新增至 Xamarin 應用程式。

## <a name="working-with-the-xamarin-azure-component"></a>使用 Xamarin Azure 元件

一般檔[與 Xamarin 用戶端程式庫（元件）](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library)搭配使用，以使用 Azure Mobile Apps 完成各種工作。 此頁面包含許多範例程式碼片段，沒有詳細的說明和範例，請見下列每個逐步解說文章。

## <a name="getting-started"></a>快速入門

本文提供逐步指示，讓您的第一個 Xamarin Azure 應用程式啟動並執行。
其中涵蓋在入口網站中建立新的 Azure 行動應用程式，然後下載並執行預先設定的應用程式。

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
- [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>使用者入門

提供使用 Azure 行動服務設定登入畫面並為其編寫程式碼的完整指示。 支援的驗證提供者包括 Microsoft、Google、Facebook 和 Twitter。

- [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
- [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)

## <a name="authorize-users-in-scripts"></a>在腳本中授權使用者

JAVAscript 後端的一些範例程式碼

- [Todo .js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)

## <a name="get-started-with-push"></a>開始使用 Push

在 Apple 和 Google 網站上設定推播通知的完整指示，然後從 Azure 行動服務傳送推播通知至裝置。

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)

## <a name="get-started-with-notification-hubs"></a>開始使用通知中樞

在 Apple 和 Google 網站上設定推播通知的完整指示、設定 Azure 通知中樞，然後產生推播通知至裝置。

- [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
- [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)

## <a name="related-links"></a>相關連結

- [GettingStarted （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Azure Mobile Apps 學習路徑](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
