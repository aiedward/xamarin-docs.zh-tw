---
title: "Microsoft Azure 行動應用程式"
description: "如需 Azure 入口網站的文件會下載範例和程式碼。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fb3c26b7d090ca42328c61192c794dec1544d1d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure 行動應用程式

_如需 Azure 入口網站的文件會下載範例和程式碼。_

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/en-us/develop/mobile/xamarin/
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


這些連結是 Xamarin 文件上可用的[Azure 行動應用程式](https://azure.microsoft.com/en-us/documentation/services/app-service/mobile/)網站。
將 Azure 的功能加入至 Xamarin 應用程式很簡單，只下載[Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)。

## <a name="working-with-the-xamarin-azure-component"></a>使用 Xamarin Azure 元件

一般的文件[使用 Xamarin 的用戶端程式庫 （元件）](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/)來完成與 Azure 行動應用程式的各種工作。 此頁面包含許多範例程式碼片段，而不需要詳細的說明和每個下列逐步解說文件中的範例。

## <a name="getting-started"></a>快速入門

本文提供以啟動並執行第一個 Xamarin Azure 應用程式的逐步指示。
它涵蓋了在入口網站建立新的 Azure 行動應用程式然後下載並執行預先設定的應用程式。

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started/)

## <a name="validate-modify-and-augment-data-in-scripts"></a>驗證、 修改及增強的指令碼中的資料

示範如何將伺服器端指令碼新增至 Azure 行動服務資料表，以實作伺服器端驗證和其他功能。

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)


## <a name="add-paging-to-data"></a>加入分頁資料

分頁處理大量的資料使用 Skip() 和 Take() 簡單的範例。

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)


## <a name="get-started-with-users"></a>開始使用使用者

提供設定和使用 Azure 行動服務的登入畫面撰寫程式碼的完整指示。 支援的驗證提供者包括 Microsoft、 Google、 Facebook 和 Twitter。

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>授權使用者的指令碼

Javascript 後端的一些範例程式碼

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>開始使用推播

完成設定推播通知上的 Apple 與 Google 的網站，然後從 Azure 行動服務傳送推播通知給裝置的指示。

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-push/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-push/)


## <a name="get-started-with-notification-hubs"></a>開始使用通知中樞

完成 Apple 與 Google 網站上設定推播通知設定 Azure 通知中樞，然後產生推播通知給裝置的指示。

-  [iOS](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/)
-  [Android](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/)



## <a name="related-links"></a>相關連結

- [GettingStarted （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
- [通知中樞 （範例）](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure 的 PCL 範例 (根據@paulbatum) （範例）](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure 行動用戶端](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Azure 行動應用程式的學習路徑](https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/)
