---
title: 傳送及接收推播通知，使用 Azure 通知中樞和 Xamarin.Forms
description: 這篇文章說明如何使用 Azure 通知中樞將跨平台推播通知傳送至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: fb2f108ba115690ca181738486fd8310f26bb909
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674521"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>傳送及接收推播通知，使用 Azure 通知中樞和 Xamarin.Forms

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

行動應用程式，從後端系統推送通知傳遞資訊。 Apple、 Google 和其他平台各有自己推播通知服務 (PNS)。 Azure 通知中樞可讓您集中通知處理跨平台，讓您的後端應用程式可以與單一中樞中，這會負責散發給每個平台特定 PNS 通知通訊。

將 Azure 通知中樞整合到行動裝置應用程式中，依照下列步驟：

1. [設定推播通知服務和 Azure 通知中樞](#set-up-push-notification-services-and-azure-notification-hub)。
1. [了解如何使用範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)。
1. [建立跨平台 Xamarin.Forms 應用程式](#xamarinforms-application-functionality)。
1. [設定原生的 Android 專案的推播通知](#configure-the-android-application-for-notifications)。
1. [設定原生的 iOS 專案的推播通知](#configure-ios-for-notifications)。
1. [測試使用 Azure 通知中樞通知](#test-notifications-in-the-azure-portal)。
1. [建立後端應用程式傳送通知](#create-a-notification-dispatcher)。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>設定推播通知服務和 Azure 通知中樞

Azure 通知中樞整合 Xamarin.Forms 行動應用程式大致與 Xamarin 原生應用程式中整合 Azure 通知中樞。 設定好**FCM 應用程式**依照 Firebase 主控台中的步驟[推播通知給使用 Azure 通知中樞的 Xamarin.Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging)。 完成下列步驟使用 Xamarin.Android 的教學課程：

1. 定義一個 Android 套件名稱，例如`com.xamarin.notifysample`，這此範例中使用。
1. 下載**google-services.json**透過 Firebase 主控台。 此檔案將加入 Android 應用程式以在未來的步驟。
1. 建立由 Azure 通知中樞執行個體，並為它命名。 此文件及範例使用`xdocsnotificationhub`做為中樞名稱。
1. 複製的 FCM**伺服器金鑰**並將它儲存成**API 金鑰**之下**Google (GCM/FCM)** 在您的 Azure 通知中樞。

下列螢幕擷取畫面會顯示 Google 平台設定中 「 Azure 通知中樞：

![Azure 通知中樞 Google 組態的螢幕擷取畫面](azure-notification-hub-images/fcm-notification-hub-config.png "Azure 通知中樞 Google 設定")

您必須完成適用於 iOS 裝置設定的 macOS 電腦。 下列初始步驟中設定 APNS[推播通知給使用 Azure 通知中樞的 Xamarin.iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file)。 完成下列步驟使用 Xamarin.iOS 的教學課程：

1. 定義 iOS 套件組合識別碼。 此文件及範例使用`com.xamarin.notifysample`為套件組合識別碼。
1. 建立憑證簽署要求 (CSR) 檔案，並使用它來產生推播通知憑證。
1. 將推播通知憑證上傳**Apple (APNS)** 在您的 Azure 通知中樞。

下列螢幕擷取畫面會顯示 Apple 平台設定中 「 Azure 通知中樞：

![Azure 通知中樞 Apple 組態的螢幕擷取畫面](azure-notification-hub-images/apns-notification-hub-config.png "Azure 通知中樞 Apple 組態")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>向 Azure 通知中樞的範本和標記

Azure 通知中樞需要向中樞註冊、 定義範本和訂閱來標記的行動應用程式。 註冊會連結到 「 Azure 通知中樞中的識別項的平台特定 PNS 控制代碼。 若要深入了解註冊，請參閱[註冊管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。

範本可讓裝置，以指定參數化的訊息範本。 每個裝置，每個標記，您可以自訂內送訊息。 若要深入了解範本，請參閱[範本](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

標記可以用來訂閱新聞、 運動等天氣的訊息類別。 為了簡單起見，範例應用程式，請定義具有單一參數，呼叫的預設範本`messageParam`，而且單一標記中呼叫`default`。 在更複雜的系統中，使用者專屬標記可用來跨裝置的個人化通知訊息的使用者。 若要深入了解標記，請參閱[路由與標記運算式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。

若要成功接收訊息，每個原生應用程式必須執行下列步驟：

1. 取得從平台 PNS 的 PNS 控制代碼或語彙基元。
1. 使用 Azure 通知中樞註冊的 PNS 控制代碼。
1. 指定包含外寄訊息的相同參數的範本。
1. 訂閱外寄訊息的目標的標記。

在每個平台將會詳細說明這些步驟[設定 Android 應用程式，以通知](#configure-the-android-application-for-notifications)並[設定 iOS 的通知](#configure-ios-for-notifications)區段。

## <a name="xamarinforms-application-functionality"></a>Xamarin.Forms 應用程式的功能

Xamarin.Forms 應用程式範例會顯示一份推播通知訊息。 這透過`AddMessage`方法，將指定的推播通知訊息加入至 UI。 這個方法也會防止重複的訊息加入至 UI，並會在主執行緒上執行，因此它可以從任何執行緒呼叫。 下列程式碼顯示 `AddMessage` 方法：

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

範例應用程式含有**AppConstants.cs**檔案，定義平台專案所使用的屬性。 此檔案必須可供自訂您的 Azure 通知中樞的值。 下列程式碼示範**AppConstants.cs**檔案：

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

自訂中的下列值`AppConstants`範例應用程式連接到您的 Azure 通知中樞：

* `NotificationHubName`：使用您在 Azure 入口網站中建立的 Azure 通知中樞名稱。
* `ListenConnectionString`：此值位於在 「 Azure 通知中樞**存取原則**。

下列螢幕擷取畫面顯示在 Azure 入口網站中這些值的位置：

![Azure 通知中樞存取原則的螢幕擷取畫面](azure-notification-hub-images/notification-hub-access-policy.png "Azure 通知中樞存取原則")

## <a name="configure-the-android-application-for-notifications"></a>設定 Android 應用程式，以通知

請完成下列步驟來設定 Android 應用程式來接收和處理通知：

1. 設定 Android**封裝名稱**以符合在 Firebase 主控台中的封裝名稱。
1. 安裝下列 NuGet 封裝互動 Google Play、 Firebase 與 Azure 通知中樞：
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging。
    1. Xamarin.Azure.NotificationHubs.Android.
1. 複製`google-services.json`檔案，您已下載 FCM 安裝至專案時，並將建置動作設定成`GoogleServicesJson`。
1. [設定與 Firebase 通訊 AndroidManifest.xml](#configure-android-manifest)。
1. [Firebase 與 Azure 通知中樞註冊的應用程式使用`FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice)。
1. [處理的訊息`FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice)。
1. [將內送通知新增至 Xamarin.Forms UI](#add-incoming-notifications-to-the-xamarinforms-ui)。

> [!NOTE]
> **GoogleServicesJson**建置動作屬於**Xamarin.GooglePlayServices.Base** NuGet 套件。 Visual Studio 2019 在啟動期間設定可用的建置動作。 如果您看不見**GoogleServicesJson**作為建置動作之後, 重新啟動 Visual Studio 2019 安裝 NuGet 套件。

### <a name="configure-android-manifest"></a>設定 Android 資訊清單

`receiver`內的項目`application`項目允許應用程式與 Firebase 通訊。 `uses-permission`項目允許的應用程式來處理訊息，並向 Azure 通知中樞註冊。 完整**AndroidManifest.xml**看起來應該類似以下範例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>使用自訂 FirebaseInstanceIdService 註冊

Firebase 簽發唯一識別的裝置上的 PNS 的權杖。 權杖有很長的存留時間，但偶爾重新整理。 當發行或重新整理權杖時，應用程式必須向 Azure 通知中樞註冊新的權杖。 註冊由衍生自類別的執行個體`FirebaseInstanceIdService`。

在範例應用程式`FirebaseRegistrationService`類別繼承自`FirebaseInstanceIdService`。 這個類別具有`IntentFilter`，其中包含`com.google.firebase.INSTANCE_ID_EVENT`，允許 Android OS 自動呼叫`OnTokenRefresh`Firebase 所簽發權杖時。

下列程式碼顯示自訂`FirebaseInstanceIdService`從範例應用程式：

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

`SendRegistrationToServer`方法中的`FirebaseRegistrationClass`向 Azure 通知中樞註冊裝置，並訂閱使用範本的標籤。 範例應用程式定義的單一標記`default`及呼叫具有單一參數的樣板`messageParam`中**AppConstants.cs**檔案。 如需註冊、 標記和範本的詳細資訊，請參閱[向 Azure 通知中樞的範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>FirebaseMessagingService 程序訊息

內送訊息會路由傳送至`FirebaseMessagingService`執行個體，其中它們可以轉換成本機通知。 範例應用程式中的 Android 專案包含類別，稱為`FirebaseService`繼承自`FirebaseMessagingService`。 這個類別具有`IntentFilter`，其中包含`com.google.firebase.MESSAGING_EVENT`，允許 Android OS 自動呼叫`OnMessageReceived`收到推播通知訊息時。

下列範例所示`FirebaseService`從範例應用程式：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

內送訊息會轉換成使用本機通知`SendLocalNotification`方法。 這個方法會建立新`Intent`，並將訊息放在內容讀入`Intent`作為`string` `Extra`。 當使用者點選本機通知，應用程式是否在前景或背景`MainActivity`會啟動，並可透過訊息內容的存取`Intent`物件。

本機通知和`Intent`範例需要使用者能夠點選動作的通知。 使用者應該之前採取動作的應用程式狀態變更時，這是理想。 不過，您可以存取訊息資料，而不需要使用者動作，在某些情況下。 前一個範例也會將訊息傳送至目前直接`MainPage`執行個體與`SendMessageToMainPage`方法。 在生產環境，如果您實作單一的訊息類型，這兩種方法中`MainPage`物件將會收到重複的訊息，如果在使用者點選通知。

> [!NOTE]
> 如果它在背景或前景執行 Android 的應用程式只會收到推播通知。 若要接收推播通知時主要`Activity`是未執行，您必須實作的服務，已超出此範例的範圍。 如需詳細資訊，請參閱[建立 Android 服務](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>將內送通知新增至 Xamarin.Forms UI

`MainActivity`類別需要取得處理通知及管理傳入的訊息資料的權限。 下列程式碼示範完整`MainActivity`實作：

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

`Activity`屬性會設定應用程式`LaunchMode`至`SingleTop`。 此模式中啟動會告知 Android OS，只允許此活動的單一執行個體。 在此的啟動模式，內送`Intent`資料路由至`OnNewIntent`方法，它會擷取訊息資料，並將它傳送至`MainPage`執行個體透過`AddMessage`方法。 如果您的應用程式使用不同的啟動模式，它必須處理`Intent`資料以不同的方式。

`OnCreate`方法使用 helper 方法，稱為`IsPlayServiceAvailable`以確保裝置支援 Google Play 服務。 模擬器或裝置不支援 Google Play 服務無法從 Firebase 接收推播通知。

## <a name="configure-ios-for-notifications"></a>設定 iOS 的通知

設定 iOS 應用程式以接收通知的程序是：

1. 設定**套件組合識別碼**中**Info.plist**檔案，以符合佈建設定檔中使用的值。
1. 新增**啟用推播通知**選項設定為**Entitlements.plist**檔案。
1. 新增**Xamarin.Azure.NotificationHubs.iOS** NuGet 封裝加入您的專案。
1. [向 APNS 註冊通知](#register-for-notifications-with-apns)。
1. [使用 Azure 通知中樞註冊的應用程式，並訂閱標記](#register-with-azure-notification-hub-and-subscribe-to-tags)。
1. [將 APNS 通知新增至 Xamarin.Forms UI](#add-apns-notifications-to-xamarinforms-ui)。

下列螢幕擷取畫面示**啟用推播通知**中選取選項**Entitlements.plist** Visual Studio 內的檔案：

![螢幕擷取畫面的推播通知的權利](azure-notification-hub-images/push-notification-entitlement.png "推播通知的權利")

### <a name="register-for-notifications-with-apns"></a>註冊與 APNS 的通知

`FinishedLaunching`方法中的**AppDelegate.cs**必須覆寫檔案，來註冊遠端通知。 註冊是根據所使用的裝置上的 iOS 版本而有所不同。 範例應用程式中的 iOS 專案會覆寫`FinishedLaunching`方法來呼叫`RegisterForRemoteNotifications`如下列範例所示：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>向 Azure 通知中樞，並訂閱標記

當裝置已成功註冊的遠端通知期間`FinishedLaunching`方法，iOS 會呼叫`RegisteredForRemoteNotifications`方法。 應該覆寫這個方法，以執行下列動作：

1. 具現化`SBNotificationHub`。
1. 取消註冊任何現有的註冊。
1. 使用通知中樞註冊裝置。
1. 訂閱特定標籤的範本。

如需註冊的裝置、 範本和標記，請參閱 <<c0> [ 向 Azure 通知中樞的範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)。 下列程式碼示範如何註冊裝置和範本：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> 註冊遠端通知可能在沒有網路連線等情況下會失敗。 您可以選擇覆寫`FailedToRegisterForRemoveNotifications`方法以處理註冊失敗。

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>將 APNS 通知新增至 Xamarin.Forms UI

當裝置收到遠端通知，iOS 呼叫`ReceivedRemoteNotification`方法。 內送訊息的 JSON 轉換成`NSDictionary`物件，而`ProcessNotification`方法會從字典擷取值，並將它們傳送至 Xamarin.Forms`MainPage`執行個體。 `ReceivedRemoteNotifications`方法會覆寫來呼叫`ProcessNotification`如下列程式碼所示：

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>在 Azure 入口網站中測試通知

Azure 通知中樞可讓您檢查您的應用程式可以接收測試訊息。 **測試傳送**通知中樞內的區段可讓您選擇的目標平台，並傳送訊息。 設定**傳送至標籤運算式**要`default`會將訊息傳送至已註冊的範本的應用程式`default`標記。 按一下 **傳送**按鈕會產生包含訊息到達的裝置數目的報表。 下列螢幕擷取畫面顯示 Azure 入口網站中的 Android 通知測試：

![Azure 通知中樞測試訊息的螢幕擷取畫面](azure-notification-hub-images/azure-notification-hub-test-send.png "Azure 通知中樞測試訊息")

### <a name="testing-tips"></a>測試祕訣

1. 在測試應用程式可以接收推播通知時，您必須使用實體裝置。 Android 和 iOS 的虛擬裝置可能未正確設定以接收推播通知。
1. 範例的 Android 應用程式註冊其語彙基元和範本之後何時發出 Firebase 語彙基元。 在測試期間，您可能需要要求新權杖，並重新向 Azure 通知中樞。 若要強制執行，最好是清除您的專案，請刪除`bin`和`obj`資料夾從裝置解除安裝應用程式，再重新建置及部署。
1. 推播通知流程中的許多部分會以非同步方式執行。 這可能會導致未叫用或未預期的順序叫用的中斷點。 而不會中斷應用程式流程中使用追蹤執行的裝置或偵錯記錄。 Android 裝置記錄檔使用篩選`DebugTag`中所指定`Constants`。

## <a name="create-a-notification-dispatcher"></a>建立通知發送器

Azure 通知中樞可讓您跨平台分派通知到裝置的後端應用程式。 此範例會示範使用通知分派**NotificationDispatcher**主控台應用程式。 應用程式包括**DispatcherConstants.cs**檔案，其中會定義下列屬性：

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

您必須設定**DispatcherConstants.cs**以符合您的 Azure 通知中樞組態。 值`SubscriptionTags`屬性應該符合用戶端應用程式中使用的值。 `NotificationHubName`屬性是您的 Azure 通知中樞執行個體的名稱。 `FullAccessConnectionString`屬性是位於您的通知中樞的存取金鑰**存取原則**。 下列螢幕擷取畫面顯示的位置`NotificationHubName`和`FullAccessConnectionString`在 Azure 入口網站中的屬性：

![螢幕擷取畫面的 Azure 通知中樞名稱和 FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "Azure 通知中樞名稱和 FullAccessConnectionString")

主控台應用程式中執行迴圈每`SubscriptionTags`值，並將通知傳送給訂閱者使用的執行個體`NotificationHubClient`類別。 下列程式碼會顯示主控台應用程式`Program`類別：

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

範例主控台應用程式執行時，則可以按下空格鍵來傳送訊息。 所提供的裝置執行用戶端應用程式應該會收到已編號的通知，他們都已正確設定。

## <a name="related-links"></a>相關連結

* [推播通知範本](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。
* [裝置註冊管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。
* [路由與標記運算式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。
* [Xamarin.Android 的 Azure 通知中樞教學課程](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)。
* [Xamarin.iOS 的 Azure 通知中樞教學課程](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)。
