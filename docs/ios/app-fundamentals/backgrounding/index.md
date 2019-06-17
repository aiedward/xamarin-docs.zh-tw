---
title: 在 Xamarin.iOS 中背景
description: 背景處理，或背景就是讓應用程式在背景中執行工作，而另一個應用程式會在前景執行的處理程序。 本指南可在 iOS 中處理的背景介紹。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: a4f5112b6e77ab6e00453c19c766d1e905df1144
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946653"
---
# <a name="backgrounding-in-xamarinios"></a>在 Xamarin.iOS 中背景

_背景處理，或背景就是讓應用程式在背景中執行工作，而另一個應用程式會在前景執行的處理程序。本指南可在 iOS 中處理的背景介紹。_

Ios 行動應用程式中的背景是本質上不同於傳統多工作業在桌面上的概念。 桌面的機器上有各種不同的應用程式，包括螢幕使用空間、 電源和記憶體的可用資源。 應用程式是由並行執行，並仍提供高效能且可使用。 在行動裝置上，資源是更多的限制。 很難在小型螢幕上，顯示多個應用程式，並以全速執行數個應用程式就會耗盡電池。 背景是讓應用程式執行所需，執行背景工作的資源，並保留 foregrounded 應用程式和裝置的回應式的常數折衷辦法。 IOS 和 Android 的已佈建的背景，但它們以非常不同的方式處理。

在 iOS 中，背景辨識為應用程式狀態，，和應用程式移出根據行為的應用程式和使用者背景狀態。 iOS 也提供數個接線應用程式在背景進行，包括要求以完成重要工作，作為一種已知的背景必要應用程式，運作時間的 OS 中執行的選項並重新整理應用程式的內容，指定時間間隔。

在本指南和隨附的逐步解說中，我們要了解如何在背景中執行應用程式工作。 我們將涵蓋的重要概念和最佳作法，並逐步建立實際的應用程式會在背景中接收位置更新。

## <a name="contents"></a>內容

1.  [iOS 中的背景簡介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [應用程式生命週期示範](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [iOS 背景技術](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [逐步解說：iOS 中的背景處理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [iOS 背景指引](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>總結

本指南中，我們引進了執行背景處理，在 iOS 中的不同方式。 我們涵蓋 iOS 應用程式狀態，並檢查背景在 iOS 應用程式生命週期中的所扮演的角色。 此外，我們已了解我們如何可以註冊個別的工作或整個應用程式在 iOS 中的背景中運作。 最後，我們會根據我們了解的背景在 iOS 上建置的應用程式，在背景中執行更新。



## <a name="related-links"></a>相關連結

- [在 Android 上背景](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo （範例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [位置 （範例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [簡單背景傳送 （範例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS 背景執行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
