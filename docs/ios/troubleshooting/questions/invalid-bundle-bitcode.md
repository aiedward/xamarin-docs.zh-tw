---
title: 提交至 App Store 時發生錯誤：「在提交時，偵測到不允許內嵌在 bitcode 中的組合-選項」
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: c6a03fa3327e81f577f85b05a033d9c97495eb2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031068"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交至 App Store 時發生錯誤：「在提交時，偵測到不允許內嵌在 bitcode 中的組合-選項」

watchOS apps 和 tvOS apps 在提交至 App Store 時，_需要_bitcode。 使用 Xcode 8.3 或更早版本來建立和提交 watchOS 和 tvOS 應用程式時，可能會在嘗試上傳至 App Store 時，發生下列錯誤（透過電子郵件通知）：

>套件組合無效-無法處理應用程式，因為提交中偵測到不允許內嵌在 bitcode 中的選項。 您可能不會使用 Xcode 中提供的工具鏈來建立應用程式。

此問題的解決方法是使用 Xcode 9 和最新版本的 Xamarin 來建立應用程式。
