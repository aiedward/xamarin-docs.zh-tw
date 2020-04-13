---
title: Xamarin.表單本地通知
description: 本文介紹如何在 Xamarin.Forms 中發送和接收本地通知。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72371568"
---
# <a name="local-notifications-in-xamarinforms"></a>Xamarin.表單中的本地通知

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

本地通知是安裝在行動裝置上的應用程式發送的警報。 本地通知通常用於功能,例如:

- 行事曆事件
- 提醒事項
- 基於位置的觸發器

每個平臺以不同的方式處理本地通知的創建、顯示和使用。 本文介紹如何創建跨平臺抽象以使用 Xamarin.Forms 發送和接收本地通知。

[![iOS 和安卓上的本地通知應用程式](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>建立跨平台介面

Xamarin.Forms 應用程式應創建和使用通知,而不考慮基礎平台實現。 以下`INotificationManager`介面在共享程式庫中實現,並定義了應用程式可用於與通知互動的跨平臺 API:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

此介面將在每個平台項目中實現。 該`NotificationReceived`事件允許應用程式處理傳入通知。 該方法`Initialize`應執行準備通知系統所需的任何本機平臺邏輯。 該方法`ScheduleNotification`應發送通知。 收到`ReceiveNotification`消息時,基礎平台應調用該方法。

## <a name="consume-the-interface-in-xamarinforms"></a>使用 Xamarin 中的介面。

創建介面後,即使在尚未創建平台實現時,也可以在共用 Xamarin.Forms 專案中使用它。 範例應用程式包含一個`ContentPage`稱為**MainPage.xaml,** 包含以下內容:

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

佈局包含一個`Label`元素,其中包含用戶說明,以及應`Button`在 點擊時安排通知的元素。

`MainPage`類別代碼後面處理通知的傳送與接收:

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

類`MainPage`建構函數使用 Xamarin.Forms`DependencyService`檢索`INotificationManager`特定於 平台的實例。 該方法`OnScheduleClicked`使用實`INotificationManager`例 來安排新的通知。 該方法`ShowNotification`從附加`NotificationReceived`到 事件的事件處理程式調用,並在調用事件時`Label`將新 添加到頁中。

有關 Xamarin.表`DependencyService`單的詳細資訊,請參閱[Xamarin.窗體相依項服務](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)。

## <a name="create-the-android-interface-implementation"></a>建立 Android 介面實作

對於 Xamarin.Forms 應用程式要在 Android 上發送和接收通知`INotificationManager`,應用程式必須提供介面的實現。

### <a name="create-the-androidnotificationmanager-class"></a>建立 Android 通知管理員 類別

類別`AndroidNotificationManager`的`INotificationManager`介面 :

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

命名`assembly`空間上方的屬性將`INotificationManager`介面設定註冊`DependencyService`到 。

Android 允許應用程式為通知定義多個通道。 該方法`Initialize`創建範例應用程式用於發送通知的基本通道。 該方法`ScheduleNotification`定義創建和發送通知所需的特定於平台的邏輯。 最後,當`ReceiveNotification`收到消息時,Android 操作系統調用該方法,並調用事件處理程式。

> [!NOTE]
> 類`Application``Xamarin.Forms`在`Android.App`與命名空間中定義`AndroidApp`, 因此在語`using`句中 定義別名以區分兩者。

### <a name="handle-incoming-notifications-on-android"></a>在 Android 上處理傳入通知

類`MainActivity`必須檢測傳入通知並通知`AndroidNotificationManager`實例。 類別`Activity`的`MainActivity`屬性應指定`LaunchMode``LaunchMode.SingleTop`值:

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

該`SingleTop`模式可防止在應用程序處於前`Activity`台 時啟動 的多個實例。 這可能`LaunchMode`不適用於在更複雜的通知方案中啟動多個活動的應用程式。 有關枚舉值`LaunchMode`的詳細資訊,請參閱 Android[活動啟動模式](https://developer.android.com/guide/topics/manifest/activity-element#lmode)。

在`MainActivity`類別變更以接收傳入通知:

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

該方法`CreateNotificationFromIntent``intent`從 參數中提取通知數據,並將其`AndroidNotificationManager`提供給`ReceiveNotification`使用的方法。 該方法`CreateNotificationFromIntent`從 該`OnCreate``OnNewIntent`方法和 方法中調用:

- 當應用程式由通知數據啟動時,`Intent`數據將傳遞給方法`OnCreate`。
- 如果應用程式已在前臺,數據`Intent`將傳遞給`OnNewIntent`方法。

Android 為通知提供了許多高級選項。 有關詳細資訊,請參閱[Xamarin.Android 中的通知](~/android/app-fundamentals/notifications/index.md)。

## <a name="create-the-ios-interface-implementation"></a>建立 iOS 介面

對於 Xamarin.Forms 應用程式要在 iOS 上發送和接收通知`INotificationManager`,應用程式必須提供的實現。

### <a name="create-the-iosnotificationmanager-class"></a>建立 iOS 通知管理員類別

類別`iOSNotificationManager`的`INotificationManager`介面 :

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

命名`assembly`空間上方的屬性將`INotificationManager`介面設定註冊`DependencyService`到 。

在 iOS 上,您必須請求使用通知的許可權,然後才能嘗試安排通知。 該方法`Initialize`請求使用本地通知的授權。 該方法`ScheduleNotification`定義創建和發送通知所需的邏輯。 最後,iOS 將在收到消息時調`ReceiveNotification`用 該方法,並調用事件處理程式。

### <a name="handle-incoming-notifications-on-ios"></a>在 iOS 上處理傳入通知

在 iOS 上,必須創建一`UNUserNotificationCenterDelegate`個委託 ,該子類來處理傳入消息。 範例應用程式定義類別`iOSNotificationReceiver`:

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

類使用`DependencyService``iOSNotificationManager`獲取 類的實例,併`ReceiveNotification`為 方法 提供傳入通知數據。

類`AppDelegate`必須在應用程式啟動期間指定自定義委託。 類`AppDelegate`必須在應用程式啟動期間`iOSNotificationReceiver`指定物件`UNUserNotificationCenter`作為 委託。 這一個方法`FinishedLaunching`中 :

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS 為通知提供了許多高級選項。 有關詳細資訊,請參閱[Xamarin.iOS 中的通知](~/ios/platform/user-notifications/index.md)。

## <a name="test-the-application"></a>測試應用程式

一旦平臺專案包含`INotificationManager`介面的註冊實現,可以在兩個平臺上測試應用程式。 運行應用程式並按下**計畫通知按鈕**以建立通知。

在 Android 上,通知將顯示在通知區域中。 當點擊通知時,應用程式將收到通知並在 **「計劃通知」** 按鈕下方顯示一條訊息:

![Android 上的本地通知](local-notifications-images/local-notifications-android.png)

在 iOS 上,應用程式會自動接收傳入通知,而無需使用者輸入。 應用程式收到通知並在 **「計劃通知」** 按鈕下方顯示一條訊息:

![iOS 上的本地通知](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>相關連結

- [範例專案](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Xamarin.安卓中的通知](~/android/app-fundamentals/notifications/index.md)
- [Xamarin.iOS 中的通知](~/ios/platform/user-notifications/index.md)
- [Xamarin.表單相依項.服務](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
