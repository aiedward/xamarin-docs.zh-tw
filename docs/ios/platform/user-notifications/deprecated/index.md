---
title: 在 Xamarin 中淘汰的通知技術
description: 本檔說明在 iOS 10 引進的 iOS 通知技術，已被取代為使用者通知架構。
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2016
ms.openlocfilehash: 0822f630d74563f5e6e0dcefa456cf5f5c07a8f5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436940"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>在 Xamarin 中淘汰的通知技術

本節說明如何在 Xamarin 中執行本機和推播通知。 它將說明 iOS 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。

> [!IMPORTANT]
> 本章節中的資訊適用于 iOS 9 和更早版本，其為支援較舊的 iOS 版本。 若為 iOS 10 和更新版本，請參閱 [使用者通知架構指南](~/ios/platform/user-notifications/index.md) ，以在 ios 裝置上支援本機和遠端通知。

## <a name="sections"></a>章節

<a name="Local Notifications In iOS"></a>

## <a name="local-notifications-in-ios"></a>[IOS 中的本機通知](local-notifications-in-ios.md)

本節將討論如何在 Xamarin 中執行本機通知。 它將說明 iOS 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。

<a name="Local Notifications Walkthrough"></a>

## <a name="walkthrough---using-local-notifications-in-xamarinios"></a>[逐步解說-在 Xamarin 中使用本機通知](local-notifications-in-ios-walkthrough.md)

在本節中，我們將逐步解說如何在 Xamarin iOS 應用程式中使用本機通知。 它會示範建立和發佈通知的基本概念，此通知會在應用程式收到警示時顯示警示。

<a name="Remote Notifications In iOS"></a>

## <a name="remote-notifications-in-ios"></a>[IOS 中的遠端通知](remote-notifications-in-ios.md)

本節將涵蓋 iOS 中的推播通知。 它引進了 Apple 推播通知閘道服務 (APNS) ，以及它在發佈通知至 iOS 應用程式時所扮演的角色。 它將說明如何建立啟用推播通知和討論所需的安全性憑證。 最後，本節將討論一些應用程式伺服器必須執行的日常工作，以追蹤用戶端行動裝置。

## <a name="related-links"></a>相關連結

- [ (範例) 的通知 ](/samples/xamarin/ios-samples/notifications)