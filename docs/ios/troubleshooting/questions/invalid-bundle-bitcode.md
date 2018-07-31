---
title: '提交至 App Store 時的錯誤: 「 在送出作業中，會偵測到無效的套件組合-不允許內嵌在 bitcode 中的選項 」'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 393c1ed81c68d21b610781dfe09de97969e031d1
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350920"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交至 App Store 時的錯誤: 「 在送出作業中，會偵測到無效的套件組合-不允許內嵌在 bitcode 中的選項 」

watchOS 應用程式和 tvOS 應用程式_需要_bitcode 時提交至 App Store。 當建立並提交 watchOS 和 tvOS 應用程式使用 Xcode 8.3 或更早版本時，可能會發生下列錯誤 （透過電子郵件通知） 時嘗試上傳至 App Store:

>無效的套件組合-提交中偵測到不允許內嵌在 bitcode 中的選項，因為無法處理的應用程式。 很可能您不使用 Xcode 中所提供的工具鏈建置應用程式。

此問題的解決方案是建置 Xcode 9 與最新版本的 Xamarin.iOS 應用程式。
