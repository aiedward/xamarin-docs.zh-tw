---
title: Microsoft Azure 行動應用程式
description: 指南說明如何建置 Xamarin 應用程式連接到 Azure 的此文件連結。 其中也會討論使用 Xamarin Azure 元件、 使用者和推播通知。
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: baa687bfb3b2e8306e70e83b6a6ee54595110860
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780626"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure 行動應用程式

_如需 Azure 入口網站的文件會下載範例和程式碼。_

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/develop/mobile/xamarin/
as https://developer.xamarin.com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started  http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data   http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push   http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs  http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data    http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


這些連結是 Xamarin 文件上可用的[Azure 行動應用程式](https://docs.microsoft.com/azure/app-service-mobile/)網站。
加入的 Xamarin 應用程式中的 Azure 功能，藉由下載[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。

## <a name="working-with-the-xamarin-azure-component"></a>使用 Xamarin Azure 元件

一般的文件[使用 Xamarin 的用戶端程式庫 （元件）](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library)來完成與 Azure 行動應用程式的各種工作。 此頁面包含許多範例程式碼片段，而不需要詳細的說明和每個下列逐步解說文件中的範例。

## <a name="getting-started"></a>快速入門

本文提供以啟動並執行第一個 Xamarin Azure 應用程式的逐步指示。
它涵蓋了在入口網站建立新的 Azure 行動應用程式然後下載並執行預先設定的應用程式。

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>開始使用使用者

提供設定和使用 Azure 行動服務的登入畫面撰寫程式碼的完整指示。 支援的驗證提供者包括 Microsoft、 Google、 Facebook 和 Twitter。

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>授權使用者的指令碼

Javascript 後端的一些範例程式碼

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>開始使用推播

完成設定推播通知上的 Apple 與 Google 的網站，然後從 Azure 行動服務傳送推播通知給裝置的指示。

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>開始使用通知中樞

完成 Apple 與 Google 網站上設定推播通知設定 Azure 通知中樞，然後產生推播通知給裝置的指示。

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>相關連結

- [GettingStarted （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [通知中樞 （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Azure 行動應用程式的學習路徑](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
