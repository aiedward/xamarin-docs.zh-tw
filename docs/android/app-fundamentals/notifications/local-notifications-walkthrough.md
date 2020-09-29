---
title: 逐步解說-在 Xamarin 中使用本機通知
description: 本逐步解說示範如何在 Xamarin Android 應用程式中使用本機通知。 它會示範建立和發佈本機通知的基本概念。 當使用者按一下通知區域中的通知時，它會啟動第二個活動。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 09603fa86bce18c7cfdc141938267ee54710842c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455701"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>逐步解說-在 Xamarin 中使用本機通知

_本逐步解說示範如何在 Xamarin Android 應用程式中使用本機通知。它會示範建立和發佈本機通知的基本概念。當使用者按一下通知區域中的通知時，它會啟動第二個活動。_

## <a name="overview"></a>概觀

在此逐步解說中，我們將建立 Android 應用程式，以在使用者按一下活動中的按鈕時引發通知。 當使用者按一下通知時，它會啟動第二個活動，顯示使用者在第一個活動中按一下按鈕的次數。

下列螢幕擷取畫面說明此應用程式的一些範例：

[![具有通知的範例螢幕擷取畫面](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> 本指南著重于[Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中的[NotificationCompat api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) 。 這些 Api 可確保 Android 4.0 (API 層級 14) 的最高回溯相容性。

## <a name="creating-the-project"></a>建立專案

首先，讓我們使用 **Android 應用程式** 範本來建立新的 android 專案。 讓我們呼叫此專案 **LocalNotifications**。 如果您不熟悉如何建立 Xamarin Android 專案，請 (，請參閱 [Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md). ) 

編輯資源檔 **值/Strings.xml** ，讓它包含兩個額外的字串資源，這些資源會在建立通知通道的時候使用：

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

在這個逐步解說中，我們將使用 `NotificationCompat.Builder` 來建立我們的本機通知。 如 [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)中所述，我們必須在專案中包含 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 才能使用 `NotificationCompat.Builder` 。

接下來，讓我們來編輯 **MainActivity.cs** 並加入下列 `using` 語句，讓程式碼能夠使用中的類型 `Android.Support.V4.App` ：

```csharp
using Android.Support.V4.App;
```

此外，我們也必須讓編譯器清楚地使用 `Android.Support.V4.App` 版本 `TaskStackBuilder` 而非 `Android.App` 版本。 新增下列 `using` 語句以解決任何混淆：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>建立通知通道

接下來，將方法新增至 `MainActivity` ，以便在必要時 (建立通知通道) ：

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

更新 `OnCreate` 方法以呼叫這個新的方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```

### <a name="define-the-notification-id"></a>定義通知識別碼

我們的通知和通知通道都需要唯一的識別碼。 讓我們編輯 **MainActivity.cs** ，並將下列靜態執行個體變數新增至 `MainActivity` 類別：

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>新增程式碼以產生通知

接下來，我們需要為按鈕事件建立新的事件處理常式 `Click` 。 將下列方法新增至 `MainActivity`：

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

`OnCreate`>mainactivity 的方法必須進行呼叫以建立通知通道，並將 `ButtonOnClick` 方法指派給按鈕的 `Click` 事件 (取代範本所提供的委派事件處理常式) ：

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

現在，我們需要建立另一個當使用者按一下通知時，Android 會顯示的活動。 將另一個 Android 活動新增至名為 **SecondActivity**的專案。 開啟 **SecondActivity.cs** ，並將其內容取代為下列程式碼：

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

此外，我們也必須建立 **SecondActivity**的資源版面配置。 將新的 **Android 版面** 配置檔案新增至名為 **.axml**的專案。 編輯 **第二個. .axml** ，並貼上下列版面配置程式碼：

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

最後，新增會在通知啟動時出現在通知區域中的小圖示。 您可以將 [此圖示](local-notifications-walkthrough-images/ic-stat-button-click.png) 複製到您的專案，或建立您自己的自訂圖示。 將 [ **ic stat] \_ \_ 按鈕 \_ ** 命名為click.png，然後將它複製到 [ **Resources/可繪製** ] 資料夾。 請記得使用 [ **新增 > 現有專案** ]，在您的專案中包含此圖示檔。

### <a name="run-the-application"></a>執行應用程式

建置並執行應用程式。 您應該會看到第一個活動，類似于下列螢幕擷取畫面：

[![第一個活動螢幕擷取畫面](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

當您按一下按鈕時，您應該會注意到通知的小圖示會出現在通知區域中：

[![通知圖示隨即出現](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果您向下滑動並公開通知隱藏式選單，您應該會看到通知：

[![通知訊息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

當您按一下通知時，應該會消失，而我們的其他活動應該會 &ndash; 像下列螢幕擷取畫面一樣啟動：

[![第二個活動螢幕擷取畫面](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

恭喜！ 至此，您已完成 Android 本機通知逐步解說，而且您有一個可供參考的實用範例。 通知的內容遠多於我們在這裡所顯示的內容，因此，如果您需要詳細資訊，請參閱 [Google 的檔](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)以取得通知。

## <a name="summary"></a>摘要

本逐步解說用 `NotificationCompat.Builder` 來建立和顯示通知。 它會示範如何啟動第二個活動以回應使用者與通知互動的基本範例，並示範如何將第一個活動的資料傳輸到第二個活動。

## <a name="related-links"></a>相關連結

- [LocalNotifications (範例) ](/samples/xamarin/monodroid-samples/localnotifications)
- [Android Oreo 通知通道](https://blog.xamarin.com/android-oreo-notification-channels/)
- [通知](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)