---
title: Google 傳訊
description: 本節包含的指南說明如何使用 Google 郵件服務來執行 Xamarin Android 應用程式。
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: bfcc526d1787caede4361030f5bf6718a59672b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754396"
---
# <a name="google-messaging"></a>Google 傳訊

_本節包含的指南說明如何使用 Google 郵件服務來執行 Xamarin Android 應用程式。_

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Firebase Cloud Messaging](firebase-cloud-messaging.md)

Firebase 雲端通訊（FCM）是一種服務，可在行動應用程式和伺服器應用程式之間進行訊息處理。 FCM 是 Google Google 雲端通訊的後繼。 本文概述 FCM 的運作方式，並提供逐步程式來取得認證，讓您的應用程式可以使用 FCM 服務。

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Firebase 雲端通訊的遠端通知](remote-notifications-with-fcm.md)

本逐步解說提供如何在 Xamarin Android 應用程式中使用 Firebase 雲端通訊來執行遠端通知（也稱為推播通知）的逐步說明。 其中說明如何執行與 Firebase 雲端通訊（FCM）通訊所需的各種類別，提供如何設定 Android 資訊清單以存取 FCM 的範例，並示範使用 Firebase 的下游訊息控制.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google Cloud Messaging](google-cloud-messaging.md)

本節概述 Google 雲端通訊（GCM）如何在您的應用程式與應用程式伺服器之間路由傳送訊息，並提供逐步程式來取得認證，讓您的應用程式可以使用 GCM 服務。 （請注意，GCM 已被 FCM 取代）。

> [!NOTE]
> GCM 已被[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)（FCM）取代。
> GCM 伺服器和用戶端 Api 已[淘汰](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)，將不再于2019年4月11日推出。

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Google 雲端通訊的遠端通知](remote-notifications-with-gcm.md)

本節提供逐步解說，說明如何使用 Google 雲端通訊在 Xamarin 中執行遠端通知。
其中說明在 Android 應用程式中啟用 Google 雲端通訊時，必須利用的各種元件。
