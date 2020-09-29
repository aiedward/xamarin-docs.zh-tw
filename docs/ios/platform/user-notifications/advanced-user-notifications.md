---
title: Xamarin 中的 Advanced User 通知
description: 本文深入探討 iOS 10 引進的使用者通知架構。 它會討論使用者通知、使用者通知介面、媒體附件、自訂使用者介面等等。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: c5a352ce2bdf4df5e19e49c52966c6230af1f4b2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435319"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Xamarin 中的 Advanced user 通知

使用者通知架構是 iOS 10 的新手，可提供本機和遠端通知的傳遞和處理。 使用此架構，應用程式或應用程式擴充功能可以藉由指定一組條件（例如，一天中的位置或時間）來排程本機通知的傳遞。

## <a name="about-user-notifications"></a>關於使用者通知

新的使用者通知架構可提供本機和遠端通知的傳遞和處理。 使用此架構，應用程式或應用程式擴充功能可以藉由指定一組條件（例如，一天中的位置或時間）來排程本機通知的傳遞。

此外，應用程式或延伸模組可以接收 (，而且可能會在將本機和遠端通知傳遞給使用者的 iOS 裝置時，修改) 。

新的使用者通知 UI 架構可讓應用程式或應用程式延伸模組在呈現給使用者時，自訂本機和遠端通知的外觀。

此架構提供下列方法，讓應用程式可以將通知傳遞給使用者：

- **視覺效果警示** -通知會從螢幕頂端向下匯總為橫幅。
- **音效和振動** -可與通知相關聯。
- **應用程式圖示徽章** -其中應用程式圖示會顯示徽章，顯示有新的內容可供使用。 例如，未讀取的電子郵件訊息數目。

此外，根據使用者的目前內容，有不同的方式會顯示通知：

- 如果裝置已解除鎖定，則會將通知從畫面頂端滾動為橫幅。
- 如果裝置已鎖定，通知將會顯示在使用者的鎖定畫面上。
- 如果使用者錯過通知，他們可以開啟通知中心，並在該處查看任何可用的等候通知。

Xamarin iOS 應用程式有兩種可以傳送的使用者通知類型：

- **本機通知** -這些會由本機安裝在使用者裝置上的應用程式傳送。
- **遠端通知** -從遠端伺服器傳送，並向使用者顯示，或觸發應用程式內容的背景更新。

如需詳細資訊，請參閱我們的 [增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md) 檔。

## <a name="the-new-user-notification-interface"></a>新的使用者通知介面

IOS 10 中的使用者通知會提供新的 UI 設計，以提供更多內容，例如可在鎖定畫面上顯示的標題、子標題和選用媒體附件，作為裝置頂端或通知中心內的橫幅。

無論使用者在 iOS 10 中的何種通知為何，都能以相同的外觀和風格，以及相同的特性和功能來呈現。

在 iOS 8 中，Apple 引進了可操作的通知，開發人員可以將自訂動作附加至通知，並允許使用者對通知採取動作，而不需要啟動應用程式。 在 iOS 9 中，Apple 增強了可操作的通知與快速回復，可讓使用者以文字輸入回應通知。

由於使用者通知是 iOS 10 中使用者體驗的一部分，因此 Apple 已進一步展開可操作的通知，以支援 3D Touch，其中使用者按下通知和自訂使用者介面，以提供與通知的豐富互動。

當顯示自訂使用者通知 UI 時，如果使用者與任何附加至通知的動作互動，則可立即更新自訂 UI，以提供有關已變更內容的意見反應。

使用 iOS 10 的新功能，使用者通知 UI API 可讓 Xamarin iOS 應用程式輕鬆地利用這些新的使用者通知 UI 功能。

## <a name="adding-media-attachments"></a>新增媒體附件

在使用者之間共用的其中一個較常見的專案是相片，因此 iOS 10 新增了附加媒體 (專案的功能，像是相片) 直接到通知，在這種情況下，將會向使用者顯示並立即提供給使用者使用，以及通知內容的其餘部分。

