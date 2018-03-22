---
title: "加強的使用者通知"
description: "本文涵蓋的所有使用者通知已經過增強，iOS 10，以及如何在 Xamarin.iOS 應用程式中使用它們的方式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 50553cb1dc5f7ea782c0f13e32f60d7b6ce3e181
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="enhanced-user-notifications"></a>加強的使用者通知

_本文涵蓋的所有使用者通知已經過增強，iOS 10，以及如何在 Xamarin.iOS 應用程式中使用它們的方式。_

新增 iOS 10、 傳遞和處理的本機和遠端通知架構可讓使用者通知。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定的一組條件，例如位置或一天的時間。

## <a name="about-user-notifications"></a>關於使用者通知

如前所述，新的使用者通知架構允許的傳遞與處理本機與遠端的通知。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定的一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （且可能會修改） 本機和遠端的通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂的本機和遠端通知的外觀，提供給使用者的應用程式擴充功能。

此架構提供下列應用程式可以傳送通知給使用者的方式：

- **視覺警示**-向下通知會從做為橫幅螢幕頂端位置。
- **聲音和振動的情況下**-可以與通知相關聯。
- **應用程式圖示徽章**-顯示應用程式的圖示顯示新的內容可以使用，例如未讀取的電子郵件訊息數目的徽章。

此外，根據使用者的目前內容中，有不同的方式，將會顯示通知：

- 如果裝置已解除鎖定，通知會向下滾動從畫面頂端做為橫幅。
- 鎖定裝置時，如果將使用者的鎖定螢幕上顯示通知。
- 如果使用者已錯過通知，他們可以開啟通知中心，並檢視任何可用，等待通知那里。

Xamarin.iOS 應用程式有兩種類型的使用者可以傳送的通知：

- **本機通知**-這些在使用者裝置上本機安裝的應用程式所傳送。
- **遠端通知**-傳送從遠端伺服器，然後呈現給使用者，或其所觸發的背景更新應用程式的內容。

### <a name="about-local-notifications"></a>關於本機通知

本機 iOS 應用程式可以傳送的通知會有下列功能和屬性：

- 本機使用者的裝置上的應用程式會傳送它們。 
- 它們是可以設定若要使用的時間或位置為主的觸發程序。 
- 應用程式排程通知使用者的裝置，並符合觸發條件時，它會顯示。
- 當使用者互動時使用通知時，應用程式會接收回呼。

本機通知的一些範例包括：

- 行事曆警示
- 提醒警示
- 位置感知觸發程序

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文件。

### <a name="about-remote-notifications"></a>關於遠端通知

遠端傳送通知，iOS 應用程式可以有下列功能和屬性：

- 應用程式必須與其進行通訊的伺服器端元件。
- Apple Push Notification Service (APNs) 用來從開發人員以雲端為基礎的伺服器，傳輸至使用者的裝置的最佳方式傳遞的遠端通知。
- 應用程式收到遠端通知時，它會顯示給使用者。
- 當使用者互動與通知時，應用程式會接收回呼。

遠端通知的一些範例包括：

- 新聞警示
- 運動的更新
- 立即訊息的訊息

有兩種類型的遠端通知之 iOS 應用程式：

- **使用者對象**-這些在裝置上對使用者顯示。
- **無訊息更新**-這些資料行會提供更新之 iOS 應用程式在背景中內容的機制。 當收到無訊息的更新時，應用程式可以連接至最新的內容中移除的伺服器提取。

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文件。

### <a name="about-the-existing-notifications-api"></a>關於現有通知應用程式開發介面

在之前 iOS 10、 iOS 應用程式會使用`UIApplication`註冊通知的系統，並排程 （可由時間和地點） 如何觸發該通知。

有數個使用現有的通知應用程式開發介面時，開發人員可能會遇到的問題：

- 沒有所需的本機或遠端通知可能會導致重複程式碼的不同回呼。
- 應用程式具有有限之後有已排定與系統的通知的控制。
- Apple 的現有平台的所有沒有支援的不同層級。

### <a name="about-the-new-user-notification-framework"></a>有關新的使用者通知架構

使用 iOS 10，Apple 導入了新的使用者通知架構，會取代現有`UIApplication`先前所述的方法。

使用者通知架構提供下列功能：

- 熟悉的 API 包含功能同位檢查與先前的方法，因此可方便移植程式碼從現有的架構。
- 包含一組擴充的內容選項，可讓更豐富的通知傳送給使用者。
- 本機和遠端通知可以處理相同的程式碼與回呼。
- 簡化處理使用者互動，以通知時，會傳送至應用程式的回呼程的序。
- 增強型的管理擱置中和已傳遞的通知，包括移除或更新通知的能力。
- 加入要通知的應用程式中展示的功能。
- 增加排程和處理來自應用程式擴充功能中的通知的能力。
- 加入新通知本身的擴充點。 

