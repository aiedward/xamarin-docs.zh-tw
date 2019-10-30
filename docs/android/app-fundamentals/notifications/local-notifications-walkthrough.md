---
title: 逐步解說-在 Xamarin 中使用本機通知
description: 本逐步解說示範如何在 Xamarin Android 應用程式中使用本機通知。 它會示範建立和發行本機通知的基本概念。 當使用者按一下通知區域中的通知時，就會啟動第二個活動。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 6d48d650b0900e71b7d3d4d5e1ff1ac919dcb948
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025542"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>逐步解說-在 Xamarin 中使用本機通知

_本逐步解說示範如何在 Xamarin Android 應用程式中使用本機通知。它會示範建立和發行本機通知的基本概念。當使用者按一下通知區域中的通知時，就會啟動第二個活動。_

## <a name="overview"></a>總覽

在本逐步解說中，我們將建立 Android 應用程式，以在使用者按一下活動中的按鈕時引發通知。 當使用者按一下通知時，它會啟動第二個活動，顯示使用者在第一個活動中按一下按鈕的次數。

下列螢幕擷取畫面說明此應用程式的一些範例：

[使用通知![範例螢幕擷取畫面](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> 本指南著重于[Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)的[NotificationCompat api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) 。 這些 Api 會確保最大的 Android 4.0 回溯相容性（API 層級14）。

## <a name="creating-the-project"></a>建立專案

首先，讓我們使用**Android 應用程式**範本來建立新的 android 專案。 讓我們來呼叫這個專案**LocalNotifications**。 （如果您不熟悉如何建立 Xamarin Android 專案，請參閱[Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md)）。

編輯資源檔**值/字串**，使其包含兩個額外的字串資源，這會在建立通知通道時使用：

```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>新增 Android. 支援 V4 NuGet 套件

在本逐步解說中，我們會使用 `NotificationCompat.Builder` 來建立我們的本機通知。 如[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)中所述，我們必須在專案中包含[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet，才能使用 `NotificationCompat.Builder`。

接下來，讓我們編輯**MainActivity.cs**並新增下列 `using` 語句，讓 `Android.Support.V4.App` 中的類型可供我們的程式碼使用：

```csharp
using Android.Support.V4.App;
```

此外，我們還必須讓編譯器清楚地說，我們使用的是 `TaskStackBuilder` 的 `Android.Support.V4.App` 版本，而不是 `Android.App` 版本。 新增下列 `using` 語句，以解決任何不明確的情況：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>建立通知通道

接下來，將方法新增至會建立通知通道的 `MainActivity` （如有必要）：

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

更新 `OnCreate` 方法以呼叫這個新方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```

### <a name="define-the-notification-id"></a>定義通知識別碼

我們需要通知和通知通道的唯一識別碼。 讓我們編輯**MainActivity.cs** ，並將下列靜態執行個體變數新增至 `MainActivity` 類別：

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>新增程式碼以產生通知

接下來，我們需要為按鈕 `Click` 事件建立新的事件處理常式。 將下列方法新增至 `MainActivity`：

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

The `OnCreate` method of MainActivity must make the call to create the notification channel and assign the `ButtonOnClick` method to the `Click` event of the button (replace the delegate event handler provided by the template):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```

### <a name="create-a-second-activity"></a>Create a second activity

Now we need to create another activity that Android will display when the user clicks our notification. Add another Android Activity to your project called **SecondActivity**. Open **SecondActivity.cs** and replace its contents with this code:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

We must also create a resource layout for **SecondActivity**. Add a new **Android Layout** file to your project called **Second.axml**. Edit **Second.axml** and paste in the following layout code:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```

### <a name="add-a-notification-icon"></a>Add a notification icon

Finally, add a small icon that will appear in the notification area when the notification is launched. You can copy [this icon](local-notifications-walkthrough-images/ic-stat-button-click.png) to your project or create your own custom icon. Name the icon file **ic\_stat\_button\_click.png** and copy it to the **Resources/drawable** folder. Remember to use **Add > Existing Item ...** to include this icon file in your project.

### <a name="run-the-application"></a>執行應用程式

建置並執行應用程式。 You should be presented with the first activity, similar to the following screenshot:

[![First activity screenshot](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

As you click the button, you should notice that the small icon for the notification appears in the notification area:

[![Notification icon appears](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

If you swipe down and expose the notification drawer, you should see the notification:

[![Notification message](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

When you click the notification, it should disappear, and our other activity should be launched &ndash; looking somewhat like the following screenshot:

[![Second activity screenshot](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

恭喜您！ At this point you have completed the Android local notification walkthrough and you have a working sample that you can refer to. There is a lot more to notifications than we have shown here, so if you want more information, take a look at [Google's documentation on notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="summary"></a>總結

This walkthrough used `NotificationCompat.Builder` to create and display notifications. It showed a basic example of how to start up a second Activity as a way to respond to user interaction with the notification, and it demonstrated the transfer of data from the first Activity to the second Activity.

## <a name="related-links"></a>相關連結

- [LocalNotifications (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Android Oreo Notification Channels](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notification](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
