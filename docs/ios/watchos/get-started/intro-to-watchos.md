---
title: watchOS 簡介
description: 本檔提供 watchOS 的總覽，其中描述應用程式生命週期、使用者介面類別型、螢幕大小、限制等等。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 1b71ff60ea0e23ce9d631286aec624a84f163ce5
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937490"
---
# <a name="introduction-to-watchos"></a>watchOS 簡介

> [!NOTE]
> 如需最新功能的總覽，請參閱[watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)。

## <a name="about-watchos"></a>關於 watchOS

WatchOS 應用程式解決方案有3個專案：

- **監看式延伸**模組–包含 Watch 應用程式之程式碼的專案。
- **監看式應用程式**–包含使用者介面分鏡腳本和資源。
- **IOS 父系應用**程式–此應用程式是一般的 iPhone 應用程式。 Watch 應用程式和延伸模組會配套到 iPhone 應用程式，以傳遞給使用者的監看式。

在 watchOS 1 應用程式中，延伸模組中的程式碼會在 iPhone 上執行– Apple Watch 實際上是外部顯示。 watchOS 2 和3應用程式完全在 Apple Watch 上執行。 這項差異如下圖所示：

[![此圖表顯示 watchOS 1 和 watchOS 2 （及更新版本）之間的差異](intro-to-watchos-images/arch-sml.png)](intro-to-watchos-images/arch.png#lightbox)

無論 watchOS 的目標版本為何，在 Visual Studio for Mac 的 Solution Pad，完整的解決方案會如下所示：

[![Solution Pad](intro-to-watchos-images/projectstructure-sml.png)](intro-to-watchos-images/projectstructure.png#lightbox)

WatchOS 解決方案中的*父系應用*程式是一般的 iOS 應用程式。 這是在**手機上**顯示的解決方案中唯一的專案。 此應用程式的使用案例包括教學課程、系統管理畫面，以及仲介層篩選、cacheing 等等。不過，使用者可以安裝和執行 watch 應用程式/延伸模組 **，而不需要開啟父系**應用程式，因此，如果您需要父應用程式來執行單次初始化或管理，您需要設計監看式應用程式/擴充功能來告知使用者。

雖然父系應用程式會傳遞監看式應用程式和延伸模組，但它們會在不同的沙箱中執行。

在 watchOS 1 上，他們可以透過共用應用程式群組或透過靜態函式共用資料 `WKInterfaceController.OpenParentApplication` ，這會 `UIApplicationDelegate.HandleWatchKitExtensionRequest` 在您的父系應用程式中觸發方法 `AppDelegate` （請參閱[使用父應用程式](~/ios/watchos/app-fundamentals/parent-app.md)）。

在 watchOS 2 （含）以後版本中，監看式連線架構會用來與父應用程式通訊（使用 `WCSession` 類別）。

## <a name="application-lifecycle"></a>應用程式週期

在監看式延伸模組中， `WKInterfaceController` 會為每個分鏡腳本場景建立類別的子類別。

這些 `WKInterfaceController` 類別類似于 iOS 程式 `UIViewController` 設計中的物件，但不具有對 view 的相同存取層級。
例如，您無法以動態方式將控制項加入或重新組織您的 UI。
不過，您可以隱藏和顯示控制項，並使用一些控制項來變更其大小、透明度和外觀選項。

物件的生命週期 `WKInterfaceController` 涉及下列呼叫：

- [喚醒](xref:WatchKit.WKInterfaceController.Awake*)：您應該在這個方法中執行大部分的初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) ：在監看式應用程式向使用者顯示之前，很快就會呼叫。 使用此方法可執行最後的初始化、開始動畫等。
- 此時，監看式應用程式隨即出現，且延伸模組會開始回應使用者輸入，並根據您的應用程式邏輯更新監看式應用程式的顯示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate)使用者關閉監看式應用程式之後，就會呼叫這個方法。 這個方法傳回之後，必須等到下次呼叫之後，才能修改使用者介面控制項 `WillActivate` 。 如果與 iPhone 的連線中斷，也會呼叫這個方法。
- 停用延伸模組之後，您的程式就無法存取該擴充功能。 暫止的非同步函式**將不**會被呼叫。 監看套件延伸模組可能不會使用背景處理模式。 如果該程式是由使用者重新開機，但作業系統並未終止應用程式，則第一個呼叫的方法會是 `WillActivate` 。

