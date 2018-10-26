---
title: 在 Xamarin.iOS 中的進階的使用者通知
description: 這篇文章會帶您深入探討使用者通知架構，在 iOS 10 中引進。 它討論使用者通知、 使用者通知介面、 媒體附件、 自訂使用者介面等。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: b0571f826101576b402368923c2147e35aa9299e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116325"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>在 Xamarin.iOS 中的進階的使用者通知

新 iOS 10，使用者通知架構可讓傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

## <a name="about-user-notifications"></a>使用者通知的相關資訊

新的使用者通知架構用來傳遞和本機及遠端通知的處理。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （並可能修改） 本機及遠端通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂本機及遠端通知的外觀，提供給使用者的應用程式擴充功能。

此架構提供下列應用程式可以將通知傳遞給使用者的方式：

- **Visual 警示**-關閉通知會從畫面頂端的橫幅為位置。
- **音效和振動的情況下**-可以與通知相關聯。
- **應用程式圖示徽章**-如有應用程式的圖示會顯示徽章，顯示新的內容可供使用。 例如，未讀取電子郵件訊息的數目。

此外，根據使用者的目前內容中，有許多種通知將會看到：

- 如果裝置已解除鎖定，通知會向下捲動從畫面頂端做為橫幅。
- 如果裝置已鎖定，則會在使用者的鎖定畫面上顯示通知。
- 如果使用者已錯過通知，他們可以開啟通知中心，並檢視任何可用，等待通知那里。

Xamarin.iOS 應用程式有兩種類型的使用者就能夠向傳送的通知：

- **本機通知**-這些在本機安裝在使用者裝置上的應用程式所傳送。
- **遠端通知**-從遠端伺服器，而是向使用者傳送或觸發程序的背景更新應用程式的內容。

