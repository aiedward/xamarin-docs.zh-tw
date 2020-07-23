---
title: 在 Xamarin 中增強的使用者通知
description: 本文說明 iOS 10 中引進的使用者通知架構。 它討論本機通知、遠端通知、通知管理、通知動作等等。
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: d4fe04412eb4fb456bc49d71c1e5fe87df5f9e76
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939632"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>在 Xamarin 中增強的使用者通知

IOS 10 的新手，使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構，應用程式或應用程式擴充功能可以指定一組條件（例如位置或一天的時間）來排程本機通知的傳遞。

## <a name="about-user-notifications"></a>關於使用者通知

如上所述，新的使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構，應用程式或應用程式擴充功能可以指定一組條件（例如位置或一天的時間）來排程本機通知的傳遞。

此外，應用程式或延伸模組可以在本機和遠端通知傳遞給使用者的 iOS 裝置時，同時接收（而且可能會修改）。

新的使用者通知 UI 架構可讓應用程式或應用程式延伸模組在呈現給使用者時，自訂本機和遠端通知的外觀。

此架構提供下列方式，讓應用程式可以將通知傳遞給使用者：

- **視覺效果警示**-通知會從畫面頂端向下匯總為橫幅。
- **音效和 Vibrations** -可以與通知相關聯。
- **應用程式圖示徽章**-應用程式的圖示會在其中顯示徽章，顯示有新的內容可供使用，例如未閱讀的電子郵件訊息數目。

此外，視使用者目前的內容而定，有不同的方式可以呈現通知：

- 如果裝置已解除鎖定，則會以橫幅的形式從畫面頂端向下復原通知。
- 如果裝置已鎖定，通知就會顯示在使用者的鎖定畫面上。
- 如果使用者錯過了通知，他們可以開啟 [通知中心]，並在該處查看任何可用、等待中的通知。

一個 Xamarin iOS 應用程式有兩種類型的使用者通知可以傳送：

- **本機通知**-這些是由本機使用者裝置上安裝的應用程式所傳送。
- **遠端通知**-會從遠端伺服器傳送，並向使用者顯示，或觸發應用程式內容的背景更新。

### <a name="about-local-notifications"></a>關於本機通知

IOS 應用程式可以傳送的本機通知具有下列功能和屬性：

- 它們是由使用者裝置上的本機應用程式所傳送。 
- 它們可以設定為使用時間或以位置為基礎的觸發程式。 
- 應用程式會在使用者的裝置上排程通知，並在符合觸發條件時顯示。
- 當使用者與通知互動時，應用程式將會收到回呼。

本機通知的一些範例包括：

- 行事曆警示
- 提醒警示
- 位置感知觸發程式

如需詳細資訊，請參閱 Apple 的[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)檔。

### <a name="about-remote-notifications"></a>關於遠端通知

IOS 應用程式可以傳送的遠端通知具有下列功能和屬性：

- 應用程式具有與其通訊的伺服器端元件。
- Apple Push Notification Service （APNs）是用來從開發人員的雲端伺服器，將遠端通知的最佳傳遞傳送到使用者的裝置。
- 當應用程式收到遠端通知時，就會向使用者顯示。
- 當使用者與通知互動時，應用程式將會收到回呼。

遠端通知的一些範例包括：

- 新聞警示
- 運動更新
- 立即訊息訊息

IOS 應用程式可使用兩種類型的遠端通知：

- **使用者**對向-在裝置上向使用者顯示這些資訊。
- **無訊息更新**-這些會提供在背景中更新 iOS 應用程式內容的機制。 收到無訊息更新時，應用程式可以連接到遠端伺服器，以提取最新的內容。

如需詳細資訊，請參閱 Apple 的[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)檔。

### <a name="about-the-existing-notifications-api"></a>關於現有的通知 API

在 iOS 10 之前，iOS 應用程式會使用 `UIApplication` 向系統註冊通知，並排程應如何觸發該通知（依時間或位置）。

開發人員在使用現有的通知 API 時可能會遇到幾個問題：

