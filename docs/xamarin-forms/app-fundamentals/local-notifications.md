---
title: Xamarin.Forms 本機通知
description: 本文說明如何在中傳送、排程和接收本機通知 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 02/12/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2ebc226e865bbf3e482857b9c99fdda5d4922a44
ms.sourcegitcommit: 0a6b19004932c1ac82e16c95d5d3d5eb35a5b17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100255376"
---
# <a name="local-notifications-in-xamarinforms"></a>中的本機通知 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/local-notifications)

本機通知是安裝在行動裝置上的應用程式所傳送的警示。 本機通知通常用於下列功能：

- 行事曆事件
- 提醒事項
- 以位置為基礎的觸發程式

每個平臺都會以不同的方式處理本機通知的建立、顯示和耗用量。 本文說明如何使用來建立跨平臺抽象概念，以傳送、排程和接收本機通知 Xamarin.Forms 。

[![IOS 和 Android 上的本機通知應用程式](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>建立跨平臺介面

Xamarin.Forms應用程式應該建立並取用通知，而不需要考慮基礎平臺的執行。 下列 `INotificationManager` 介面會在共用程式碼程式庫中執行，並定義可讓應用程式用來與通知互動的跨平臺 API：

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;
    void Initialize();
    void SendNotification(string title, string message, DateTime? notifyTime = null);
    void ReceiveNotification(string title, string message);
}
```

此介面會在每個平臺專案中執行。 此 `NotificationReceived` 事件可讓應用程式處理傳入的通知。 `Initialize`方法應該執行準備通知系統所需的任何原生平臺邏輯。 `SendNotification`方法應該以選擇性的方式傳送通知 `DateTime` 。 `ReceiveNotification`收到訊息時，基礎平臺應呼叫方法。

## <a name="consume-the-interface-in-xamarinforms"></a>使用中的介面 Xamarin.Forms

一旦建立介面之後，即使尚未建立平臺，也可以在共用的專案中使用 Xamarin.Forms 。 範例應用程式包含 `ContentPage` 呼叫的 **MainPage** ，其中包含下列內容：

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button below to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnSendClick" />
    <Label Text="Click the button below to schedule a local notification for in 10 seconds time."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick" />
</StackLayout>
```

版面配置包含 `Label` 說明指示的專案，以及 `Button` 在點擊時傳送或排程通知的元素。

`MainPage`類別程式碼後端會處理通知的傳送和接收：

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

    void OnSendClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message);
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message, DateTime.Now.AddSeconds(10));
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

類別的函 `MainPage` 式會使用 Xamarin.Forms `DependencyService` 來取出的平臺特定實例 `INotificationManager` 。 `OnSendClick`和 `OnScheduleClicked` 方法會使用 `INotificationManager` 實例來傳送和排程新的通知。 `ShowNotification`系統會從附加至事件的事件處理常式呼叫方法 `NotificationReceived` ，並在叫用事件時，將新的插入 `Label` 頁面中。

`NotificationReceived`事件處理常式會將其事件引數轉換成 `NotificationEventArgs` 。 此類型定義于共用專案中 Xamarin.Forms ：

```csharp
public class NotificationEventArgs : EventArgs
{
    public string Title { get; set; }
    public string Message { get; set; }
}
```

如需的詳細資訊 Xamarin.Forms `DependencyService` ，請參閱[ Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)。

## <a name="create-the-android-interface-implementation"></a>建立 Android 介面執行

Xamarin.Forms若要讓應用程式在 Android 上傳送和接收通知，應用程式必須提供介面的實作為 `INotificationManager` 。

### <a name="create-the-androidnotificationmanager-class"></a>建立 AndroidNotificationManager 類別

