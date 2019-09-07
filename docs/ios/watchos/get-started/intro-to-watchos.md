---
title: WatchOS 簡介
description: 本文件提供 watchOS 應用程式生命週期、 使用者介面型別、 螢幕大小、 限制和多個描述的概觀。
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: df1177d55510571da3369d298bd05aa9bd0734a9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767934"
---
# <a name="introduction-to-watchos"></a>WatchOS 簡介

> [!NOTE]
> 請參閱[watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)如需最新功能的概觀。

## <a name="about-watchos"></a>關於 watchOS

WatchOS 應用程式方案具有 3 個專案：

- **監看的延伸模組**– 包含監看式應用程式的程式碼的專案。
- **監看應用程式**– 包含使用者介面的分鏡腳本和資源。
- **父應用程式 iOS** – 此應用程式是正常的 iPhone 應用程式。 Watch 應用程式和擴充功能會結合成傳遞給使用者的監看式的 iPhone 應用程式。

在 watchOS 1 應用程式，在 iPhone 上執行的擴充功能中的程式碼 – Apple Watch 實際上是外部顯示器。 watchOS 2 和 3 的應用程式會執行完全在 Apple Watch 上。 下圖顯示這項差異：

[![](intro-to-watchos-images/arch-sml.png "此圖表顯示 watchOS 1 和 watchOS 2 （及更新版本）之間的差異")](intro-to-watchos-images/arch.png#lightbox)

不論 watchOS 版本為目標時，Visual Studio for Mac 的 Solution Pad 中完整的解決方案會看起來像這樣：

[![](intro-to-watchos-images/projectstructure-sml.png "Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

*父應用程式*在 watchOS 中解決方案是一般的 iOS 應用程式。 這是唯一的專案在方案中，會顯示**電話**。 此應用程式的使用案例會包括教學課程、 系統管理畫面，以及中介層篩選，cacheing 等等。不過，很可能會讓使用者安裝及執行監看式應用程式/擴充功能不含**曾經**具有開啟父應用程式，因此如果您需要父應用程式，執行單次初始化或系統管理，您需要設計您的監看式若要告訴使用者的應用程式/擴充功能的。

雖然父應用程式提供的 watch 應用程式和延伸模組，其會在不同的沙箱中執行。

WatchOS 1 上，他們可以分享資料，透過共用的應用程式群組，或透過靜態函式`WKInterfaceController.OpenParentApplication`，這將會觸發`UIApplicationDelegate.HandleWatchKitExtensionRequest`方法中的父代應用程式`AppDelegate`(請參閱[父應用程式使用](~/ios/watchos/app-fundamentals/parent-app.md))。

在 watchOS 2 或更新版本的監看式連線架構用來與父系應用程式通訊使用`WCSession`類別。

## <a name="application-lifecycle"></a>應用程式週期

在 監看的子類別副檔名`WKInterfaceController`類別會為每個分鏡腳本場景中建立。

這些`WKInterfaceController`類別是類似於`UIViewController`iOS 程式設計中的物件，但並沒有存取檢視的相同層級。
比方說，您無法以動態方式將控制項加入或重新建構您的 UI。
您可以不過，隱藏和顯示控制項，與一些控制項中，變更其大小、 透明度及外觀選項。

生命週期`WKInterfaceController`物件包含下列呼叫：

- [喚醒](xref:WatchKit.WKInterfaceController.Awake*)：您應該在這個方法中執行大部分的初始化。
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) ：很快就會在監看式應用程式向使用者顯示之前呼叫。 使用此方法來執行最後一個時間初始化、 啟動動畫，依此類推。
- 此時，監看式應用程式會出現，擴充功能可讓您開始回應使用者輸入，並更新每個應用程式邏輯的監看式應用程式的顯示。
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate)使用者已關閉之後 Watch 應用程式，會呼叫這個方法。 這個方法傳回之後，使用者介面控制項無法修改，直到下次`WillActivate`呼叫。 如果 iPhone 的連線已中斷，也會呼叫這個方法。
- 已停用擴充功能之後，就無法存取您的程式。 暫止的非同步函式**不會**呼叫。 監看式套件延伸模組不可以使用背景處理模式。 如果使用者的程式就會重新啟動，但未由作業系統終止應用程式，將會呼叫第一個方法`WillActivate`。

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "應用程式生命週期概觀")

