---
title: Microsoft Azure Mobile Apps
description: 本文件所連結說明如何建置 Xamarin 應用程式連線到 Azure 的指南。 它會討論使用 Xamarin 的 Azure 元件、 使用者和推播通知。
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: a1a0b078659441f0f45af66728a5f37d578d6274
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675092"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

_範例和程式碼會下載 Azure 入口網站的文件。_

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
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


這些連結是針對 Xamarin 文件上可用[Azure Mobile Apps](https://docs.microsoft.com/azure/app-service-mobile/)網站。
加入 Xamarin 應用程式中的 Azure 功能，藉由下載[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。

## <a name="working-with-the-xamarin-azure-component"></a>使用 Xamarin 的 Azure 元件

一般文件[使用 Xamarin 用戶端程式庫 （元件）](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library)完成使用 Azure Mobile Apps 的各種工作。 此頁面包含大量範例程式碼片段，而不需要詳細的說明和逐步解說文章下面所列的每個中可用的範例。

## <a name="getting-started"></a>快速入門

本文提供逐步指示，以取得 Xamarin 的 Azure 應用程式第一次，啟動並執行。
它涵蓋了在入口網站中建立新的 Azure 行動應用程式然後下載並執行預先設定的應用程式。

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

## <a name="get-started-with-users"></a>讓使用者開始使用

提供完整的指示來設定及撰寫程式碼使用 Azure 行動服務的登入畫面。 支援的驗證提供者包括 Microsoft、 Google、 Facebook 和 Twitter。

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>授權使用者的指令碼

Javascript 後端的一些範例程式碼

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>開始使用推播

完成設定推播通知，在 Apple 與 Google 的網站，然後從 Azure 行動服務的推播通知傳送給裝置的指示。

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>開始使用通知中樞

完成設定 Apple 與 Google 網站上的推播通知、 設定 「 Azure 通知中樞，然後產生推播通知至裝置的指示。

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>相關連結

- [GettingStarted （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [通知中樞 （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Azure Mobile Apps 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
