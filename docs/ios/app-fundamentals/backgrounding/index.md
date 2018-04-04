---
title: Backgrounding
description: 背景處理或 backgrounding 是讓應用程式在前景執行另一個應用程式時，在背景中執行工作的程序。 本指南可做為背景處理在 iOS 中的簡介。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ae3e732008c6503f511dc4be9cad874ecfe1272d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="backgrounding"></a>Backgrounding

_背景處理或 backgrounding 是讓應用程式在前景執行另一個應用程式時，在背景中執行工作的程序。本指南可做為背景處理在 iOS 中的簡介。_

行動應用程式中 backgrounding 是比傳統的多工桌面上概念本質上不同。 桌面的電腦發生各種不同的應用程式可使用，包括螢幕面積、 能力和記憶體資源。 應用程式都可以執行的並行並維持在高效能且可使用。 在行動裝置上，資源是更多限制。 很難小型螢幕上顯示多個應用程式和全速執行數個應用程式會消耗電池電量。 Backgrounding 是讓應用程式執行背景工作執行，它們需要的資源，並讓 foregrounded 應用程式和裝置保持回應之間的常數洩露。 IOS 和 Android 已佈建的 backgrounding，但它們以非常不同的方式處理。

在 iOS、 backgrounding 辨識為應用程式狀態，應用程式會移入和移出的應用程式和使用者行為根據背景狀態。 iOS 也提供幾個選項用於接線應用程式，包括要求時間 OS 才能完成的重要工作，作為一種已知背景必要應用程式，在背景中執行且重新整理應用程式的內容指定時間間隔。

在本指南和隨附的逐步解說中，我們會了解如何在背景中執行應用程式工作。 我們將涵蓋重要概念和最佳作法，並逐步建立真實世界應用程式會在背景中接收位置更新。

## <a name="contents"></a>內容

1.  [iOS 中的背景簡介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [iOS 背景技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [逐步解說：iOS 中的背景](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [iOS 背景指引](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>總結

本指南中，我們引進了不同的方式執行背景處理，在 iOS 中的動作。 我們涵蓋 iOS 應用程式狀態，並檢查 backgrounding 在 iOS 應用程式生命週期中的所扮演的角色。 此外，我們學到如何我們無法註冊的個別工作或整個應用程式在 iOS 中背景中操作。 最後，我們加強我們了解 backgrounding 在 iOS 上建置的應用程式，在背景中執行更新。



## <a name="related-links"></a>相關連結

- [在 Android 上 backgrounding](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo （範例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [位置 （範例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [簡單背景傳送 （範例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS 背景執行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