## <a name="types-of-user-interface"></a>類型的使用者介面

有三種類型的使用者可以擁有與您的監看式應用程式的互動。
所有設計方式使用的自訂子類別`WKInterfaceController`，因此先前所討論的生命週期順序適用於通用 (通知與子類別的設計方式`WKUserNotificationController`，而其本身是子類別`WKInterfaceController`):

### <a name="normal-interaction"></a>一般的互動

子類別會監看式應用程式/擴充功能互動的大部分`WKInterfaceController`您撰寫來對應至您的監看式應用程式中的場景**Interface.storyboard**。 這會詳細涵蓋[安裝](~/ios/watchos/get-started/installation.md)並[開始使用](~/ios/watchos/get-started/index.md)文章。
下圖顯示的某一部分[監看式套件目錄](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例的分鏡腳本。 以下顯示每個場景，沒有相對應的自訂`WKInterfaceController`(`LabelDetailController`， `ButtonDetailController`，`SwitchDetailController`等) 在擴充功能專案。

![](intro-to-watchos-images/scenes.png "一般互動範例")

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)對於 Apple Watch 上的主要使用案例。 支援本機及遠端通知。 在兩個階段中，呼叫簡短和長時間查詢，就會發生與通知互動。

簡短看起來會簡短地顯示，並顯示 監看式應用程式圖示，其名稱和標題 (依照`WKInterfaceController.SetTitle`)。

系統提供長的查詢結合**窗格**區域和與您的分鏡腳本為基礎的自訂內容的 [解除] 按鈕。

`WKUserNotificationInterfaceController` 擴充`WKInterfaceController`的方法`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`。
覆寫這些方法來回應通知事件。

如需有關通知 UI 設計的詳細資訊，請參閱[Apple Watch 人性化介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "範例通知")

## <a name="screen-sizes"></a>螢幕大小

Apple Watch 有兩個臉部大小：38mm 和42mm，兩者都有5:4 顯示比例，以及 Retina 顯示。 其容易使用的大小如下：

- 38mm:136 x 170 邏輯圖元（272 x 340 實體圖元）
- 42mm:156 x 195 邏輯圖元（312 x 390 實體圖元）。

使用`WKInterfaceDevice.ScreenBounds`來判斷哪個顯示器上執行您的監看式應用程式。

一般而言，很容易開發您的文字和配置設計，以更受條件約束的 38 公釐顯示，並再相應增加。
如果您啟動與較大的環境，相應減少可能會導致不美觀的重疊或文字截斷。

深入了解[使用 螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)。

## <a name="limitations-of-watchos"></a>WatchOS 的限制

有 watchOS 要注意的開發 watchOS 應用程式時的一些限制：

- Apple Watch 裝置具有有限的儲存體-之前下載大型檔案 （例如必須注意的可用空間。 音訊或電影檔案）。

- 許多 watchOS[控制項](~/ios/watchos/user-interface/index.md)UIKit，有雷同，但不同的類別 (`WKInterfaceButton`而非`UIButton`，`WKInterfaceSwitch`如`UISwitch`等)，而且有一組有限的相較於其 UIKit 方法對等項目。 此外，watchOS 有一些控制項這類`WKInterfaceDate`不具有該 UIKit （適用於顯示日期和時間）。

  - 您無法路由傳送通知，以監看式或只 （哪種控制項使用者已透過路由尚未宣 apple） iPhone。

其他已知的限制 / 常見問題集：

- Apple 不允許第 3 方自訂監看的臉部。

- 允許控制連線的手機上的 iTunes 監看式的 Api 都是私用。

## <a name="further-reading"></a>進一步閱讀

請參閱 Apple 的文件：

- [針對 監看式套件進行開發](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [觀看套件程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Apple Watch 人性化介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>相關連結

- [watchOS 3 目錄 （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 目錄 （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [設定和安裝](~/ios/watchos/get-started/installation.md)
- [Watch 應用程式上的影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的開發的監看式組件指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
