---
title: Xamarin 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7d153491802a74ff06b3a5cd63e585b7fd18ff03
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644322"
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin 中的通知

本節說明如何在 Xamarin 中執行通知。 其中說明 Android 通知的各種 UI 元素, 並討論與建立和顯示通知相關的 API。

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Android 中的本機通知](local-notifications.md)

本節說明如何在 Xamarin 中執行本機通知。 其中說明 Android 通知的各種 UI 元素, 並討論建立和顯示通知所牽涉到的 Api。

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[逐步解說-在 Xamarin 中使用本機通知](local-notifications-walkthrough.md)  
 
本逐步解說說明如何在 Xamarin Android 應用程式中使用本機通知。 它會示範建立和發佈通知的基本概念。 當使用者按一下通知抽屜中的通知時, 會啟動第二個活動。 

## <a name="further-reading"></a>進一步閱讀

[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)&ndash; Firebase 雲端通訊 (FCM) 是一種服務, 可在行動應用程式和伺服器應用程式之間進行訊息處理。 Firebase 雲端通訊可以用來在 Xamarin Android 應用程式中執行遠端通知 (也稱為推播通知)。

[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)&ndash;此 android 開發人員主題是 android 通知的最終指南。 其中包含設計考慮一節, 可協助您設計您的通知, 使其符合 Android 使用者介面的指導方針。 當您啟動活動時, 它會提供更多關於 preserviing 導覽的背景資訊, 並說明如何在通知中顯示進度, 並控制鎖定畫面上的媒體播放。

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService)&ndash;此 Android 服務可讓您的應用程式接聽 Android 裝置上張貼的所有通知 (並與之互動), 而不只是您的應用程式註冊要接收的通知。
請注意, 使用者必須明確授與您應用程式的許可權, 讓它能夠接聽裝置上的通知。

## <a name="related-links"></a>相關連結

- [本機通知 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [遠端通知 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
