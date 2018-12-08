---
title: 從 Azure Mobile Apps 傳送推播通知
description: 這篇文章說明如何使用 Azure 通知中樞將推播通知從 Azure Mobile Apps 執行個體傳送至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 42ec5ddb6846ccf733f2bf18812f43e7afd45f34
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058568"
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>從 Azure Mobile Apps 傳送推播通知

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)

_Azure 通知中樞提供可調整的推播基礎結構從任何後端傳送推播通知到任何行動平台，同時不需要不同的平台通知系統與通訊的後端的複雜性。這篇文章說明如何使用 Azure 通知中樞將推播通知從 Azure Mobile Apps 執行個體傳送至 Xamarin.Forms 應用程式。_

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure 藉由將通知中樞和 Xamarin.Forms 的推送[Xamarin University](https://university.xamarin.com/)**

推播通知用來傳遞資訊，例如一則訊息，從行動裝置上的應用程式，以增加應用程式的參與和使用率的後端系統。 通知可以隨時隨地傳送在即使使用者未主動使用目標應用程式。

後端系統，將推播通知傳送至行動裝置透過平台通知系統 (PNS)，如下圖所示：

[![](azure-images/pns.png "平台通知系統")](azure-images/pns-large.png#lightbox "平台通知系統")

若要傳送推播通知後, 端系統會連絡平台特定 PNS，以傳送通知給用戶端應用程式執行個體。 這會大幅增加複雜度後, 端的跨平台推播通知時必要的因為後端必須使用每個平台特定 PNS API 和通訊協定。

Azure 通知中樞消除這種複雜性不同平台通知系統的詳細資料，藉以讓跨平台傳送通知，則使用單一 API 呼叫，如下圖所示：

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

若要傳送推播通知後, 端系統唯一連絡人 「 Azure 通知中樞，其反過來與不同的平台通知系統，因此減少複雜度的後端程式碼，傳送推播通知。

Azure Mobile Apps 都使用通知中樞的推播通知的內建支援。 從 Azure Mobile Apps 執行個體的推播通知傳送至 Xamarin.Forms 應用程式的程序如下所示：

1. Xamarin.Forms 應用程式會向 PNS，傳回的控制代碼。
1. Azure Mobile Apps 執行個體傳送通知到其 Azure 通知中樞中，指定要作為目標裝置的控制代碼。
1. Azure 通知中樞將通知傳送到適當的裝置 PNS。
1. PNS 會將通知傳送至指定的裝置。
1. Xamarin.Forms 應用程式會處理通知，並顯示它。

範例應用程式示範待辦事項清單的應用程式，其資料會儲存在 Azure Mobile Apps 執行個體。 每當新項目新增至 Azure Mobile Apps 執行個體，會將推播通知傳送至 Xamarin.Forms 應用程式。 下列螢幕擷取畫面顯示顯示收到的推播通知的每個平台：

[![](azure-images/screenshots.png "範例應用程式接收推播通知")](azure-images/screenshots-large.png#lightbox "範例應用程式接收推播通知")

如需有關 Azure 通知中樞的詳細資訊，請參閱 < [Azure 通知中樞](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)並[將推播通知新增至 Xamarin.Forms 應用程式](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/)。

## <a name="azure-and-platform-notification-system-setup"></a>Azure 與平台通知系統設定

Azure 通知中樞整合 Azure Mobile Apps 執行個體的程序如下所示：

1. 建立 Azure Mobile Apps 執行個體。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 設定通知中樞。 如需詳細資訊，請參閱 <<c0> [ 設定通知中樞](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub)。
1. 更新 Azure 行動應用程式執行個體，以傳送推播通知。 如需詳細資訊，請參閱 <<c0> [ 更新伺服器專案以傳送推播通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications)。
1. 向每個 PNS。
1. 設定通知中樞，來與每個 PNS 通訊。

下列各節提供每個平台的其他安裝指示。

### <a name="ios"></a>iOS

下列額外步驟，必須使用 Apple Push Notification Service (APNS) 從 Azure 通知中樞進行：

1. 產生憑證簽署要求推播憑證，使用 [鑰匙圈存取] 工具。 如需詳細資訊，請參閱 <<c0> [ 產生的推播憑證的憑證簽署要求檔案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate)上 Azure 文件中心。
1. 註冊推播通知支援，在 Apple 開發人員中心的 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱 <<c0> [ 註冊您的應用程式的推播通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications)上 Azure 文件中心。
1. 在 Apple Developer Center 上建立推播通知已啟用佈建設定檔 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱 <<c0> [ 建立應用程式的佈建設定檔](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app)上 Azure 文件中心。
1. 設定通知中樞為與 APNS 通訊。 如需詳細資訊，請參閱 <<c0> [ 設定適用於 APNS 的通知中樞](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns)。
1. 設定 Xamarin.Forms 應用程式，以使用新的應用程式識別碼和佈建設定檔。 如需詳細資訊，請參閱 < [Xamarin Studio 中設定 iOS 專案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio)或是[Visual Studio 中設定 iOS 專案](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio)上 Azure 文件中心。

### <a name="android"></a>Android

下列額外步驟必須執行使用 Firebase 雲端通訊 (FCM) 從 Azure 通知中樞：

1. FCM 註冊。 伺服器 API 金鑰與用戶端識別碼會自動產生，並封裝成`google-services.json`下載檔案。 如需詳細資訊，請參閱 <<c0> [ 啟用 Firebase 雲端通訊 (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm)。
1. 設定通知中樞，以使用 fcm 後進行通訊。 如需詳細資訊，請參閱 <<c0> [ 設定 Mobile Apps 後端以使用 FCM 傳送推送要求](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm)。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列額外步驟，必須使用 Azure 通知中樞從 Windows 通知服務 (WNS) 進行：

1. 註冊 Windows 通知服務 (WNS)。 如需詳細資訊，請參閱 <<c0> [ 向 WNS 註冊您的 Windows 應用程式的推播通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns)上 Azure 文件中心。
1. 設定通知中樞為與 WNS 通訊。 如需詳細資訊，請參閱 <<c0> [ 設定適用於 WNS 的通知中樞](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns)上 Azure 文件中心。

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>將推播通知支援新增至 Xamarin.Forms 應用程式

下列各節將討論支援推播通知所需的每個平台特定專案的實作。

### <a name="ios"></a>iOS

實作推播通知支援的 iOS 應用程式的程序如下所示：

1. 註冊使用 Apple Push Notification Service (APNS) 中`AppDelegate.FinishedLaunching`方法。 如需詳細資訊，請參閱 <<c0> [ 向 Apple 推播通知系統](#ios_register)。
1. 實作`AppDelegate.RegisteredForRemoteNotifications`方法以處理註冊回應。 如需詳細資訊，請參閱 <<c0> [ 處理註冊回應](#ios_registration_response)。
1. 實作`AppDelegate.DidReceiveRemoteNotification`方法來處理內送的推播通知。 如需詳細資訊，請參閱 <<c0> [ 處理內送的推播通知](#ios_process_incoming)。

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>向 Apple 推播通知服務

IOS 應用程式可以接收推播通知之前，它必須註冊使用 Apple Push Notification Service (APNS)，這會產生唯一的裝置權杖，並將它傳回應用程式。 註冊會在叫用`FinishedLaunching`覆寫中`AppDelegate`類別：

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

使用 APNS 註冊的 iOS 應用程式時，它必須指定它想要收到推播通知的類型。 `RegisterUserNotificationSettings`方法註冊的應用程式可以接收的通知類型與`RegisterForRemoteNotifications`方法接收來自 APNS 的推播通知註冊。

> [!NOTE]
> 無法呼叫`RegisterUserNotificationSettings`方法將會導致以無訊息模式所接收的應用程式的推播通知。

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>處理註冊回應

APNS 註冊要求會在背景進行。 當收到回應時，會呼叫 iOS`RegisteredForRemoteNotifications`覆寫中`AppDelegate`類別：

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

這個方法會建立簡單的通知訊息範本為 JSON，並註冊裝置以接收來自通知中樞的範本通知。

> [!NOTE]
> `FailedToRegisterForRemoteNotifications`應該實作覆寫，以處理沒有網路連線等情況。 這很重要，因為使用者可能會啟動應用程式，同時離線。

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>處理內送的推播通知

`DidReceiveRemoteNotification`覆寫中`AppDelegate`類別用來處理內送的推播通知，當應用程式正在執行，而且會叫用時收到通知：

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

`userInfo`字典包含`aps`索引鍵，其值是`alert`剩餘的通知資料字典。 擷取這個字典時，使用`string`在對話方塊中顯示的通知訊息。

> [!NOTE]
> 如果應用程式未執行的推播通知抵達時，會啟動應用程式但`DidReceiveRemoteNotification`方法不會處理通知。 相反地，取得通知裝載，並適當地回應來自`WillFinishLaunching`或`FinishedLaunching`會覆寫。

如需 APNS 的詳細資訊，請參閱[在 iOS 中的推播通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)。

### <a name="android"></a>Android

實作推播通知支援 Android 應用程式中的程序如下所示：

1. 新增[Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 封裝，Android 專案，並設定應用程式的目標版本為 Android 7.0 或更高。
1. 新增`google-services.json`檔案中，從 Firebase 主控台中，下載到 Android 專案的根目錄，並將其建置動作設定為**GoogleServicesJson**。 如需詳細資訊，請參閱 <<c0> [ 新增 Google Services JSON 檔案](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。
1. 藉由宣告接收者，以在 Android 資訊清單中的暫存器使用 Firebase 雲端通訊 (FCM) 檔案，並藉由實作`FirebaseRegistrationService.OnTokenRefresh`方法。 如需詳細資訊，請參閱 <<c0> [ 向 Firebase 雲端通訊](#android_register_fcm)。
1. 向 Azure 通知中樞註冊`AzureNotificationHubService.RegisterAsync`方法。 如需詳細資訊，請參閱 <<c0> [ 向 Azure 通知中樞](#android_register_azure)。
1. 實作`FirebaseNotificationService.OnMessageReceived`方法來處理內送的推播通知。 如需詳細資訊，請參閱 <<c0> [ 顯示的推播通知內容](#android_displaying_notification)。

如需 Firebase 雲端通訊的詳細資訊，請參閱[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)並[使用 Firebase 雲端通訊的遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>向 Firebase 雲端傳訊

Android 應用程式可以接收推播通知，它必須先註冊了 fcm 後，將產生的註冊權杖並將它傳回至應用程式。 如需有關註冊權杖的詳細資訊，請參閱[向 FCM 註冊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration)。

這被透過：

- 宣告在 Android 資訊清單中的接收器。 如需詳細資訊，請參閱 <<c0> [ 宣告在 Android 資訊清單接收者](#declaring_a_receiver)。
- 實作 Firebase 執行個體識別碼服務。 如需詳細資訊，請參閱 <<c0> [ 實作 Firebase 執行個體識別碼服務](#implementing-firebase-instance-id-service)。

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>宣告在 Android 資訊清單中的接收器

編輯**AndroidManifest.xml** ，並將下列`<receiver>`項目`<application>`項目：

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

- 宣告的內部`FirebaseInstanceIdInternalReceiver`實作，用來安全地啟動服務。
- 宣告`FirebaseInstanceIdReceiver`實作，提供每個應用程式執行個體的唯一識別碼。 此收件者也會驗證並授權動作。

`FirebaseInstanceIdReceiver`接收`FirebaseInstanceId`並`FirebaseMessaging`事件並將它們傳遞至衍生自類別`FirebasesInstanceIdService`。

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>實作 Firebase 執行個體識別碼服務

向 FCM 註冊應用程式之後，即可衍生的類別`FirebaseInstanceIdService`類別。 這個類別是負責產生安全性權杖，授權的用戶端應用程式存取 FCM 的。 在範例應用程式`FirebaseRegistrationService`類別衍生自`FirebaseInstanceIdService`類別，並在下列程式碼範例所示：

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

`OnTokenRefresh`應用程式從 FCM 收到註冊權杖時，會叫用方法。 方法會擷取來自權杖`FirebaseInstanceId.Instance.Token`FCM 會以非同步方式更新的屬性。 `OnTokenRefresh`不常叫用方法，因為應用程式是安裝或解除安裝，當使用者在應用程式清除執行個體識別碼，刪除應用程式資料時，才會更新權杖或權杖的安全性時外洩。 此外，FCM 執行個體識別碼服務會要求，應用程式定期重新整理其權杖，通常每隔 6 個月一次。

`OnTokenRefresh`方法也會叫用`SendRegistrationTokenToAzureNotificationHub`方法，用來將使用者的註冊權杖與 Azure 通知中樞產生關聯。

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>向 Azure 通知中樞

`AzureNotificationHubService`類別提供`RegisterAsync`方法，會將使用者的註冊權杖與 Azure 通知中樞產生關聯。 下列程式碼範例所示`RegisterAsync`方法，這個方法會叫用方式是`FirebaseRegistrationService`類別使用者的註冊權杖變更時：

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

這個方法會建立簡單的通知訊息範本為 JSON，並註冊以接收來自通知中樞，並使用 Firebase 註冊權杖範本通知。 這可確保從 Azure 通知中樞傳送任何通知目標註冊權杖所代表的裝置。

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>顯示內容的推播通知

顯示內容的推播通知之後，即可衍生的類別`FirebaseMessagingService`類別。 這個類別包含可覆寫`OnMessageReceived`方法，它會叫用應用程式從 FCM 收到通知時，提供應用程式會在前景執行。 在範例應用程式`FirebaseNotificationService`類別衍生自`FirebaseMessagingService`類別，並在下列程式碼範例所示：

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

應用程式從 FCM 收到通知時`OnMessageReceived`方法擷取訊息內容，並呼叫`SendNotification`方法。 這個方法會將訊息內容轉換成執行應用程式時，與通知不會出現在通知區域啟動的本機通知。

##### <a name="handling-notification-intents"></a>處理通知的對應方式

當使用者點選通知時，伴隨的通知訊息的任何資料可在`Intent`額外項目。 這項資料可以擷取為下列程式碼：

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

應用程式啟動器`Intent`當使用者點選通知訊息，因此這段程式碼會記錄任何隨附資料時會引發`Intent`到輸出視窗。

### <a name="universal-windows-platform"></a>通用 Windows 平台

之前通用 Windows 平台 (UWP) 應用程式可接收推播通知，它必須註冊使用 Windows 通知服務 (WNS)，這會傳回通知通道。 註冊叫用`InitNotificationsAsync`方法中的`App`類別：

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

這個方法會取得推播通知通道、 建立通知訊息範本為 JSON，並註冊裝置以接收來自通知中樞的範本通知。

`InitNotificationsAsync`方法會從叫用`OnLaunched`覆寫中`App`類別：

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

這可確保都會建立或每次啟動應用程式，如此可確保 WNS 推送通道永遠為使用重新整理推播通知註冊。

收到推播通知時它會自動顯示成*快顯*– 包含訊息的非強制回應視窗。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Azure 通知中樞將推播通知從 Azure Mobile Apps 執行個體傳送至 Xamarin.Forms 應用程式。 Azure 通知中樞提供可調整的推播基礎結構從任何後端傳送推播通知到任何行動平台，同時不需要不同的平台通知系統與通訊的後端的複雜性。


## <a name="related-links"></a>相關連結

- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Azure 通知中樞](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [將推播通知新增至 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [在 iOS 中的推播通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
