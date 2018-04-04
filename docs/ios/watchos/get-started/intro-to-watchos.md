---
title: WatchOS 簡介
description: WatchOS 方案結構和限制的概觀
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 0a0adbab54fc134eaf2e69cc8088713e54b15d3b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-watchos"></a>WatchOS 簡介

> [!NOTE]
> 簽出[簡介 watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)如需最新功能的概觀。

## <a name="about-watchos"></a>關於 watchOS

WatchOS 應用程式方案有 3 的專案：

- **監看延伸**– 包含監看式應用程式的程式碼的專案。
- **監看應用程式**– 包含使用者介面的分鏡腳本和資源。
- **iOS 父應用程式**– 此應用程式不正常的 iPhone 」 應用程式。 監看式應用程式和延伸模組會集結成傳遞給使用者的監看的 iPhone 應用程式。

在 watchOS 1 應用程式，在 iPhone 上執行的擴充功能中的程式碼-Apple Watch 是有效的外部顯示器。 完全在 Apple Watch 上執行 watchOS 2 和 3 的應用程式。 下圖顯示這項差異：

[ ![](intro-to-watchos-images/arch-sml.png "在此圖中顯示 watchOS 1 和 2 （和大於） watchOS 之間的差異")](intro-to-watchos-images/arch.png#lightbox)

不論 watchOS 的版本為目標，在 Visual Studio for Mac 的方案板的完整解決方案看起來像這樣：

[![](intro-to-watchos-images/projectstructure-sml.png "方案填補")](intro-to-watchos-images/projectstructure.png#lightbox)

*父應用程式*watchOS 中解決方案是一般的 iOS 應用程式。 這是唯一的專案，在方案中，會顯示**電話**。 此應用程式的使用案例包括教學課程、 系統管理畫面和中介層篩選 cacheing 等等。不過，可能會安裝及執行監看式應用程式/擴充功能不含使用者**曾經**具有開啟父應用程式，因此如果您需要父應用程式執行一次初始化或系統管理，您需要程式設計您監看式要告訴使用者的應用程式/擴充功能的。

雖然父應用程式提供的監看式應用程式和延伸模組，其會在沙箱中執行。

它們可以 watchOS 1 上共用資料透過共用應用程式群組，或是透過靜態函式`WKInterfaceController.OpenParentApplication`，將觸發`UIApplicationDelegate.HandleWatchKitExtensionRequest`方法在父應用程式的`AppDelegate`(請參閱[父應用程式使用](~/ios/watchos/app-fundamentals/parent-app.md))。

在 watchOS 2 或更新版本的監看式連線架構用來通訊父應用程式時，使用`WCSession`類別。

## <a name="application-lifecycle"></a>應用程式週期

在 監看式副檔名的子類別`WKInterfaceController`類別建立的每個分鏡腳本場景。

這些`WKInterfaceController`類別是類似於`UIViewController`iOS 程式設計中的物件，但無法將相同層級的存取檢視。
比方說，您無法以動態方式將控制項加入，或重建您的 UI。
您可以不過，隱藏和顯示控制項，使用一些控制項，變更其大小、 透明度、 和外觀選項。

生命週期`WKInterfaceController`物件包含了下列呼叫：

- [而醒著](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/)： 您應該在這個方法來執行大部分的程式初始化。
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) ： 監看式應用程式對使用者顯示的不久之前所呼叫。 使用這個方法來執行最後時刻初始化、 開始動畫，依此類推。
- 此時，監看式應用程式會出現，擴充功能就開始回應使用者輸入並更新每個應用程式邏輯的監看式應用程式的顯示。
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/)使用者已關閉之後監看式應用程式，會呼叫這個方法。 這個方法傳回之後，使用者介面控制項無法修改，直到下次`WillActivate`呼叫。 如果在 iPhone 的連線已中斷，也會呼叫這個方法。
- 已停用擴充功能之後，就無法存取您的程式。 暫止的非同步函式**則不會**呼叫。 監看式套件擴充功能可能不會使用背景處理模式。 如果程式就會由使用者重新啟動，但未由作業系統終止應用程式，將會第一種方法呼叫`WillActivate`。

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "應用程式生命週期概觀")

