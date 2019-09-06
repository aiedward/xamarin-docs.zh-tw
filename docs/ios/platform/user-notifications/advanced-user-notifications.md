---
title: Xamarin 中的 Advanced User 通知
description: 本文會深入探討 iOS 10 中引進的使用者通知架構。 它討論使用者通知、使用者通知介面、媒體附件、自訂使用者介面等等。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: a22e0d6979173ca29596687da8a0b54c6fc565a7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279058"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Xamarin 中的 Advanced user 通知

IOS 10 的新手，使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構，應用程式或應用程式擴充功能可以指定一組條件（例如位置或一天的時間）來排程本機通知的傳遞。

## <a name="about-user-notifications"></a>關於使用者通知

新的使用者通知架構允許傳遞和處理本機和遠端通知。 藉由使用此架構，應用程式或應用程式擴充功能可以指定一組條件（例如位置或一天的時間）來排程本機通知的傳遞。

此外，應用程式或延伸模組可以在本機和遠端通知傳遞給使用者的 iOS 裝置時，同時接收（而且可能會修改）。

新的使用者通知 UI 架構可讓應用程式或應用程式延伸模組在呈現給使用者時，自訂本機和遠端通知的外觀。

此架構提供下列方式，讓應用程式可以將通知傳遞給使用者：

- **視覺效果警示**-通知會從畫面頂端向下匯總為橫幅。
- **音效和 Vibrations** -可以與通知相關聯。
- **應用程式圖示徽章**-應用程式的圖示會在其中顯示徽章，顯示有新內容可供使用。 例如未閱讀的電子郵件訊息數。

此外，視使用者目前的內容而定，有不同的方式可以呈現通知：

- 如果裝置已解除鎖定，則會以橫幅的形式從畫面頂端向下復原通知。
- 如果裝置已鎖定，通知就會顯示在使用者的鎖定畫面上。
- 如果使用者錯過了通知，他們可以開啟 [通知中心]，並在該處查看任何可用、等待中的通知。

一個 Xamarin iOS 應用程式有兩種類型的使用者通知可以傳送：

- **本機通知**-這些是由本機使用者裝置上安裝的應用程式所傳送。
- **遠端通知**-會從遠端伺服器傳送，並顯示給使用者或觸發應用程式內容的背景更新。

如需詳細資訊，請參閱我們的[增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)檔。

## <a name="the-new-user-notification-interface"></a>新的使用者通知介面

IOS 10 中的使用者通知會以新的 UI 設計呈現，其中提供更多的內容，例如標題、子標題和選擇性媒體附件，可以在鎖定畫面上顯示，做為裝置頂端或通知中心的橫幅。

無論使用者通知在 iOS 10 中的顯示位置為何，都會以相同的外觀與風格，以及相同的特性和功能來呈現。

在 iOS 8 中，Apple 引進了可採取動作的通知，其中開發人員可以將自訂動作附加至通知，並允許使用者對通知採取行動，而不需要啟動應用程式。 在 iOS 9 中，具有快速回復的 Apple 增強可採取動作通知，可讓使用者以文字輸入回應通知。

由於使用者通知是 iOS 10 使用者體驗中不可或缺的一部分，因此 Apple 已進一步擴充可採取動作的通知，以支援 3D Touch，使用者按下通知並顯示自訂使用者介面，以提供豐富的互動通知。

當自訂使用者通知 UI 顯示時，如果使用者與任何附加至通知的動作互動，則自訂 UI 可以立即更新，以提供有關已變更內容的意見反應。

IOS 10 的新手，使用者通知 UI API 可讓 Xamarin iOS 應用程式輕鬆地利用這些新的使用者通知 UI 功能。

## <a name="adding-media-attachments"></a>新增媒體附件

使用者之間共用的一個較常見的專案是相片，因此 iOS 10 新增了將媒體專案（例如相片）直接附加至通知的功能，在此將會顯示並立即提供給使用者，以及其他通知的 content.

