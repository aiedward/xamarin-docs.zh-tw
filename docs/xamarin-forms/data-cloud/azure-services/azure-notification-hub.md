---
title: 使用 Azure 通知中樞和來傳送和接收推播通知Xamarin.Forms
description: 本文說明如何使用 Azure 通知中樞，將跨平臺推播通知傳送至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- Firebase
ms.openlocfilehash: 5fd657a3d55bd26b95e79e39540dcfe5b8bce08f
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918588"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-no-locxamarinforms"></a>使用 Azure 通知中樞和來傳送和接收推播通知Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

推播通知會將來自後端系統的資訊傳遞至行動應用程式。 Apple、Google 和其他平臺各自有自己的推播通知服務 (PNS) 。 Azure 通知中樞可讓您集中跨平臺的通知，讓您的後端應用程式可以與單一中樞進行通訊，這會負責將通知散發到每個平臺特定的 PNS。

遵循下列步驟，將 Azure 通知中樞整合到行動應用程式：

1. [設定推播 Notification Services 和 Azure 通知中樞](#set-up-push-notification-services-and-azure-notification-hub)。
1. [瞭解如何使用範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)。
1. [建立跨平臺 Xamarin.Forms 應用程式](#xamarinforms-application-functionality)。
1. [針對推播通知設定原生 Android 專案](#configure-the-android-application-for-notifications)。
1. [設定推播通知的原生 iOS 專案](#configure-ios-for-notifications)。
1. [使用 Azure 通知中樞的測試通知](#test-notifications-in-the-azure-portal)。
1. [建立後端應用程式以傳送通知](#create-a-notification-dispatcher)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>設定推播通知服務和 Azure 通知中樞

將 Azure 通知中樞與行動裝置應用程式整合 Xamarin.Forms ，類似于整合 azure 通知中樞與 Xamarin 原生應用程式。 Firebase遵循 Firebase [使用 Azure 通知中樞將通知推送至 Xamarin](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging)的主控台步驟，來設定雲端通訊 (FCM) 應用程式。 使用 Xamarin. Android 教學課程來完成下列步驟：

1. 定義範例中使用的 Android 套件名稱 `com.xamarin.notifysample` ，例如。
1. `google-services.json`從主控台下載 Firebase 。 您會在未來的步驟中將此檔案新增至 Android 應用程式。
1. 建立 Azure 通知中樞實例，並為其命名。 本文和範例會使用 `xdocsnotificationhub` 做為中樞名稱。
1. 複製 FCM**伺服器金鑰**，並將它儲存為 Azure 通知中樞中**Google (GCM/FCM) **下的**API 金鑰**。

下列螢幕擷取畫面顯示 Azure 通知中樞的 Google 平臺設定：

![Azure 通知中樞 Google 設定的螢幕擷取畫面](azure-notification-hub-images/fcm-notification-hub-config.png "Azure 通知中樞 Google 設定")

您將需要 macOS 機器，才能完成 iOS 裝置的設定。 遵循[使用 Azure 通知中樞將推播通知推送至 Xamarin](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file)的初始步驟，設定 Apple Push NOTIFICATION SERVICES (APNS) 。 使用 Xamarin 教學課程來完成下列步驟：

1. 定義 iOS 套件組合識別碼。 本文和範例會使用 `com.xamarin.notifysample` 做為套件組合識別碼。
1.  (CSR) 檔案中建立憑證簽署要求，並使用它來產生推播通知憑證。
1. 將推播通知憑證上傳至 Azure 通知中樞內的**Apple (APNS) ** 。

下列螢幕擷取畫面顯示 Azure 通知中樞的 Apple 平臺設定：

![Azure 通知中樞 Apple 設定的螢幕擷取畫面](azure-notification-hub-images/apns-notification-hub-config.png "Azure 通知中樞 Apple 設定")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>使用 Azure 通知中樞註冊範本和標記

Azure 通知中樞需要行動應用程式向中樞註冊、定義範本和訂閱標記。 註冊會將平臺特定的 PNS 控制碼連結至 Azure 通知中樞內的識別碼。 若要深入瞭解註冊，請參閱[註冊管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。

範本可讓裝置指定參數化訊息範本。 內送訊息可以自訂每個裝置，每個標記。 若要深入瞭解範本，請參閱[範本](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。

標記可以用來訂閱訊息類別，例如新聞、體育和氣象。 為了簡單起見，範例應用程式會使用名為的單一參數 `messageParam` 和名為的單一標記來定義預設範本 `default` 。 在更複雜的系統中，您可以使用使用者特定的標籤，將裝置的使用者訊息用於個人化通知。 若要深入瞭解標記，請參閱[路由與標記運算式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。

若要成功接收訊息，每個原生應用程式都必須執行下列步驟：

1. 從平臺 PNS 取得 PNS 控制碼或 token。
1. 向 Azure 通知中樞註冊 PNS 控制碼。
1. 指定範本，其中包含與外寄訊息相同的參數。
1. 訂閱外寄訊息的目標標記。

針對[設定通知的 Android 應用程式](#configure-the-android-application-for-notifications)和[設定通知的 iOS](#configure-ios-for-notifications)一節中的每個平臺，會進一步詳細說明這些步驟。

## <a name="no-locxamarinforms-application-functionality"></a>Xamarin.Forms應用程式功能

範例 Xamarin.Forms 應用程式會顯示推播通知訊息的清單。 這是透過方法來達成，這會 `AddMessage` 將指定的推播通知訊息新增至 UI。 這個方法也可防止將重複的訊息加入至 UI，並在主執行緒上執行，以便從任何執行緒呼叫它。 下列程式碼顯示 `AddMessage` 方法：

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

範例應用程式包含檔案 `AppConstants.cs` ，該檔案會定義平臺專案所使用的屬性。 此檔案必須使用來自 Azure 通知中樞的值進行自訂。 下列程式碼顯示檔案 `AppConstants.cs` ：

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

在中自訂下列值 `AppConstants` ，以將範例應用程式連線至您的 Azure 通知中樞：

* `NotificationHubName`：使用您在 Azure 入口網站中建立的 Azure 通知中樞名稱。
* `ListenConnectionString`：在 Azure 通知中樞的 [**存取原則**] 下找到此值。

下列螢幕擷取畫面顯示這些值位於 Azure 入口網站中的位置：

![Azure 通知中樞存取原則的螢幕擷取畫面](azure-notification-hub-images/notification-hub-access-policy.png "Azure 通知中樞存取原則")

## <a name="configure-the-android-application-for-notifications"></a>設定 Android 應用程式的通知

請完成下列步驟，將 Android 應用程式設定為接收和處理通知：

1. 設定 Android**套件名稱**，使其符合主控台中的套件名稱 Firebase 。
1. 安裝下列 NuGet 套件，以便與 Google Play Firebase 和 Azure 通知中樞互動：
    1. `Xamarin.GooglePlayServices.Base`
    1. `Xamarin.Firebase.Messaging`
    1. `Xamarin.Azure.NotificationHubs.Android`
1. 將 `google-services.json` 您在 FCM 安裝期間下載的檔案複製到專案中，並將組建動作設為 `GoogleServicesJson` 。
1. [設定](#configure-android-manifest) `AndroidManifest.xml`與通訊 Firebase 。
1. 覆[寫](#override-firebasemessagingservice-to-handle-messages) `FirebaseMessagingService`以處理訊息。
1. [將](#add-incoming-notifications-to-the-xamarinforms-ui)傳入通知新增至 Xamarin.Forms UI。

> [!NOTE]
> `GoogleServicesJson`組建動作是 NuGet 套件的一部分 `Xamarin.GooglePlayServices.Base` 。 Visual Studio 2019 會在啟動期間設定可用的組建動作。 如果您看不 `GoogleServicesJson` 到建立動作，請在安裝 NuGet 套件後重新開機 Visual Studio 2019。

### <a name="configure-android-manifest"></a>設定 Android 資訊清單

`receiver`元素內的元素 `application` 可讓應用程式與進行通訊 Firebase 。 `uses-permission`元素可讓應用程式處理訊息，並向 Azure 通知中樞註冊。 完整的 `AndroidManifest.xml` 看起來應該與下列範例類似：

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

### <a name="override-no-locfirebasemessagingservice-to-handle-messages"></a>覆寫 `FirebaseMessagingService` 以處理訊息

若要向註冊 Firebase 並處理訊息，請將類別子類別化 `FirebaseMessagingService` 。 範例應用程式會定義 `FirebaseService` 類別，以子類別化 `FirebaseMessagingService` 。 這個類別是以屬性標記 `IntentFilter` ，其中包含 `com.google.firebase.MESSAGING_EVENT` 篩選準則。 此篩選準則可讓 Android 將傳入訊息傳遞至這個類別以進行處理：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

當應用程式啟動時， Firebase SDK 會自動向伺服器要求唯一的 token 識別碼 Firebase 。 成功要求時， `OnNewToken` 會在類別上呼叫方法 `FirebaseService` 。 範例專案會覆寫此方法，並向 Azure 通知中樞註冊權杖：

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

方法會向 `SendRegistrationToServer` Azure 通知中樞註冊裝置，並使用範本來訂閱標記。 範例應用程式會定義名為的單一標記 `default` ，以及在檔案中有一個稱為的範本 `messageParam` `AppConstants.cs` 。 如需有關註冊、標記和範本的詳細資訊，請參閱[使用 Azure 通知中樞註冊範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)。

當收到訊息時， `OnMessageReceived` 會在類別上呼叫方法 `FirebaseService` ：

```csharp
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

    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

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
```

傳入訊息會使用方法轉換成本機通知 `SendLocalNotification` 。 這個方法會建立新的 `Intent` ，並將訊息內容放入中 `Intent` 做為 `string` `Extra` 。 當使用者按下本機通知時，不論應用程式是在前景還是背景中， `MainActivity` 都會啟動，並可透過物件存取訊息內容 `Intent` 。

本機通知和 `Intent` 範例要求使用者採取動作以在通知上點擊。 當使用者應該在應用程式狀態變更之前採取動作時，這是很好的做法。 不過，在某些情況下，您可能會想要存取訊息資料，而不需要使用者動作。 先前的範例也會使用方法，直接將訊息傳送至目前的 `MainPage` 實例 `SendMessageToMainPage` 。 在生產環境中，如果您針對單一訊息類型執行這兩種方法， `MainPage` 當使用者按下通知時，該物件將會收到重複的訊息。

> [!NOTE]
> Android 應用程式只會在背景或前景中執行時，才會收到推播通知。 若要在主要未執行時接收推播通知 `Activity` ，您必須實作用於此範例範圍外的服務。 如需詳細資訊，請參閱[建立 Android 服務](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-no-locxamarinforms-ui"></a>將傳入通知新增至 Xamarin.Forms UI

`MainActivity`類別需要取得處理通知和管理傳入訊息資料的許可權。 下列程式碼顯示完整的 `MainActivity` 執行方式：

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

        if (!IsPlayServiceAvailable())
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

`Activity`屬性會將應用程式設定 `LaunchMode` 為 `SingleTop` 。 此啟動模式會告知 Android OS 只允許此活動的單一實例。 使用此啟動模式時，傳入 `Intent` 的資料會路由傳送至 `OnNewIntent` 方法，這會解壓縮訊息資料，並透過方法將它傳送至 `MainPage` 實例 `AddMessage` 。 如果您的應用程式使用不同的啟動模式，它必須 `Intent` 以不同的方式處理資料。

`OnCreate`方法會使用名為的 helper 方法 `IsPlayServiceAvailable` ，以確保裝置支援 Google Play 服務。 不支援 Google Play 服務的模擬器或裝置無法從接收推播通知 Firebase 。

## <a name="configure-ios-for-notifications"></a>設定 iOS 的通知

設定 iOS 應用程式以接收通知的流程如下：

1. 在檔案中設定套件組合**識別碼** `Info.plist` ，以符合布建設定檔中所使用的值。
1. 將 [**啟用推播通知**] 選項新增至檔案 `Entitlements.plist` 。
1. 將 `Xamarin.Azure.NotificationHubs.iOS` NuGet 套件新增至您的專案。
1. [向 APNS 註冊](#register-for-notifications-with-apns)通知。
1. 向 Azure 通知中樞[註冊](#register-with-azure-notification-hub-and-subscribe-to-tags)應用程式並訂閱標記。
1. [新增](#add-apns-notifications-to-xamarinforms-ui)使用者介面的 APNS 通知 Xamarin.Forms 。

下列螢幕擷取畫面顯示在 Visual Studio 的檔案中選取 [**啟用推播通知**] 選項 `Entitlements.plist` ：

![推播通知權利的螢幕擷取畫面](azure-notification-hub-images/push-notification-entitlement.png "推播通知權利")

### <a name="register-for-notifications-with-apns"></a>向 APNS 註冊通知

`FinishedLaunching`必須覆寫檔案中的方法 `AppDelegate.cs` ，才能註冊遠端通知。 註冊會根據裝置上使用的 iOS 版本而有所不同。 範例應用程式中的 iOS 專案會覆寫 `FinishedLaunching` 要呼叫的方法， `RegisterForRemoteNotifications` 如下列範例所示：

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

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>向 Azure 通知中樞註冊並訂閱標記

當裝置在方法期間成功註冊遠端通知時 `FinishedLaunching` ，iOS 會呼叫 `RegisteredForRemoteNotifications` 方法。 應該覆寫此方法，以執行下列動作：

1. 具現化 `SBNotificationHub` 。
1. 取消註冊任何現有的註冊。
1. 向您的通知中樞註冊裝置。
1. 使用範本訂閱特定標記。

如需註冊裝置、範本和標記的詳細資訊，請參閱[使用 Azure 通知中樞註冊範本和標記](#register-templates-and-tags-with-the-azure-notification-hub)。 下列程式碼示範裝置和範本的註冊：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
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
> 註冊遠端通知可能會在沒有網路連線的情況下失敗。 您可以選擇覆寫 `FailedToRegisterForRemoteNotifications` 方法來處理註冊失敗。

### <a name="add-apns-notifications-to-no-locxamarinforms-ui"></a>將 APNS 通知新增至 Xamarin.Forms UI

當裝置收到遠端通知時，iOS 會呼叫 `ReceivedRemoteNotification` 方法。 傳入訊息 JSON 會轉換成 `NSDictionary` 物件，而方法會 `ProcessNotification` 從字典中解壓縮值，並將其傳送至 Xamarin.Forms `MainPage` 實例。 `ReceivedRemoteNotifications`會覆寫方法以呼叫， `ProcessNotification` 如下列程式碼所示：

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

## <a name="test-notifications-in-the-azure-portal"></a>Azure 入口網站中的測試通知

Azure 通知中樞可讓您檢查應用程式是否可以接收測試訊息。 [通知中樞] 中的 [**測試傳送**] 區段可讓您選擇目標平臺，並傳送訊息。 將**傳送至**標籤運算式設定為， `default` 會將訊息傳送至已為標記註冊範本的應用程式 `default` 。 按一下 [**傳送**] 按鈕會產生一份報告，其中包含與訊息一起到達的裝置數目。 下列螢幕擷取畫面顯示 Azure 入口網站中的 Android 通知測試：

![Azure 通知中樞測試訊息的螢幕擷取畫面](azure-notification-hub-images/azure-notification-hub-test-send.png "Azure 通知中樞測試訊息")

### <a name="testing-tips"></a>測試秘訣

1. 測試應用程式能否接收推播通知時，您必須使用實體裝置。 Android 和 iOS 虛擬裝置可能未正確設定，而無法接收推播通知。
1. Android 應用程式範例會在簽發權杖時，註冊其權杖和範本一次 Firebase 。 在測試期間，您可能需要要求新的權杖，並使用 Azure 通知中樞重新註冊。 強制執行此工作的最佳方式是清理您的專案、刪除 `bin` 和 `obj` 資料夾，並從裝置卸載應用程式，然後再重建和部署。
1. 推播通知流程的許多部分會以非同步方式執行。 這可能會導致中斷點未被叫用，或未依預期的順序叫用。 使用裝置或 debug 記錄來追蹤執行，而不中斷應用程式流程。 使用中指定的來篩選 Android 裝置記錄 `DebugTag` `Constants` 。
1. 當 Visual Studio 中的偵錯工具停止時，應用程式會強制關閉。 任何在偵錯工具中啟動的訊息接收者或其他服務都會關閉，而且不會回應訊息事件。

## <a name="create-a-notification-dispatcher"></a>建立通知發送器

Azure 通知中樞可讓您的後端應用程式將通知分派給跨平臺的裝置。 此範例示範使用主控台應用程式的通知分派。 應用程式包含檔案，該檔案會 `DispatcherConstants.cs` 定義下列屬性：

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

您必須設定檔案 `DispatcherConstants.cs` 以符合您的 Azure 通知中樞設定。 屬性的值 `SubscriptionTags` 應該符合用戶端應用程式中所使用的值。 `NotificationHubName`屬性是 Azure 通知中樞實例的名稱。 `FullAccessConnectionString`屬性是在您的通知中樞**存取原則**中找到的存取金鑰。 下列螢幕擷取畫面顯示 `NotificationHubName` 和 `FullAccessConnectionString` 屬性在 Azure 入口網站中的位置：

![Azure 通知中樞名稱和 FullAccessConnectionString 的螢幕擷取畫面](azure-notification-hub-images/notification-hub-full-access-policy.png "Azure 通知中樞名稱和 FullAccessConnectionString")

主控台應用程式會迴圈處理每個 `SubscriptionTags` 值，並使用類別的實例，將通知傳送給訂閱者 `NotificationHubClient` 。 下列程式碼顯示主控台應用程式 `Program` 類別：

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

執行範例主控台應用程式時，可以按下空格鍵來傳送訊息。 執行用戶端應用程式的裝置應該會收到編號的通知，前提是它們已正確設定。

## <a name="related-links"></a>相關連結

* [推播通知範本](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)。
* [裝置註冊管理](/azure/notification-hubs/notification-hubs-push-notification-registration-management)。
* [路由與標記運算式](/azure/notification-hubs/notification-hubs-tags-segment-push-message)。
* [Xamarin. Android Azure 通知中樞教學](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)課程。
* [Xamarin IOS Azure 通知中樞教學](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)課程。