## <a name="types-of-user-interface"></a>使用者介面的型別

有三種類型的使用者可以有監看式應用程式的互動。
所有已使用的自訂子類別來編寫`WKInterfaceController`，因此先前所討論的生命週期順序也適用於通用 (通知進行程式設計的子類別`WKUserNotificationController`，而後者是子類別`WKInterfaceController`):

### <a name="normal-interaction"></a>一般互動

監看式應用程式/擴充功能互動的大部分會是子類別的`WKInterfaceController`您撰寫來對應至您的監看式應用程式中的場景**Interface.storyboard**。 這點會將詳細說明[安裝](~/ios/watchos/get-started/installation.md)和[入門](~/ios/watchos/get-started/index.md)文件。
下圖顯示的某一部分[監看式套件目錄](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)範例的分鏡腳本。 以下顯示每個場景，沒有相對應的自訂`WKInterfaceController`(`LabelDetailController`， `ButtonDetailController`，`SwitchDetailController`等等) 擴充功能專案中。

![](intro-to-watchos-images/scenes.png "標準互動的範例")

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)對於 Apple Watch 的主要使用案例。 支援本機和遠端的通知。 與通知的互動，就會發生在兩個階段中，呼叫短和長時間查詢。

簡短的外觀會短暫顯示和顯示 監看式應用程式圖示，其名稱和標題 (與指定`WKInterfaceController.SetTitle`)。

系統提供長的查詢結合**窗格**區域和與您分鏡腳本為基礎的自訂內容的 [解除] 按鈕。

`WKUserNotificationInterfaceController` 擴充`WKInterfaceController`方法`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`。
覆寫這些方法來回應通知事件。

如需通知 UI 設計的詳細資訊，請參閱[Apple Watch 人性化介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "範例通知")

## <a name="screen-sizes"></a>螢幕大小

Apple Watch 有兩個圖示的大小： 38 公釐，42 公釐，都具有 5:4 顯示比例和 Retina 顯示器。 其使用的大小如下：

- 38 公釐： 136 x 170 邏輯像素 （272 x 340 實體像素為單位）
- 42 公釐： 156 x 195 邏輯像素 （312 x 390 實體像素為單位）。

使用`WKInterfaceDevice.ScreenBounds`以判斷哪個顯示器上監看式應用程式正在執行。

一般而言，很容易開發您的文字和配置設計，具有極 38 公釐 display，然後向上延展。
如果您開始使用較大的環境，向下擴充可能會導致醜陋重疊或文字截斷。

深入了解[使用的螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)。


## <a name="limitations-of-watchos"></a>WatchOS 的限制

有 watchOS 時要注意的開發 watchOS 應用程式的一些限制：

- Apple Watch 裝置具有有限的儲存體-請留意的可用空間之前 （例如下載大型檔案 音訊或電影檔案）。

- 許多 watchOS[控制項](~/ios/watchos/user-interface/index.md)UIKit，有雷同但是不同類別 (`WKInterfaceButton`而非`UIButton`，`WKInterfaceSwitch`如`UISwitch`等等)，而且有一組有限的相較於其 UIKit 方法對等項目。 此外，watchOS 有一些控制項例如`WKInterfaceDate`不具有該 UIKit （適用於顯示日期和時間）。

  - 您無法路由通知，以監看式或只 （哪種控制項使用者已透過路由已經不宣告由 Apple） 在 iPhone 中。

其他已知的限制 / 常見問題集：

- Apple 將不允許第 3 合作對象自訂監看的字體。

- 允許控制連線的電話上的 iTunes 監看式應用程式開發介面都是私用。


## <a name="further-reading"></a>進一步閱讀

請參閱 apple 文件：

* [開發監看式套件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [觀看套件程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Apple Watch 人性化介面指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>相關連結

- [watchOS 3 目錄 （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 類別目錄 （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [安裝程式並安裝](~/ios/watchos/get-started/installation.md)
- [第一個監看式應用程式的視訊](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的開發的監看式組件指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
