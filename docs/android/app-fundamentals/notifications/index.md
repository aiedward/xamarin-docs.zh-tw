---
title: "Xamarin.Android 中的通知"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 0dbf8c32ca7b7565105c01cfaa077fe792b09b18
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin.Android 中的通知

<a name="Overview" />

## <a name="overview"></a>總覽

本節示範如何實作 Xamarin.Android 中的通知。
它說明 Android 的通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。

<a name="Sections" />

## <a name="sections"></a>章節

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[在 Android 中的本機通知](local-notifications.md)

本節說明如何實作 Xamarin.Android 在本機的通知。 它說明 Android 的通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[逐步解說-Xamarin.Android 中使用本機的通知](local-notifications-walkthrough.md)  
 
這個逐步解說將說明如何在 Xamarin.Android 應用程式中使用本機的通知。 它會示範建立和發佈通知的基本概念。 當使用者按一下通知抽屜通知並啟動第二個活動。 


## <a name="for-further-reading"></a>進一步閱讀

[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase 雲端傳訊 (FCM) 是一項服務，可促進行動裝置應用程式與伺服器應用程式之間的傳訊。 Firebase Cloud Messaging 可用來實作遠端 （也稱為推播通知） 的通知 Xamarin.Android 應用程式中。

[通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)&ndash;此 Android 開發人員的主題是針對 Android 通知的最後指南。 它包含的設計考量 > 一節，可協助您設計您的通知，以便符合 Android 使用者介面的指導方針。 活動，在啟動時，它會提供有關 preserviing 瀏覽詳細背景資訊，並說明如何在通知和控制播放媒體鎖定螢幕上顯示進度。 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash; Android 此服務可讓您聆聽 （並與其互動） 的應用程式的所有通知都張貼在 Android 裝置，而不只是通知您的應用程式已向接收。 請注意，使用者必須明確授與您的應用程式讓它成為能夠接聽通知，在裝置上的權限。





## <a name="related-links"></a>相關連結

- [本機通知 （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [遠端通知 （範例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