如需詳細資訊，請參閱我們[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文件。

## <a name="the-new-user-notification-interface"></a>新的使用者通知介面

在 iOS 10 中的使用者通知會顯示新的 UI 設計做為橫幅上方的裝置，或在通知中心，在鎖定畫面上，提供更多的內容，例如標題、 子標題和選擇性的媒體附件可顯示。

不論其中會顯示使用者通知，iOS 10 中，它會顯示具有相同的外觀及操作，並以相同的特性和功能。

在 iOS 8 中，Apple 已引進可採取動作的通知，開發人員無法附加自訂動作的通知，並允許使用者對通知採取動作，而不必啟動應用程式。 在 iOS 9 中，Apple 會增強可採取動作的通知和快速回覆可讓使用者回應通知與文字項目。

因為使用者通知是在 iOS 10 中的使用者體驗更加不可或缺的一部分，Apple 已進一步擴充可採取動作的通知，以支援 3D 觸控，其中使用者按下通知和自訂使用者介面的顯示畫面來提供豐富的互動與通知。

顯示自訂的使用者通知 UI 時，如果使用者所互動的任何動作附加至通知，自訂使用者介面可以立即更新以提供有關已變更的意見反應。

新增至 iOS 10，使用者通知 UI API 可讓 Xamarin.iOS 應用程式輕鬆地利用這些新的使用者通知 UI 功能。

## <a name="adding-media-attachments"></a>新增媒體附件

取得使用者之間共用的常見項目是 conte 的相片，因此 iOS 10 新增能力以附加媒體項目 （例如相片） 直接通知，將可在其中呈現和方便使用的使用者，以及剩餘的通知nt。

不過，因為傳送相關的大小甚至小型影像，將它附加到遠端的通知裝載會變得不切實際。 若要處理這種情況下，開發人員可以使用新的服務延伸模組在 iOS 10 中從其他來源 （例如 CloudKit 資料存放區） 中下載映像，並將它附加至通知的內容，顯示給使用者之前。

服務延伸模組所要修改遠端通知，其裝載必須標示為可變動。 例如: 

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

看看以下程序的概觀：

[![](advanced-user-notifications-images/extension02.png "新增媒體附件處理程序")](advanced-user-notifications-images/extension02.png#lightbox)

一旦遠端通知傳遞到裝置 （透過 APNs)，服務延伸模組可以下載所需的映像，透過任何想要的方式 (例如`NSURLSession`) 和它接收到影像之後，它可以修改的通知和顯示的內容它的使用者。

此程序可能會如何處理在程式碼中的範例如下：

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
        public NotificationService ()
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

來自 APNs 收到通知時，映像的自訂位址讀取內容，並從伺服器下載檔案。 然後`UNNotificationAttachement`建立的唯一識別碼與本機映像的位置 (為`NSUrl`)。 建立 Notification Content 的可變動複本，並加入媒體附件。 最後，通知會顯示給使用者藉由呼叫`contentHandler`。

通知加入附件之後, 系統會移動和管理的檔案。

除了上述遠端通知時，媒體附件也支援從本機通知，其中`UNNotificationAttachement`在建立並連接至通知以及其內容。

通知在 iOS 10 支援的映像的媒體附件 (靜態和 Gif)，音訊或視訊和系統會自動顯示正確的自訂 UI 的每一種類型的附件時向使用者顯示通知。

> [!NOTE]
> 應該小心以最佳化媒體大小和執行應用程式的服務延伸模組時花費的時間下載媒體，從遠端伺服器 （或本機通知的組合媒體） 做為系統加諸兩者的嚴格限制。 比方說，請考慮將映像的簡要的版本或小剪輯的視訊顯示在通知中傳送。

## <a name="creating-custom-user-interfaces"></a>建立自訂使用者介面

若要建立其使用者通知的自訂使用者介面，開發人員必須將 （新增 iOS 10） Notification Content 延伸模組加入至應用程式的方案。

通知內容延伸模組可讓開發人員將他們自己的檢視加入至通知 UI，並繪製出他們想要的任何內容。 從 iOS 12，通知內容延伸模組支援互動式 UI 控制項，例如按鈕和滑桿。 如需詳細資訊，請參閱 <<c0> [ 互動式的通知，在 iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md)文件。

若要支援使用者通知的使用者互動，自訂動作都應該建立，向系統，排程與系統之前，連接到通知。 Notification Content 延伸模組將被呼叫來處理這些動作的處理。 請參閱[使用 通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一節[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)如需詳細資訊，請在 自訂動作上的文件。

當使用者通知中使用自訂 UI 呈現給使用者時，它會具有下列項目：

[![](advanced-user-notifications-images/customui01.png "使用者通知與自訂 UI 項目")](advanced-user-notifications-images/customui01.png#lightbox)

如果使用者所互動的自訂動作 （通知下方顯示），則可以更新使用者介面，以提供使用者意見反應，做為其叫用指定的動作時，發生了什麼事。

### <a name="adding-a-notification-content-extension"></a>新增 notification content 延伸模組

若要實作自訂使用者通知 UI 的 Xamarin.iOS 應用程式中，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 應用程式的方案在 Visual Studio for mac 中開啟
2. 中的方案名稱上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增** > **加入新的專案**。
3. 選取 [ **iOS** > **延伸模組** > **通知內容延伸模組**然後按一下**下一步]** 按鈕： 

    [![](advanced-user-notifications-images/notify01.png "選取通知內容延伸模組")](advanced-user-notifications-images/notify01.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 [**下一步]** 按鈕： 

    [![](advanced-user-notifications-images/notify02.png "輸入延伸模組的名稱")](advanced-user-notifications-images/notify02.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下**建立**按鈕： 

    [![](advanced-user-notifications-images/notify03.png "調整的專案名稱和/或方案名稱")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 應用程式的方案在 Visual Studio for mac 中開啟
2. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增 > 新增專案...**.
3. 選取  **Visual C# > iOS 延伸模組 > Notification Content 延伸模組**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "選取通知內容延伸模組")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 [**確定**] 按鈕。

-----

Notification Content 延伸模組加入至方案時，將會在擴充功能的專案中建立三個檔案：

1. `NotificationViewController.cs` -這是 Notification Content 延伸模組的主要檢視控制器。
2. `MainInterface.storyboard` -其中開發人員會配置視覺化 UI 的 Notification Content 延伸模組在 iOS 設計工具中。
3. `Info.plist` -控制 Notification Content 延伸模組的組態。

預設值`NotificationViewController.cs`檔案看起來如下所示：

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

`DidReceiveNotification`方法呼叫時，讓 Notification Content 延伸模組可以填入內容的自訂 UI，通知會根據使用者展開`UNNotification`。 如上述範例中，標籤已新增至檢視，公開到程式碼名稱`label`和用來顯示通知的主體。

### <a name="setting-the-notification-content-extensions-categories"></a>設定 Notification Content 延伸模組的分類

系統需要以了解如何尋找應用程式的 Notification Content 延伸模組會根據其回應的特定類別。 請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下副檔名`Info.plist`檔案中**Solution Pad**以開啟它進行編輯。
2. 若要切換**來源**檢視。
3. 展開`NSExtension`索引鍵。
4. 新增`UNNotificationExtensionCategory`金鑰做為類型**字串**與擴充功能屬於類別目錄的值 (在此範例中 ' 事件邀請): 

    [![](advanced-user-notifications-images/customui02.png "新增 UNNotificationExtensionCategory 機碼")](advanced-user-notifications-images/customui02.png#lightbox)
5. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下副檔名`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
3. 展開`NSExtension`索引鍵。
4. 新增`UNNotificationExtensionCategory`金鑰做為類型**字串**與擴充功能屬於類別目錄的值 (在此範例中 ' 事件邀請): 

    [![](advanced-user-notifications-images/customui02w.png "新增 UNNotificationExtensionCategory 機碼")](advanced-user-notifications-images/customui02w.png#lightbox)
5. 儲存您的變更。

-----

通知內容延伸模組類別 (`UNNotificationExtensionCategory`) 使用相同的類別目錄值用來註冊通知動作。 在其中的應用程式時，會針對多個類別使用相同的 UI 的情況下，切換`UNNotificationExtensionCategory`型別**陣列**，並提供所有必要的類別。 例如: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "通知內容延伸模組類別")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "通知內容延伸模組類別")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隱藏預設通知內容

在其中自訂通知 UI 將會顯示相同的內容為預設值 （標題、 副標題和內文會自動顯示在 UI 底部的通知） 的通知的情況下，可以隱藏這項預設資訊加上`UNNotificationExtensionDefaultContentHidden`機碼`NSExtensionAttributes`金鑰做為類型**布林**值為`YES`中的延伸模組`Info.plist`檔案：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "尋找預設的資訊")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "尋找預設的資訊")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>設計自訂的 UI

若要設計 Notification Content 延伸模組的自訂使用者介面，按兩下`MainInterface.storyboard`拖曳建置所需的介面所需的項目中的檔案將它開啟以在 iOS 設計工具中編輯 (例如`UILabels`和`UIImageViews`)。

> [!NOTE]
> 從 iOS 12，開始 Notification Content 延伸模組可以包含互動的控制項，例如按鈕和文字欄位。 如需詳細資訊，請參閱 <<c0> [ 互動式的通知，在 iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md)文件。

當 UI 配置，而且必要的控制項公開至C#程式碼中，開啟`NotificationViewController.cs`編輯和修改`DidReceiveNotification`來填入 UI，當使用者展開通知的方法。 例如: 

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

下列程式碼設定來調整內容區域向使用者顯示的大小，請`PreferredContentSize`屬性中的`ViewDidLoad`至所需大小的方法。 這個大小可能也會藉由將條件約束套用至 iOS 設計工具中檢視調整，它就到開發人員挑選最適合他們的方法。

因為系統已在執行之前叫用內容的延伸模組時的通知內容區域的通知將會開始完整大小，和動畫顯示到時向使用者顯示要求的大小。

若要消除這種效果，請編輯`Info.plist`延伸模組並將設定檔`UNNotificationExtensionInitialContentSizeRatio`的索引鍵`NSExtensionAttributes`類型的索引鍵**數目**與值，表示所需的比率。 例如: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "UNNotificationExtensionInitialContentSizeRatio 金鑰")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "UNNotificationExtensionInitialContentSizeRatio 金鑰")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>使用自訂的 UI 中的媒體附件

因為媒體附件 (如中所示[新增媒體附件](#Adding-Media-Attachments)上一節) 會通知承載的一部分，可以存取並顯示在 Notification Content 延伸模組，就像您在 預設值時，其方式是UI 的通知。

例如，如果上述的自訂 UI 包含`UIImageView`的已公開至C#程式碼，下列程式碼可用來從填入媒體附件：

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

媒體的附件由系統管理，因為它是外部應用程式的沙箱。 延伸模組必須通知系統，它想存取的檔案，藉由呼叫`StartAccessingSecurityScopedResource`方法。 延伸模組完成時的檔案，還需要呼叫`StopAccessingSecurityScopedResource`釋放它的連線。

### <a name="adding-custom-actions-to-a-custom-ui"></a>將自訂動作加入至自訂 UI

自訂動作按鈕可用來將互動功能加入至自訂通知 UI。 請參閱[使用 通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一節[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文件的自訂動作的更多詳細資料。

除了自訂動作，下列內建動作也可以回應 Notification Content 延伸模組：

- **預設動作**-這是當使用者點選通知以開啟應用程式，並顯示特定的通知的詳細資料。
- **關閉動作**-此動作會傳送至應用程式，當使用者關閉特定的通知。

通知內容延伸模組也可以在使用者叫用其中一個自訂動作時，更新其 UI，例如顯示為已接受時的日期在使用者點選**接受**自訂動作按鈕。 此外，通知內容延伸模組可以告訴系統以延遲的通知 ui dismissal，所以通知關閉之前，使用者可以看到其動作的效果。

這是藉由實作第二個版本`DidReceiveNotification`包含完成處理常式的方法。 例如: 

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

藉由新增`Server.PostEventResponse`處理常式，以便`DidReceiveNotification`Notification Content 延伸模組，擴充功能方法*必須*處理所有的自訂動作。 擴充功能也可以藉由變更轉送至包含應用程式的自訂動作`UNNotificationContentExtensionResponseOption`。 例如: 

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自訂的 UI 中的文字輸入動作

根據應用程式和通知的設計，可能需要使用者輸入文字 （例如，回覆訊息） 通知的時間。 Notification Content 延伸模組有內建的文字輸入動作的存取權，就像沒有標準的通知。

例如: 

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

此程式碼會建立新的文字輸入的動作，並將它加入至擴充功能的類別目錄 (在`MakeExtensionCategory`) 方法。 在 `DidReceive`覆寫方法，它會處理使用者輸入文字，以下列程式碼：

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

如果設計呼叫會在文字輸入欄位中加入自訂按鈕，新增下列程式碼，以將其包含：

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

使用者所觸發的註解動作時，需要啟用檢視控制器和自訂的文字輸入的欄位：

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

## <a name="summary"></a>總結

這篇文章已在 Xamarin.iOS 應用程式中使用新的使用者通知架構的進階的檢視。 它涵蓋新增至本機和遠端通知的媒體附件，其中涵蓋了使用新的使用者通知 UI 來建立自訂的通知 Ui。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
