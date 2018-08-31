---
title: 本機通知
description: 本節說明如何在 Xamarin.Android 中實作本機通知。 它說明 Android 的通知的各種 UI 項目，並討論 API 的涉及建立和顯示通知。
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/16/2018
ms.openlocfilehash: 221fa9b70eeba2c4ca08433c627e5648470a7fac
ms.sourcegitcommit: 7ffbecf4a44c204a3fce2a7fb6a3f815ac6ffa21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2018
ms.locfileid: "39514527"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>本機通知

_本節說明如何在 Xamarin.Android 中實作本機通知。它說明 Android 的通知的各種 UI 項目，並討論 API 的涉及建立和顯示通知。_

## <a name="local-notifications-overview"></a>本機通知概觀

Android 提供兩個系統控制區域向使用者顯示通知圖示和通知資訊。 當第一次發行通知時，它的圖示會顯示在*通知區域*，如下列螢幕擷取畫面所示：

![在裝置上的範例通知區域](local-notifications-images/01-notification-shade.png)

若要取得有關該通知的詳細資料，使用者可以開啟通知抽屜 （這會展開以顯示通知內容的每個通知圖示），並執行與通知相關聯的任何動作。 下列螢幕擷取畫面顯示*通知抽屜*，其對應於通知區域上方顯示：

