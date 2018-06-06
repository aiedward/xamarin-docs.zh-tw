---
title: watchOS Xamarin 中的通知
description: 本文件說明如何使用 Xamarin watchOS 通知。 其中也會討論建立產生通知，以及測試通知的通知控制站。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 42b0354f19a9e0c31b7a859d598526fddad726cd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791906"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS Xamarin 中的通知

監看式應用程式可以收到通知，如果包含的 iOS 應用程式支援它們。 沒有內建的通知處理，因此您不會執行*需要*加入其他通知支援，如下所述，不過如果您想要自訂通知行為，並在讀取外觀。

請參閱[iOS 通知](~/ios/platform/user-notifications/deprecated/index.md)如需有關將通知的支援加入至方案中的 iOS 應用程式的文件。

## <a name="creating-notification-controllers"></a>建立通知控制站

在腳本上通知控制器會有一種特殊型別的話題觸發它們。 當您拖曳新**通知介面控制器**到分鏡腳本它就會自動擁有附加 segue:

![](notifications-images/notification-storyboard1.png "新的通知介面控制站，以附加 segue")

已選取 通知的話題時您可以編輯其屬性：

![](notifications-images/notification-storyboard2.png "通知話題選取")

自訂控制器之後看起來就像從 WatchKitCatalog 此範例：

![](notifications-images/notifications-segue.png "通知屬性")


有兩種類型的通知：

- **Short 外觀**-非可捲動的靜態檢視由系統定義的。

- **長時間查詢**-可捲動的您所定義的自訂檢視 ！ 可以指定更簡單、 靜態版本和更複雜的動態版本。

### <a name="short-look-notification-controller"></a>簡短查詢通知控制站

Short 外觀 UI 是由應用程式圖示，應用程式名稱及通知的標題字串所組成。

如果使用者不會忽略的通知，系統會自動切換長時間查詢通知可提供詳細資訊。


### <a name="long-look-notification-controller"></a>長時間查詢通知控制站

作業系統會決定是否要顯示的數項因素為基礎的靜態或動態檢視。 您必須提供靜態的介面，並可以選擇性地也包含 通知的動態介面。

#### <a name="static"></a>Static

簡單且快速地顯示，應該是靜態檢視。

![](notifications-images/notification-static.png "[靜態] 檢視")

#### <a name="dynamic"></a>動態

動態檢視顯示詳細資料，並提供更多的互動性。

![](notifications-images/notification-dynamic.png "動態檢視")


## <a name="generating-notifications"></a>產生通知

通知可以來自遠端伺服器 ([Apple 推播通知服務](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)，或 APNS) 可以在本機產生的 iOS 應用程式中。

請參閱[iOS 通知逐步解說](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md)如何產生本機通知，例如和[WatchNotifications 範例](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)的操作範例。

必須有本機通知`AlertTitle`設定顯示在 Apple Watch-`AlertTitle`字串會顯示在短外觀介面。 同時`AlertTitle`和`AlertBody`會顯示在通知清單; 而`AlertBody`會顯示在長時間查詢介面。

這個螢幕擷取畫面顯示`AlertTitle`顯示在通知清單中，而`AlertBody`長時間查詢介面中顯示 (使用[範例程式碼](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "這個螢幕擷取畫面顯示的通知清單中顯示 AlertTitle") ![ ] (notifications-images/watch-notificationcontroller-sml.png "AlertBody 長時間查詢介面中顯示")

## <a name="testing-notifications"></a>測試通知

通知 （本機和遠端），才能正確測試在裝置上，不過它們可以使用在模擬 **.json**的 iOS 模擬器中的檔案。

### <a name="testing-on-apple-watch"></a>在 Apple Watch 上測試

當測試 Apple Watch 上的通知，請記住， [Apple 文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)狀態下：

> 當使用者的 iPhone 上的其中一個應用程式的本機或遠端通知抵達時，iOS 會決定是否要在 iPhone 或 Apple Watch 上顯示通知。

這事實 alluding 該 iOS 決定監看式或在 iPhone 上，是否會出現通知。 如果配對的 iPhone 作用中時收到通知，通知是有可能在 iPhone 上顯示和*不*路由傳送至監看式。

若要確保通知會出現在 監看式，關閉 iPhone 螢幕 （一次按下電源按鈕），或讓它進入睡眠狀態。 如果配對的監看式範圍內，有能力，正在磨損手腕上，通知將會那里路由，並會出現在 監看式 （伴隨著細微） 上。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模擬器上測試

您*必須*在 iOS 模擬器中測試通知模式時，提供測試 JSON 裝載。 在 設定路徑**自訂執行引數**視窗在 Visual Studio for mac。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Visual Studio for Mac 將會顯示其他選項，當 watch 擴充功能會設定為**啟始專案**。
監看式擴充功能專案上按一下滑鼠右鍵，然後選擇 **執行與 > 自訂參數...**:
    
[![](notifications-images/runwith-customparams-sml.png "執行含有自訂內容")](notifications-images/runwith-customparams.png#lightbox)
    
這會開啟**執行引數**視窗，其中包含**WatchKit**  索引標籤。選取**通知**並提供 JSON 承載，然後按下**Execute**在模擬器中啟動監看式應用程式：
    
[![](notifications-images/runwith-execargs-sml.png "選取通知裝載的預設值")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要設定測試通知裝載在 Visual Studio 以滑鼠右鍵按一下来編輯的監看式延伸**專案屬性**。 移至**偵錯**區段，然後從清單中 （它會自動將會列出所有包含在專案中的 JSON 檔案） 選取通知 JSON 檔案。
    
[![](notifications-images/runwith-execargs-sml-vs.png "選取通知 JSON 檔案")](notifications-images/runwith-execargs-vs.png#lightbox)

監看式延伸模組時**啟始專案**，Visual Studio 會顯示其他選項，如下所示。 選擇其中一個**通知**監看式應用程式的啟動選項**通知**模式 （使用 [屬性] 視窗中選取之 JSON 檔案）：
    
![](notifications-images/runwith-vs.png "裝置功能表")

-----

在模擬器上測試使用預設裝載 JSON 檔案時，預設的通知控制站看起來像這樣：

![](notifications-images/notification-debug-sml.png "範例通知")

它也可使用[命令列](~/ios/watchos/troubleshooting.md#command_line)啟動 iOS 模擬器。

### <a name="example-notification-payload"></a>通知裝載範例

在[監看式套件目錄](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)有範例為裝載 JSON 檔案範例**NotificationPayload.json** （如下所示）。

```csharp
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

- [WatchNotifications （本機通知） （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Apple Watch 套件通知文件](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
