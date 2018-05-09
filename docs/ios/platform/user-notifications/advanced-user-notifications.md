---
title: 進階的使用者通知
description: 本文會深入探討新使用者通知架構以及如何充分利用它 Xamarin.iOS 應用程式中。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: bd8a95afc5bdd5aed958913d63f9b6cfe853677e
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="advanced-user-notifications"></a>進階的使用者通知

_本文會深入探討新使用者通知架構以及如何充分利用它 Xamarin.iOS 應用程式中。_

新增 iOS 10、 傳遞和處理的本機和遠端通知架構可讓使用者通知。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定的一組條件，例如位置或一天的時間。

## <a name="about-user-notifications"></a>關於使用者通知

新的使用者通知架構允許的傳遞與處理本機與遠端的通知。 使用此架構，應用程式或應用程式擴充功能可以排程傳遞本機通知藉由指定的一組條件，例如位置或一天的時間。

此外，應用程式或延伸模組可以接收 （且可能會修改） 本機和遠端的通知時傳送至使用者的 iOS 裝置。

新的使用者通知 UI 架構可讓應用程式或自訂的本機和遠端通知的外觀，提供給使用者的應用程式擴充功能。

此架構提供下列應用程式可以傳送通知給使用者的方式：

- **視覺警示**-向下通知會從做為橫幅螢幕頂端位置。
- **聲音和振動的情況下**-可以與通知相關聯。
- **應用程式圖示徽章**-應用程式的圖示會顯示徽章，顯示是否有新的內容。 例如，未讀取的電子郵件訊息數目。

此外，根據使用者的目前內容中，有不同的方式，將會顯示通知：

- 如果裝置已解除鎖定，通知會向下滾動從畫面頂端做為橫幅。
- 鎖定裝置時，如果將使用者的鎖定螢幕上顯示通知。
- 如果使用者已錯過通知，他們可以開啟通知中心，並檢視任何可用，等待通知那里。

Xamarin.iOS 應用程式有兩種類型的使用者可以傳送的通知：

- **本機通知**-這些在使用者裝置上本機安裝的應用程式所傳送。
- **遠端通知**-從遠端伺服器，然後向使用者顯示傳送或觸發程序的背景更新應用程式的內容。

如需詳細資訊，請參閱我們[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文件。

## <a name="the-new-user-notification-interface"></a>新的使用者通知介面

在鎖定畫面上，提供更多的內容，例如標題、 子標題和可以呈現的選擇性媒體附件，做為橫幅頂端的裝置或通知中心中的新 UI 設計會出現在 iOS 10 中的使用者通知。

不論其中會顯示使用者通知 iOS 10 中，它會使用相同的外觀及操作與相同的特性和功能。

在 iOS 8，Apple 導入了可採取動作的通知，開發人員無法通知附加自訂動作，讓使用者對通知採取行動，而不需要啟動應用程式。 Apple iOS 9，增強可採取動作以回應與文字項目告知使用者可快速回覆通知。

使用者通知整數更屬於 iOS 10 中的使用者經驗，因為 Apple 進一步擴充之後可採取動作的通知，以支援 3D Touch，其中，使用者按下通知和自訂使用者介面的顯示畫面來提供豐富的互動與通知。

顯示自訂的使用者通知 UI 時，如果使用者互動的通知附加任何動作，自訂使用者介面可以立即更新以提供回應已變更。

新增 ios 10，使用者通知 UI API 可讓 Xamarin.iOS 應用程式輕鬆地利用這些新的使用者通知 UI 功能。

## <a name="adding-media-attachments"></a>新增媒體的附件

取得使用者之間共用的常見項目是 conte 的相片，因此 iOS 10 新增，能夠連線媒體項目 （例如相片） 直接通知，它將會呈現和通知的其餘部分以及使用者方便使用nt。

不過，由於傳送相關大小甚至的小型影像，將它附加到遠端的通知裝載會變得不切實際。 若要處理這種情況下，開發人員可以 iOS 10 使用新的服務延伸模組從其他來源 （例如 CloudKit 資料存放區） 下載映像，並將它附加至通知的內容，顯示給使用者之前。

修改服務延伸模組為遠端通知，其裝載必須標示為可變動。 例如: 

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

一旦遠端通知傳遞至裝置 （透過 APNs) 時，服務延伸模組然後可以下載所需的映像，透過任何想要的方式 (例如`NSURLSession`) 與它接收到影像之後，它就可以修改的通知和顯示的內容它給使用者。