新的使用者通知架構提供統一的通知 API 跨平台的多個 Apple 支援包括： 

- **iOS** -完整的支援來管理及排程的通知。
- **tvOS** -將功能加入至本機和遠端通知徽章應用程式圖示。
- **watchOS** -增加轉寄至其 Apple Watch 通知使用者的配對的 iOS 裝置的能力，並授與監看式應用程式進行直接上監看式本身的本機通知。

如需詳細資訊，請參閱 Apple [UserNotifications Framework 參考](https://developer.apple.com/reference/usernotifications)和[UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)文件。

## <a name="preparing-for-notification-delivery"></a>準備通知傳遞

之前的 iOS 應用程式可以傳送通知給應用程式必須向系統使用者，並通知是使用者發生中斷，因為應用程式必須明確地要求權限，才能將它們傳送。

有三個不同的使用者可以核准的應用程式的通知要求層級：

- 橫幅顯示。
- 聲音的警示。
- 徽章的應用程式圖示。

此外，這些層級必須要求和設定本機和遠端的通知。

只要加上下列程式碼，以啟動應用程式不應要求 notification 權限`FinishedLaunching`方法`AppDelegate`並設定所需的通知類型 (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

此外，使用者可以隨時變更應用程式在任何時間使用的通知權限**設定**在裝置上的應用程式。 使用者的要求的通知權限應該檢查應用程式前提出通知，請使用下列程式碼：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>設定遠端通知環境

新增 ios 10，開發人員必須通知推播通知何種環境中執行做為開發或實際執行的作業系統。 無法提供這項資訊可能會導致應用程式遭到拒絕時送出至 iTune App Store 以通知如下所示：

> 遺失的推播通知權利-您的應用程式包含的 API，以便 Apple Push Notification service，但`aps-environment`權利遺漏應用程式的簽章。

若要提供必要的權限，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Entitlements.plist`檔案**方案板**開啟進行編輯。
2. 切換至**來源**檢視： 

    [![](enhanced-user-notifications-images/setup01.png "原始碼檢視")](enhanced-user-notifications-images/setup01.png#lightbox)
3. 按一下 **+** 按鈕即可加入新的金鑰。
4. 輸入`aps-environment`如**屬性**，保留**類型**為`String`並輸入 `development`或`production`如**值**: 

    [![](enhanced-user-notifications-images/setup02.png "Aps 環境屬性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 將變更儲存到檔案。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下`Entitlements.plist`檔案**方案總管 中**開啟進行編輯。
3. 按一下 **+** 按鈕即可加入新的金鑰。
4. 輸入`aps-environment`如**屬性**，保留**類型**為`String`並輸入 `development`或`production`如**值**: 

    [![](enhanced-user-notifications-images/setup02w.png "Aps 環境屬性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 將變更儲存到檔案。

-----

### <a name="registering-for-remote-notifications"></a>註冊遠端通知

如果應用程式會同時傳送和接收遠端的通知，它仍然需要執行_語彙基元註冊_使用現有`UIApplication`應用程式開發介面。 此註冊需要裝置才能存取即時網路連線 APNs，將會產生將傳送至應用程式的必要語彙基元。 應用程式必須將此權杖來註冊遠端通知開發人員的伺服器端應用程式：

[![](enhanced-user-notifications-images/token01.png "語彙基元註冊概觀")](enhanced-user-notifications-images/token01.png#lightbox)

您可以使用下列程式碼來初始化必要的登錄：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

取得傳送到開發人員的伺服器端應用程式的權杖必須是包含通知裝載該 get 的一部分從伺服器傳送至 APNs 傳送遠端通知時：

[![](enhanced-user-notifications-images/token02.png "通知裝載中包含的語彙基元")](enhanced-user-notifications-images/token02.png#lightbox)

語彙基元做為通知和用來開啟或回應通知的應用程式緊密整合的索引鍵。

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文件。

## <a name="notification-delivery"></a>通知傳遞

完整登錄應用程式的必要權限要求和授與使用者，應用程式已經準備好要傳送和接收通知。 

### <a name="providing-notification-content"></a>提供通知的內容

新增 ios 10，所有通知同時都包含**標題**和**副標題**，一律會顯示與**主體**通知內容。 新的也能夠加入**媒體附件**通知內容。

若要建立本機通知的內容，請使用下列程式碼：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

遠端的通知，程序如下：

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>排程時的通知會傳送

建立通知的內容，應用程式需要排程何時將會向使用者顯示通知，藉由設定*觸發程序*。 iOS 10 提供四種不同的觸發程序類型：

- **推播通知**-專門搭配遠端通知並傳送 APNs 通知封裝應用程式在裝置上執行時觸發。
- **時間間隔**-可讓本機通知排程的時間間隔開始的現在和結束某些未來的點。 例如：`var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **行事曆日期**-可讓本機通知，以排定在特定日期和時間。
- **位置為基礎**-可讓本機通知，當 iOS 裝置進入或離開特定地理位置或任何藍芽指標指定靠近排程。

準備本機通知時，應用程式需要呼叫`Add`方法`UNUserNotificationCenter`排程其顯示在使用者的物件。 遠端的通知，請將伺服器端應用程式將傳送通知裝載至 APNs，然後傳送給使用者的裝置入封包。

將各部分結合在一起，本機通知的範例可能如下：

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>處理前景應用程式通知

新增 ios 10，應用程式可以處理通知以不同的方式在前景，且時，就會觸發通知。 藉由提供`UNUserNotificationCenterDelegate`和實作`UserNotificationCenter`方法，應用程式可以接管負責顯示通知。 例如: 

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

這段程式碼只會寫出的內容`UNNotification`應用程式輸出以及詢問系統所要顯示的標準警示通知。 

如果應用程式想要在前景時顯示通知訊息本身不使用系統預設值，傳遞`None`來完成處理常式。 範例：

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

這個程式碼的位置中，開啟`AppDelegate.cs`檔案進行編輯，並且變更`FinishedLaunching`方法看起來像下面這樣：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

此程式碼會附加自訂`UNUserNotificationCenterDelegate`上述目前`UNUserNotificationCenter`讓作用中時，應用程式可以處理通知和在前景。

## <a name="notification-management"></a>通知管理

新增 ios 10，通知管理提供暫止與已傳遞通知的存取，並新增移除、 更新或升級這些通知的能力。

通知管理很重要一部分是_要求識別碼_，已建立且排程與系統時指派的通知。 遠端的通知，這會指派新`apps-collapse-id`HTTP 要求標頭中的欄位。

要求識別碼用來選取想要對通知管理的應用程式的通知。

### <a name="removing-notifications"></a>移除通知

若要從系統移除暫止的通知，請使用下列程式碼：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

若要移除已傳遞的通知，請使用下列程式碼：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>更新現有的通知

若要更新現有的通知，只要具有所需的參數修改 （例如新的觸發程序時間） 建立新的通知，並將它加入至具有相同的要求識別項為必須修改通知系統。 範例：


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

已傳遞通知，請將取得更新並提升為首頁和鎖定畫面上，並在通知中心清單頂端，使用者必須具有已被讀取現有的通知。

## <a name="working-with-notification-actions"></a>使用通知動作

IOS 10，會傳遞給使用者的通知不是靜態的並提供與它們的使用者可以互動的數種方式 （從內建到自訂動作）。

有三種類型的 iOS 應用程式可以回應的動作：

- **預設動作**-這是當使用者點選通知，開啟應用程式，並顯示指定的通知的詳細資料。
- **自訂動作**-這些 iOS 8 中已加入，並提供快速的方法，讓使用者直接從通知執行自訂工作，而不需要啟動應用程式。 他們可以看到一份可自訂的標題與按鈕或文字輸入的欄位 （其中應用程式為指定少量的時間才能完成要求） 的背景或前景可執行 （其中啟動應用程式在前景 fulfill 要求)。 使用 iOS 和 watchOS 上自訂動作。
- **關閉動作**-當使用者關閉指定的通知時，將會傳送至應用程式的這個動作。

### <a name="creating-custom-actions"></a>建立自訂動作

建立及使用系統中註冊自訂動作，請使用下列程式碼：

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

當建立新`UNNotificationAction`，它會指派唯一識別碼和會出現在按鈕的標題。 根據預設，動作會建立做為背景動作，但是可以提供選項，以調整動作的行為 （例如將它設定為前景動作）。

每個建立的動作需要與分類產生關聯。 當建立新`UNNotificationCategory`、 指派的唯一識別碼，它執行的動作清單，提供類別目錄中的動作之意圖的詳細資訊與某些選項，以控制行為的類別目錄的意圖識別碼的清單。

最後，所有的類別會向系統使用`SetNotificationCategories`方法。

### <a name="presenting-custom-actions"></a>呈現的自訂動作

一旦已建立並系統註冊的一組自訂動作和類別，則他們可以看到從本機或遠端通知。

如需遠端通知，設定`category`遠端通知裝載符合其中一個上面所建立的類別目錄中。 例如: 

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

本機的通知，請設定`CategoryIdentifier`屬性`UNMutableNotificationContent`物件。 例如: 

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

同樣地，此識別碼必須符合其中一個上面所建立的類別。

### <a name="handling-dismiss-actions"></a>處理解除動作

如上所述，解除的動作可以傳送至應用程式，當使用者關閉通知。 由於這不是標準動作，將需要類別建立時設定選項。 例如: 

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>處理動作回應

當使用者互動的自訂動作與上面所建立的類別時，應用程式必須滿足要求的工作。 這是藉由提供`UNUserNotificationCenterDelegate`和實作`UserNotificationCenter`方法。 例如: 

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

傳入中`UNNotificationResponse`類別具有`ActionIdentifier`屬性，可以是預設的動作，或是關閉動作。 使用`response.Notification.Request.Identifier`來測試任何自訂動作。

`UserText`屬性會保留的任何使用者輸入的文字值。 `Notification`屬性會保留原始的通知，包括觸發程序的要求和通知內容。 如果這是本機或遠端通知根據觸發程序的類型，可以決定應用程式。

## <a name="working-with-service-extensions"></a>使用服務延伸模組

使用遠端的通知時_服務延伸_提供一個方式來啟用通知裝載內的端對端加密。 服務延伸模組是通知的非使用者介面執行的擴充功能 （適用於 iOS 10） 在背景以加強或取代可見內容，它會呈現給使用者之前的主要用途。 

[![](enhanced-user-notifications-images/extension01.png "服務延伸模組概觀")](enhanced-user-notifications-images/extension01.png#lightbox)

服務延伸模組的用意是要快速地執行，並只可以在短一段時間的系統執行。 確認服務延伸模組無法在分配的時間內完成其工作，會呼叫後援方法。 如果後援的失敗，原始的通知內容將會顯示給使用者。

服務延伸模組的一些可能用法包括：

- 提供遠端通知內容的端對端的加密。
- 加入遠端的通知，以使它們更豐富的附件。

### <a name="implementing-a-service-extension"></a>實作服務延伸模組

若要實作服務延伸模組 Xamarin.iOS 應用程式中，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Visual Studio 中開啟應用程式的方案，for mac。
2. 以滑鼠右鍵按一下方案名稱在**方案板**選取**新增** > **加入新的專案**。
3. 選取**iOS** > **延伸** > **通知服務延伸**按一下**下一步**按鈕： 

    [![](enhanced-user-notifications-images/extension02.png "選取通知服務延伸模組")](enhanced-user-notifications-images/extension02.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**下一步**按鈕： 

    [![](enhanced-user-notifications-images/extension03.png "輸入副檔名的名稱")](enhanced-user-notifications-images/extension03.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下 **建立**按鈕： 

    [![](enhanced-user-notifications-images/extension04.png "調整的專案名稱和/或方案名稱")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 Visual Studio 中開啟應用程式的方案。
2. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**新增** > **加入新的專案**。
3. 選取**iOS** > **延伸** > **通知服務延伸**: 

    [![](enhanced-user-notifications-images/extension01w.png "選取通知服務延伸模組")](enhanced-user-notifications-images/extension01w.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**確定** 按鈕。

-----

> [!IMPORTANT]
> 服務延伸的配套識別碼應符合主應用程式配套識別碼`.appnameserviceextension`附加至結尾。 例如，如果主應用程式套件組合識別碼`com.xamarin.monkeynotify`，服務延伸模組都應該有的配套識別碼`com.xamarin.monkeynotify.monkeynotifyserviceextension`。 這應該會自動設定當延伸項目新增至方案。 

在需要修改，以提供必要的功能通知服務延伸模組沒有一個主要類別。 例如: 

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

第一種方法， `DidReceiveNotificationRequest`，會傳遞給通知識別碼，以及通知內容透過`request`物件。 傳入中`contentHandler`需要被呼叫來向使用者顯示通知。

第二種方法， `TimeWillExpire`，將會在時間即將執行，服務延伸模組來處理要求之前呼叫。 如果服務延伸模組無法呼叫`contentHandler`在分配的時間量，原始的內容將會顯示給使用者。

### <a name="triggering-a-service-extension"></a>觸發服務延伸模組

副檔名為服務建立與傳遞與應用程式，它可藉由修改遠端通知裝載傳送至裝置。 例如: 

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新`mutable-content`索引鍵會指定服務延伸模組，將需要啟動來更新遠端通知內容。 `encrypted-content`金鑰保存服務延伸模組可以解密才能呈現給使用者的加密的資料。

看看下列範例服務延伸模組：

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

此程式碼會解密加密的內容從`encrypted-content`金鑰、 建立新`UNMutableNotificationContent`，設定`Body`屬性來解密的內容，並使用`contentHandler`向使用者顯示通知。

## <a name="summary"></a>總結

這篇文章已涵蓋的所有使用者通知已經過增強，iOS 10 的方式。 它會顯示新的使用者通知架構以及如何使用 Xamarin.iOS 應用程式或應用程式擴充功能中。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications Framework 參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
