---
title: Xamarin 中的通知
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: ce19a286a288846a67b73e77d31a5da4c77a2462
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455582"
---
# <a name="notifications-in-xamarinandroid"></a>Xamarin 中的通知

本節說明如何在 Xamarin 中執行通知。 它會說明 Android 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。

## <a name="local-notifications-in-android"></a>[Android 中的本機通知](local-notifications.md)

本節說明如何在 Xamarin 中執行本機通知。 它會說明 Android 通知的各種 UI 元素，並討論與建立和顯示通知相關的 Api。

## <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>[逐步解說-在 Xamarin 中使用本機通知](local-notifications-walkthrough.md)  

本逐步解說涵蓋如何在 Xamarin Android 應用程式中使用本機通知。 它會示範建立和發佈通知的基本概念。 當使用者按一下通知選單中的通知時，它會啟動第二個活動。 

## <a name="further-reading"></a>進一步閱讀

[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase 雲端通訊 (FCM) 是一種服務，可促進行動應用程式與伺服器應用程式之間的訊息傳遞。 Firebase 雲端通訊可以用來在 Xamarin Android 應用程式中， (也稱為推播通知) 來執行遠端通知。

[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; 此 Android 開發人員主題是 Android 通知的最終指南。 它包含設計考慮區段，可協助您設計通知，使其符合 Android 使用者介面的指導方針。 它會提供有關在啟動活動時保留導覽的更多背景資訊，並說明如何在通知中顯示進度，以及如何在鎖定畫面上控制媒體播放。

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; 此 Android 服務可讓您的應用程式接聽 (，並與 Android 裝置上張貼的所有通知) 互動，而不只是您的應用程式已註冊要接收的通知。
請注意，使用者必須明確地將許可權授與您的應用程式，才能接聽裝置上的通知。

## <a name="related-links"></a>相關連結

- [本機通知 (範例) ](/samples/xamarin/monodroid-samples/localnotifications)