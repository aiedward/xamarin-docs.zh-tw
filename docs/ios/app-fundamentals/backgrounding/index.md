---
title: 在 Xamarin 中背景處理
description: 背景處理或背景處理是在另一個應用程式于前景執行時，讓應用程式在背景中執行工作的進程。 本指南可做為 iOS 中的背景處理簡介。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 5afb87eb3f84e00427072ef6c4e2c19b46de0186
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766574"
---
# <a name="backgrounding-in-xamarinios"></a>在 Xamarin 中背景處理

_背景處理或背景處理是在另一個應用程式于前景執行時，讓應用程式在背景中執行工作的進程。本指南可做為 iOS 中的背景處理簡介。_

行動應用程式中的背景處理基本上不同于桌上型電腦上多工的傳統概念。 桌上型電腦有各種可供應用程式使用的資源，包括螢幕房地產、電源和記憶體。 應用程式能夠並存執行並維持效能和可用性。 在行動裝置上，資源的限制較多。 很難以在小型螢幕上顯示一個以上的應用程式，而且以全速執行數個應用程式會耗盡電池。 背景處理在提供應用程式資源來執行正常運作所需的背景工作，以及讓 foregrounded 應用程式和裝置回應時，都是一種持續的折衷。 IOS 和 Android 都有提供背景處理，但它們會以非常不同的方式處理它。

在 iOS 中，會將背景處理辨識為應用程式狀態，並根據應用程式和使用者的行為，將應用程式移入和移出背景狀態。 iOS 也提供數個選項，可讓應用程式在背景中執行，包括要求 OS 完成重要工作、以已知的背景必要應用程式類型運作，以及重新整理應用程式的內容（指定時）間隔.

在本指南和隨附的逐步解說中，我們將學習如何在背景中執行應用程式工作。 我們將討論重要的概念和最佳作法，然後逐步建立會在背景中接收位置更新的真實世界應用程式。

## <a name="contents"></a>內容

1. [iOS 中的背景簡介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [iOS 背景技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [逐步解說：iOS 中的背景處理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [iOS 背景指引](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>總結

在本指南中，我們介紹了在 iOS 中執行背景處理的不同方式。 我們涵蓋了 iOS 應用程式狀態，並已檢查在 iOS 應用程式生命週期中背景處理扮演的角色。 此外，我們也已瞭解如何在 iOS 中註冊個別工作或整個應用程式，以在背景中作業。 最後，我們藉由建立在背景執行更新的應用程式，加強我們對 iOS 背景處理的瞭解。

## <a name="related-links"></a>相關連結

- [Android 上的背景處理](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
- [位置（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [簡單背景傳輸（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [iOS 背景執行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
