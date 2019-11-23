---
title: Xamarin. 表單本機通知
description: 本文說明如何在 Xamarin 中傳送和接收本機通知。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: 49afc8c6484c465bf5c9e1d64a8c9122a0bcf506
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72371568"
---
# <a name="local-notifications-in-xamarinforms"></a>Xamarin 中的本機通知

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

本機通知是安裝在行動裝置上的應用程式所傳送的警示。 本機通知通常用於下列功能：

- 行事曆事件
- 提醒
- 以位置為基礎的觸發程式

每個平臺都會以不同的方式處理本機通知的建立、顯示和取用。 本文說明如何建立跨平臺抽象概念，以使用 Xamarin 來傳送和接收本機通知。

[在 iOS 和 Android 上 ![本機通知應用程式](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>建立跨平臺介面

Xamarin 應用程式應該建立並取用通知，而不需要考慮基礎平臺的執行。 下列 `INotificationManager` 介面是在共用的程式碼程式庫中執行，並定義應用程式可以用來與通知互動的跨平臺 API：

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

這個介面將會在每個平臺專案中執行。 `NotificationReceived` 事件可讓應用程式處理傳入的通知。 `Initialize` 方法應執行準備通知系統所需的任何原生平臺邏輯。 `ScheduleNotification` 方法應傳送通知。 接收訊息時，基礎平臺應呼叫 `ReceiveNotification` 方法。

## <a name="consume-the-interface-in-xamarinforms"></a>使用 Xamarin 中的介面

一旦建立介面之後，就可以在共用的 Xamarin. form 專案中使用它，即使尚未建立平臺程式也是一樣。 範例應用程式包含名為**MainPage**的 `ContentPage`，並具有下列內容：

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

版面配置包含一個 `Label` 元素，其中含有使用者的指示，以及應該在點擊時排程通知的 `Button`。

`MainPage` 類別程式碼後置會處理通知的傳送和接收：

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

`MainPage` 類別的函式會使用 Xamarin `DependencyService` 來取出 `INotificationManager`的平臺特定實例。 `OnScheduleClicked` 方法會使用 `INotificationManager` 實例來排程新的通知。 系統會從附加至 `NotificationReceived` 事件的事件處理常式呼叫 `ShowNotification` 方法，並在叫用事件時，將新的 `Label` 插入至頁面。

如需有關 `DependencyService`的詳細資訊，請參閱[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)。

## <a name="create-the-android-interface-implementation"></a>建立 Android 介面執行

若要讓 Xamarin 應用程式在 Android 上傳送和接收通知，應用程式必須提供 `INotificationManager` 介面的執行。

### <a name="create-the-androidnotificationmanager-class"></a>建立 AndroidNotificationManager 類別

`AndroidNotificationManager` 類別會實 `INotificationManager` 介面：

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

命名空間上方的 `assembly` 屬性會向 `DependencyService`註冊 `INotificationManager` 介面執行。

Android 可讓應用程式為通知定義多個通道。 `Initialize` 方法會建立範例應用程式用來傳送通知的基本通道。 `ScheduleNotification` 方法會定義建立和傳送通知所需的平臺特定邏輯。 最後，在收到訊息時，Android OS 會呼叫 `ReceiveNotification` 方法，並叫用事件處理常式。

> [!NOTE]
> `Application` 類別定義于 `Xamarin.Forms` 和 `Android.App` 命名空間中，因此 `AndroidApp` 別名定義于 `using` 語句中，以區分兩者。

### <a name="handle-incoming-notifications-on-android"></a>處理 Android 上的傳入通知

`MainActivity` 類別必須偵測傳入通知，並通知 `AndroidNotificationManager` 實例。 `MainActivity` 類別上的 `Activity` 屬性應該指定 `LaunchMode.SingleTop`的 `LaunchMode` 值：

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

當應用程式在前景時，`SingleTop` 模式可防止 `Activity` 的多個實例啟動。 對於在較複雜的通知案例中啟動多個活動的應用程式而言，此 `LaunchMode` 可能不適用。 如需 `LaunchMode` 列舉值的詳細資訊，請參閱[Android 活動 LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode)。

在中，已修改 `MainActivity` 類別以接收傳入通知：

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

`CreateNotificationFromIntent` 方法會從 `intent` 引數解壓縮通知資料，並使用 `ReceiveNotification` 方法將其提供給 `AndroidNotificationManager`。 從 `OnCreate` 方法和 `OnNewIntent` 方法呼叫 `CreateNotificationFromIntent` 方法：

- 當通知資料啟動應用程式時，`Intent` 的資料會傳遞至 `OnCreate` 方法。
- 如果應用程式已在前景中，`Intent` 資料將會傳遞至 `OnNewIntent` 方法。

Android 提供許多通知的先進選項。 如需詳細資訊，請參閱[在 Xamarin 中的通知](~/android/app-fundamentals/notifications/index.md)。

## <a name="create-the-ios-interface-implementation"></a>建立 iOS 介面執行

若要讓 Xamarin 應用程式在 iOS 上傳送和接收通知，應用程式必須提供 `INotificationManager`的執行。

### <a name="create-the-iosnotificationmanager-class"></a>建立 iOSNotificationManager 類別

`iOSNotificationManager` 類別會實 `INotificationManager` 介面：

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

命名空間上方的 `assembly` 屬性會向 `DependencyService`註冊 `INotificationManager` 介面執行。

在 iOS 上，您必須先要求許可權以使用通知，然後再嘗試排程通知。 `Initialize` 方法會要求授權使用本機通知。 `ScheduleNotification` 方法會定義建立和傳送通知所需的邏輯。 最後，當收到訊息時，iOS 會呼叫 `ReceiveNotification` 方法，並叫用事件處理常式。

### <a name="handle-incoming-notifications-on-ios"></a>處理 iOS 上的傳入通知

在 iOS 上，您必須建立子類別 `UNUserNotificationCenterDelegate` 的委派，以處理傳入訊息。 範例應用程式會定義 `iOSNotificationReceiver` 類別：

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

這個類別會使用 `DependencyService` 來取得 `iOSNotificationManager` 類別的實例，並將傳入的通知資料提供給 `ReceiveNotification` 方法。

`AppDelegate` 類別必須在應用程式啟動期間指定自訂委派。 `AppDelegate` 類別必須在應用程式啟動期間，將 `iOSNotificationReceiver` 物件指定為 `UNUserNotificationCenter` 委派。 這會發生在 `FinishedLaunching` 方法中：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS 提供許多通知的先進選項。 如需詳細資訊，請參閱[在 Xamarin 中的通知](~/ios/platform/user-notifications/index.md)。

## <a name="test-the-application"></a>測試應用程式

一旦平臺專案包含已註冊的 `INotificationManager` 介面執行，就可以在這兩個平臺上測試應用程式。 執行應用程式，然後按一下 [**排程通知**] 按鈕以建立通知。

在 Android 上，通知會出現在通知區域中。 當您按下通知時，應用程式會收到通知，並在 [**排程通知**] 按鈕下方顯示一則訊息：

![Android 上的本機通知](local-notifications-images/local-notifications-android.png)

在 iOS 上，應用程式會自動接收傳入通知，而不需要使用者輸入。 應用程式會收到通知，並在 [**排程通知**] 按鈕下方顯示一則訊息：

![IOS 上的本機通知](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>相關連結

- [範例專案](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Xamarin 中的通知](~/android/app-fundamentals/notifications/index.md)
- [在 Xamarin 中的通知](~/ios/platform/user-notifications/index.md)
- [Xamarin. Forms 相依性。服務](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
