---
title: 在 Xamarin.iOS 中增強的使用者通知
description: 這篇文章描述在 iOS 10 中導入的使用者通知架構。 它討論本機通知、 遠端通知、 通知管理、 通知動作和更多功能。
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: bfbb8c2b189defeb6efb07388ea34425c239c061
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459885"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>在 Xamarin.iOS 中增強的使用者通知

新 iOS 10，使用者通知架構可讓傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

## <a name="about-user-notifications"></a>使用者通知的相關資訊

如上所述，新的使用者通知架構可讓傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （並可能修改） 本機及遠端通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂本機及遠端通知的外觀，提供給使用者的應用程式擴充功能。

此架構提供下列應用程式可以將通知傳遞給使用者的方式：

- **Visual 警示**-關閉通知會從畫面頂端的橫幅為位置。
- **音效和振動的情況下**-可以與通知相關聯。
- **應用程式圖示徽章**-顯示應用程式的圖示顯示新的內容可以使用，例如未閱讀的電子郵件訊息數目的徽章。

此外，根據使用者的目前內容中，有許多種通知將會看到：

- 如果裝置已解除鎖定，通知會向下捲動從畫面頂端做為橫幅。
- 如果裝置已鎖定，則會在使用者的鎖定畫面上顯示通知。
- 如果使用者已錯過通知，他們可以開啟通知中心，並檢視任何可用，等待通知那里。

Xamarin.iOS 應用程式有兩種類型的使用者就能夠向傳送的通知：

- **本機通知**-這些在本機安裝在使用者裝置上的應用程式所傳送。
- **遠端通知**-從遠端傳送伺服器並且呈現給使用者，或其所觸發的應用程式內容的背景更新。

### <a name="about-local-notifications"></a>本機通知的相關資訊

IOS 應用程式可以傳送本機通知具有下列功能和屬性：

- 本機使用者的裝置上的應用程式會傳送它們。 
- 它們是您可以設定使用時間或位置為基礎的觸發程序。 
- 應用程式與使用者的裝置排程通知，並符合觸發條件時，它會顯示。
- 當使用者與通知互動時，應用程式會接收回呼。

本機通知的範例包括：

- 行事曆警示
- 提醒警示
- 位置感知的觸發程序

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)文件。

### <a name="about-remote-notifications"></a>關於遠端通知

IOS 應用程式可以傳送遠端通知具有下列功能和屬性：

- 應用程式擁有與其進行通訊的伺服器端元件。
- Apple Push Notification Service (APNs) 用來傳輸遠端通知的最佳方式傳遞至使用者的裝置，從開發人員的雲端式伺服器。
- 當應用程式接收遠端通知時它會顯示給使用者。
- 當使用者互動與通知時，應用程式會接收回呼。

遠端通知的範例包括：

- 新聞警示
- 運動的更新
- 立即訊息的訊息

有兩種類型的遠端通知至 iOS 應用程式：

- **使用者對象**-使用者在裝置上看到這些。
- **無訊息更新**-這些提供一個機制來更新 iOS 應用程式在背景中的內容。 當收到無訊息的更新時，應用程式可以連到移除的伺服器會下拉最新的內容。

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)文件。

### <a name="about-the-existing-notifications-api"></a>現有的通知 API 的相關資訊

IOS 應用程式會使用在 iOS 10 之前,`UIApplication`向系統註冊通知，並排程 （可由時間和地點） 如何觸發該通知。

有數個使用現有的通知 API 時，開發人員可能會遇到的問題：

- 有個不同的回呼所需的本機或遠端通知可能會導致重複的程式碼。
- 應用程式之後有已排定與系統有限控制項的通知。
- 有個不同層級的支援所有 Apple 的現有平台。

### <a name="about-the-new-user-notification-framework"></a>有關新的使用者通知架構

使用 iOS 10，Apple 已引進新的使用者通知架構，會取代現有`UIApplication`先前所述的方法。

使用者通知架構，提供下列功能：

- 熟悉的 API，其中包含與先前的方法，方便您移植程式碼從現有的 framework 的功能同位檢查。
- 包含一組擴充的內容選項，可讓更豐富的通知傳送給使用者。
- 可由相同的程式碼和回呼處理本機和遠端通知。
- 簡化處理使用者互動的通知時，會傳送至應用程式的回呼程序。
- 增強的暫止和已傳遞的通知，包括能夠移除或更新通知的管理。
- 將執行應用程式內呈現通知的能力。
- 新增排程和處理應用程式擴充功能中的通知的能力。
- 加入新的通知本身的擴充點。 

新的使用者通知架構會提供統一的通知 API 跨平台的多個 Apple 支援包括： 

