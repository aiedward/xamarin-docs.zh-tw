---
title: 逐步解說-在 Xamarin.Android 中使用本機通知
description: 本逐步解說示範如何在 Xamarin.Android 應用程式中使用本機通知。 它示範如何建立和發佈本機通知的基本概念。 當使用者按一下通知區域中的通知時，它會啟動第二個活動。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: e60ed6cc49921fc7b6e8e2616a6b0bf6f8abb401
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026803"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>逐步解說-在 Xamarin.Android 中使用本機通知

_本逐步解說示範如何在 Xamarin.Android 應用程式中使用本機通知。它示範如何建立和發佈本機通知的基本概念。當使用者按一下通知區域中的通知時，它會啟動第二個活動。_


## <a name="overview"></a>總覽

在本逐步解說中，我們將建立 Android 應用程式，當使用者按一下按鈕，以在活動引發通知。 當使用者按一下通知時，它會啟動第二個活動顯示使用者已按下的第一個活動中的按鈕的次數。

下列螢幕擷取畫面說明此應用程式的一些範例：

[![具有通知的範例螢幕擷取畫面](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> 本指南著重[NotificationCompat Api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html)從[Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 這些 Api 可確保最大值回溯相容性以 Android 4.0 （API 層級為 14）。

## <a name="creating-the-project"></a>建立專案

若要開始，讓我們來建立新的 Android 專案使用**Android 應用程式**範本。 讓我們來呼叫此專案**LocalNotifications**。 (如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)

編輯資源檔**values/Strings.xml** ，使其包含將用於建立通知通道時的兩個額外的字串資源：


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>新增 Android.Support.V4 NuGet 套件

在此逐步解說中，我們會使用`NotificationCompat.Builder`建置我們本機的通知。 中所述[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)，我們必須包含[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)我們的專案中使用的 NuGet `NotificationCompat.Builder`。

接下來，讓我們編輯**MainActivity.cs**並新增下列`using`陳述式以便中的型別`Android.Support.V4.App`是提供給我們的程式碼：

```csharp
using Android.Support.V4.App;
```

此外，我們必須讓清楚，我們將使用的編譯器`Android.Support.V4.App`新版`TaskStackBuilder`而非`Android.App`版本。 新增下列`using`解決任何模稜兩可的陳述式：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>建立通知通道

接下來，新增方法以`MainActivity`（如有必要），這會建立通知通道：

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

更新`OnCreate`方法來呼叫此新方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>定義的通知識別碼

我們必須為我們的通知和通知通道的唯一識別碼。 讓我們編輯**MainActivity.cs**並加入下列的靜態執行個體變數，以`MainActivity`類別：

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>加入程式碼以產生通知

接下來，我們需要建立新的事件處理常式按鈕`Click`事件。 將下列方法加入`MainActivity`:

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

`OnCreate` MainActivity 方法必須呼叫以建立通知通道，並指派`ButtonOnClick`方法，以`Click`（取代該範本所提供的委派事件處理常式） 按鈕的事件：

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


### <a name="create-a-second-activity"></a>建立第二個活動

現在，我們需要建立 Android 將會顯示在使用者按一下我們的通知時的另一個活動。 呼叫專案中加入另一個的 Android 活動**SecondActivity**。 開啟**SecondActivity.cs**和其內容取代此程式碼：

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

我們也必須建立的資源配置**SecondActivity**。 加入新**Android 配置**檔案加入專案呼叫**Second.axml**。 編輯**Second.axml**並貼上下列版面配置程式碼：

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


### <a name="add-a-notification-icon"></a>新增通知圖示

最後，新增啟動通知時，會出現在通知區域中的小圖示。 您可以複製[這個圖示](local-notifications-walkthrough-images/ic-stat-button-click.png)至您的專案或建立您自己的自訂圖示。 將檔案命名為圖示**ic\_stat\_按鈕\_click.png**將它複製到**資源/drawable**資料夾。 請務必使用**新增 > 現有項目...** 在您的專案中包含此圖示檔案。


### <a name="run-the-application"></a>執行應用程式

建置並執行應用程式。 您應該會看到第一個活動，類似下列螢幕擷取畫面：

[![第一個活動的螢幕擷取畫面](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

當您按一下按鈕時，您應該注意到，通知之小圖示會出現在通知區域：

[![通知圖示會出現](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果您向下撥動，並公開 （expose） 通知抽屜，您應該會看到通知：

[![通知訊息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

當您按一下通知時，它應該會消失，而應該啟動我們的其他活動&ndash;尋找些許類似下列螢幕擷取畫面：

[![第二個活動的螢幕擷取畫面](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

恭喜您！ 此時，您已完成 Android 本機通知逐步解說，並可讓您可以參考的工作範例。 有很多個通知比我們已在這裡顯示，因此如果您想要的詳細資訊，看看[Google 的文件通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


## <a name="summary"></a>總結

使用本逐步解說`NotificationCompat.Builder`來建立並顯示通知。 它示範如何啟動來回應使用者互動與通知，第二個活動的基本範例，它示範從第一個活動的資料傳輸至第二個活動。


## <a name="related-links"></a>相關連結

- [LocalNotifications （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Android Oreo 通知通道](https://blog.xamarin.com/android-oreo-notification-channels/)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
