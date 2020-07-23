---
title: 在 Xamarin 中 watchOS 通知
description: 本檔說明如何在 Xamarin 中使用 watchOS 通知。 它討論如何建立通知控制器、產生通知和測試通知。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 0358b2b422e4cc69faa15187ee24d72c7d02ca38
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937926"
---
# <a name="watchos-notifications-in-xamarin"></a>在 Xamarin 中 watchOS 通知

如果包含的 iOS 應用程式支援，監看應用程式可以接收通知。 有內建的通知處理，因此您不*需要*新增以下所述的其他通知支援，不過，如果您想要自訂通知行為和外觀，請繼續閱讀。

如需在解決方案中將通知支援新增至 iOS 應用程式的詳細資訊，請參閱[IOS 通知](~/ios/platform/user-notifications/deprecated/index.md)檔。

## <a name="creating-notification-controllers"></a>建立通知控制器

在分鏡腳本通知控制器上，有特殊類型的 segue 觸發它們。 當您將新的**通知介面控制器**拖曳至分鏡腳本時，它會自動附加 segue：

![已附加 segue 的新通知介面控制器](notifications-images/notification-storyboard1.png)

當您選取 [通知] segue 時，您可以編輯其屬性：

![已選取通知 segue](notifications-images/notification-storyboard2.png)

自訂控制器之後，它可能會從 WatchKitCatalog 中看起來像這個範例：

![通知屬性](notifications-images/notifications-segue.png)

通知的類型有兩種：

- 系統所定義的「**短期**不受滾動」靜態視圖。

- 由您定義的**完整外觀**、可自訂的視圖！ 您可以指定較簡單的靜態版本和更複雜的動態版本。

### <a name="short-look-notification-controller"></a>簡短外觀通知控制器

簡短外觀 UI 只包含應用程式圖示、應用程式名稱和通知標題字串。

如果使用者不忽略通知，系統將會自動切換至提供詳細資訊的長時間查看通知。

### <a name="long-look-notification-controller"></a>長時間查看通知控制器

OS 會決定是否要根據數個因素來顯示靜態或動態視圖。 您必須提供靜態介面，而且也可以選擇性地包含通知的動態介面。

#### <a name="static"></a>靜態

靜態視圖應該簡單且快速地顯示。

![靜態視圖](notifications-images/notification-static.png)

#### <a name="dynamic"></a>動態

動態視圖可以顯示更多的資料，並提供更多的互動性。

![動態視圖](notifications-images/notification-dynamic.png)

## <a name="generating-notifications"></a>產生通知

通知可以來自遠端伺服器，也可以在 iOS 應用程式的本機上產生。

如需如何產生本機通知的範例，請參閱[IOS 通知逐步](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md)解說。

本機通知必須將 `AlertTitle` 設定顯示在 Apple Watch 上-此 `AlertTitle` 字串會顯示在簡短外觀的介面中。 `AlertTitle`和 `AlertBody` 都會顯示在 [通知] 清單中，而且 `AlertBody` 會顯示在「長時間尋找」介面中。

這個螢幕擷取畫面顯示顯示 `AlertTitle` 在 [通知] 清單中，並顯示在 [ `AlertBody` 長期外觀] 介面中：

![此螢幕擷取畫面顯示 [通知] 清單中顯示的 AlertTitle](notifications-images/watch-notificationslist-sml.png) ![AlertBody 會顯示在「長外觀」介面中](notifications-images/watch-notificationcontroller-sml.png)

## <a name="testing-notifications"></a>測試通知

通知（本機和遠端）只能在裝置上適當地測試，不過，您可以使用 iOS 模擬器中的**json**檔案來模擬它們。

### <a name="testing-on-apple-watch"></a>在 Apple Watch 上測試

在 Apple Watch 上測試通知時，請記住[Apple 的檔](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)會指出下列事項：

> 當您的其中一個應用程式本機或遠端通知抵達使用者的 iPhone 時，iOS 會決定是否要在 iPhone 或 Apple Watch 上顯示該通知。

這 alluding 于 iOS 決定通知是否會出現在 iPhone 或 Watch 上。 如果在收到通知時，配對的 iPhone 是作用中，則通知可能會顯示在 iPhone 上，而*不*會路由傳送至監看式。

若要確保通知會出現在監看上，請關閉 iPhone 畫面（按電源按鈕一次），或讓它進入睡眠狀態。 如果配對的監看在範圍內，有電力並在手腕上磨損，則會在該處路由傳送通知，並顯示在監看式上（附有微妙）。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模擬器上進行測試

在 iOS 模擬器中測試通知模式時，您*必須*提供測試 JSON 承載。 在 Visual Studio for Mac 的 [**自訂執行引數**] 視窗中設定路徑。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當監看式擴充功能設定為**啟始專案**時，Visual Studio for Mac 將會顯示其他選項。
以滑鼠右鍵按一下 [監看式] 延伸模組專案，然後選擇 [以 **> 自訂參數執行**]：

[![以自訂屬性執行](notifications-images/runwith-customparams-sml.png)](notifications-images/runwith-customparams.png#lightbox)

這會開啟 [**執行引數**] 視窗，其中包含 [ **WatchKit** ] 索引標籤。選取 [**通知**] 並提供 JSON 承載，然後按 [**執行**] 以啟動模擬器中的監看式應用程式：

[![選取通知承載預設值](notifications-images/runwith-execargs-sml.png)](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在中設定測試通知承載 Visual Studio 以滑鼠右鍵按一下 [監看式] 延伸模組，以編輯**專案屬性**。 移至 [ **Debug** ] 區段，並從清單中選取通知 json 檔案（它會自動列出專案中包含的所有 json 檔案）。

[![選取通知 JSON 檔案](notifications-images/runwith-execargs-sml-vs.png)](notifications-images/runwith-execargs-vs.png#lightbox)

當 [監看式] 延伸模組是**啟始專案**時，Visual Studio 會顯示其他選項，如下所示。 選擇其中一個**通知**選項，以**通知**模式啟動監看式應用程式（使用 [屬性] 視窗中選取的 JSON 檔案）：

![[裝置] 功能表](notifications-images/runwith-vs.png)

-----

使用預設承載 JSON 檔案在模擬器上進行測試時，預設通知控制器看起來會像這樣：

![範例通知](notifications-images/notification-debug-sml.png)

您也可以使用[命令列](~/ios/watchos/troubleshooting.md#command_line)來啟動 iOS 模擬器。

### <a name="example-notification-payload"></a>範例通知承載

在「[監看套件目錄](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)」範例中，有一個範例承載 JSON 檔案**NotificationPayload.js于**（如下所示）。

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

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的監看套件通知檔](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
