---
title: 在 Xamarin.Android 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a0778de5dcc7e21ba011fe144be703b3b83b8247
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114037"
---
# <a name="notifications-in-xamarinandroid"></a>在 Xamarin.Android 中的通知


## <a name="overview"></a>總覽

本節說明如何在 Xamarin.Android 中實作通知。 它說明 Android 通知的各種不同的 UI 項目，並討論 API 的涉及建立和顯示通知。


## <a name="sections"></a>章節

### <a name="local-notifications-in-androidlocal-notificationsmd"></a>[在 Android 中的本機通知](local-notifications.md)

本節說明如何在 Xamarin.Android 中實作本機通知。 它說明 Android 通知的各種不同的 UI 項目，並討論 API 的涉及建立和顯示通知。 

### <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[逐步解說-在 Xamarin.Android 中使用本機通知](local-notifications-walkthrough.md)  
 
本逐步解說將說明如何在 Xamarin.Android 應用程式中使用本機通知。 它示範如何建立和發行通知的基本概念。 當使用者按一下 [通知] 下拉式清單中的通知時啟動第二個活動。 


## <a name="for-further-reading"></a>進一步閱讀

[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase 雲端通訊 (FCM) 是一項服務，可協助行動裝置應用程式和伺服器應用程式之間通訊。 Firebase 雲端通訊可用來實作 （也稱為 「 推播通知 」） 的遠端通知在 Xamarin.Android 應用程式。

[通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)&ndash;此 Android 開發人員主題是 Android 通知的完整指南。 它包含的設計考量 > 一節，可協助您設計您的通知，使它們符合 Android 使用者介面的指導方針。 啟動活動時，它會提供有關 preserviing 導覽的詳細背景資訊，並說明如何在通知和控制媒體播放在鎖定畫面上顯示進度。 

[NotificationListenerService](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) &ndash;此 Android 服務可讓您的應用程式，接聽 （並與其互動） 的所有通知都張貼在 Android 裝置，而不只是您的應用程式已向通知上接收。 請注意，使用者必須明確地授與您的應用程式的權限，才能夠接聽在裝置上的通知。





## <a name="related-links"></a>相關連結

- [本機通知 （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [遠端通知 （範例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications/)