不過，由於傳送甚至小型影像時所牽涉到的大小，將它附加至遠端通知承載並不切合實際。 若要處理這種情況，開發人員可以使用 iOS 10 中的新服務延伸模組，從其他來源（例如 CloudKit 資料存放區）下載映射，並將其附加至通知的內容，然後才向使用者顯示。

若要讓服務延伸模組修改遠端通知，其承載必須標示為可變動。 例如：

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

請查看下列程式的總覽：

[![](advanced-user-notifications-images/extension02.png "新增媒體附件進程")](advanced-user-notifications-images/extension02.png#lightbox)

一旦將遠端通知傳遞至裝置（透過 APNs），服務延伸模組就可以透過任何想要的方式（例如`NSURLSession`）下載所需的映射，並在收到影像之後，修改通知和顯示的內容使用者。

以下是如何在程式碼中處理此程式的範例：

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

從 APNs 收到通知時，會從內容讀取影像的自訂位址，並從伺服器下載檔案。 然後， `NSUrl`會使用唯一識別碼和影像的本機位置（做為）來建立。 `UNNotificationAttachement` 系統會建立可變動的通知內容複本，並新增媒體附件。 最後，會呼叫`contentHandler`來向使用者顯示通知。

一旦將附件新增至通知之後，系統就會接管檔案的移動和管理。

除了上述的遠端通知之外，本機通知也支援媒體附件，其中`UNNotificationAttachement`會建立並附加至通知及其內容。

IOS 10 中的通知支援影像的媒體附件（靜態和 Gif）、音訊或影片，而系統將會在通知呈現給使用者時，自動顯示每一種附件類型的正確自訂 UI。

> [!NOTE]
> 在執行應用程式的服務延伸模組時，應該小心將媒體大小以及從遠端伺服器下載媒體所需的時間（或用來組合媒體以進行本機通知）進行優化。 例如，請考慮傳送影像的縮小版本或影片的小型剪輯，以在通知中呈現。

## <a name="creating-custom-user-interfaces"></a>建立自訂使用者介面

若要建立使用者通知的自訂使用者介面，開發人員必須將通知內容延伸（iOS 10 的新功能）新增至應用程式的解決方案。

通知內容延伸可讓開發人員將自己的視圖新增至通知 UI，並繪製出他們想要的任何內容。 從 iOS 12 開始，通知內容延伸模組支援互動式 UI 控制項，例如按鈕和滑杆。 如需詳細資訊，請參閱[iOS 12 檔中的互動式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md)。

若要支援使用者與使用者的互動，必須先建立自訂動作，並將其註冊至系統並附加至通知，然後再與系統進行排程。 將會呼叫通知內容延伸，以處理這些動作的處理。 如需自訂動作的詳細資訊，請參閱[增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)檔的[使用通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一節。

當具有自訂 UI 的使用者通知呈現給使用者時，它會具有下列元素：

[![](advanced-user-notifications-images/customui01.png "具有自訂 UI 元素的使用者通知")](advanced-user-notifications-images/customui01.png#lightbox)

如果使用者與自訂動作互動（出現在通知底下），可以更新使用者介面，以提供使用者在叫用指定動作時所發生的意見反應。

### <a name="adding-a-notification-content-extension"></a>新增通知內容延伸模組

若要在 Xamarin iOS 應用程式中執行自訂使用者通知 UI，請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**Solution Pad**中的方案名稱，**然後選取** > [新增] [**新增專案**]。
3. 選取 [ **iOS**  > **延伸** > 模組**通知內容延伸**]，然後按 [**下一步]** 按鈕： 

    [![](advanced-user-notifications-images/notify01.png "選取通知內容延伸模組")](advanced-user-notifications-images/notify01.png#lightbox)
4. 輸入擴充功能的**名稱**，然後按 [**下一步]** 按鈕： 

    [![](advanced-user-notifications-images/notify02.png "輸入延伸模組的名稱")](advanced-user-notifications-images/notify02.png#lightbox)
5. 視需要調整 [**專案名稱**] 和/或 [**方案名稱**]，然後按一下 [**建立**] 按鈕： 

    [![](advanced-user-notifications-images/notify03.png "調整專案名稱和（或）方案名稱")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選取 [**加入 > 新增專案**...]。
3. 選取 **[ C# Visual > iOS 延伸模組] > 通知內容延伸**模組：

    [![](advanced-user-notifications-images/notify01.w157-sml.png "選取通知內容延伸模組")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 輸入擴充功能的**名稱**，然後按一下 [**確定]** 按鈕。

-----

將通知內容延伸新增至解決方案時，會在擴充功能的專案中建立三個檔案：

1. `NotificationViewController.cs`-這是通知內容延伸模組的主要視圖控制器。
2. `MainInterface.storyboard`-開發人員會在 iOS 設計工具中，針對通知內容延伸模組的可見 UI 進行配置。
3. `Info.plist`-控制通知內容延伸模組的設定。

預設`NotificationViewController.cs`檔案看起來如下所示：

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

當使用者展開通知時，會呼叫`UNNotification`方法，讓通知內容延伸模組可以將的內容填入自訂UI。`DidReceiveNotification` 在上述範例中，已將標籤新增至視圖，並以名稱`label`公開至程式碼，用來顯示通知的主體。

### <a name="setting-the-notification-content-extensions-categories"></a>設定通知內容延伸的類別

系統必須通知您如何根據其回應的特定類別來尋找應用程式的通知內容延伸模組。 請執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 Solution Pad 中的延伸`Info.plist`模組檔案，將它開啟以供編輯。
2. 切換至 [**來源**] 視圖。
3. 展開索引`NSExtension`鍵。
4. 以副檔名所屬類別的值（在此範例中為「事件-邀請」）新增金鑰做為類型`UNNotificationExtensionCategory`字串： 

    [![](advanced-user-notifications-images/customui02.png "新增 UNNotificationExtensionCategory 鍵")](advanced-user-notifications-images/customui02.png#lightbox)
5. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下 方案總管中的延伸`Info.plist`模組檔案，將它開啟以供編輯。
2. 展開索引`NSExtension`鍵。
3. 以副檔名所屬類別的值（在此範例中為「事件-邀請」）新增金鑰做為類型`UNNotificationExtensionCategory`字串： 

    [![](advanced-user-notifications-images/customui02w.png "新增 UNNotificationExtensionCategory 鍵")](advanced-user-notifications-images/customui02w.png#lightbox)
4. 儲存您的變更。

-----

通知內容延伸類別（`UNNotificationExtensionCategory`）會使用用來註冊通知動作的相同類別目錄值。 在應用程式將針對多個類別使用相同 UI 的情況下，請將`UNNotificationExtensionCategory`切換至類型**陣列**，並提供所需的所有類別。 例如：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "通知內容延伸類別")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "通知內容延伸類別")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隱藏預設通知內容

在自訂通知 UI 顯示與預設通知（[標題]、[副標題] 和 [主體] 自動顯示在通知 UI 底部）相同的情況下，您可以新增`UNNotificationExtensionDefaultContentHidden`下列內容來隱藏此預設資訊：金鑰的索引鍵，做為類型**布林**值， `YES`並在擴充功能`Info.plist`的檔案中包含的值： `NSExtensionAttributes`

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "尋找預設資訊")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "尋找預設資訊")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>設計自訂 UI

若要設計通知內容延伸的自訂使用者介面，請按兩下`MainInterface.storyboard`檔案以在 iOS 設計工具中進行編輯，並在您需要建立所需介面的元素中拖曳（ `UILabels`例如和`UIImageViews`）。

> [!NOTE]
> 從 iOS 12，通知內容延伸模組可以包含互動式控制項，例如按鈕和文字欄位。 如需詳細資訊，請參閱[iOS 12 檔中的互動式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md)。

一旦 UI 已配置完成，且所需的控制項已公開C#至`NotificationViewController.cs`程式碼，請開啟進行`DidReceiveNotification`編輯，然後修改方法，以在使用者展開通知時填入 UI。 例如：

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

若要調整顯示給使用者的內容區域大小，下列程式碼會將`PreferredContentSize` `ViewDidLoad`方法中的屬性設為所需的大小。 此大小也可以藉由將條件約束套用至 iOS 設計工具中的觀點來調整，讓開發人員挑選最適合其的方法。

因為在叫用通知內容延伸之前，通知系統已在執行，所以內容區域將會以完整大小開始，並在向使用者顯示時，向下動畫到要求的大小。

若要消除此效果，請`Info.plist`編輯延伸模組的檔案，並`UNNotificationExtensionInitialContentSizeRatio`將`NSExtensionAttributes`金鑰的索引鍵設定為輸入**數位**，並將值表示所需的比例。 例如：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "UNNotificationExtensionInitialContentSizeRatio 鍵")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "UNNotificationExtensionInitialContentSizeRatio 鍵")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自訂 UI 中使用媒體附件

由於媒體附件（如上面的[新增媒體附件](#adding-media-attachments)一節所示）是通知承載的一部分，因此可以存取並顯示在通知內容延伸中，就像是在預設通知 UI 中一樣。

例如，如果上述的自訂 UI 包含`UIImageView`已公開至C#程式碼的，則可以使用下列程式碼，將其填入媒體附件：

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

因為媒體附件是由系統所管理，所以它不在應用程式的沙箱中。 延伸模組需要呼叫`StartAccessingSecurityScopedResource`方法來通知系統它想要存取檔案。 當使用檔案完成擴充時，它需要呼叫`StopAccessingSecurityScopedResource`以釋放其連接。

### <a name="adding-custom-actions-to-a-custom-ui"></a>將自訂動作新增至自訂 UI

您可以使用自訂動作按鈕，將互動功能新增至自訂通知 UI。 如需自訂動作的詳細資訊，請參閱[增強型使用者通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)檔的[使用通知動作](~/ios/platform/user-notifications/enhanced-user-notifications.md)一節。

除了自訂動作以外，通知內容延伸也可以回應下列內建動作：

- **預設動作**-這是當使用者按下通知來開啟應用程式，並顯示指定通知的詳細資料時。
- **關閉動作**-當使用者關閉指定的通知時，此動作會傳送至應用程式。

通知內容延伸也能夠在使用者叫用其中一個自訂動作（例如，在使用者按下 [**接受**自訂動作] 按鈕時顯示已接受的日期）時，更新其 UI。 此外，通知內容延伸模組可以告訴系統延遲通知 UI 的關閉，讓使用者可以在通知關閉前看到其動作的效果。

這是藉由執行包含完成處理常式之`DidReceiveNotification`方法的第二個版本來完成。 例如：

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

藉由將`Server.PostEventResponse`處理常式新增`DidReceiveNotification`至通知內容延伸的方法，延伸模組*必須*處理所有自訂動作。 延伸模組也可以藉由變更`UNNotificationContentExtensionResponseOption`，將的自訂動作轉送至包含的應用程式。 例如：

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>在自訂 UI 中使用文字輸入動作

根據應用程式的設計和通知，有時可能需要使用者在通知中輸入文字（例如，回復訊息）。 通知內容延伸具有內建文字輸入動作的存取權，就像標準通知一樣。

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

此程式碼會建立新的文字輸入動作，並將它新增至擴充功能的`MakeExtensionCategory`類別目錄（在）方法中。 在覆`DidReceive`寫方法中，它會使用下列程式碼來處理使用者輸入文字：

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

如果設計會呼叫將自訂按鈕加入至文字輸入欄位，請加入下列程式碼以包含它們：

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

當使用者觸發批註動作時，必須啟用 [view controller] 和 [custom text] 輸入欄位：

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

本文已深入探討如何在 Xamarin iOS 應用程式中使用新的使用者通知架構。 其中涵蓋將媒體附件新增至本機和遠端通知，並涵蓋如何使用新的使用者通知 UI 來建立自訂通知 ui。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [UserNotifications 架構參考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本機和遠端通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