- **iOS** -完整的支援來管理及排程的通知。
- **tvOS** -將功能加入至本機及遠端通知徽章的應用程式圖示。
- **watchOS** -新增轉送至其 Apple Watch 通知使用者的配對的 iOS 裝置的功能，並授與監看式應用程式能夠執行直接在 watch 本身上的本機通知。

如需詳細資訊，請參閱 Apple [UserNotifications 架構參照](https://developer.apple.com/reference/usernotifications)並[UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)文件。

## <a name="preparing-for-notification-delivery"></a>準備通知傳遞

之前的 iOS 應用程式可以傳送通知給使用者應用程式必須向系統，並通知是使用者中斷，因為應用程式必須明確地要求權限，再將它們傳送。

有三種不同的層級的使用者可以核准應用程式的通知要求：

- 橫幅會顯示。
- 聲音的警示。
- 徽章的應用程式圖示。

此外，這些核准的層級必須要求和設定的本機和遠端的通知。

只要加上下列程式碼，以啟動應用程式不應要求通知的權限`FinishedLaunching`方法`AppDelegate`並設定所需的通知類型 (`UNAuthorizationOptions`):

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

此外，使用者可以隨時變更在任何時候使用的應用程式的通知權限**設定**在裝置上的應用程式。 應用程式應該檢查使用者的要求的通知權限之前顯示通知，使用下列程式碼：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>設定遠端通知環境

新增至 iOS 10，開發人員必須通知哪些環境推播通知以開發或生產環境執行的作業系統。 無法提供這項資訊可能會導致應用程式提交到要應用程式存放區，如下所示的通知時遭到拒絕：

> 遺漏的推播通知權利-您的應用程式包含一個適用於 Apple Push Notification service，API，但`aps-environment`權利缺少應用程式的簽章。

若要提供必要的權限，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Entitlements.plist`檔案中**Solution Pad**以開啟它進行編輯。
2. 若要切換**來源**檢視： 

    [![](enhanced-user-notifications-images/setup01.png "原始碼檢視")](enhanced-user-notifications-images/setup01.png#lightbox)
3. 按一下  **+** 按鈕以新增新的金鑰。
4. 輸入`aps-environment`for**屬性**，保留**類型**做為`String`，然後輸入其中一個`development`或`production`的**值**: 

    [![](enhanced-user-notifications-images/setup02.png "Aps 環境屬性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 將變更儲存到檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下`Entitlements.plist`檔案中**方案總管 中**以開啟它進行編輯。
3. 按一下  **+** 按鈕以新增新的金鑰。
4. 輸入`aps-environment`for**屬性**，保留**類型**做為`String`，然後輸入其中一個`development`或`production`的**值**: 

    [![](enhanced-user-notifications-images/setup02w.png "Aps 環境屬性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 將變更儲存到檔案。

-----

### <a name="registering-for-remote-notifications"></a>註冊遠端通知

如果應用程式會同時傳送和接收遠端通知時，它仍必須執行_語彙基元註冊_使用現有`UIApplication`API。 這項註冊需要裝置，以提供即時的網路連接存取 apns 來說，會產生將會傳送至應用程式的必要權杖。 應用程式需要再轉送此權杖來向遠端通知的開發人員的伺服器端應用程式：

[![](enhanced-user-notifications-images/token01.png "語彙基元註冊概觀")](enhanced-user-notifications-images/token01.png#lightbox)

您可以使用下列程式碼來初始化必要的註冊：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

通知承載一部分的 get 的從伺服器傳送至 APNs 傳送到遠端通知時，要包含需要的語彙基元會傳送給開發人員的伺服器端應用程式：

[![](enhanced-user-notifications-images/token02.png "通知承載中包含此語彙基元")](enhanced-user-notifications-images/token02.png#lightbox)

語彙基元做為繫結在一起的通知和應用程式用來開啟或回應通知的索引鍵。

如需詳細資訊，請參閱 Apple[本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)文件。

## <a name="notification-delivery"></a>通知傳遞

完全註冊應用程式和從要求的必要權限，授與使用者應用程式現在已準備好要傳送和接收通知。 

### <a name="providing-notification-content"></a>提供通知內容

新增至 iOS 10，所有通知同時都包含這兩者**標題**並**副標題**，一律會顯示與**主體**通知內容。 還新，是能夠加入**媒體附件**通知內容。

若要建立本機通知的內容，請使用下列程式碼：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

遠端通知，程序會類似：

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

建立通知的內容，應用程式需要通知會呈現給使用者的設定，排定*觸發程序*。 iOS 10 提供四種不同的觸發程序類型：

- **推播通知**-專門搭配遠端通知，並在裝置上執行的應用程式封裝 APNs 傳送通知時，會觸發。
- **時間間隔**-可讓本機通知排程的時間間隔開始，現在再結束某些未來的點。 例如： `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);` 
- **行事曆日期**-可讓特定的日期和時間排程的本機通知。
- **位置型**-可讓排程時進入或離開特定地理位置或任何藍芽指標指定靠近 iOS 裝置的本機通知。

備妥本機通知時，應用程式需要呼叫`Add`方法的`UNUserNotificationCenter`排程其顯示給使用者的物件。 遠端通知，伺服器端應用程式會傳送通知承載給 APNs，然後將傳送的封包到使用者的裝置。

請在結合所有部分，本機通知的範例可能都如下：

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

新增至 iOS 10，應用程式可以通知以不同方式處理時它是在幕前及觸發通知。 藉由提供`UNUserNotificationCenterDelegate`並實作`UserNotificationCenter`方法，應用程式可以接管負責顯示通知。 例如: 

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

此程式碼直接寫出的內容`UNNotification`應用程式的輸出並要求系統，以顯示標準的警示通知。 

如果應用程式想要在前景時顯示通知訊息本身，而且不不是使用系統預設值，傳遞`None`至完成處理常式。 範例：

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

此程式碼就緒之後，開啟`AppDelegate.cs`檔案進行編輯，並變更`FinishedLaunching`方法看起來如下所示：

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

此程式碼附加自訂`UNUserNotificationCenterDelegate`上述目前`UNUserNotificationCenter`讓應用程式可以處理通知，在作用中狀態，並在前景中。

## <a name="notification-management"></a>通知管理

新增至 iOS 10，通知管理提供暫止並傳遞通知的存取，並新增移除、 更新或升級這些通知的能力。

是通知管理很重要的一部分_要求識別碼_，時指派給通知它已建立並排程與系統。 遠端通知，這個參數會指派新`apps-collapse-id`HTTP 要求標頭中的欄位。

要求識別碼用來選取想要對通知管理的應用程式的通知。

### <a name="removing-notifications"></a>移除通知

若要從系統中移除暫止的通知，請使用下列程式碼：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

若要移除的已傳遞的通知，請使用下列程式碼：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>更新現有的通知

若要更新現有的通知，只具有所需的參數，修改 （例如新的觸發程序時間） 建立新的通知，並將它新增至具有相同的要求識別項為必須修改通知系統。 範例：


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

已傳遞的通知，會取得更新並升級至清單頂端的首頁和鎖定畫面上，並在通知中心，如果它已讀取使用者現有的通知。

## <a name="working-with-notification-actions"></a>使用 通知動作

在 iOS 10 中，傳遞給使用者的通知不是靜態的並與他們的使用者可以互動的數種方式提供 （從內建到自訂動作）。

有三種類型的 iOS 應用程式可以回應的動作：

- **預設動作**-這是當使用者點選通知以開啟應用程式，並顯示特定的通知的詳細資料。
- **自訂動作**-這些已新增在 iOS 8 中，提供快速的方式，讓使用者直接從通知執行自訂的工作，而不需要啟動應用程式。 它們可以顯示為清單中的按鈕可自訂的項目或文字輸入的欄位 （其中應用程式會提供少量的時間才能完成要求） 的背景或前景中執行 （其中啟動應用程式在前景 fulfill 要求)。 自訂動作是適用於 iOS 和 watchOS。
- **關閉動作**-此動作會傳送至應用程式，當使用者關閉特定的通知。

### <a name="creating-custom-actions"></a>建立自訂動作

若要建立並註冊自訂動作與系統，請使用下列程式碼：

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

當建立新`UNNotificationAction`，它被指派的唯一識別碼，並會出現在按鈕的標題。 根據預設，動作會建立以背景的動作，但是可以提供選項，以調整動作的行為 （例如將它設定為前景動作）。

每個建立的動作必須要與類別相關聯。 當建立新`UNNotificationCategory`、 指派的唯一識別碼，一份動作來執行，以提供類別目錄中的動作的意圖的詳細資訊和一些選項，以控制行為的類別目錄的意圖識別碼清單。

最後，所有的類別會向系統使用`SetNotificationCategories`方法。

### <a name="presenting-custom-actions"></a>呈現自訂動作

一旦已建立並向系統註冊一組自訂動作和類別目錄，他們可以看到從本機或遠端通知。

遠端通知，請設定`category`遠端通知承載符合其中一個先前建立的類別目錄中。 例如: 

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

本機通知，設定`CategoryIdentifier`屬性`UNMutableNotificationContent`物件。 例如: 

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

### <a name="handling-dismiss-actions"></a>處理關閉動作

如上所述，關閉的動作可以傳送至應用程式，當使用者關閉通知。 由於這不是標準的動作，將需要類別建立時設定的選項。 例如: 

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>處理動作的回應

當使用者互動的自訂動作與上述建立的類別時，應用程式必須滿足要求的工作。 這是藉由提供`UNUserNotificationCenterDelegate`並實作`UserNotificationCenter`方法。 例如: 

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

傳遞中`UNNotificationResponse`類別具有`ActionIdentifier`屬性，可以是預設的動作 或 關閉 動作。 使用`response.Notification.Request.Identifier`來測試任何自訂動作。

`UserText`屬性會保留任何使用者文字輸入的值。 `Notification`屬性會保留原始的通知，包括要求觸發程序和通知內容。 如果是本機或遠端的通知會根據觸發程序的類型，可以決定應用程式。

> [!NOTE]
> iOS 12 就可以自訂通知 UI 來修改其在執行階段的 [動作] 按鈕。 如需詳細資訊，看看[動態通知動作按鈕](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md)文件。

## <a name="working-with-service-extensions"></a>使用服務延伸模組

使用 遠端通知時_服務延伸_可用來啟用通知承載內的端對端加密。 服務延伸模組是通知的一個非使用者介面延伸模組 （適用於 iOS 10），與擴充或取代可見內容，它會呈現給使用者之前的主要目的在背景中執行。 

[![](enhanced-user-notifications-images/extension01.png "服務延伸模組概觀")](enhanced-user-notifications-images/extension01.png#lightbox)

服務延伸模組要快速地執行，並只可以在短時間執行的系統內。 服務延伸模組無法在分配的時間內完成其工作，就會呼叫後援方法。 如果後援的失敗，原始的通知內容將會顯示給使用者。

服務延伸模組的一些可能用法包括：

- 提供遠端通知內容的端對端的加密。
- 新增遠端的通知，讓它們更豐富的附件。

### <a name="implementing-a-service-extension"></a>實作服務延伸模組

若要實作服務延伸模組的 Xamarin.iOS 應用程式中，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 應用程式的方案在 Visual Studio for mac 中開啟
2. 中的方案名稱上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增** > **加入新的專案**。
3. 選取 [ **iOS** > **延伸模組** > **通知服務延伸**然後按一下**下一步]** 按鈕： 

    [![](enhanced-user-notifications-images/extension02.png "選取 通知服務延伸模組")](enhanced-user-notifications-images/extension02.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 [**下一步]** 按鈕： 

    [![](enhanced-user-notifications-images/extension03.png "輸入延伸模組的名稱")](enhanced-user-notifications-images/extension03.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下**建立**按鈕： 

    [![](enhanced-user-notifications-images/extension04.png "調整的專案名稱和/或方案名稱")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Visual Studio 中開啟應用程式的方案。
2. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增 > 新增專案...**.
3. 選取  **Visual C# > iOS 延伸模組 > Notification Service 延伸模組**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "選取 通知服務延伸模組")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 [**確定**] 按鈕。

-----

> [!IMPORTANT]
> 服務延伸模組的套件組合識別碼應符合套件組合識別碼與主應用程式的`.appnameserviceextension`附加至結尾。 例如，如果主要的應用程式的套件組合識別碼`com.xamarin.monkeynotify`，服務延伸模組都應該有的套件組合識別碼`com.xamarin.monkeynotify.monkeynotifyserviceextension`。 這應該會自動設定時的擴充功能加入至方案。 

中會有一個主要的類別必須修改，以提供必要的功能通知服務延伸模組。 例如: 

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

第一種方法， `DidReceiveNotificationRequest`，將會傳遞通知識別碼，以及通知內容透過`request`物件。 傳遞在`contentHandler`必須呼叫以呈現給使用者的通知。

第二個方法， `TimeWillExpire`，將會在時間即將用盡服務延伸模組來處理要求之前呼叫。 如果服務延伸模組的失敗呼叫`contentHandler`分配的時間量，在原始的內容將會顯示給使用者。

### <a name="triggering-a-service-extension"></a>觸發服務延伸模組

副檔名為服務建立及提供應用程式，它可藉由修改遠端通知裝載傳送至裝置。 例如: 

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新`mutable-content`索引鍵會指定服務延伸模組，將需要啟動來更新遠端通知內容。 `encrypted-content`機碼會保有服務延伸模組可以解密再呈現給使用者的加密的資料。

看看下列的範例服務延伸模組：

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

此程式碼會解密加密的內容，從`encrypted-content`機碼，請建立新`UNMutableNotificationContent`，設定`Body`屬性，以解密的內容，並使用`contentHandler`向使用者顯示通知。

## <a name="summary"></a>總結

這篇文章已涵蓋所有的使用者通知已經過增強，iOS 10 的方式。 它會顯示新的使用者通知架構，以及如何在 Xamarin.iOS 應用程式或應用程式擴充功能中使用它。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/documentation/usernotifications)
