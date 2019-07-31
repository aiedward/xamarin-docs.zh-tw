---
title: Xamarin 中已淘汰的通知技術
description: 本檔說明 iOS 通知技術, 其已被取代為 iOS 10 中引進的使用者通知架構。
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: af9f4d606dbe258b224d09f68f9b65d6f56ab28f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653902"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Xamarin 中已淘汰的通知技術

本節說明如何在 Xamarin 中執行本機和推播通知。 其中將說明 iOS 通知的各種 UI 元素, 並討論與建立和顯示通知相關的 API。

> [!IMPORTANT]
> 本節中的資訊適用于 iOS 9 和之前的版本, 它已留在這裡以支援舊版 iOS。 針對 iOS 10 和更新版本, 請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md), 以在 ios 裝置上同時支援本機和遠端通知。

## <a name="sections"></a>章節

<a name="Local Notifications In iOS" />

## <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[IOS 中的本機通知](local-notifications-in-ios.md)

本節將討論如何在 Xamarin 中執行本機通知。 其中將說明 iOS 通知的各種 UI 元素, 並討論與建立和顯示通知相關的 API。

<a name="Local Notifications Walkthrough" />

## <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[逐步解說 - 在 Xamarin.iOS 中使用本機通知](local-notifications-in-ios-walkthrough.md)

在本節中, 我們將逐步解說如何在 Xamarin iOS 應用程式中使用本機通知。 它會示範建立和發佈通知的基本概念, 而當應用程式收到警示時, 將會顯示警示。

<a name="Remote Notifications In iOS" />

## <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[IOS 中的遠端通知](remote-notifications-in-ios.md)

本節將涵蓋 iOS 中的推播通知。 其中引進了 Apple Push 通知閘道服務 (APNS), 以及它在對 iOS 應用程式發佈通知時所扮演的角色。 其中將說明如何建立啟用推播通知和討論所需的安全性憑證。 最後, 本節將討論一些應用程式伺服器為了追蹤用戶端行動裝置而必須執行的日常作業。

## <a name="related-links"></a>相關連結

- [通知 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
