---
title: watchOS 簡介
description: 本檔提供 watchOS 的總覽，說明應用程式生命週期、使用者介面類別型、螢幕大小、限制等等。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 9cbf6e58586a223de51f1294aa1b4235b3b631f8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436278"
---
# <a name="introduction-to-watchos"></a>watchOS 簡介

> [!NOTE]
> 如需最新功能的總覽，請參閱 [watchOS 3 的簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md) 。

## <a name="about-watchos"></a>關於 watchOS

WatchOS 應用程式解決方案有3個專案：

- **監看延伸** 模組–包含 Watch 應用程式之程式碼的專案。
- **Watch 應用程式** –包含使用者介面分鏡腳本和資源。
- **IOS 父應用** 程式-此應用程式是一般 iPhone 應用程式。 Watch 應用程式和延伸模組已配套至 iPhone 應用程式，以傳遞給使用者的監看式。

在 watchOS 1 應用程式中，擴充功能中的程式碼會在 iPhone 上執行– Apple Watch 實際上是外部顯示器。 watchOS 2 和3應用程式完全在 Apple Watch 上執行。 這項差異如下圖所示：

[![WatchOS 1 和 watchOS 2 (和更大) 之間的差異如下圖所示](intro-to-watchos-images/arch-sml.png)](intro-to-watchos-images/arch.png#lightbox)

無論以何種版本的 watchOS 為目標，在 Visual Studio for Mac 的 Solution Pad 完整的解決方案看起來會像這樣：

[![Solution Pad](intro-to-watchos-images/projectstructure-sml.png)](intro-to-watchos-images/projectstructure.png#lightbox)

WatchOS 解決方案中的 *父應用程式* 是一般的 iOS 應用程式。 這是方案中唯一可在 **手機上**看到的專案。 此應用程式的使用案例包括教學課程、系統管理畫面，以及中介層篩選、cacheing 等。不過，使用者可以安裝和執行 watch 應用程式/擴充功能， **而不需要先開啟父** 應用程式，因此如果您需要父應用程式執行單次初始化或系統管理，您必須為 watch 應用程式/擴充程式撰寫程式來告訴使用者。

雖然父應用程式會提供 watch 應用程式和擴充功能，但它們會在不同的沙箱中執行。

在 watchOS 1 上，他們可以透過共用的應用程式群組或透過靜態函式共用資料 `WKInterfaceController.OpenParentApplication` ，這會 `UIApplicationDelegate.HandleWatchKitExtensionRequest` 在您的父應用程式 (中觸發方法， `AppDelegate` 請參閱 [使用父應用程式](~/ios/watchos/app-fundamentals/parent-app.md)) 。

在 watchOS 2 或更新版本上，會使用「監看式」連線架構，透過類別與父應用程式進行通訊 `WCSession` 。

## <a name="application-lifecycle"></a>應用程式週期

在監看式擴充功能中，會為每個分鏡腳本 `WKInterfaceController` 場景建立類別的子類別。

這些 `WKInterfaceController` 類別類似于 iOS 程式 `UIViewController` 設計中的物件，但不具有與該視圖相同的存取層級。
比方說，您無法以動態方式將控制項新增至 UI 或重新組織您的 UI。
不過，您可以隱藏和顯示控制項，並透過一些控制項變更其大小、透明度和外觀選項。

物件的生命週期 `WKInterfaceController` 包含下列呼叫：

- [喚醒](xref:WatchKit.WKInterfaceController.Awake*) ：您應該在此方法中執行大部分的初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) ：在 Watch 應用程式向使用者顯示之前立即呼叫。 使用這個方法可執行最後的初始化、開始動畫等等。
- 此時，Watch 應用程式隨即出現，且延伸模組會開始回應使用者輸入，並根據您的應用程式邏輯更新 Watch 應用程式的顯示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) 使用者關閉 Watch 應用程式之後，就會呼叫這個方法。 在這個方法傳回之後，必須等到下一次呼叫之後，才能修改使用者介面控制項 `WillActivate` 。 如果與 iPhone 的連接中斷，也會呼叫這個方法。
- 停用擴充功能之後，您的程式就無法存取它。 暫止的非同步函數 **將不** 會被呼叫。 Watch 套件延伸模組可能不會使用背景處理模式。 如果使用者重新開機程式，但作業系統尚未終止應用程式，則第一個呼叫的方法會是 `WillActivate` 。