[![顯示三個通知的範例通知抽屜](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android 的通知會使用兩種類型的配置：

-   ***基底的版面配置***&ndash;精簡且固定的呈現格式。

-   ***展開的版面配置***&ndash;可以展開為較大的大小，以顯示詳細資訊的呈現格式。

下列各節將說明每一種版面配置類型 （以及如何建立它們）。

> [!NOTE]
> 本指南著重[NotificationCompat Api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html)從[Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 這些 Api 可確保最大值回溯相容性以 Android 4.0 （API 層級為 14）。


### <a name="base-layout"></a>基底的版面配置

Android 的所有通知都建置在基底的配置格式，其中至少包含下列元素：

1.  A*通知圖示*，如果應用程式支援不同類型的通知代表原始的應用程式或通知類型。

2.  通知*title*，或如果通知是個人訊息寄件者的名稱。

3.  通知訊息。

4.  A*時間戳記*。

這些項目會顯示如下圖所示：

[![通知元素的位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

基底的配置是限制為 64 密度獨立像素 (dp) 的高度。 根據預設，android 會建立此基本通知樣式。

（選擇性） 通知可以顯示大型圖示，表示應用程式] 或 [寄件者的相片。 通知在 Android 5.0 和更新版本中使用大圖示時，小型通知圖示會顯示以徽章形式，大型圖示上方：

![簡單的通知相片](local-notifications-images/04-simple-notification-photo.png)

從 Android 5.0 開始，通知也會出現在鎖定畫面上：

[![範例鎖定畫面通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

使用者可以點選兩下解除鎖定裝置，並跳至產生該通知，應用程式的鎖定畫面通知或撥動以關閉通知。 應用程式可以設定來控制的鎖定畫面上顯示的內容通知的可視性層級，使用者可以選擇是否允許要在鎖定畫面通知中顯示的敏感性內容。

Android 5.0 引進稱為 「 高優先順序通知的呈現格式*抬頭*。 抬頭通知向下滑動從畫面頂端幾秒鐘的時間，然後撤退往 [通知] 區域：

[![範例 heads-up 通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

抬頭通知可讓系統 UI 放入在使用者之前的重要資訊，而不會中斷目前執行活動的狀態。

Android 支援通知的中繼資料，讓通知可以排序，並以智慧方式顯示。 通知中繼資料也會控制在鎖定畫面上和抬頭格式通知的呈現方式。 應用程式可以設定下列類型的通知中繼資料：

-   **優先權**&ndash;優先順序層級可讓您決定如何及何時會顯示通知。 例如，在 Android 5.0，高優先順序通知會顯示為抬頭通知。

-   **可視性**&ndash;指定要在鎖定畫面上會顯示通知時要顯示多少通知內容。

-   **類別目錄**&ndash;會通知系統如何處理在各種情況下，例如當裝置處於通知 *「 請勿打擾 」* 模式。

**注意：** **可見性**並**分類**在 Android 5.0 和無法使用在舊版的 Android 中推出。 從 Android 8.0[通知通道](#notif-chan)用來控制向使用者通知的呈現方式。


### <a name="expanded-layouts"></a>展開的版面配置

從 Android 4.1 開始，可以使用擴充的版面配置樣式，可讓使用者依序展開 通知以檢視其他內容的高度設定通知。 例如，下列範例會說明擴充的版面配置中的通知合約的模式：

![合約的通知](local-notifications-images/07-contracted-notification.png)

展開此通知時，它會顯示整個訊息：

![展開的通知](local-notifications-images/08-expanded-notification.png)

Android 支援三種擴充的版面配置樣式，單一事件通知：

-   ***大型文字***&ndash;在簽約的模式中，會顯示訊息，後面接著兩個句點的第一行的摘錄。 在展開模式中，會顯示整個訊息 （如上述範例所示）。

-   ***收件匣***&ndash;在簽約的模式中，會顯示新的訊息數目。 在展開模式中，會顯示第一個電子郵件訊息或收件匣中的訊息清單。

-   ***映像***&ndash;在簽約的模式中，會顯示訊息文字。 在展開模式中，會顯示文字和影像。

[基本通知之外](#beyond-the-basic-notification)（稍後在本文中） 說明如何建立*大型文字*，*收件匣*，和*映像*通知。

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>通知通道

從 Android 8.0 oreo （版），您可以使用*通知通道*功能來建立使用者可自訂的每一種您想要顯示的通知通道。 通知通道使您能夠為您群組的通知，讓所有通知都張貼至通道展示相同的行為。 比方說，您可能會有適用於需要立即注意的通知的通知通道和不同的 「 更安靜 」 通道用來參考用訊息。

**YouTube**一起安裝的 Android Oreo 的應用程式會列出兩個通知的類別：**下載通知**並**一般通知**:

[![在 Android Oreo 的 YouTube 的通知畫面](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

這些類別對應至通知通道。 YouTube 應用程式會實作**下載通知**通道並**一般通知**通道。 使用者可以點選**下載通知**，其中會顯示 [設定] 畫面，如應用程式的下載通知通道：

[![下載 YouTube 應用程式的 [通知] 畫面](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

在此畫面中，使用者可以修改的行為**下載**通知通道，執行下列動作：

-   設定的重要性層級**Urgent**，**高**，**中型**，或**低**，也就是設定音效及視覺化中斷的層級。

-   開啟或關閉的通知點。

-   開啟或關閉，請開啟閃爍的燈。

-   顯示或隱藏在鎖定畫面上的通知。

-   覆寫 **「 請勿打擾 」** 設定。

**一般通知**通道有類似的設定：

[![YouTube 應用程式的一般通知畫面](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

請注意，您不需要對您的通知通道與使用者互動的方式絕對控制&ndash;使用者可以修改任何裝置上的通知通道的設定，如上方螢幕擷取畫面所示。 不過，您可以設定預設值 （會如下所述）。 如這些範例示範的則 新的通知通道功能可讓您為使用者提供更細微的控制不同種類的通知。


## <a name="notification-creation"></a>建立通知

若要在 Android 中建立的通知，您使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder)從類別[Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 套件。 這個類別可讓您能夠建立及發行在舊版 Android 上的通知。 如需使用詳細資訊`NotificationCompat.Builder`，請參閱 <<c2> [ 相容性](#compatibility)本主題稍後的。

`NotificationCompat.Builder` 提供方法來設定各種選項在通知中，例如：

-   內容，包括標題、 訊息文字，以及 [通知] 圖示。

-   樣式的通知，例如*大型文字*，*收件匣*，或*映像*樣式。

-   通知的優先順序： 最小值、 低，預設值，高，或最大值。 Android 8.0 和更新版本，透過設定優先權[_通知通道_](#notification-channels)。

-   在鎖定畫面通知的可見性： 公用、 私人或祕密。

-   類別目錄中繼資料，可協助 Android 分類和篩選通知。

-   選擇性的意圖，表示用來在點選通知時啟動的活動。

-   通知通道，將於 (Android 8.0 和更新版本) 發行通知的識別碼。

產生器中設定這些選項之後，您就會產生包含設定的通知物件。 若要發行通知，您此通知將物件傳遞至*通知管理員*。 Android 提供[NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)類別，這是負責發行通知，並向使用者顯示它們。 可以從任何內容，例如活動或服務取得這個類別的參考。


### <a name="creating-a-notification-channel"></a>建立通知通道

Android 8.0 執行的應用程式必須建立其通知的通知通道。 通知通道需要下列三段資訊：

* 唯一識別通道的套件識別碼字串。
* 通道會向使用者顯示的名稱。  名稱必須是介於 1 到 40 個字元。
* 通道的重要性。

應用程式需要檢查它們正在執行的 Android 版本。
執行版本早於 Android 8.0 裝置不應建立通知通道。 下列的方法是如何建立通知通道，在活動中的其中一個範例：

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

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

每次建立活動時，應該建立通知通道。 針對`CreateNotificationChannel`方法，它應該呼叫`OnCreate`活動的方法。

### <a name="creating-and-publishing-a-notification"></a>建立和發行通知

若要在 Android 中產生通知，請遵循下列步驟：

1.  具現化`NotificationCompat.Builder`物件。

2.  在呼叫各種方法`NotificationCompat.Builder`物件來設定通知選項。

3.  呼叫[建置](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/)方法`NotificationCompat.Builder`物件具現化的通知物件。

4.  呼叫[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification))通知管理員來發行通知的方法。

您必須至少提供每個通知的下列資訊：

-   小圖示 (24 x 24 dp 的大小)

-   簡短標題

-   通知的文字

下列程式碼範例說明如何使用`NotificationCompat.Builder`產生基本的通知。 請注意，`NotificationCompat.Builder`方法支援[方法鏈結](http://en.wikipedia.org/wiki/Method_chaining); 也就是說，每個方法會傳回產生器物件讓您可以使用最後一個方法呼叫的結果來叫用下一個方法呼叫：

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此範例中，新`NotificationCompat.Builder`物件呼叫`builder`會具現化，以及要使用通知通道的識別碼。 設定標題和通知的文字，並通知圖示會從載入**Resources/drawable/ic_notification.png**。 通知產生器 」 的呼叫`Build`方法會建立使用這些設定的通知物件。 下一個步驟是呼叫`Notify`通知管理員的方法。 若要找出通知管理員，請呼叫`GetSystemService`，如上所示。

`Notify`方法接受兩個參數： 通知識別碼和通知的物件。 通知識別碼是唯一的整數，識別您的應用程式通知。 在此範例中，通知識別碼設定為零 (0);不過，在生產環境應用程式中，您會想要讓每個通知的唯一識別碼。 重複使用先前的識別碼值，在呼叫`Notify`都會導致覆寫最後一則通知。

當 Android 5.0 裝置上執行此程式碼時，它會產生一則通知，如下列範例所示：

![通知的範例程式碼的結果](local-notifications-images/09-hello-world.png)

通知圖示會顯示在通知左側&ndash;圈選這個映像&ldquo;我&rdquo;的 alpha 色板，以便 Android 可以繪製其背後的灰色循環背景。 您也可以提供沒有 alpha 色板圖示。 若要以圖示顯示相片的映像，請參閱[大型圖示格式](#large-icon-format)本主題稍後的。

時間戳記會設定自動執行，但您可以藉由呼叫覆寫此設定[SetWhen](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/)通知產生器的方法。 比方說，下列程式碼範例會將時間戳記設定為目前時間：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>啟用音效和震動

如果您想您也播放音效的通知，您可以呼叫通知產生器[SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/)方法並傳入`NotificationDefaults.Sound`旗標：

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

此呼叫`SetDefaults`會導致發佈通知時播放音效裝置。 如果您想要震動，而不是播放音效的裝置，您可以傳遞`NotificationDefaults.Vibrate`要`SetDefaults.`如果您想要播放的音效和震動裝置的裝置，您可以傳遞至這兩個旗標`SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果您未指定要播放的聲音啟用音效，Android 會使用預設系統通知音效。 不過，您可以在其中變更藉由呼叫通知產生器，就會播放音效[SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/)方法。 比方說，播放警示音效與您的通知 （而不是預設通知音效），您可以取得的 URI 警示從音效[RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/)並將它傳遞給`SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，您可以使用系統預設鈴聲音效通知：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

建立通知物件之後，就能夠通知物件上設定通知屬性 (而不是將其設定為透過事先`NotificationCompat.Builder`方法)。 比方說，而不是呼叫`SetDefaults`方法，以啟用震動通知，您可以直接修改通知的位元旗標[預設](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/)屬性：

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

此範例會在發佈通知時震動裝置。

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>正在更新通知

如果您想要更新的通知內容，已發行之後，您可以重複使用現有`NotificationCompat.Builder`物件來建立新的通知物件，並發行這個識別碼上次告知的通知。 例如: 

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此範例中，現有`NotificationCompat.Builder`物件用來建立新的通知物件不同的標題和訊息。
使用的識別項的上一個告知，發行新的通知物件，這會更新先前已發行通知的內容：

![已更新的通知](local-notifications-images/12-updated-notification.png)

上一個告知的主體會重複使用&ndash;只有標題及通知有更多的變更時通知會顯示在 [通知] 下拉式清單中的文字。 從 「 範例通知 」 變更為 「 更新通知 」 標題文字，並從"Hello World 訊息文字變更 ！ 這是我第一次的通知 ！ 」 以"變更為此訊息。 」

其中三個項目之前，仍會顯示通知：

-   使用者關閉通知 (或點選*全部清除*)。

-   應用程式會呼叫`NotificationManager.Cancel`，並傳入通知發行時已指派唯一的通知識別碼。

-   應用程式會呼叫`NotificationManager.CancelAll`。

如需詳細資訊更新 Android 的通知，請參閱 <<c0> [ 修改通知](http://developer.android.com/training/notify-user/managing.html#Updating)。


### <a name="starting-an-activity-from-a-notification"></a>從通知的活動

在 Android 中很常見的通知相關聯*動作*&ndash;使用者點選通知時，會啟動活動。 此活動可以位於另一個應用程式或甚至是在另一項工作。 若要新增動作的通知，您建立[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)物件，並將關聯`PendingIntent`與通知。 A`PendingIntent`是一種特殊的 允許收件者的應用程式傳送應用程式的權限執行預先定義的一段程式碼的意圖。 當使用者點選通知時，Android 則會啟動一項所指定的活動`PendingIntent`。

下列程式碼片段說明如何建立具有通知`PendingIntent`，將會啟動的原始應用程式中，活動`MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

此程式碼是非常類似，通知中的程式碼上節中，不同之處在於`PendingIntent`會新增至通知物件。 在此範例中，`PendingIntent`再傳遞至通知產生器是與原始的應用程式的活動相關聯[SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/)方法。 `PendingIntentFlags.OneShot`旗標傳遞至`PendingIntent.GetActivity`方法，讓`PendingIntent`只有使用一次。 此程式碼執行時，會顯示下列通知：

![第一個動作通知](local-notifications-images/10-first-action-notification.png)

點選此通知，引領使用者回到原始的活動。

在生產應用程式，您的應用程式必須處理*上一頁堆疊*當使用者按下**回**通知活動內的按鈕 （如果您不熟悉 Android 的工作和上一頁堆疊，請參閱[工作和上一頁堆疊](http://developer.android.com/guide/components/tasks-and-back-stack.html))。
在大部分情況下，向後巡覽通知活動應該會傳回使用者登出應用程式，並回到主畫面。 若要管理上一頁堆疊，也就是您的應用程式會使用[TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/)類別來建立`PendingIntent`上一頁堆疊使用。

真實世界的另一個考量是原始的活動可能需要將資料傳送至通知活動。 例如，通知可能表示文字訊息到達，並通知活動 （訊息檢視畫面），需要向使用者顯示訊息之訊息的識別碼。 建立活動`PendingIntent`可以使用[Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/)方法，以將資料 （例如，字串） 新增至目的，讓這項資料會傳遞至通知活動。

下列程式碼範例說明如何使用`TaskStackBuilder`來管理上一頁堆疊，並包含如何將單一訊息字串傳送至呼叫的通知活動的範例`SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此範例中，應用程式包含兩個活動： `MainActivity` （包含以上的通知程式碼），和`SecondActivity`，使用者會看到點選通知之後的畫面。 執行此程式碼時，會使用簡單的通知 （類似於先前的範例）。 點選通知，讓使用者存取`SecondActivity`螢幕：

![第二個活動的螢幕擷取畫面](local-notifications-images/11-second-activity.png)

字串訊息 (傳入的意圖`PutExtra`方法) 中擷取`SecondActivity`透過這行程式碼：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

中會顯示這個擷取的訊息，「 Greetings 從 MainActivity ！、 「`SecondActivity`畫面，如上述螢幕擷取畫面所示。 當使用者按下**回復**中的按鈕時`SecondActivity`，登出應用程式，再回復成先前的應用程式啟動畫面，會導致瀏覽。

如需建立擱置中的對應方式的詳細資訊，請參閱[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)。


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>除了基本的通知

通知預設為在 Android 中，以簡單的基本配置格式，但您可以藉由其他加強此基本格式`NotificationCompat.Builder`方法呼叫。 在本節中，您將了解如何將大型相片圖示新增至您的通知，您會看到如何建立擴充的版面配置通知的範例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大圖示格式

Android 的通知通常會顯示原始的應用程式圖示 （左邊的通知）。 不過，通知可以顯示影像或相片 (*大圖示*) 而不是標準的小圖示。 例如，傳訊應用程式可以顯示相片的寄件者，而不是應用程式圖示。

以下是基本的 Android 5.0 通知的範例&ndash;它會顯示只有小型的應用程式圖示：

![範例一般通知](local-notifications-images/13-sample-notification.png)

以下是顯示大圖示之後修改其通知的螢幕擷取畫面&ndash;它會使用從 Xamarin 程式碼猴映像建立的圖示：

![範例大圖示通知](local-notifications-images/14-large-icon-sample.png)

請注意，通知會以大型圖示格式呈現，小型的應用程式圖示會顯示為右上角的大圖示徽章。

為大型圖示通知中使用的映像，您可以呼叫通知產生器[SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/)方法並傳入之影像的點陣圖。 不同於`SetSmallIcon`，`SetLargeIcon`只接受一個點陣圖。 若要將影像檔轉換成點陣圖，您使用[BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/)類別。 例如: 

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

此程式碼範例會開啟在映像檔**Resources/drawable/monkey_icon.png**、 將它轉換成點陣圖，並傳遞至產生的點陣圖`NotificationCompat.Builder`。 一般而言，來源影像解析度大於小圖示&ndash;但更大。 映像太大，可能會導致不必要的調整大小作業，可能會延遲張貼通知。


### <a name="big-text-style"></a>大型文字樣式

*大型文字*樣式是您用來顯示通知中的長訊息展開的版面配置範本。 所有展開的版面配置通知，例如大型文字通知最初顯示在精簡的呈現格式：

![範例大型文字通知](local-notifications-images/15-big-text-notification.png)

在這種格式，會顯示只有訊息的摘錄，兩個句號來終止。 當使用者拖曳通知時，則會展開以顯示整個通知訊息：

![擴充的大型文字通知](local-notifications-images/16-big-text-expanded.png)

此擴充的配置格式也會包含摘要的文字底部的通知。 最大高度*大型文字*通知是 256 dp。

若要建立*大型文字*通知，您具現化`NotificationCompat.Builder`物件，如往常一般，然後具現化，並新增[BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/)物件`NotificationCompat.Builder`物件。 請看以下範例：

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

在此範例中，訊息文字和摘要的文字會儲存在`BigTextStyle`物件 (`textStyle`) 則會傳遞至之前 `NotificationCompat.Builder.`


### <a name="image-style"></a>映像樣式

*映像*樣式 (也稱為*全貌*樣式) 是一種展開的通知的格式，您可以使用通知的主體中顯示影像。 例如，螢幕擷取畫面的應用程式或相片應用程式可以使用*映像*通知要對使用者提供的最後一個通知樣式映像的擷取。 請注意，最大高度*映像*通知是 256 dp &ndash; Android 會調整大小以符合此最大高度限制，在可用記憶體的限制範圍內的映像。

如同所有擴充配置通知*映像*通知會先顯示壓縮的格式顯示摘錄自隨附的訊息文字：

![壓縮映像的通知會顯示任何映像](local-notifications-images/17-image-compact.png)

當使用者拖曳*映像*通知，它會展開以顯示影像。 例如，以下是上一個告知的展開的版本：

![展開的影像通知會顯示映像](local-notifications-images/18-image-expanded.png)

請注意，當以壓縮格式會顯示通知，它會顯示通知文字 (傳遞至通知產生器 」 的文字`SetContentText`方法，如稍早所示)。 不過，當通知展開以顯示影像時，它就會顯示在影像上方的摘要文字。

若要建立*映像*通知，您具現化`NotificationCompat.Builder`物件，然後建立並插入[BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/)物件插入`NotificationCompat.Builder`物件。 例如: 

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

像是`SetLargeIcon`方法`NotificationCompat.Builder`，則[BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/)方法`BigPictureStyle`需要您想要通知的主體中顯示之影像的點陣圖。 在此範例中， [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32))方法`BitmapFactory`讀取映像檔位於**Resources/drawable/x_bldg.png**並將它轉換成點陣圖。

您也可以顯示不會封裝的映像做為資源。 比方說，下列範例程式碼載入影像從本機的 sd 記憶卡，而會顯示在*映像*通知：

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

在此範例中，映像檔位於 **/sdcard/Pictures/my-tshirt.jpg**是載入、 調整大小，以其原始大小的一半，然後轉換在通知中使用的點陣圖：

![在通知中的範例 t 恤映像](local-notifications-images/19-tshirt-notification.png)

如果您事先不知道映像檔的大小，最好在其中將呼叫包裝至[BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/)中的例外狀況處理常式&ndash;`OutOfMemoryError`如果影像太大，可能會擲回例外狀況若要調整大小的 android。

如需有關載入和大型點陣圖影像的解碼的詳細資訊，請參閱[載入大型點陣圖有效率地](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently)。


### <a name="inbox-style"></a>收件匣樣式

*收件匣*格式不展開的版面配置範本，適用於通知的主體中顯示的文字 （例如電子郵件收件匣摘要） 的不同行。 *收件匣*格式通知第一次顯示壓縮的格式：

![範例 compact 的收件匣通知](local-notifications-images/20-inbox-compact.png)

當使用者拖曳通知時，它會展開以顯示電子郵件傳送摘要，如以下螢幕擷取畫面所示：

![展開範例收件匣通知](local-notifications-images/21-inbox-expanded.png)

若要建立*收件匣*通知，您具現化`NotificationCompat.Builder`物件，如往常一般，並新增[InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/)物件`NotificationCompat.Builder`。 請看以下範例：

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

若要將新的文字行新增至通知本文中，呼叫[Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/)方法`InboxStyle`物件 (的最大高度*收件匣*通知是 256 dp)。 請注意，不同於*大型文字*樣式*收件匣*樣式支援通知主體中的個別的文字行。

您也可以使用*收件匣*任何通知，需要在擴充格式中顯示個別的文字行的樣式。 例如，*收件匣*通知樣式可以用來結合多個擱置中的通知，摘要通知&ndash;您可以更新單一*收件匣*樣式以新的通知通知內容的行 (請參閱[更新通知](#updating-a-notification)上方)，而不是比產生連續的資料流的新，大部分是類似的通知。


## <a name="configuring-metadata"></a>設定中繼資料

`NotificationCompat.Builder` 包含您可以呼叫以設定您的通知，例如優先順序、 可見性和類別目錄的相關中繼資料的方法。 Android 使用此資訊&mdash;使用者喜好設定以及&mdash;來決定如何及何時要顯示通知。


### <a name="priority-settings"></a>優先順序設定

執行 Android 7.1 和較低的應用程式必須直接在通知本身上設定的優先順序。 發行通知時，通知的優先順序設定會決定兩種結果：

-   通知出現的位置相對於其他通知。
    比方說，高優先順序通知是以上所顯示較低的優先順序通知，在 [通知] 下拉式清單中，不論每個通知發行時。

-   是否會顯示通知，Heads-up 通知格式 (Android 5.0 及更新版本)。 只有*高*並*上限*優先順序通知會顯示為抬頭通知。

Xamarin.Android 會定義下列列舉來設定通知優先順序：

-   `NotificationPriority.Max` &ndash; 提醒使用者 （比方說，來電，開啟所開啟的方向或緊急警示） 的緊急或重大狀況。 在 Android 5.0 和更新版本的裝置上，最高優先順序通知會顯示抬頭格式。

-   `NotificationPriority.High` &ndash; 通知 （例如重要的電子郵件或即時聊天訊息抵達） 的重要事件的使用者。 在 Android 5.0 和更新版本的裝置上，高優先順序通知會顯示抬頭格式。

-   `NotificationPriority.Default` &ndash; 通知使用者有中度重要性的層級的條件。

-   `NotificationPriority.Low` &ndash; 使用者必須被告知 （例如，軟體更新的提醒或社交網路更新） 的非緊急性的資訊。

-   `NotificationPriority.Min` &ndash; 如需更多資訊，使用者通知時，才檢視通知 （例如，位置或天氣資訊）。

若要設定通知的優先順序，請呼叫[SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/)方法`NotificationCompat.Builder`物件以傳遞的優先順序層級。 例如: 

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下列範例中，高優先順序通知、 「 重要訊息 ！ 」 會出現在 [通知] 下拉式清單的頂端：

![範例高優先順序通知](local-notifications-images/22-hi-priority-drawer.png)

因為這是高優先順序通知時，它也會顯示為 Android 5.0 中使用者的目前活動畫面上的預告通知：

![範例抬頭通知](local-notifications-images/23-heads-up-example.png)

在下一個範例中，低優先權 「 相信有一天一天 」 通知會顯示在電池更高的優先順序層級的通知：

![範例低優先順序的通知](local-notifications-images/24-lo-priority-drawer.png)

由於 「 思考每日 」 通知是低優先順序的通知，Android 不會顯示它 Heads-up 格式。

> [!NOTE]
> Android 8.0 和更新版本中，通知通道和使用者設定的優先順序會決定通知的優先順序。

### <a name="visibility-settings"></a>可見性設定

從 Android 5.0*可視性*設定是可用來控制多少通知內容出現在安全的鎖定畫面上。
Xamarin.Android 會定義下列列舉來設定通知可見性：

-   `NotificationVisibility.Public` &ndash; 在安全的鎖定畫面上顯示通知的完整內容。

-   `NotificationVisibility.Private` &ndash; 只有必要的資訊會顯示在安全的鎖定畫面 （例如 [通知] 圖示和張貼其應用程式名稱），但通知的詳細資料的其餘部分會隱藏。 所有通知都預設為`NotificationVisibility.Private`。

-   `NotificationVisibility.Secret` &ndash; 不會顯示在安全的鎖定畫面，甚至不通知圖示。 使用者解除鎖定裝置時，才使用通知內容。

若要設定通知，應用程式呼叫的可視性`SetVisibility`方法的`NotificationCompat.Builder`物件以傳遞的可見性設定。 例如，此呼叫`SetVisibility`可讓通知`Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

當`Private`通知公佈時，只有名稱和應用程式的圖示會顯示安全的鎖定畫面上。 而非通知訊息，使用者會看到 「 解除鎖定您的裝置若要查看此通知 」:

![解除鎖定您裝置的通知訊息](local-notifications-images/25-lockscreen-private.png)

在此範例中， **NotificationsLab**是原始的應用程式的名稱。 此修訂的版本的通知會出現鎖定畫面時才安全 （亦即，透過 PIN、 圖樣或密碼保護）&ndash;鎖定畫面並不安全，通知的完整內容是否可在鎖定畫面上取得。


### <a name="category-settings"></a>類別目錄設定

從 Android 5.0 開始，則可供進行排名與篩選通知中預先定義的類別。 Xamarin.Android 提供下列的列舉，這些類別：

-   `Notification.CategoryCall` &ndash; 內送的電話。

-   `Notification.CategoryMessage` &ndash; 內送的文字訊息。

-   `Notification.CategoryAlarm` &ndash; 警示條件或計時器到期時間。

-   `Notification.CategoryEmail` &ndash; 內送的電子郵件訊息。

-   `Notification.CategoryEvent` &ndash; 行事曆事件。

-   `Notification.CategoryPromo` &ndash; 促銷的訊息或公告。

-   `Notification.CategoryProgress` &ndash; 背景作業的進度。

-   `Notification.CategorySocial` &ndash; 社交網路的更新。

-   `Notification.CategoryError` &ndash; 背景作業或驗證處理序失敗。

-   `Notification.CategoryTransport` &ndash; 媒體的播放更新。

-   `Notification.CategorySystem` &ndash; 保留供系統使用 （系統或裝置狀態）。

-   `Notification.CategoryService` &ndash; 指出背景服務正在執行。

-   `Notification.CategoryRecommendation` &ndash; 建議相關的訊息至目前執行的應用程式。

-   `Notification.CategoryStatus` &ndash; 裝置的相關資訊。

通知排序時，通知的優先順序的優先順序高於其分類設定。 比方說，高優先順序通知將會顯示為抬頭即使它屬於`Promo`類別目錄。 若要設定通知的類別目錄，請呼叫`SetCategory`方法的`NotificationCompat.Builder`物件以傳遞的分類設定。 例如: 

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*「 請勿打擾 」* （新功能 Android 5.0） 的功能來篩選依類別區分的通知。 例如，*請勿打擾*畫面**設定**允許豁免通知使用者的通話和訊息：

![請勿打擾 」 畫面參數](local-notifications-images/26-do-not-disturb.png)

當使用者設定 *「 請勿打擾 」* 若要封鎖所有的插斷，除了撥打電話 （如上述螢幕擷取畫面所示），Android 可讓類別目錄設定為通知`Notification.CategoryCall`裝置時顯示處於 *「 請勿打擾 」* 模式。 請注意，`Notification.CategoryAlarm`通知永遠不會在封鎖*請勿打擾*模式。

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications)範例示範如何使用`NotificationCompat.Builder`啟動通知的第二個活動。 此範例程式碼會說明[在 Xamarin.Android 中使用本機通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)逐步解說。


### <a name="notification-styles"></a>通知樣式

若要建立*大型文字*，*映像*，或*收件匣*樣式通知`NotificationCompat.Builder`，您的應用程式必須使用這些樣式的相容性版本。 例如，若要使用*大型文字*樣式，具現化`NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同樣地，您的應用程式可以使用`NotificationCompat.InboxStyle`和`NotificationCompat.BigPictureStyle`如*收件匣*並*映像*分別設定樣式。


### <a name="notification-priority-and-category"></a>通知的優先順序和類別

`NotificationCompat.Builder` 支援`SetPriority`方法 （可從 Android 4.1）。 不過，`SetCategory`方式*不*受到`NotificationCompat.Builder`因為類別是 Android 5.0 中引進的新通知中繼資料系統的一部分。

為了支援舊版的 Android、 何處`SetCategory`是無法使用，您的程式碼可以檢查在執行階段，有條件地呼叫的 API 層級`SetCategory`API 層級等於或大於 Android 5.0 (API level 21) 時：

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此範例中，應用程式的**目標 Framework**設為 Android 5.0 和**最低 Android 版本**設定為**Android 4.1 (API 層級 16)**。 因為`SetCategory`是可在 API 層級 21 及更新版本中，此程式碼範例會呼叫`SetCategory`它時才可用&ndash;它將不會呼叫`SetCategory`API 層級時小於 21。


### <a name="lockscreen-visibility"></a>鎖定畫面的可見性

因為 Android 不支援 Android 5.0 (API level 21) 之前的鎖定畫面通知`NotificationCompat.Builder`不支援`SetVisibility`方法。 如前文所述的`SetCategory`，您的程式碼可以檢查在執行階段和呼叫的 API 層級`SetVisiblity`它時才可用：

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>總結

這篇文章說明如何在 Android 中建立本機通知。 它說明通知的結構，它說明如何使用`NotificationCompat.Builder`若要建立通知，如何在大型圖示，樣式通知*大型文字*，*映像*和*收件匣*格式、 如何設定通知的中繼資料的設定，例如優先順序、 可見性和類別目錄和如何啟動通知的活動。 本文也說明了這些通知設定如何使用新的抬頭，鎖定畫面，並 *「 請勿打擾 」* Android 5.0 中引進的功能。 最後，您已了解如何使用`NotificationCompat.Builder`為了維持與舊版 Android 通知相容性。

如需有關設計適用於 Android 的通知的指導方針，請參閱[通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


## <a name="related-links"></a>相關連結

- [NotificationsLab （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [在 Android 逐步解說中的本機通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知使用者](http://developer.android.com/training/notify-user/index.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