此程序可能會如何處理程式碼中的範例如下：

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

來自 APNs 收到通知時，影像的自訂位址讀取內容，並從伺服器下載檔案。 然後`UNNotificationAttachement`建立映像的本機位置與唯一識別碼 (為`NSUrl`)。 建立可變動通知內容的副本，並加入媒體附件。 最後，會顯示通知給使用者藉由呼叫`contentHandler`。

一旦加入附件通知，系統會接管移動和管理的檔案。

除了上述遠端通知時，媒體附件也支援從本機通知，其中`UNNotificationAttachement`會建立並附加至通知以及其內容。

通知 iOS 10 中的支援的映像的媒體附件 (靜態和 Gif)、 音訊或視訊及系統會自動顯示正確的自訂使用者介面的這兩種類型的附件時通知呈現給使用者。

> [!NOTE]
> 應該小心最佳化媒體大小且花費的時間下載媒體，從遠端伺服器 （或本機通知組合媒體） 做為系統嚴格限制同時執行的應用程式服務延伸模組時。 例如，請考慮傳送映像的簡易的版本或小剪輯的視訊顯示在通知。

## <a name="creating-custom-user-interfaces"></a>建立自訂使用者介面

若要建立其使用者通知的自訂使用者介面，開發人員必須將 （新增 iOS 10） 的通知內容延伸加入至應用程式的方案。

通知內容延伸模組可讓開發人員將自己的檢視加入至通知 UI，並繪製出他們想要的任何內容。 不過，（例如按鈕或文字欄位） 的互動式 UI widgets 是**不**受到通知 UI，而且永遠不會加入自訂的 UI 設計。