![應用程式生命週期總覽](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png)

## <a name="types-of-user-interface"></a>消費者介面的類型

使用者可與您的 watch 應用程式互動的互動類型有三種。
所有都是使用的自訂子類別進行程式設計 `WKInterfaceController` ，因此先前討論的生命週期順序會套用通用 (通知會以的子類別進行 `WKUserNotificationController` 程式設計，而這本身就是) 的子類別 `WKInterfaceController` ：

### <a name="normal-interaction"></a>一般互動

大部分的監看式應用程式/延伸模組互動將會使用 `WKInterfaceController` 您所撰寫的子類別，以對應至 watch 應用程式 **介面**中的場景。分鏡腳本。 本主題將在 [安裝](~/ios/watchos/get-started/installation.md) 和 [消費者入門](~/ios/watchos/get-started/index.md) 文章中詳細說明。
下圖顯示「 [監看式套件目錄](/samples/xamarin/ios-samples/watchos-watchkitcatalog) 」範例的分鏡腳本部分。 針對此處所示的每個場景，擴充功能專案中有對應的自訂 `WKInterfaceController` (、、等 `LabelDetailController` `ButtonDetailController` `SwitchDetailController` ) 。

![一般互動範例](intro-to-watchos-images/scenes.png)

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md) 是 Apple Watch 的主要使用案例。 本機和遠端通知都受到支援。 與通知的互動會以兩個階段進行，稱為「短期」和「長期搜尋」。

簡短的外觀會短暫顯示，並顯示 watch 應用程式圖示、名稱和標題 (如) 所指定 `WKInterfaceController.SetTitle` 。

長時間的外觀會結合系統提供的 **窗扇** 區域和 [關閉] 按鈕與您自訂的分鏡腳本型內容。

`WKUserNotificationInterfaceController` 擴充 `WKInterfaceController` 方法 `DidReceiveLocalNotification` 和 `DidReceiveRemoteNotification` 。
覆寫這些方法以回應通知事件。

如需有關通知 UI 設計的詳細資訊，請參閱 [Apple Watch 的人體介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![範例通知](intro-to-watchos-images/notifications.png)

## <a name="screen-sizes"></a>螢幕大小

Apple Watch 有兩種臉部尺寸：38mm 和42mm，兩者都有5:4 顯示比例和 Retina 顯示。 其可用大小如下：

- 38mm： 136 x 170 邏輯圖元 (272 x 340 實體圖元) 
- 42mm： 156 x 195 邏輯圖元 (312 x 390 實體圖元) 。

用 `WKInterfaceDevice.ScreenBounds` 來判斷您的 Watch 應用程式執行所在的畫面。

一般來說，您可以更輕鬆地使用較受限制的38mm 顯示來開發文字和版面配置設計，然後擴大。
如果您從較大的環境開始，相應減少可能會導致不美觀的重迭或文字截斷。

深入瞭解如何使用 [螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)。

## <a name="limitations-of-watchos"></a>WatchOS 的限制

開發 watchOS 應用程式時，有一些 watchOS 限制要注意：

- Apple Watch 裝置的儲存空間有限，請注意下載大型檔案之前的可用空間 (例如 音訊或電影檔案) 。

- 許多 watchOS [控制項](~/ios/watchos/user-interface/index.md) 在 UIKit 中都有雷同，但它們是不同的類別 (`WKInterfaceButton` 而不是 `UIButton` ) ， `WKInterfaceSwitch` `UISwitch` 而且有一組有限的方法，相較于其 UIKit 對等專案。 此外，watchOS 還有一些控制項，例如 `WKInterfaceDate` (，以顯示 UIKit 沒有的日期和) 時間。

  - 您無法將通知路由傳送至僅限 Watch，或僅限 iPhone (Apple) 尚未宣告使用者透過路由傳送的控制項種類。

一些其他已知的限制/常見問題：

- Apple 將不會允許協力廠商自訂監看臉部。

- 允許 Watch 控制連線電話上 iTunes 的 Api 是私用的。

## <a name="further-reading"></a>深入閱讀

請參閱 Apple 的檔：

- [開發 Watch 套件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [觀看套件程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Apple Watch 人體介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>相關連結

- [watchOS 3 目錄 (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 Catalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [設定和安裝](~/ios/watchos/get-started/installation.md)
- [第一次觀看應用程式影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 開發 Watch 套件指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)