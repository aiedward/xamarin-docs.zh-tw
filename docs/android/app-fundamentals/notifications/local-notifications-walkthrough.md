---
title: 逐步解說-Xamarin.Android 中使用本機的通知
description: 本逐步解說示範如何在 Xamarin.Android 應用程式中使用本機的通知。 它會示範建立和發佈本機通知的基本概念。 當使用者按一下通知區域中的通知時，它會啟動第二個活動。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766578"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>逐步解說-Xamarin.Android 中使用本機的通知

_本逐步解說示範如何在 Xamarin.Android 應用程式中使用本機的通知。它會示範建立和發佈本機通知的基本概念。當使用者按一下通知區域中的通知時，它會啟動第二個活動。_


## <a name="overview"></a>總覽

在此逐步解說中，我們將建立 Android 應用程式引發通知，當使用者按一下按鈕，以在活動中。 當使用者按一下通知時，它會啟動第二個活動顯示使用者已按下的第一個活動中的按鈕的次數。

下列螢幕擷取畫面說明此應用程式的一些範例：

[![具有通知的範例螢幕擷取畫面](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>逐步解說

若要開始，我們來建立新的 Android 專案使用**Android 應用程式**範本。 這個專案，我們稱為**LocalNotifications**。 (如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)


### <a name="add-the-androidsupportv4app-component"></a>新增 Android.Support.V4.App 元件

在本逐步解說使用`NotificationCompat.Builder`建置我們本機的通知。 中所述[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)，我們必須包含[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)我們的受測專案来使用的 NuGet `NotificationCompat.Builder`。

接下來，讓編輯**Weatherapp**並加入下列`using`陳述式以便中的型別`Android.Support.V4.App`可用的程式碼：

```csharp
using Android.Support.V4.App;
```

此外，我們必須對它清楚，我們將使用的編譯器`Android.Support.V4.App`版本`TaskStackBuilder`而不是`Android.App`版本。 加入下列`using`解決任何模稜兩可的陳述式：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>定義通知識別碼

我們必須我們通知的唯一識別碼。 開始編輯**Weatherapp**並加入下列的靜態執行個體變數，以`MainActivity`類別：

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>加入程式碼產生的通知

接下來，我們必須建立新的事件處理常式按鈕`Click`事件。 將下列方法加入`MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

在`OnCreate`方法中，指派此`ButtonOnClick`方法`Click`按鈕 （程式碼取代該範本所提供的委派事件處理常式） 的事件：

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>建立第二個活動

現在我們需要建立另一個活動 Android 將會顯示當使用者按一下通知我們。 呼叫專案中加入另一個的 Android 活動**SecondActivity**。 開啟**SecondActivity.cs**並將其內容取代此程式碼：

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
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

我們也必須建立的資源配置**SecondActivity**。 加入新**Android 配置**檔案加入您專案中呼叫**Second.axml**。 編輯**Second.axml**並貼上下列版面配置程式碼：

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
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>加入通知圖示

最後，讓我們加入我們的通知啟動時，會出現在通知區域中的小圖示。 您可以複製[這個圖示](local-notifications-walkthrough-images/ic-stat-button-click.png)至您的專案或建立您自己自訂的圖示。 圖示檔案**ic\_stat\_按鈕\_click.png**並將它複製到**資源/drawable**資料夾。 請務必使用**新增 > 現有項目...** 專案中包含這個圖示檔。


### <a name="run-the-application"></a>執行應用程式

讓我們來建置並執行應用程式。 您應該會看到第一個活動，類似於下列螢幕擷取畫面：

[![第一個活動螢幕擷取畫面](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

當您按一下按鈕時，您應該注意到在通知區域中顯示之小圖示的通知：

[![通知圖示會出現](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果您上下並公開 （expose） 通知抽屜，您應該會看到通知：

[![通知訊息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

當您按一下通知時，應該就會消失，而其他活動，我們應該啟動&ndash;尋找類似以下的螢幕擷取畫面：

[![第二個活動的螢幕擷取畫面](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

恭喜您！ 此時您已經完成 Android 本機通知逐步解說，並可讓您可以使用參照的工作範例。 有很多個通知要比我們已在這裡顯示，因此如果您想要的詳細資訊，看看[Google 的文件通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)和 Android[通知](http://developer.android.com/design/patterns/notifications.html)設計指南。



## <a name="summary"></a>總結

本逐步解說使用`NotificationCompat.Builder`來建立並顯示通知。 我們了解如何啟動來回應使用者互動與通知，第二個活動的基本範例，我們示範從第一個活動的資料傳送至第二個活動。


## <a name="related-links"></a>相關連結

- [LocalNotifications （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [通知設計指南模式](http://developer.android.com/design/patterns/notifications.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
