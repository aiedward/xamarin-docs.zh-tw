---
title: 提交至 App Store 時的錯誤：「 在送出作業中，會偵測到無效的套件組合-不允許內嵌在 bitcode 中的選項 」
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 867ad29abfa6a38971b60ac9ebf181905949dafd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421725"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交至 App Store 時的錯誤：「 在送出作業中，會偵測到無效的套件組合-不允許內嵌在 bitcode 中的選項 」

watchOS 應用程式和 tvOS 應用程式_需要_bitcode 時提交至 App Store。 當建立並提交 watchOS 和 tvOS 應用程式使用 Xcode 8.3 或更早版本時，可能會發生下列錯誤 （透過電子郵件通知） 時嘗試上傳至 App Store:

>無效的套件組合-提交中偵測到不允許內嵌在 bitcode 中的選項，因為無法處理的應用程式。 很可能您不使用 Xcode 中所提供的工具鏈建置應用程式。

此問題的解決方案是建置 Xcode 9 與最新版本的 Xamarin.iOS 應用程式。