類別會實 `AndroidNotificationManager` 作為 `INotificationManager` 介面：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Graphics;
using Android.OS;
using AndroidX.Core.App;
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

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = 0;
        int pendingIntentId = 0;

        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public static AndroidNotificationManager Instance { get; private set; }

        public AndroidNotificationManager() => Initialize();

        public void Initialize()
        {
            if (Instance == null)
            {
                CreateNotificationChannel();
                Instance = this;
            }
        }

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            if (notifyTime != null)
            {
                Intent intent = new Intent(AndroidApp.Context, typeof(AlarmHandler));
                intent.PutExtra(TitleKey, title);
                intent.PutExtra(MessageKey, message);

                PendingIntent pendingIntent = PendingIntent.GetBroadcast(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.CancelCurrent);
                long triggerTime = GetNotifyTime(notifyTime.Value);
                AlarmManager alarmManager = AndroidApp.Context.GetSystemService(Context.AlarmService) as AlarmManager;
                alarmManager.Set(AlarmType.RtcWakeup, triggerTime, pendingIntent);
            }
            else
            {
                Show(title, message);
            }
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

        public void Show(string title, string message)
        {
            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.UpdateCurrent);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            Notification notification = builder.Build();
            manager.Notify(messageId++, notification);
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

        long GetNotifyTime(DateTime notifyTime)
        {
            DateTime utcTime = TimeZoneInfo.ConvertTimeToUtc(notifyTime);
            double epochDiff = (new DateTime(1970, 1, 1) - DateTime.MinValue).TotalSeconds;
            long utcAlarmTime = utcTime.AddSeconds(-epochDiff).Ticks / 10000;
            return utcAlarmTime; // milliseconds
        }
    }
}
```

`assembly`命名空間上方的屬性會 `INotificationManager` 向註冊介面實 `DependencyService` 。

Android 可讓應用程式針對通知定義多個通道。 `Initialize`方法會建立範例應用程式用來傳送通知的基本通道。 `SendNotification`方法會定義建立和傳送通知所需的平臺特定邏輯。 `ReceiveNotification`當收到訊息時，ANDROID 作業系統會呼叫此方法，並叫用事件處理常式。

`SendNotification`方法會立即或精確地建立本機通知 `DateTime` 。 您可以使用類別來排定確切的通知 `DateTime` `AlarmManager` ，而此通知將由衍生自類別的物件接收 `BroadcastReceiver` ：

```csharp
[BroadcastReceiver(Enabled = true, Label = "Local Notifications Broadcast Receiver")]
public class AlarmHandler : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        if (intent?.Extras != null)
        {
            string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
            string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);

            AndroidNotificationManager manager = AndroidNotificationManager.Instance ?? new AndroidNotificationManager();
            manager.Show(title, message);
        }
    }
}
```

> [!IMPORTANT]
> 根據預設，使用類別排程的通知 `AlarmManager` 不會在裝置重新開機後存活。 不過，您可以設計應用程式，以便在裝置重新開機時自動重新排程通知。 如需詳細資訊，請在 [裝置重新開機時重新](https://developer.android.com/training/scheduling/alarms#boot) 啟動警示，並在 Developer.android.com 上 [排程重複](https://developer.android.com/training/scheduling/alarms) 的警示和 [範例](/samples/xamarin/xamarin-forms-samples/local-notifications)。 如需有關 Android 背景處理的詳細資訊，請參閱 developer.android.com 上的 [背景處理指南](https://developer.android.com/guide/background) 。

如需廣播接收器的詳細資訊，請參閱 [Xamarin 中的廣播接收者](~/android/app-fundamentals/broadcast-receivers.md)。

### <a name="handle-incoming-notifications-on-android"></a>處理 Android 上的傳入通知

`MainActivity`類別必須偵測傳入通知並通知 `AndroidNotificationManager` 實例。 `Activity`類別上的屬性 `MainActivity` 應指定 `LaunchMode` `LaunchMode.SingleTop` 下列值：

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

`SingleTop` `Activity` 當應用程式在前景時，此模式可避免啟動的多個實例。 這 `LaunchMode` 可能不適合在更複雜的通知案例中啟動多個活動的應用程式。 如需列舉值的詳細資訊 `LaunchMode` ，請參閱 [Android 活動 LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode)。

在中， `MainActivity` 會修改類別以接收傳入通知：

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
        string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
        string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

`CreateNotificationFromIntent`方法會從引數解壓縮通知資料 `intent` ，並使用方法將它提供給 `AndroidNotificationManager` `ReceiveNotification` 。 方法 `CreateNotificationFromIntent` 會從 `OnCreate` 方法和 `OnNewIntent` 方法呼叫：

- 當應用程式是由通知資料啟動時， `Intent` 資料將會傳遞至 `OnCreate` 方法。
- 如果應用程式已在前景中，則 `Intent` 會將資料傳遞給 `OnNewIntent` 方法。

Android 提供許多通知的 advanced 選項。 如需詳細資訊，請參閱 [Xamarin 中的通知](~/android/app-fundamentals/notifications/index.md)。

## <a name="create-the-ios-interface-implementation"></a>建立 iOS 介面執行

Xamarin.Forms若要讓應用程式在 iOS 上傳送和接收通知，應用程式必須提供的實作為 `INotificationManager` 。

### <a name="create-the-iosnotificationmanager-class"></a>建立 iOSNotificationManager 類別

類別會實 `iOSNotificationManager` 作為 `INotificationManager` 介面：

```csharp
using System;
using Foundation;
using UserNotifications;
using Xamarin.Forms;