![應用程式生命週期總覽](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png)

## <a name="types-of-user-interface"></a>使用者介面的類型

使用者可以與您的 watch 應用程式互動的類型有三種。
所有都是使用的自訂子類別進行設計 `WKInterfaceController` ，因此，先前討論的生命週期順序會廣泛套用（通知會以的子類別進行設計 `WKUserNotificationController` ，而其本身是的子類別 `WKInterfaceController` ）：

### <a name="normal-interaction"></a>一般互動

大部分的監看式應用程式/延伸模組互動，會使用 `WKInterfaceController` 您所撰寫的子類別，以對應至監看式應用程式介面中的場景 **。** 這在[安裝](~/ios/watchos/get-started/installation.md)和[消費者入門](~/ios/watchos/get-started/index.md)文章中有詳細的說明。
下圖顯示部分的[監看套件目錄](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例的腳本。 針對此處所示的每個場景，延伸模組專案中會有對應的自訂 `WKInterfaceController` （ `LabelDetailController` 、 `ButtonDetailController` 、等 `SwitchDetailController` ）。

![一般互動範例](intro-to-watchos-images/scenes.png)

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)是 Apple Watch 的主要使用案例。 本機和遠端通知都受到支援。 與通知的互動會以兩個階段進行，稱為「短期」和「完整外觀」。

簡短的外觀會短暫顯示，並顯示監看式應用程式圖示、其名稱和標題（如所指定 `WKInterfaceController.SetTitle` ）。

完整的外觀會將系統提供的**窗扇**區域和 [關閉] 按鈕與您的自訂分鏡腳本內容結合。

`WKUserNotificationInterfaceController``WKInterfaceController`使用方法和擴充 `DidReceiveLocalNotification` `DidReceiveRemoteNotification` 。
覆寫這些方法以回應通知事件。

如需有關通知 UI 設計的詳細資訊，請參閱[Apple Watch 的人為介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![範例通知](intro-to-watchos-images/notifications.png)

## <a name="screen-sizes"></a>螢幕大小

Apple Watch 有兩個臉部大小：38mm 和42mm，兩者都有5:4 顯示比例，以及 Retina 顯示。 其可用大小如下：

- 38mm： 136 x 170 邏輯圖元（272 x 340 實體圖元）
- 42mm： 156 x 195 邏輯圖元（312 x 390 實體圖元）。

用 `WKInterfaceDevice.ScreenBounds` 來判斷您的監看式應用程式正在執行的顯示。

一般來說，使用更受限制的38mm 顯示來開發您的文字和版面配置設計會更容易，然後再相應增加。
如果您從較大的環境開始，相應減少可能會導致不好的重迭或文字截斷。

深入瞭解如何使用[螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)。

## <a name="limitations-of-watchos"></a>WatchOS 的限制

開發 watchOS 應用程式時，必須注意一些 watchOS 的限制：

- Apple Watch 裝置的儲存空間有限-請注意下載大型檔案之前的可用空間（例如 音訊或電影檔案）。

- 許多 watchOS[控制項](~/ios/watchos/user-interface/index.md)在 UIKit 中都有雷同，但它們是不同的類別（ `WKInterfaceButton` 而不是 `UIButton` ）， `WKInterfaceSwitch` `UISwitch` 而且具有一組有限的方法，相較于其 UIKit 對等專案。 此外，watchOS 還有一些控制項（例如 `WKInterfaceDate` 顯示日期和時間），UIKit 沒有。

  - 您無法將通知路由傳送至僅限監看式，或僅限 iPhone （Apple 未宣告使用者透過路由的控制項類型）。

其他已知限制/常見問題：

- Apple 將不會允許協力廠商自訂監看面。

- 允許「監看式」控制連線電話上 iTunes 的 Api 是私用的。

## <a name="further-reading"></a>深入閱讀

請參閱 Apple 的檔：

- [針對 Watch 套件進行開發](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [觀看套件程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Apple Watch 人為介面方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>相關連結

- [watchOS 3 目錄（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 目錄（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [設定和安裝](~/ios/watchos/get-started/installation.md)
- [第一次監看應用程式影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的監看式開發工具組指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