不過，由於傳送甚至是小型影像的相關大小，因此將它附加至遠端通知承載將會變得不切實際。 若要處理這種情況，開發人員可以使用 iOS 10 中的新服務延伸模組，從另一個來源 (（例如 CloudKit 資料存放區) ）下載映射，並在向使用者顯示通知的內容之前將它附加至通知內容。

針對要由服務延伸模組修改的遠端通知，其裝載必須標記為可變動。 例如：

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

請看看下列流程的總覽：

[![新增媒體附件進程](advanced-user-notifications-images/extension02.png)](advanced-user-notifications-images/extension02.png#lightbox)

一旦透過 APNs) 將遠端通知傳送至裝置 (之後，服務延伸就可以透過任何 (方式（例如) ）下載必要的影像 `NSURLSession` ，並在收到影像之後，就可以修改通知的內容，並將其顯示給使用者。

下列範例說明如何在程式碼中處理此進程：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService (IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

當收到來自 APNs 的通知時，會從內容讀取影像的自訂位址，並從伺服器下載檔案。 然後， `UNNotificationAttachement` 會使用唯一識別碼和映射的本機位置來建立， (為 `NSUrl`) 。 系統會建立通知內容的可變動複本，並新增媒體附件。 最後，藉由呼叫，將通知顯示給使用者 `contentHandler` 。

一旦將附件新增至通知，系統就會接管檔案的移動和管理。

除了上述的遠端通知之外，本機通知也支援媒體附件，其中 `UNNotificationAttachement` 會建立並附加至通知及其內容。

IOS 10 中的通知支援影像的媒體附件 (靜態和 Gif) 、音訊或影片，而且系統會在向使用者顯示通知時，自動為每一種類型的附件顯示正確的自訂 UI。

> [!NOTE]
> 請務必小心將媒體大小和從遠端伺服器下載媒體所需的時間優化 (或將媒體用於本機通知) ，因為系統會在執行應用程式的服務延伸時，對兩者強制執行嚴格的限制。 例如，考慮傳送影像的縮小版本或影片的小型剪輯，以顯示在通知中。

## <a name="creating-custom-user-interfaces"></a>建立自訂使用者介面

若要為其使用者通知建立自訂消費者介面，開發人員必須將通知內容延伸模組新增至應用程式解決方案 (iOS 10) 的新功能。

通知內容延伸模組可讓開發人員將自己的視圖新增至通知 UI，並繪製出想要的任何內容。 從 iOS 12 開始，通知內容延伸模組支援互動式 UI 控制項，例如按鈕和滑杆。 如需詳細資訊，請參閱 [iOS 12 檔中的互動式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md) 。

若要支援使用者與使用者的互動，請先建立自訂動作、向系統註冊並附加至通知，然後再使用系統進行排程。 系統會呼叫通知內容延伸來處理這些動作的處理。 如需自訂動作的詳細資訊，請參閱[增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)檔中的「使用[通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)」一節。

當使用者提供具有自訂 UI 的使用者通知時，將會有下列元素：

[![具有自訂 UI 元素的使用者通知](advanced-user-notifications-images/customui01.png)](advanced-user-notifications-images/customui01.png#lightbox)

如果使用者與自訂動作互動 (在通知) 底下顯示，則可以更新消費者介面，以提供使用者在叫用指定動作時所發生的意見反應。

### <a name="adding-a-notification-content-extension"></a>新增通知內容延伸模組

若要在 Xamarin iOS 應用程式中執行自訂使用者通知 UI，請執行下列動作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**Solution Pad**中的方案名稱，然後選取 [**加入**  >  **新專案**]。
3. 選取 [ **iOS**  >  **延伸**模組  >  **通知內容延伸**模組]，然後按一下 [**下一步]** 按鈕： 

    [![選取通知內容延伸模組](advanced-user-notifications-images/notify01.png)](advanced-user-notifications-images/notify01.png#lightbox)
4. 輸入擴充功能的 **名稱** ，然後按 [ **下一步]** 按鈕： 

    [![輸入擴充功能的名稱](advanced-user-notifications-images/notify02.png)](advanced-user-notifications-images/notify02.png#lightbox)
5. 視需要調整 **專案名稱** 和（或） **方案名稱** ，然後按一下 [ **建立** ] 按鈕： 

    [![調整專案名稱和（或）方案名稱](advanced-user-notifications-images/notify03.png)](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 在 **方案總管** 中，以滑鼠右鍵按一下方案名稱，然後選取 [ **加入 > 新增專案**]。
3. 選取 **Visual c # > IOS 延伸模組 > 通知內容延伸**模組：

    [![選取通知內容延伸模組](advanced-user-notifications-images/notify01.w157-sml.png)](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 輸入擴充功能的 **名稱** ，然後按一下 [ **確定]** 按鈕。

-----

當通知內容延伸模組新增至方案時，會在擴充功能的專案中建立三個檔案：

1. `NotificationViewController.cs` -這是通知內容延伸的主 view 控制器。
2. `MainInterface.storyboard` -開發人員會在 iOS 設計工具中配置通知內容延伸模組的可見 UI。
3. `Info.plist` -控制通知內容延伸模組的設定。

預設檔案 `NotificationViewController.cs` 看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification`當使用者展開通知時，會呼叫方法，通知內容延伸可以使用的內容填入自訂 UI `UNNotification` 。 在上述範例中，已將標籤加入至視圖，並以名稱公開給程式碼， `label` 用來顯示通知的主體。

### <a name="setting-the-notification-content-extensions-categories"></a>設定通知內容延伸模組的類別

系統必須根據其所回應的特定類別，取得如何尋找代理程式更新內容延伸模組的通知。 執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 Solution Pad 中的延伸模組檔案 `Info.plist` ，將**Solution Pad**它開啟以供編輯。
2. 切換至 [來源] 檢視。
3. 展開機 `NSExtension` 碼。
4. `UNNotificationExtensionCategory`使用延伸模組所屬類別的值，將索引鍵新增為類型**字串**， (在此範例中的「事件-邀請) ： 

    [![新增 UNNotificationExtensionCategory 金鑰](advanced-user-notifications-images/customui02.png)](advanced-user-notifications-images/customui02.png#lightbox)
5. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下方案總管中的延伸模組檔案 `Info.plist` ，將**Solution Explorer**它開啟以供編輯。
2. 展開機 `NSExtension` 碼。
3. `UNNotificationExtensionCategory`使用延伸模組所屬類別的值，將索引鍵新增為類型**字串**， (在此範例中的「事件-邀請) ： 

    [![新增 UNNotificationExtensionCategory 金鑰](advanced-user-notifications-images/customui02w.png)](advanced-user-notifications-images/customui02w.png#lightbox)
4. 儲存變更。

-----

通知內容延伸類別 (`UNNotificationExtensionCategory`) 使用用來註冊通知動作的相同類別值。 在應用程式會針對多個類別使用相同的 UI 的情況下，請將切換 `UNNotificationExtensionCategory` 至類型 **陣列** ，並提供所有必要的類別。 例如：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![通知內容延伸類別](advanced-user-notifications-images/customui03.png)](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![通知內容延伸類別](advanced-user-notifications-images/customui03w.png)](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隱藏預設通知內容

在自訂通知 UI 將會顯示與預設通知相同的內容時 (標題、子標題和主體會自動顯示在通知 UI) 的底部，將索引 `UNNotificationExtensionDefaultContentHidden` 鍵新增至索引 `NSExtensionAttributes` 鍵做為類型 **布林** 值（ `YES` 在副檔名的檔案中），即可隱藏此預設資訊 `Info.plist` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![尋找預設資訊](advanced-user-notifications-images/customui04.png)](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![尋找預設資訊](advanced-user-notifications-images/customui04w.png)](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>設計自訂 UI

若要設計通知內容延伸的自訂使用者介面，請按兩下檔案 `MainInterface.storyboard` 將其開啟，以在 IOS 設計工具中進行編輯，拖曳您需要的元素來建立所需的介面 (例如 `UILabels` 和 `UIImageViews`) 。

> [!NOTE]
> 從 iOS 12，通知內容延伸可以包含互動式控制項，例如按鈕和文字欄位。 如需詳細資訊，請參閱 [iOS 12 檔中的互動式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md) 。

一旦 UI 已配置，且已對 c # 程式碼公開必要的控制項，請開啟 `NotificationViewController.cs` 以進行編輯，並修改 `DidReceiveNotification` 方法以在使用者展開通知時填入 UI。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>設定內容區域大小

若要調整顯示給使用者的內容區域大小，下列程式碼會將 `PreferredContentSize` 方法中的屬性設定 `ViewDidLoad` 為所需的大小。 您也可以藉由將條件約束套用至 iOS 設計工具中的觀點來調整這個大小，讓開發人員挑選最適合的方法。

因為在叫用通知內容延伸之前，通知系統已經在執行，所以內容區域將會從完整大小開始，並在向使用者呈現所要求的大小時進行動畫。

若要消除此效果，請編輯副檔名的檔案 `Info.plist` ，並將索引鍵 `UNNotificationExtensionInitialContentSizeRatio` 的索引鍵設定為輸入數位，並以 `NSExtensionAttributes` 值表示所需的比率。 **Number** 例如：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![UNNotificationExtensionInitialContentSizeRatio 鍵](advanced-user-notifications-images/customui05.png)](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![UNNotificationExtensionInitialContentSizeRatio 鍵](advanced-user-notifications-images/customui05w.png)](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自訂 UI 中使用媒體附件

因為媒體附件 (如上面 [ [新增媒體附件](#adding-media-attachments) ] 區段中所示) 是通知承載的一部分，所以可以像在預設通知 UI 中一樣存取及顯示在通知內容延伸中。

例如，如果上面的自訂 UI 包含了 `UIImageView` 對 c # 程式碼公開的，則可以使用下列程式碼，從媒體附件填入：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

因為媒體附件是由系統管理，所以它會在應用程式的沙箱之外。 延伸模組必須透過呼叫方法，通知系統它想要存取檔案 `StartAccessingSecurityScopedResource` 。 當擴充功能完成檔案時，它需要呼叫 `StopAccessingSecurityScopedResource` 來釋放其連接。

### <a name="adding-custom-actions-to-a-custom-ui"></a>將自訂動作新增至自訂 UI

自訂動作按鈕可以用來將互動功能新增至自訂通知 UI。 如需自訂動作的詳細資訊，請參閱[增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)檔中的「使用[通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)」一節。

除了自訂動作之外，通知內容延伸也可以回應下列內建動作：

- **預設動作** -當使用者按下通知以開啟應用程式，並顯示指定通知的詳細資料時。
- **關閉動作** -當使用者關閉指定的通知時，此動作會傳送至應用程式。

當使用者叫用其中一個自訂動作時，通知內容延伸也有能力更新 UI，例如，當使用者按下 [ **接受** 自訂動作] 按鈕時，顯示 [已接受]。 此外，通知內容延伸模組可以告訴系統延遲通知 UI 的關閉，讓使用者可以在通知關閉之前查看其動作的效果。

這是透過執行 `DidReceiveNotification` 包含完成處理常式之方法的第二個版本來完成。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

藉由將 `Server.PostEventResponse` 處理常式新增至 `DidReceiveNotification` 通知內容延伸模組的方法，延伸模組 *必須* 處理所有自訂動作。 延伸模組也可以藉由變更，將自訂動作轉寄至包含的應用程式 `UNNotificationContentExtensionResponseOption` 。 例如：

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自訂 UI 中的文字輸入動作

視應用程式和通知的設計而定，可能需要使用者在通知 (中輸入文字，例如回復訊息) 。 通知內容延伸可以存取內建文字輸入動作，就像標準通知一樣。

例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

此程式碼會建立新的文字輸入動作，並將它新增至) 方法中的擴充功能類別 (`MakeExtensionCategory` 。 在覆 `DidReceive` 寫方法中，它會使用下列程式碼來處理使用者輸入文字：

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

如果設計呼叫將自訂按鈕新增至文字輸入欄位，請新增下列程式碼以將其納入：

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

當使用者觸發批註動作時，必須啟用 [視圖控制器] 和 [自訂文字輸入] 欄位：

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>摘要

本文已深入探討如何在 Xamarin iOS 應用程式中使用新的使用者通知架構。 它涵蓋了將媒體附件新增至本機和遠端通知，以及使用新的使用者通知 UI 來建立自訂的通知 UI 所涵蓋的內容。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)