- 本機或遠端通知需要不同的回呼，這可能會導致程式碼重複。
- 應用程式在系統排程之後，對通知的控制受到限制。
- 所有 Apple 現有平臺的支援各有不同層級。

### <a name="about-the-new-user-notification-framework"></a>關於新的使用者通知架構

在 iOS 10 中，Apple 引進了新的使用者通知架構，其取代了先前所述的現有 `UIApplication` 方法。

使用者通知架構提供下列各項：

- 熟悉的 API，其中包含與先前方法相同的功能，可讓您輕鬆地從現有的架構移植程式碼。
- 包含一組擴充的內容選項，可讓您將更豐富的通知傳送給使用者。
- 本機和遠端通知都可以透過相同的程式碼和回呼來處理。
- 簡化在使用者與通知互動時，處理傳送至應用程式之回呼的進程。
- 增強的擱置和傳遞通知管理，包括移除或更新通知的能力。
- 新增在應用程式內呈現通知的功能。
- 新增排程和處理應用程式延伸模組內通知的功能。
- 加入通知本身的新擴充點。 

新的使用者通知架構在 Apple 支援的多個平臺上提供統一的通知 API，包括： 

- **iOS** -管理和排程通知的完整支援。
- **tvOS** -新增徽章應用程式圖示的功能，以進行本機和遠端通知。
- **watchOS** -新增將通知從使用者的已配對 iOS 裝置轉送至其 Apple Watch 的功能，並讓 watch 應用程式能夠直接在監看式上執行本機通知。

如需詳細資訊，請參閱 Apple 的[UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)和[UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)檔。

## <a name="preparing-for-notification-delivery"></a>準備通知傳遞

在 iOS 應用程式可以將通知傳送給使用者之前，必須先向系統註冊應用程式，而且由於通知會中斷使用者，因此應用程式必須先明確要求許可權，才會將其送出。

使用者可以核准的應用程式有三個不同層級的通知要求：

- 橫幅會顯示。
- 音效警示。
- 徽章應用程式圖示。

此外，您必須針對本機和遠端通知要求和設定這些核准層級。

只要應用程式啟動，就應該要求通知許可權，方法是將下列程式碼新增至的 `FinishedLaunching` 方法 `AppDelegate` ，並設定所需的通知類型（ `UNAuthorizationOptions` ）：

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

此外，使用者隨時都可以使用裝置上的 [**設定**] 應用程式變更應用程式的通知許可權。 應用程式應該先檢查使用者的要求通知許可權，然後再使用下列程式碼來呈現通知：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>設定遠端通知環境

IOS 10 的新功能，開發人員必須通知 OS 哪個環境推播通知是以開發或實際執行的形式執行。 若無法提供此資訊，可能會導致應用程式在提交至 iTune App Store 時遭到拒絕，並出現類似下列的通知：

> 缺少推播通知權利-您的應用程式包含適用于 Apple 推播通知服務的 API，但 `aps-environment` 應用程式的簽章中缺少權利。