若要支援使用者通知與使用者互動，自訂動作都應該建立、 向系統和排程與系統之前附加到通知。 通知內容延伸模組將被呼叫來處理這些動作的處理。 請參閱[使用通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)區段[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文件，如需有關自訂動作。

當使用者通知與自訂 UI 呈現給使用者時，它會有下列項目：

[![](advanced-user-notifications-images/customui01.png "使用者通知與自訂 UI 項目")](advanced-user-notifications-images/customui01.png#lightbox)

如果使用者互動 （通知下方所顯示） 的自訂動作時，您就可以更新使用者介面提供的使用者意見反應，做為其叫用指定的動作時，發生了什麼事。

### <a name="adding-a-notification-content-extension"></a>正在將通知內容擴充功能

若要實作自訂使用者通知 UI Xamarin.iOS 應用程式中，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Visual Studio 中開啟應用程式的方案，for mac。
2. 以滑鼠右鍵按一下方案名稱在**方案板**選取**新增** > **加入新的專案**。
3. 選取**iOS** > **延伸** > **通知內容延伸**按一下**下一步**按鈕： 

    [![](advanced-user-notifications-images/notify01.png "選取通知內容的延伸模組")](advanced-user-notifications-images/notify01.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**下一步**按鈕： 

    [![](advanced-user-notifications-images/notify02.png "輸入副檔名的名稱")](advanced-user-notifications-images/notify02.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下 **建立**按鈕： 

    [![](advanced-user-notifications-images/notify03.png "調整的專案名稱和/或方案名稱")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 Visual Studio 中開啟應用程式的方案，for mac。
2. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**新增 > 新的專案...**.
3. 選取**Visual C# > iOS 擴充功能 > 通知內容延伸**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "選取通知內容的延伸模組")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**確定** 按鈕。

-----

通知內容延伸加入至方案時，將會擴充功能的專案中建立三個檔案：

1. `NotificationViewController.cs` -這是通知內容延伸的主要檢視控制站。
2. `MainInterface.storyboard` -其中開發人員會配置看 UI iOS 設計工具的通知內容延伸模組。
3. `Info.plist` -控制通知內容擴充的組態。

預設值`NotificationViewController.cs`檔看起來如下所示：

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

`DidReceiveNotification`呼叫方法時，以便通知內容延伸可以填入自訂使用者介面的內容，通知由使用者展開`UNNotification`。 如上述範例中，標籤已加入至檢視，公開給具有名稱的程式碼`label`並用來顯示通知的本文。

### <a name="setting-the-notification-content-extensions-categories"></a>設定為通知內容延伸類別

系統必須了解如何尋找其回應的特定類別為基礎的應用程式的通知內容延伸。 請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下副檔名`Info.plist`檔案中**方案板**開啟進行編輯。
2. 切換至**來源**檢視。
3. 展開`NSExtension`索引鍵。
4. 新增`UNNotificationExtensionCategory`金鑰類型為**字串**副檔名所屬的類別目錄的值 (在此範例中 ' 事件邀請): 

    [![](advanced-user-notifications-images/customui02.png "新增 UNNotificationExtensionCategory 機碼")](advanced-user-notifications-images/customui02.png#lightbox)
5. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下副檔名`Info.plist`檔案**方案總管 中**開啟進行編輯。
3. 展開`NSExtension`索引鍵。
4. 新增`UNNotificationExtensionCategory`金鑰類型為**字串**副檔名所屬的類別目錄的值 (在此範例中 ' 事件邀請): 

    [![](advanced-user-notifications-images/customui02w.png "新增 UNNotificationExtensionCategory 機碼")](advanced-user-notifications-images/customui02w.png#lightbox)
5. 儲存您的變更。

-----

通知內容的延伸模組類別 (`UNNotificationExtensionCategory`) 使用相同的類別值，可用來註冊通知動作。 其中的應用程式會使用相同的 UI 的多個分類的情況下，切換`UNNotificationExtensionCategory`類型**陣列**並提供所有必要的類別。 例如: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui03.png "通知內容的延伸模組類別")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui03w.png "通知內容的延伸模組類別")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隱藏預設的通知內容

在其中自訂通知 UI 將會顯示相同的內容做為預設值 （標題、 子標題和自動顯示在通知 UI 底部的主體） 的通知的情況下，可以隱藏這項預設資訊加`UNNotificationExtensionDefaultContentHidden`鍵`NSExtensionAttributes`金鑰類型為**布林**值是`YES`中的延伸模組`Info.plist`檔案：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui04.png "尋找預設的資訊")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui04w.png "尋找預設的資訊")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>設計自訂使用者介面

若要設計通知內容擴充功能的自訂使用者介面，按兩下`MainInterface.storyboard`檔案以開啟設計工具中，在 iOS 中進行編輯，拖曳項目，您必須先建置所需的介面中 (例如`UILabels`和`UIImageViews`)。

> [!NOTE]
> 沒有通知 UI_不_通知內容的延伸模組中支援互動式的控制項，例如文字欄位或按鈕。 雖然可以將它們加入分鏡腳本，使用者將無法與它們互動。 若要加入自訂的通知使用者介面中的使用者互動，請改用自訂動作。

一旦 UI 配置，並以 C# 程式碼公開必要的控制項，開啟`NotificationViewController.cs`進行編輯和修改`DidReceiveNotification`填入 UI，當使用者展開通知的方法。 例如: 

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

### <a name="setting-the-content-area-size"></a>將內容區域的大小設定

下列程式碼設定來調整內容區域對使用者顯示的大小，`PreferredContentSize`屬性`ViewDidLoad`至所需大小的方法。 這個大小可能也會套用到 iOS 設計工具中檢視的條件約束來調整，會維持到開發人員選擇最適合這些方法。

因為系統已在執行之前叫用內容延伸時的通知內容區域的通知將會開始完整大小，和動畫顯示到呈現給使用者時所要求的大小。

若要消除這種效果，請編輯`Info.plist`檔案延伸模組和組`UNNotificationExtensionInitialContentSizeRatio`索引鍵`NSExtensionAttributes`輸入的索引鍵**數目**與值，代表所要的比率。 例如: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](advanced-user-notifications-images/customui05.png "UNNotificationExtensionInitialContentSizeRatio 金鑰")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](advanced-user-notifications-images/customui05w.png "UNNotificationExtensionInitialContentSizeRatio 金鑰")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自訂 UI 中使用媒體的附件

因為媒體附加檔案 (如中所示[新增媒體附件](#Adding-Media-Attachments)上一節) 通知裝載的一部分，可以存取並顯示在通知內容延伸，就像它們會在預設UI 的通知。

例如，如果包含上述的自訂 UI `UIImageView` ，已公開給 C# 程式碼中，下列程式碼無法用來填入從媒體附件：

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

媒體附件已由系統管理，因為它是外部應用程式的沙箱。 延伸模組必須對系統通知它想存取的檔案，藉由呼叫`StartAccessingSecurityScopedResource`方法。 當擴充功能完成與檔案時，需要呼叫`StopAccessingSecurityScopedResource`釋放其連線。

### <a name="adding-custom-actions-to-a-custom-ui"></a>加入自訂的 UI 自訂動作

如前所述，通知 UI 不支援使用者互動讓 UI 設計中不支援控制項，例如文字欄位或按鈕。

相反地，標準自訂動作機制用於自訂的通知使用者介面中加入互動功能。 請參閱[使用通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)區段[增強使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文件，如需有關自訂動作。

自訂動作，除了下列內建動作也可以回應通知內容延伸：

- **預設動作**-這是當使用者點選通知，開啟應用程式，並顯示指定的通知的詳細資料。
- **關閉動作**-當使用者關閉指定的通知時，將會傳送至應用程式的這項動作。

通知內容的擴充功能也必須更新其 UI，當使用者叫用其中一個自訂動作的能力，例如顯示為已接受時的日期在使用者點選**接受**自訂動作按鈕。 此外，通知內容延伸模組可以告訴延後的通知 ui dismissal，讓使用者可以查看其動作的影響，通知關閉之前的系統。

這是藉由實作第二個版本`DidReceiveNotification`包含完成處理常式方法。 例如: 

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

藉由新增`Server.PostEventResponse`處理常式，以`DidReceiveNotification`通知內容延伸模組，擴充功能的方法*必須*處理所有的自訂動作。 延伸模組也可以藉由變更轉送到包含應用程式的自訂動作`UNNotificationContentExtensionResponseOption`。 例如: 

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自訂 UI 中的文字輸入動作

根據應用程式和通知的設計，可能需要將文字輸入 （例如，回覆郵件） 的通知使用者的時間。 就像標準通知未通知的內容延伸會有內建文字輸入動作的存取。

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

此程式碼會建立新的文字輸入的動作，並將它加入擴充功能的類別 (在`MakeExtensionCategory`) 方法。 在`DidReceive`覆寫方法，它會處理使用者輸入文字以下列程式碼：

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

如果設計呼叫會將自訂按鈕加入至文字輸入欄位，加入下列程式碼，以將其包含：

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

使用者所觸發的註解動作時，需要啟用檢視控制器和自訂文字輸入的欄位：

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

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得進階的查看 Xamarin.iOS 應用程式中使用新的使用者通知架構。 它涵蓋新增媒體的附件給本機和遠端的通知，它涵蓋使用新的使用者通知 UI 來建立自訂通知 Ui。


## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications Framework 參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
