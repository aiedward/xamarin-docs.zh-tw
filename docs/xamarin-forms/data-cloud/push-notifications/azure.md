---
title: "從 Azure 行動應用程式中傳送推播通知"
description: "Azure 通知中心提供可擴充的推播基礎結構，將行動推播通知從任何後端傳送至任何行動裝置的平台，同時不需與各平台通知系統進行通訊的後端的複雜性。 本文說明如何使用 Azure 通知中樞傳送推播通知從 Azure 行動應用程式執行個體 Xamarin.Forms 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: f0f767179a9280d7a6c6d7ce8125696d5e664cba
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>從 Azure 行動應用程式中傳送推播通知

_Azure 通知中心提供可擴充的推播基礎結構，將行動推播通知從任何後端傳送至任何行動裝置的平台，同時不需與各平台通知系統進行通訊的後端的複雜性。本文說明如何使用 Azure 通知中樞傳送推播通知從 Azure 行動應用程式執行個體 Xamarin.Forms 應用程式。_

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure 通知中樞和推送透過 Xamarin.Forms，藉由[Xamarin 大學](https://university.xamarin.com/)**

推播通知用來傳遞資訊，例如訊息時，從行動裝置上的應用程式，以提高應用程式部署和使用方式的後端系統。 可以傳送通知在任何時間，即使使用者未主動使用目標應用程式。

後端系統，將推播通知傳送到行動裝置透過平台通知系統 (PNS)，如下列圖表所示：

[![](azure-images/pns.png "平台通知系統")](azure-images/pns-large.png#lightbox "平台通知系統")

若要傳送推播通知後, 端系統，請連絡特定平台 PNS 傳送通知給用戶端應用程式執行個體。 這會大幅增加複雜度的後端時跨平台推播通知是必要的因為每個平台專屬 PNS 應用程式開發介面和通訊協定，必須使用後端。

Azure 通知中樞消除這種複雜性不同的平台通知系統的詳細資料，藉以允許跨平台傳送通知，則透過單一 API 呼叫，如下列圖表所示：

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

若要傳送推播通知後, 端系統只有連絡人 Azure 通知中樞，繼而與不同的平台通知系統，因此減少複雜度的後端程式碼，傳送推播通知。

Azure 行動應用程式有內建支援使用通知中樞推播通知。 從 Azure 行動應用程式執行個體的推播通知傳送至 Xamarin.Forms 應用程式的程序如下所示：

1. Xamarin.Forms 應用程式註冊具有 PNS，傳回的控制代碼。
1. Azure 行動應用程式執行個體傳送通知到其 Azure 通知中樞中，指定裝置為目標的控制代碼。
1. Azure 通知中樞會將通知傳送至適當的 PNS 裝置。
1. PNS 會將通知傳送至指定的裝置。
1. Xamarin.Forms 應用程式會處理通知，並顯示它。

範例應用程式示範 todo 清單的應用程式，其資料會儲存在 Azure 行動應用程式執行個體。 每次新項目加入至 Azure 行動應用程式執行個體，推播通知會傳送至 Xamarin.Forms 應用程式。 下列螢幕擷取畫面顯示顯示接收推播通知的每個平台：

[![](azure-images/screenshots.png "範例應用程式接收推播通知")](azure-images/screenshots-large.png#lightbox "範例應用程式接收推播通知")

如需 Azure 通知中樞的詳細資訊，請參閱[Azure 通知中樞](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)和[新增推播通知給應用程式 Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/)。

## <a name="azure-and-platform-notification-system-setup"></a>Azure 與平台通知系統設定

將 Azure 通知中樞整合到 Azure 行動應用程式執行個體的程序如下所示：

1. 建立 Azure 行動應用程式執行個體。 如需詳細資訊，請參閱[取用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 設定通知中樞。 如需詳細資訊，請參閱[設定通知中樞](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub)。
1. 更新 Azure 行動應用程式執行個體傳送推播通知。 如需詳細資訊，請參閱[更新伺服器專案，以傳送推播通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications)。
1. 每個 PNS 的註冊。
1. 設定通知中樞為您與每個 PNS 通訊。

下列各節提供每個平台的其他安裝指示。

### <a name="ios"></a>iOS

下列額外的步驟，必須使用 Apple Push Notification Service (APNS) 從 Azure 通知中心進行：

1. 產生憑證簽署要求的推播憑證，使用 [金鑰鏈存取] 工具。 如需詳細資訊，請參閱[產生推播憑證的憑證簽署要求檔案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate)Azure 文件中心上。
1. 註冊 Apple 開發人員中心的推播通知支援 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱[註冊您的應用程式的推播通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications)Azure 文件中心上。
1. 建立 Apple 開發人員中心的推播通知已啟用佈建設定檔 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱[建立應用程式的佈建設定檔](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app)Azure 文件中心上。
1. 設定通知中樞為您與 APNS 通訊。 如需詳細資訊，請參閱[APNS 設定通知中樞](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns)。
1. Xamarin.Forms 應用程式設定為使用新的應用程式識別碼和佈建設定檔。 如需詳細資訊，請參閱[Xamarin Studio 中設定的 iOS 專案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio)或[Visual Studio 中設定的 iOS 專案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio)Azure 文件中心上。

### <a name="android"></a>Android

下列額外的步驟，必須使用 Firebase 雲端傳訊 (FCM) 從 Azure 通知中心進行：

1. 註冊 FCM。 伺服器 API 金鑰與用戶端識別碼，會自動產生，而且封裝於`google-services.json`下載的檔案。 如需詳細資訊，請參閱[啟用 Firebase 雲端傳訊 (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm)。
1. 設定通知中樞為您與 FCM 通訊。 如需詳細資訊，請參閱[設定回行動應用程式結束傳送推播要求使用 FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm)。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列額外的步驟，必須使用 Azure 通知中樞從 Windows 通知服務 (WNS) 進行：

1. 註冊 Windows 通知服務 (WNS)。 如需詳細資訊，請參閱[搭配 WNS 註冊您的 Windows 應用程式的推播通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns)Azure 文件中心上。
1. 設定通知中樞為您與 WNS 通訊。 如需詳細資訊，請參閱[設定通知中樞的 WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) Azure 文件中心上。

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Xamarin.Forms 應用程式中加入推播通知支援

下列章節會討論每個平台專屬專案中，才能支援推播通知的實作。

### <a name="ios"></a>iOS

實作推播通知支援 iOS 應用程式中的程序如下所示：

1. 註冊使用 Apple Push Notification Service (APNS) 中`AppDelegate.FinishedLaunching`方法。 如需詳細資訊，請參閱[向 Apple 推播通知系統](#ios_register)。
1. 實作`AppDelegate.RegisteredForRemoteNotifications`方法以處理登錄回應。 如需詳細資訊，請參閱[處理登錄回應](#ios_registration_response)。
1. 實作`AppDelegate.DidReceiveRemoteNotification`方法來處理內送的推播通知。 如需詳細資訊，請參閱[處理內送的推播通知](#ios_process_incoming)。

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>註冊 Apple 推播通知服務

IOS 應用程式可以接收推播通知之前，它必須註冊使用 Apple Push Notification Service (APNS)，這會產生唯一的裝置權杖並將它傳回至應用程式。 登錄中叫用`FinishedLaunching`中覆寫`AppDelegate`類別：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

使用 APNS 註冊的 iOS 應用程式時它就必須指定希望接收推播通知的類型。 `RegisterUserNotificationSettings`方法註冊的應用程式可以接收，通知類型與`RegisterForRemoteNotifications`註冊以接收來自 APNS 的推播通知的方法。

> [!NOTE]
> 無法呼叫`RegisterUserNotificationSettings`方法會以無訊息模式所接收的應用程式的推播通知。

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>處理登錄回應

APNS 註冊要求會在背景進行。 當收到回應時，會呼叫 iOS`RegisteredForRemoteNotifications`中覆寫`AppDelegate`類別：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

這個方法會建立簡單的通知訊息範本為 JSON，並註冊以接收通知中樞範本通知裝置。

> [!NOTE]
> `FailedToRegisterForRemoteNotifications`覆寫應該實作來處理情況，例如沒有網路連線。 這是很重要，因為使用者可能會啟動應用程式時離線。

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>處理內送的推播通知

`DidReceiveRemoteNotification`中覆寫`AppDelegate`類別用來處理內送的推播通知，當應用程式正在執行，而且收到通知時，會叫用：

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

`userInfo`字典包含`aps`索引鍵，其值是`alert`剩餘通知資料的字典。 擷取這個字典時，與`string`對話方塊中所顯示的通知訊息。

> [!NOTE]
> 如果應用程式未執行的推播通知抵達時，會啟動應用程式但`DidReceiveRemoteNotification`方法不會處理通知。 相反地，取得通知裝載，並適當地回應來自`WillFinishLaunching`或`FinishedLaunching`會覆寫。

如需 APNS 的詳細資訊，請參閱[推播通知，在 iOS 中](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)。

### <a name="android"></a>Android

實作推播通知支援 Android 應用程式中的程序如下所示：

1. 新增[Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 封裝到的 Android 專案，並設定應用程式的目標版本為 Android 7.0 或更高。
1. 新增`google-services.json`檔案，從 Firebase 主控台中，下載到 Android 專案的根目錄，並將其建置動作設定為**GoogleServicesJson**。 如需詳細資訊，請參閱[新增 Google 服務的 JSON 檔案](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。
1. 藉由宣告接收者 Android 資訊清單中的暫存器與 Firebase 雲端傳訊 (FCM) 檔案，並藉由實作`FirebaseRegistrationService.OnTokenRefresh`方法。 如需詳細資訊，請參閱[向 Firebase Cloud Messaging](#android_register_fcm)。
1. 使用中的 Azure 通知中樞註冊`AzureNotificationHubService.RegisterAsync`方法。 如需詳細資訊，請參閱[向 Azure 通知中樞註冊](#android_register_azure)。
1. 實作`FirebaseNotificationService.OnMessageReceived`方法來處理內送的推播通知。 如需詳細資訊，請參閱[顯示內容的推播通知](#android_displaying_notification)。

如需 Firebase 雲端訊息的詳細資訊，請參閱[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)和[遠端通知 Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>向 Firebase 雲端訊息

Android 應用程式可以接收推播通知之前，它必須向 FCM，這會產生註冊憑證，並將它傳回至應用程式。 如需註冊權杖的詳細資訊，請參閱[註冊 FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration)。

這會透過：

- 宣告 Android 資訊清單中的接收者。 如需詳細資訊，請參閱[宣告 Android Manifest 中的接收者](#declaring_a_receiver)。
- 實作 Firebase 執行個體識別碼的服務。 如需詳細資訊，請參閱[實作 Firebase 執行個體識別碼服務](#implementing-firebase-instance-id-service)。

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>宣告 Android 資訊清單中的收件者

編輯**AndroidManifest.xml**下列並插入`<receiver>`項目`<application>`項目：

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

這段 XML 會執行下列作業：

- 宣告內部`FirebaseInstanceIdInternalReceiver`用來安全地啟動服務的實作。
- 宣告`FirebaseInstanceIdReceiver`實作，提供每個應用程式執行個體的唯一識別碼。 此收件者也會驗證並授權的動作。

`FirebaseInstanceIdReceiver`接收`FirebaseInstanceId`和`FirebaseMessaging`事件，並傳遞至類別，衍生自`FirebasesInstanceIdService`。

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>實作 Firebase 執行個體識別碼服務

註冊應用程式與 FCM 達成方式是衍生自`FirebaseInstanceIdService`類別。 這個類別是負責產生授權存取 FCM 用戶端應用程式的安全性權杖。 範例應用程式中`FirebaseRegistrationService`類別衍生自`FirebaseInstanceIdService`類別，並在下列程式碼範例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

`OnTokenRefresh` FCM 應用程式收到的註冊權杖時，會叫用方法。 方法會擷取從語彙基元`FirebaseInstanceId.Instance.Token`FCM 以非同步方式進行更新的屬性。 `OnTokenRefresh`不常叫用方法，因為應用程式安裝或解除安裝，當使用者將會刪除應用程式資料，當應用程式會清除執行個體識別碼時，才會更新語彙基元，或者已經權杖的安全性遭到洩露。 此外，FCM 執行個體識別碼服務會要求該應用程式定期重新整理，其語彙基元，通常每 6 個月。

`OnTokenRefresh`方法也會叫用`SendRegistrationTokenToAzureNotificationHub`方法，用來與 Azure 通知中樞關聯使用者的註冊權杖。

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>向 Azure 通知中樞

`AzureNotificationHubService`類別提供`RegisterAsync`方法，其使用 Azure 通知中樞關聯使用者的註冊權杖。 下列程式碼範例示範`RegisterAsync`方法，這個方法會叫用`FirebaseRegistrationService`類別使用者的註冊權杖變更時：

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

這個方法會建立簡單的通知訊息範本做為 JSON 和暫存器收到的通知中樞，使用 Firebase 註冊憑證的範本通知。 這可確保從 Azure 通知中樞傳送任何通知目標註冊語彙基元所代表的裝置。

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>顯示內容的推播通知

顯示內容推播通知的達成方式是衍生自`FirebaseMessagingService`類別。 這個類別包含可覆寫`OnMessageReceived`方法，會叫用應用程式從 FCM 收到通知時，提供應用程式會在前景執行。 範例應用程式中`FirebaseNotificationService`類別衍生自`FirebaseMessagingService`類別，以及下列程式碼範例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

當應用程式從 FCM，收到通知`OnMessageReceived`方法會擷取訊息內容，並呼叫`SendNotification`方法。 這個方法會將訊息內容轉換成應用程式執行時，與通知出現在通知區域啟動本機通知。

##### <a name="handling-notification-intents"></a>處理通知的對應方式

當使用者點選通知時，任何伴隨的通知訊息的資料可在`Intent`額外項目。 此資料可以擷取下列程式碼：

```csharp
if (Intent.Extras != null)
{
  foreach (var key in Intent.Extras.KeySet())
  {
    var value = Intent.Extras.GetString(key);
    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
  }
}
```

應用程式的啟動器`Intent`使用者點選通知訊息，因此這段程式碼會記錄任何隨附的資料時，會引發`Intent`到輸出視窗。

### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 之前的應用程式可以接收推播通知，它必須登錄與 Windows 通知服務 (WNS)，以傳回通知通道。 所叫用註冊`InitNotificationsAsync`方法中的`App`類別：

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

這個方法會取得推播通知通道、 建立通知訊息範本為 JSON，並登錄要收到通知中樞的範本通知的裝置。

`InitNotificationsAsync`從叫用方法`OnLaunched`中覆寫`App`類別：

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

這可確保，建立或重新整理每次啟動應用程式，因此確保 WNS 發送通道永遠為使用推播通知登錄。

接收推播通知時它將會自動顯示為*快顯*– 包含訊息的非強制回應視窗。

## <a name="summary"></a>總結

本文示範如何使用 Azure 通知中樞傳送推播通知從 Azure 行動應用程式執行個體 Xamarin.Forms 應用程式。 Azure 通知中心提供可擴充的推播基礎結構，將行動推播通知從任何後端傳送至任何行動裝置的平台，同時不需與各平台通知系統進行通訊的後端的複雜性。


## <a name="related-links"></a>相關連結

- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Azure 通知中樞](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [將推播通知新增至 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [在 iOS 中的推播通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
