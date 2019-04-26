---
title: 在 Xamarin.iOS 中已被取代的通知技術
description: 本文件說明已被使用者通知架構，在 iOS 10 中導入的 iOS 通知技術。
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: 7b0c2d99412f7a34c0e9c95a282d5d74dbe8dd78
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085645"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>在 Xamarin.iOS 中已被取代的通知技術

本節說明如何實作本機和推播通知，在 Xamarin.iOS 中。 它會說明 iOS 通知的各種 UI 項目，並討論 API 的涉及建立和顯示通知。

> [!IMPORTANT]
> 在本節中的資訊適用於 iOS 9 和之前，它已保留這裡以支援較舊 iOS 版本。 適用於 iOS 10 及更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)支援 iOS 裝置上的本機和遠端的通知。

## <a name="sections"></a>章節

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[在 iOS 中的本機通知](local-notifications-in-ios.md)

本節將討論如何在 Xamarin.iOS 中實作本機通知。 它會說明 iOS 通知的各種 UI 項目，並討論 API 的涉及建立和顯示通知。

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[逐步解說 - 在 Xamarin.iOS 中使用本機通知](local-notifications-in-ios-walkthrough.md)

這一節我們將逐步解說如何在 Xamarin.iOS 應用程式中使用本機通知。 它將示範如何建立和發行通知，就會出現警示時收到由應用程式的基本概念。

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[在 iOS 中的遠端通知](remote-notifications-in-ios.md)

本章節將涵蓋在 iOS 中的推播通知。 它引入了 Apple 推播通知閘道服務 (APNS) 和它發行的通知至 iOS 應用程式中所扮演的角色。 它會說明如何建立啟用推播通知和討論所需的安全性憑證。 最後這一節將討論一些應用程式伺服器必須執行來追蹤用戶端行動裝置的維護工作。

## <a name="related-links"></a>相關連結

- [通知 （範例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
