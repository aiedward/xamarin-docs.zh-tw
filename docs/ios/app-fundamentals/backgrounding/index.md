---
title: 在 Xamarin 中背景處理
description: 背景處理或背景處理是讓應用程式在背景執行其他應用程式時，在背景中執行工作的程式。 本指南是 iOS 中的背景處理簡介。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 3d50bf91502e7a3b7331348a61af50a5c789f838
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432418"
---
# <a name="backgrounding-in-xamarinios"></a>在 Xamarin 中背景處理

_背景處理或背景處理是讓應用程式在背景執行其他應用程式時，在背景中執行工作的程式。本指南是 iOS 中的背景處理簡介。_

行動裝置應用程式中的背景處理基本上與桌面上多工的傳統概念不同。 桌上型電腦有各種可供應用程式使用的資源，包括螢幕房地產、電源和記憶體。 應用程式可以並存執行並保持效能和可用性。 在行動裝置上，資源更受限制。 很難在小型螢幕上顯示一個以上的應用程式，而且以全速執行數個應用程式會耗盡電池。 背景處理是在為應用程式提供資源以執行正常運作所需的背景工作，以及讓 foregrounded 應用程式和裝置回應的情況之間的固定折衷。 IOS 和 Android 都有背景處理的布建，但以非常不同的方式處理。

在 iOS 中，背景處理會被辨識為應用程式狀態，而應用程式會根據應用程式和使用者的行為，移入和移出背景狀態。 iOS 也提供數個選項，可讓應用程式在背景中執行，包括詢問 OS 的時間以完成重要工作、以已知的背景必要應用程式類型運作，以及在指定的間隔重新整理應用程式的內容。

在本指南和隨附的逐步解說中，我們將瞭解如何在背景執行應用程式工作。 我們將涵蓋重要概念和最佳作法，然後逐步建立真實世界的應用程式，以在背景中接收位置更新。

## <a name="contents"></a>目錄

1. [iOS 中的背景處理簡介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [iOS 背景處理技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [逐步解說：iOS 中的背景](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [iOS 背景處理指南](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>摘要

在本指南中，我們引進了在 iOS 中執行背景處理的不同方式。 我們已討論過 iOS 應用程式狀態，並在 iOS 應用程式生命週期中檢查角色背景處理的播放。 此外，我們已瞭解如何註冊個別工作或整個應用程式，以在 iOS 的背景中操作。 最後，我們藉由建立在背景中執行更新的應用程式，加強我們對 iOS 背景處理的瞭解。

## <a name="related-links"></a>相關連結

- [Android 上的背景處理](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (範例) ](/samples/xamarin/ios-samples/lifecycledemo)
- [位置 (範例) ](/samples/xamarin/ios-samples/location)
- [簡單的背景傳輸 (範例) ](/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [iOS 背景執行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)