若要提供必要的許可權，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 `Entitlements.plist` [ **Solution Pad**中的檔案以開啟它進行編輯。
2. 切換至 [**來源**] 視圖： 

    [![來源視圖](enhanced-user-notifications-images/setup01.png)](enhanced-user-notifications-images/setup01.png#lightbox)
3. 按一下 **+** 按鈕以加入新的金鑰。
4. `aps-environment`針對**屬性**輸入，並將**類型**保留為， `String` 並輸入 `development` 或作為 `production` **值**： 

    [![[Ap-環境] 屬性](enhanced-user-notifications-images/setup02.png)](enhanced-user-notifications-images/setup02.png#lightbox)
5. 將變更儲存至檔案。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下 `Entitlements.plist` [**方案總管**中的檔案以開啟它進行編輯。
2. 按一下 **+** 按鈕以加入新的金鑰。
3. `aps-environment`針對**屬性**輸入，並將**類型**保留為， `String` 並輸入 `development` 或作為 `production` **值**： 

    [![[Ap-環境] 屬性](enhanced-user-notifications-images/setup02w.png)](enhanced-user-notifications-images/setup02.png#lightbox)
4. 將變更儲存至檔案。

-----

### <a name="registering-for-remote-notifications"></a>註冊遠端通知

如果應用程式將會傳送和接收遠端通知，它仍然需要使用現有的 API 來_註冊權杖_ `UIApplication` 。 此註冊會要求裝置必須具有即時網路連線存取 APNs，以產生將會傳送至應用程式的必要權杖。 應用程式必須將此權杖轉送至開發人員的伺服器端應用程式，以註冊遠端通知：

[![權杖註冊總覽](enhanced-user-notifications-images/token01.png)](enhanced-user-notifications-images/token01.png#lightbox)

使用下列程式碼來初始化所需的註冊：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

傳送給開發人員的伺服器端應用程式的權杖，必須納入為通知承載的一部分，而此承載會在傳送遠端通知時，從伺服器傳送到 APNs：

[![包含在通知承載中的權杖](enhanced-user-notifications-images/token02.png)](enhanced-user-notifications-images/token02.png#lightbox)

Token 會作為系結通知的金鑰，以及用來開啟或回應通知的應用程式。

如需詳細資訊，請參閱 Apple 的[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)檔。

## <a name="notification-delivery"></a>通知傳遞

應用程式已完整註冊，且使用者要求並授與所需的許可權之後，應用程式現在已準備好傳送和接收通知。 

### <a name="providing-notification-content"></a>提供通知內容

最新的 iOS 10，所有通知都包含**標題**和**副標題**，其中一律會顯示通知內容的**主體**。 此外，它也是將**媒體附件**新增至通知內容的功能。

若要建立本機通知的內容，請使用下列程式碼：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

針對遠端通知，此程式類似：

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

### <a name="scheduling-when-a-notification-is-sent"></a>排程傳送通知的時間

建立通知的內容之後，應用程式必須透過設定*觸發*程式來排程通知將呈現給使用者的時間。 iOS 10 提供四種不同的觸發程式類型：

- **推播通知**-僅用於遠端通知，並會在 APNs 將通知套件傳送至裝置上執行的應用程式時觸發。
- **時間間隔**-允許從時間間隔開始排程本機通知，從現在開始，到某個未來點結束。 例如， `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- 行事**曆日期**-允許在特定日期和時間排程本機通知。
- 以**位置為基礎**-允許在 iOS 裝置進入或離開特定地理位置，或指定接近任何藍牙指標時，排程本機通知。

當本機通知準備就緒時，應用程式需要呼叫物件的 `Add` 方法， `UNUserNotificationCenter` 以將其顯示排程給使用者。 對於遠端通知，伺服器端應用程式會將通知承載傳送至 APNs，然後將封包傳送到使用者的裝置。

將所有元件結合在一起，範例本機通知可能如下所示：

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

## <a name="handling-foreground-app-notifications"></a>處理前景代理程式更新

IOS 10 的新功能，應用程式可以在處於前景時以不同的方式處理通知，並觸發通知。 藉由提供 `UNUserNotificationCenterDelegate` 並實施 `WillPresentNotification` 方法，應用程式可以接管顯示通知的責任。 例如：

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

這段程式碼只是將的內容寫出 `UNNotification` 至應用程式輸出，並要求系統顯示通知的標準警示。 

如果應用程式想要在前景時顯示通知本身，而不是使用系統預設值，請傳遞 `None` 至完成處理常式。 範例：

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

將此程式碼備妥之後，請開啟檔案 `AppDelegate.cs` 進行編輯，然後將 `FinishedLaunching` 方法變更為如下所示：

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

此程式碼會將來自上述的自訂附加 `UNUserNotificationCenterDelegate` 至目前的， `UNUserNotificationCenter` 讓應用程式可以在使用中和前景時處理通知。

## <a name="notification-management"></a>通知管理

對於 iOS 10 的新功能，通知管理可讓您存取擱置和傳遞的通知，並新增移除、更新或升級這些通知的能力。

通知管理的一個重要部分是在建立和排程系統時指派給通知的_要求識別碼_。 對於遠端通知，這是透過 `apps-collapse-id` HTTP 要求標頭中的新欄位所指派。

[要求識別碼] 用來選取應用程式想要在其上執行通知管理的通知。

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

若要更新現有的通知，只要建立新的通知，並將所需的參數修改（例如新的觸發程式時間），並將它新增至具有與需要修改之通知相同的要求識別碼的系統中。 範例：

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

對於已經傳遞的通知，現有的通知將會更新並升級至首頁和鎖定畫面上的清單頂端，以及已由使用者讀取的通知中心。

## <a name="working-with-notification-actions"></a>使用通知動作

在 iOS 10 中，傳遞給使用者的通知不是靜態的，而且提供了數種使用者可以與其互動的方式（從內建到自訂動作）。

IOS 應用程式可以回應的動作有三種類型：

- **預設動作**-這是當使用者按下通知來開啟應用程式，並顯示指定通知的詳細資料時。
- **自訂動作**-這些是在 iOS 8 中新增的，並提供快速的方法讓使用者直接從通知執行自訂工作，而不需要啟動應用程式。 它們可以呈現為具有可自訂標題的按鈕清單，或可在背景中執行的文字輸入欄位（應用程式會提供少量時間來完成要求）或前景（應用程式會在前景中啟動以滿足要求）。 自訂動作適用于 iOS 和 watchOS。
- **關閉動作**-當使用者關閉指定的通知時，此動作會傳送至應用程式。

### <a name="creating-custom-actions"></a>建立自訂動作

若要建立並向系統註冊自訂動作，請使用下列程式碼：

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

建立新的時 `UNNotificationAction` ，系統會為其指派唯一的識別碼，以及將出現在按鈕上的標題。 根據預設，動作將會建立為背景動作，不過可以提供選項來調整動作的行為（例如，將其設定為前景動作）。

所建立的每個動作都必須與一個類別相關聯。 建立新的時， `UNNotificationCategory` 會指派唯一的識別碼、可執行檔動作清單、意圖識別碼清單，以提供有關類別中動作意圖的詳細資訊，以及一些控制分類行為的選項。

最後，所有的類別都會使用方法向系統註冊 `SetNotificationCategories` 。

### <a name="presenting-custom-actions"></a>呈現自訂動作

一旦建立了一組自訂動作和類別，並向系統註冊之後，就可以從本機或遠端通知呈現。

若為遠端通知，請 `category` 在遠端通知承載中設定，以符合上述所建立的其中一個類別。 例如：

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

針對本機通知，請設定 `CategoryIdentifier` 物件的屬性 `UNMutableNotificationContent` 。 例如：

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

同樣地，此識別碼必須符合上述所建立的其中一個類別。

### <a name="handling-dismiss-actions"></a>處理關閉動作

如上所述，當使用者關閉通知時，可以將解除動作傳送至應用程式。 由於這不是標準的動作，因此在建立類別目錄時，將需要設定一個選項。 例如：

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>處理動作回應

當使用者與上述所建立的自訂動作和類別互動時，應用程式必須完成要求的工作。 這是藉由提供 `UNUserNotificationCenterDelegate` 並執行方法來完成 `UserNotificationCenter` 。 例如：

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

傳入的 `UNNotificationResponse` 類別具有 `ActionIdentifier` 屬性，可以是預設動作或關閉動作。 用 `response.Notification.Request.Identifier` 來測試是否有任何自訂動作。

`UserText`屬性會保存任何使用者文字輸入的值。 `Notification`屬性會保留原始通知，其中包含含有觸發程式和通知內容的要求。 應用程式可以決定它是以觸發程式類型為基礎的本機或遠端通知。

> [!NOTE]
> iOS 12 讓自訂通知 UI 可以在執行時間修改其動作按鈕。 如需詳細資訊，請參閱[動態通知動作按鈕](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md)檔。

## <a name="working-with-service-extensions"></a>使用服務延伸模組

使用遠端通知時，_服務延伸_提供了在通知承載內啟用端對端加密的方法。 服務延伸是在背景中執行的非使用者介面延伸模組（在 iOS 10 中提供），主要目的是要在呈現給使用者之前擴充或取代通知的可見內容。 

[![服務延伸模組總覽](enhanced-user-notifications-images/extension01.png)](enhanced-user-notifications-images/extension01.png#lightbox)

服務延伸的目的是要快速執行，而且只有在一小段時間內，系統才會執行。 當服務延伸無法在配置的時間內完成其工作時，將會呼叫回溯方法。 如果回復失敗，則會向使用者顯示原始通知內容。

服務延伸模組的一些可能用法包括：

- 提供遠端通知內容的端對端加密。
- 將附件新增至遠端通知以擴充它們。

### <a name="implementing-a-service-extension"></a>執行服務延伸模組

若要在 Xamarin iOS 應用程式中執行服務延伸模組，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**Solution Pad**中的方案名稱，**然後選取 [新增]**[  >  **新增專案**]。
3. 選取 [ **iOS**  >  **延伸**模組  >  **通知服務延伸**模組]，然後按 [**下一步]** 按鈕： 

    [![選取通知服務延伸模組](enhanced-user-notifications-images/extension02.png)](enhanced-user-notifications-images/extension02.png#lightbox)
4. 輸入擴充功能的**名稱**，然後按 [**下一步]** 按鈕： 

    [![輸入延伸模組的名稱](enhanced-user-notifications-images/extension03.png)](enhanced-user-notifications-images/extension03.png#lightbox)
5. 視需要調整 [**專案名稱**] 和/或 [**方案名稱**]，然後按一下 [**建立**] 按鈕： 

    [![調整專案名稱和（或）方案名稱](enhanced-user-notifications-images/extension04.png)](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選取 [**加入 > 新增專案**...]。
3. 選取 [ **Visual c #] > IOS 延伸模組 > 通知服務延伸**模組：

    [![選取通知服務延伸模組](enhanced-user-notifications-images/extension01.w157-sml.png)](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. 輸入擴充功能的**名稱**，然後按一下 [**確定]** 按鈕。

-----

> [!IMPORTANT]
> 服務延伸模組的套件組合識別碼應符合主要應用程式的套件組合識別碼，並 `.appnameserviceextension` 附加至結尾。 例如，如果主要應用程式的套件組合識別碼為 `com.xamarin.monkeynotify` ，則服務延伸模組應具有的套件組合識別碼 `com.xamarin.monkeynotify.monkeynotifyserviceextension` 。 這應該會在擴充功能新增至解決方案時自動設定。 

在通知服務延伸中，有一個主要類別需要修改，才能提供必要的功能。 例如：

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

第一個方法 `DidReceiveNotificationRequest` 會透過物件傳遞通知識別碼和通知內容 `request` 。 `contentHandler`將需要呼叫傳入的，以向使用者呈現通知。

第二個方法 `TimeWillExpire` 會在即將用完服務延伸模組來處理要求之前呼叫。 如果服務延伸在配置的 `contentHandler` 時間內無法呼叫，則會向使用者顯示原始內容。

### <a name="triggering-a-service-extension"></a>觸發服務延伸

透過應用程式建立並傳遞的服務延伸模組，可以藉由修改傳送至裝置的遠端通知承載來加以觸發。 例如：

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新的 `mutable-content` 金鑰會指定需要啟動服務延伸模組，才能更新遠端通知內容。 `encrypted-content`金鑰會保存服務延伸模組在向使用者呈現之前可以解密的加密資料。

請參閱下列範例服務延伸模組：

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

此程式碼會解密金鑰中的加密內容 `encrypted-content` 、建立新的 `UNMutableNotificationContent` 、將 `Body` 屬性設定為解密的內容，並使用 `contentHandler` 向使用者呈現通知。

## <a name="summary"></a>總結

本文涵蓋了 iOS 10 已增強使用者通知的所有方式。 它呈現新的使用者通知架構，以及如何在 Xamarin iOS 應用程式或應用程式延伸模組中使用它。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)