[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = 0;
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

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            // EARLY OUT: app doesn't have permissions
            if (!hasNotificationsPermission)
            {
                return;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };            

            UNNotificationTrigger trigger;
            if (notifyTime != null)
            {
                // Create a calendar-based trigger.
                trigger = UNCalendarNotificationTrigger.CreateTrigger(GetNSDateComponents(notifyTime.Value), false);
            }
            else
            {
                // Create a time-based trigger, interval is in seconds and must be greater than 0.
                trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);
            }                      

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });
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

        NSDateComponents GetNSDateComponents(DateTime dateTime)
        {
            return new NSDateComponents
            {
                Month = dateTime.Month,
                Day = dateTime.Day,
                Year = dateTime.Year,
                Hour = dateTime.Hour,
                Minute = dateTime.Minute,
                Second = dateTime.Second
            };
        }
    }
}
```

`assembly`命名空間上方的屬性會 `INotificationManager` 向註冊介面實 `DependencyService` 。

在 iOS 上，您必須在嘗試排程通知之前要求許可權以使用通知。 `Initialize`方法會要求使用本機通知的授權。 `SendNotification`方法會定義建立和傳送通知所需的邏輯。 `ReceiveNotification`當收到訊息時，iOS 會呼叫此方法，並叫用事件處理常式。

> [!NOTE]
> `SendNotification`方法會立即建立本機通知、使用 `UNTimeIntervalNotificationTrigger` 物件，或 `DateTime` 使用物件進行精確的使用 `UNCalendarNotificationTrigger` 。

### <a name="handle-incoming-notifications-on-ios"></a>處理 iOS 上的傳入通知

在 iOS 上，您必須建立子類別的委派 `UNUserNotificationCenterDelegate` 來處理傳入訊息。 範例應用程式會定義 `iOSNotificationReceiver` 類別：

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        ProcessNotification(notification);
        completionHandler(UNNotificationPresentationOptions.Alert);
    }

    void ProcessNotification(UNNotification notification)
    {
        string title = notification.Request.Content.Title;
        string message = notification.Request.Content.Body;

        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }    
}
```

這個類別會使用 `DependencyService` 來取得類別的實例 `iOSNotificationManager` ，並提供傳入的通知資料給 `ReceiveNotification` 方法。

`AppDelegate`類別必須在 `iOSNotificationReceiver` `UNUserNotificationCenter` 應用程式啟動期間將物件指定為委派。 這會發生在 `FinishedLaunching` 方法中：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS 提供許多通知的 advanced 選項。 如需詳細資訊，請參閱 [Xamarin 中的通知](~/ios/platform/user-notifications/index.md)。

## <a name="test-the-application"></a>測試應用程式

一旦平臺專案包含已註冊的 `INotificationManager` 介面執行，就可以在這兩個平臺上測試應用程式。 執行應用程式，然後按一下其中一個 [ **建立通知** ] 按鈕以建立通知。

在 Android 上，通知會出現在通知區域中。 當您點擊通知時，應用程式會收到通知，並顯示訊息：

![Android 上的本機通知](local-notifications-images/local-notifications-android.png)

在 iOS 上，應用程式會自動接收傳入通知，而不需要使用者輸入。 應用程式會收到通知，並顯示一則訊息：

![IOS 上的本機通知](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>相關連結

- [範例專案](/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Xamarin 中的通知](~/android/app-fundamentals/notifications/index.md)
- [Xamarin 中的廣播接收者](~/android/app-fundamentals/broadcast-receivers.md)
- [Xamarin 中的通知](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
