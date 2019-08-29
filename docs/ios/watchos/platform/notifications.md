---
title: watchOS 在 Xamarin 中的通知
description: 本文件說明如何使用 Xamarin 在 watchOS 通知。 它討論建立產生通知，或測試通知的通知控制站。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 39c77b4016027171a4e76bc4fb15c77d733cf5ba
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065364"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS 在 Xamarin 中的通知

Watch 應用程式可以接收通知，如果包含的 iOS 應用程式支援它們。 沒有內建通知處理，因此您不這樣做*需要*來新增其他通知支援，如下所述，不過如果您想要自訂通知行為和外觀再繼續閱讀。

請參閱[iOS 通知](~/ios/platform/user-notifications/deprecated/index.md)如需有關將通知支援新增至方案中的 iOS 應用程式的文件。

## <a name="creating-notification-controllers"></a>建立通知控制器

在腳本上通知控制器會有一種特殊的 segue 觸發它們。 當您將新**通知介面控制器**到分鏡腳本自動會有附加的 segue:

![](notifications-images/notification-storyboard1.png "新的通知介面控制站，使用附加的 segue")

已選取 通知的 segue 時您可以編輯其屬性：

![](notifications-images/notification-storyboard2.png "選取通知的 segue")

您已自訂控制器之後，看起來如下列範例從 WatchKitCatalog:

![](notifications-images/notifications-segue.png "通知屬性")


有兩種類型的通知：

- 系統所定義的「**短期**不受滾動」靜態視圖。

- **長時間查詢**-可捲動，所以您所定義的自訂檢視 ！ 可以指定更簡單、 靜態版本和更複雜的動態版本。

### <a name="short-look-notification-controller"></a>短外觀通知控制器

短外觀 UI 是由應用程式圖示、 應用程式名稱和通知的標題字串所組成。

如果使用者不會忽略通知，系統會自動切換到長時間查詢通知，以提供更多的資訊。


### <a name="long-look-notification-controller"></a>長時間查詢通知控制器

作業系統會決定是否要顯示的數項因素為基礎的靜態或動態檢視。 您必須提供靜態的介面，並可選擇性地也包含動態通知的介面。

#### <a name="static"></a>Static

簡單且快速地顯示，應該是靜態的檢視。

![](notifications-images/notification-static.png "[靜態] 檢視")

#### <a name="dynamic"></a>動態

動態檢視可以顯示更多資料，並提供更多的互動性。

![](notifications-images/notification-dynamic.png "動態檢視")


## <a name="generating-notifications"></a>產生的通知

通知可以來自遠端伺服器 ([Apple 推播通知服務](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)，或 APNS) 或 iOS 應用程式可在本機產生。

請參閱[iOS 通知逐步解說](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md)如需如何產生本機通知，而[WatchNotifications 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)如需實用範例。

必須有本機通知`AlertTitle`設定為顯示在 Apple Watch 中-`AlertTitle`字串會顯示於短外觀的介面。 同時`AlertTitle`並`AlertBody`會顯示在 [通知] 清單中; 而`AlertBody`會顯示在長時間查詢介面。

此螢幕擷取畫面顯示`AlertTitle`顯示在 [通知] 清單中，而`AlertBody`會顯示在長時間查詢介面 (使用[範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)):

![](notifications-images/watch-notificationslist-sml.png "此螢幕擷取畫面顯示在 [通知] 清單中顯示 AlertTitle") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody 在長時間查詢介面中顯示")

## <a name="testing-notifications"></a>測試通知

通知 （本機和遠端），才能正確地測試在裝置上，不過它們可以使用來模擬 **.json** iOS 模擬器中的檔案。

### <a name="testing-on-apple-watch"></a>在 Apple Watch 上測試

在測試時在 Apple Watch 上的通知，請記住[Apple 的文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)指出下列：

> 當使用者的 iPhone 上的其中一個應用程式的本機或遠端通知抵達時，iOS 會決定是否要顯示該通知，在 iPhone 上或在 Apple Watch 上。

這事實 alluding 該 iOS 可讓您決定在 iphone 或 watch，是否會出現通知。 配對的 iPhone 為作用中時收到通知，通知是否有可能顯示在 iPhone 上與*不*路由傳送至監看式。

若要確保通知會出現在 監看式，關閉 iPhone 畫面 （一次按下電源按鈕），或讓它進入睡眠狀態。 如果配對的監看式在範圍內，具有電源，而且會在穿戴期間手腕上，通知將會那里路由傳送，並會出現在 監看式 （伴隨細微）。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模擬器上測試

您*必須*iOS 模擬器中測試通知模式時，提供測試 JSON 承載。 在 設定路徑**自訂執行引數**視窗在 Visual Studio for mac。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 會顯示其他選項，監控擴充功能會設定為當**啟始專案**。
監看式延伸模組專案上按一下滑鼠右鍵，然後選擇 **執行與 > 自訂參數...** :

[![](notifications-images/runwith-customparams-sml.png "執行具有自訂屬性")](notifications-images/runwith-customparams.png#lightbox)

這會開啟**執行引數**視窗，其中包含**WatchKit** ] 索引標籤。選取 [**通知**，並提供 JSON 承載，然後按**Execute**在模擬器中啟動監看式應用程式：

[![](notifications-images/runwith-execargs-sml.png "選取通知承載的預設值")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要設定測試通知裝載在 Visual Studio 以滑鼠右鍵按一下，監看式擴充功能，以編輯**專案屬性**。 移至**偵錯**區段，然後從清單中 （它會自動將會列出專案中包含的所有 JSON 檔案） 選取通知 JSON 檔案。

[![](notifications-images/runwith-execargs-sml-vs.png "選取通知 JSON 檔案")](notifications-images/runwith-execargs-vs.png#lightbox)

監看式延伸模組時**啟始專案**，Visual Studio 會顯示其他選項，如下所示。 選擇其中一個**通知**監看式應用程式的啟動選項**通知**（使用 [屬性] 視窗中選取的 JSON 檔案） 的模式：

![](notifications-images/runwith-vs.png "[裝置] 功能表")

-----

在模擬器上測試使用預設承載的 JSON 檔案時，預設通知控制器看起來像這樣：

![](notifications-images/notification-debug-sml.png "範例通知")

您也可使用[命令列](~/ios/watchos/troubleshooting.md#command_line)啟動 iOS 模擬器。

### <a name="example-notification-payload"></a>通知承載範例

在 [監看式套件目錄](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)有範例是範例承載 JSON 檔案**NotificationPayload.json** （如下所示）。

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>相關連結

- [WatchNotifications （本機通知） （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)
- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple Watch 套件通知文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
