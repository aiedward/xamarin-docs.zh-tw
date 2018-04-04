---
title: 提交至應用程式存放區時發生錯誤:"提交中，未偵測到無效的套件組合的選項不可以內嵌在 bitcode"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: cacb9040ddc8582490c68bcfd24e80c4c4679eb4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交至應用程式存放區時發生錯誤:"提交中，未偵測到無效的套件組合的選項不可以內嵌在 bitcode"

watchOS app 和 tvOS_需要_bitcode 送交至 App Store 時。 （透過電子郵件通知） 時，建置和送出 watchOS 和 tvOS 使用 Xcode 8.3 或更早版本的應用程式，可能會發生下列錯誤時嘗試上傳的應用程式市集：

>無效的套件組合的應用程式無法處理，因為提交中偵測到不允許內嵌 bitcode 中的選項。 很可能您未在 Xcode 中提供的工具鏈具有建立應用程式。

此問題的解決方案是建置 Xcode 9 和 Xamarin.iOS 的最新版本的應用程式。
