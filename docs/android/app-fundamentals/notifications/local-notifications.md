---
title: 本機的通知
description: 本節說明如何實作 Xamarin.Android 在本機的通知。 它說明 Android 的通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 97c8372656f0cbfa5b8f7bb12d15b00feac4b5c3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773988"
---
# <a name="local-notifications"></a>本機的通知

_本節說明如何實作 Xamarin.Android 在本機的通知。它說明 Android 的通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。_

## <a name="local-notifications-overview"></a>本機通知概觀

本主題說明如何在 Xamarin.Android 應用程式中實作本機的通知。 它討論 Android 的通知的各個部分，它會說明適用於應用程式開發人員，不同的通知樣式並導入了一些應用程式開發介面，可用來建立及發行通知。

Android 提供兩個系統控制區域向使用者顯示通知圖示和通知資訊。 當第一次發行通知時，它的圖示會顯示在*通知區域*，如下列螢幕擷取畫面所示：

![在裝置上的範例通知區域](local-notifications-images/01-notification-shade.png)

若要取得有關該通知的詳細資料，使用者可以開啟通知抽屜 （這會展開以顯示通知內容的每個通知圖示），並執行與通知相關聯的任何動作。 下列螢幕擷取畫面顯示*通知抽屜*對應至上方顯示的通知區域：

[![範例顯示三個通知的通知抽屜](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android 的通知使用兩種類型的配置：

-   ***基底的版面配置*** &ndash; compact、 固定呈現格式。

-   ***展開的版面配置***&ndash;可以展開，以較大的大小，以顯示詳細資訊的呈現格式。

下列各節將說明每個配置類型 （以及如何建立它們）。


### <a name="base-layout"></a>基底的版面配置

所有 Android 通知是基礎基底的配置格式，其中至少包含下列元素：

1.  A*通知圖示*，如果應用程式支援不同類型的通知代表原始的應用程式或通知類型。

2.  通知*標題*，或如果通知，則個人訊息寄件者的名稱。

3.  通知訊息。

4.  A*時間戳記*。

這些項目會顯示下列圖表所示：

[![通知元素的位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

基底的配置會限制為 64 密度無關的像素 (dp) 的高度。 Android 會預設建立這個基本通知樣式。

（選擇性） 通知可以顯示大型圖示，表示寄件者的相片或應用程式。 使用大型圖示時，通知 Android 5.0 及更新版本中，小型通知圖示會顯示成徽章大型圖示上方：

![簡單的通知相片](local-notifications-images/04-simple-notification-photo.png)

從 Android 5.0 開始，通知也會出現在鎖定畫面上：

[![範例鎖定畫面通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

使用者可以點選鎖定畫面通知，若要解除鎖定裝置並跳至的應用程式引發通知，或若要關閉通知撥動。 應用程式可以設定來控制在鎖定畫面，顯示的內容通知的可視性層級和使用者可以選擇是否允許敏感的內容會顯示在鎖定畫面通知。

Android 5.0 引進稱為高優先權的告知呈現格式*抬頭*。 平視通知向下滑動從畫面頂端數秒鐘，然後撤退備份至通知區域：

[![範例 heads-up 通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

平視通知可讓系統將不會中斷目前正在執行的活動狀態的重要資訊的 UI。

Android 包含通知中繼資料的支援，因此可以排序和以聰明的方式顯示通知。 通知中繼資料也會控制通知在鎖定畫面上，且抬頭格式的呈現方式。 應用程式可以設定下列類型的通知中繼資料：

-   **優先順序**&ndash;優先權等級會決定如何及何時會呈現通知。 例如，在 Android 5.0，高優先順序的通知會顯示為抬頭通知。

-   **可見性**&ndash;指定鎖定畫面上會顯示通知時所要顯示多少通知內容。

-   **類別**&ndash;會通知系統如何處理在各種情況下，例如當裝置處於通知*請勿打擾*模式。

**注意：** **可視性**和**類別**在 Android 5.0 和無法使用在舊版的 Android 中推出。 從 Android 8.0、[通知通道](#notif-chan)用來控制向使用者顯示通知的方式。


### <a name="expanded-layouts"></a>展開的版面配置

從 Android 4.1 開始，可以使用可讓使用者展開通知以檢視其他內容的高度擴充的配置樣式設定通知。 例如，下列範例說明展開版面配置中的通知合約的模式：

![簽約的通知](local-notifications-images/07-contracted-notification.png)

展開此通知時，它會顯示整個訊息：

![展開的通知](local-notifications-images/08-expanded-notification.png)

Android 支援單一事件通知的三種擴充版面配置樣式：

-   ***大文字***&ndash;在簽約模式中，會顯示訊息，後面接著兩個句號的第一行的摘錄。 在展開模式中，顯示整個訊息 （如上述範例所示）。

-   ***收件匣***&ndash;在簽約模式中，會顯示新的訊息數目。 在展開模式中，會顯示第一個電子郵件訊息或收件匣中的訊息清單。

-   ***映像***&ndash;在簽約模式中，會顯示訊息文字。 在展開模式中，會顯示文字和影像。

[除了基本通知](#beyond-the-basic-notification)（本文稍後） 說明如何建立*大文字*，*收件匣*，和*映像*通知。


## <a name="notification-creation"></a>建立通知

若要建立 Android 的通知，您使用[Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/)類別。 `Notification.Builder` 若要簡化的通知物件建立 Android 3.0 中引進了。 若要建立與 Android 的舊版本相容的通知，您可以使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)類別而不是`Notification.Builder`(請參閱[相容性](#compatibility)稍後在本主題適用於使用詳細資訊`NotificationCompat.Builder`)。
`Notification.Builder` 提供方法來設定各種選項中的通知，例如：

-   內容，包括標題、 訊息文字，以及通知圖示。

-   樣式的通知，例如*大文字*，*收件匣*，或*映像*樣式。

-   通知的優先順序： 最小值、 低，預設值，高，或最大值。

-   在鎖定畫面通知的可見性： public、 private 或密碼。

-   類別目錄中繼資料，可協助 Android 分類和篩選的通知。

-   選擇性的意圖，表示用來啟動時所點選通知的活動。

產生器中設定這些選項之後，您就會產生通知物件，包含的設定。 若要發行通知，您傳遞到此通知物件*通知管理員*。 Android 提供[NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)類別，這是負責發行通知，並向使用者顯示它們。 可以從任何內容，例如活動或服務取得這個類別的參考。


### <a name="how-to-generate-a-notification"></a>如何產生通知

若要在 Android 產生通知，請遵循下列步驟：

1.  具現化`Notification.Builder`物件。

2.  呼叫各種方法上`Notification.Builder`物件來設定通知選項。

3.  呼叫[建置](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/)方法`Notification.Builder`通知物件具現化物件。

4.  呼叫[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification))發行通知的通知管理員的方法。

您必須至少提供每個通知的下列資訊：

-   小型圖示 (24 x 24 dp 的大小)

-   簡短標題

-   通知文字

下列程式碼範例說明如何使用`Notification.Builder`可產生基本的通知。 請注意，`Notification.Builder`方法支援[方法鏈結](http://en.wikipedia.org/wiki/Method_chaining); 也就是說，每個方法會傳回產生器物件，您可以使用最後的方法呼叫的結果叫用下一個方法呼叫：

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
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

在此範例中，新`Notification.Builder`呼叫物件`builder`是具現化，標題和文字通知的設定，並通知圖示會從載入**Resources/drawable/ic_notification.png**。 通知產生器 」 的呼叫`Build`方法會使用這些設定建立通知物件。 下一個步驟是呼叫`Notify`通知管理員的方法。 若要找出通知管理員，您呼叫`GetSystemService`，如上所示。

`Notify`方法接受兩個參數： 通知識別碼和通知的物件。 通知識別碼是識別您的應用程式通知的唯一整數。 在此範例中，通知識別碼設定為零 (0);不過，在實際執行應用程式中，您會想讓每個通知的唯一識別碼。 重複使用先前的識別碼值的呼叫中`Notify`造成覆寫最後的通知。

在 Android 5.0 裝置上執行此程式碼，它會產生通知，如下列範例所示：

![通知結果的範例程式碼](local-notifications-images/09-hello-world.png)

通知圖示會顯示在左邊的通知&ndash;此映像的圈選&ldquo;我&rdquo;具有 alpha 色板，讓 Android 可以繪製循環灰其後面。 您也可以提供沒有 alpha 色板圖示。 若要以圖示顯示相片影像，請參閱[大型圖示格式](#large-icon-format)本主題稍後。

時間戳記會設定自動執行，但您可以藉由呼叫覆寫此設定[SetWhen](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/)通知的產生器的方法。 比方說，下列程式碼範例會將時間戳記設定為目前時間：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>啟用聲音和震動

如果您想要您通知也播放音效，您可以呼叫通知產生器 」 的[SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/)方法並傳入`NotificationDefaults.Sound`旗標：

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

此呼叫`SetDefaults`會導致發佈通知時播放音效裝置。 如果您要讓裝置震動，而非播放音效，您可以傳遞`NotificationDefaults.Vibrate`至`SetDefaults.`如果您想要播放的音效和震動裝置的裝置，您可以將這兩個旗標，以傳遞`SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果您未指定要播放的音效啟用聲音，Android 會使用預設系統通知音效。 不過，您可以變更呼叫的通知產生器，就會播放聲音[SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/)方法。 例如，播放警示音效與您的通知 （而不是預設通知聲音），您可以取得的 URI 警示音效從[RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/)並將它傳遞給`SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，您可以使用系統預設鈴聲音效通知您：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

建立通知物件之後，便可通知物件上設定通知屬性 (而不是將它們設定到事先`Notification.Builder`方法)。 例如，而不是呼叫`SetDefaults`方法，以啟用震動通知，您可以直接修改通知的位元旗標[預設](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/)屬性：

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

這個範例會使裝置震動發佈通知時。

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>更新通知

如果您想要更新的通知內容，已發行之後，您可以重複使用現有`Notification.Builder`物件來建立新的通知物件和發佈與識別項的上次告知此通知。 例如: 

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此範例中，現有`Notification.Builder`物件用來建立新的通知物件使用不同的標題和訊息。
使用的識別項的上一個通知，發行新的通知物件，這會更新先前已發佈通知的內容：

![更新的通知](local-notifications-images/12-updated-notification.png)

重複使用先前的通知本文&ndash;只標題和文字通知變更時通知抽屜中會顯示通知。 從 「 範例通知 」 變更為 「 更新通知 」 標題文字，並從"Hello World 變更訊息文字 ！ 這是我的第一個通知 ！ 」 以 「 變更此訊息 」。

通知保持可見狀態，直到其中三種情況發生：

-   使用者關閉通知 (或點選*全部清除*)。

-   應用程式會呼叫`NotificationManager.Cancel`，傳入發行通知時所指派的唯一的通知識別碼。

-   應用程式會呼叫`NotificationManager.CancelAll`。

如需有關更新 Android 的通知，請參閱[修改通知](http://developer.android.com/training/notify-user/managing.html#Updating)。


### <a name="starting-an-activity-from-a-notification"></a>從通知開始活動

在 Android，通常會產生關聯的通知*動作*&ndash;活動在使用者點選通知時，會啟動。 此活動可位於另一個應用程式，或甚至另一項工作。 若要將動作加入至通知，您建立[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)物件，並建立關聯`PendingIntent`與通知。 A`PendingIntent`是一種特殊的允許收件者的應用程式傳送應用程式的權限執行一段預先定義程式碼的意圖。 當使用者點選通知時，所指定的活動啟動 Android `PendingIntent`。

下列程式碼片段會示範如何建立一則通知`PendingIntent`，將會啟動，原始的應用程式中，活動`MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
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

此程式碼是非常類似通知程式碼在前一個區段中，不同處在於`PendingIntent`加入至通知物件。 在此範例中，`PendingIntent`再傳遞至通知產生器是與原始的應用程式的活動相關聯[SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/)方法。 `PendingIntentFlags.OneShot`旗標傳遞至`PendingIntent.GetActivity`方法以便`PendingIntent`只有使用一次。 當執行此程式碼時，會顯示下列通知：

![第一個動作通知](local-notifications-images/10-first-action-notification.png)

點選此通知，引領使用者回到原始的活動。

在生產環境應用程式中，您的應用程式必須處理*上一頁堆疊*當使用者按**回**通知活動中的按鈕 （如果您不熟悉 Android 工作和上一頁堆疊，請參閱[工作和上一頁堆疊](http://developer.android.com/guide/components/tasks-and-back-stack.html))。
在大部分情況下，向後巡覽通知活動應該會傳回使用者從應用程式，並返回 [首頁] 螢幕。 若要管理上一頁堆疊，您的應用程式使用[TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/)類別來建立`PendingIntent`上一頁堆疊。

真實世界的另一個考量是，原始的活動可能需要將資料傳送給通知活動。 例如，通知可能指出文字訊息到達，，和通知活動 （訊息檢視畫面），需要向使用者顯示訊息的訊息識別碼。 建立活動`PendingIntent`可以使用[Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/)新增資料 （例如字串） 之意圖，讓這項資料會傳遞至 「 通知 」 活動的方法。

下列程式碼範例說明如何使用`TaskStackBuilder`管理上一頁堆疊，並包含如何將單一訊息字串傳送至呼叫的通知活動的範例`SecondActivity`:

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
Notification.Builder builder = new Notification.Builder (this)
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

在此程式碼範例中，應用程式包含兩個活動： `MainActivity` （包含以上的通知程式碼），和`SecondActivity`，使用者會看到在點選通知螢幕。 執行此程式碼時，會顯示簡單的通知 （類似於先前的範例）。 點選的通知，讓使用者存取`SecondActivity`螢幕：

![第二個活動的螢幕擷取畫面](local-notifications-images/11-second-activity.png)

字串訊息 (傳入的意圖`PutExtra`方法) 中擷取`SecondActivity`透過這行程式碼：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

中會顯示這個擷取的訊息，「 Greetings 從 MainActivity ！、"`SecondActivity`畫面上，如上述螢幕擷取畫面所示。 當使用者按**回**中的按鈕時`SecondActivity`，瀏覽會導致從應用程式，然後再回到之前的應用程式啟動螢幕。

如需建立暫止的對應方式的詳細資訊，請參閱[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)。


<a name="notif-chan" />

## <a name="notification-channels"></a>通知通道

從 Android 8.0 (Oreo) 開始，您可以使用*通知通道*功能來建立使用者可自訂的每一種您想要顯示的通知通道。 通知通道使其可供您群組通知以便傳送到通道展示區相同的行為的所有通知。 例如，您可能必須適用於需要立即注意的通知的通知通道和參考用訊息所使用的個別 「 安靜"通道。

**YouTube**與 Android Oreo 一起安裝的應用程式會列出兩個通知類別：**下載通知**和**一般通知**:

[![在 Android Oreo YouTube 通知螢幕](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

每個類別對應至在通知通道。 YouTube 應用程式實作**下載通知**通道和**一般通知**通道。 使用者可以點選**下載通知**，如應用程式的下載通知通道，其中會顯示 [設定] 畫面：

[![下載 YouTube 應用程式的通知螢幕](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

在此畫面中，使用者可以修改的行為**下載**通知通道，方法如下：

-   設定重要性層級為**緊急**，**高**，**媒體**，或**低**，也就是設定音效及視覺化中斷的層級。

-   開啟或關閉的通知點。

-   開啟或關閉的閃爍的光線。

-   顯示或隱藏在鎖定畫面上的通知。

-   覆寫**請勿打擾**設定。

**一般通知**通道具有類似的設定：

[![YouTube 應用程式的一般通知螢幕](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

請注意，您並沒有絕對通知通道與使用者之間的互動方式控制&ndash;使用者可以修改的任何裝置上的通知通道設定，如上面的螢幕擷取畫面所示。 不過，您可以設定預設值 （如下所述將是）。 如這些範例所示，則 新的通知通道功能可讓您為使用者提供更細微的控制不同種類的通知。

您應該加入應用程式的通知通道支援？ 如果您的目標 Android 8.0、 您的應用程式*必須*實作通知通道。
針對嘗試傳送給使用者的本機通知，而不使用的通知通道的 Oreo 應用程式將無法在 Oreo 裝置上顯示通知。 如果您不要的目標 Android 8.0，您的應用程式仍會執行 Android 8.0、 但具有相同的通知行為，因為它會在執行 Android 7.1 或更早版本時表現。


### <a name="creating-a-notification-channel"></a>建立通知通道

若要建立通知通道，執行下列作業：

1. 建構[NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html)具有下列物件：

    - 您的封裝內是唯一的識別碼字串。 在下列範例中，字串`com.xamarin.myapp.urgent`用。

    - 通道 （少於 40 個字元） 的使用者可見的名稱。 在下列範例中，名稱**緊急**用。

    - 控制如何防止通知通道的重要性在公佈到`NotificationChannel`。 可以是重要性`Default`， `High`， `Low`， `Max`， `Min`， `None`，或`Unspecified`。

    將這些值來傳遞[建構函式](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29)(在此範例中，`Resource.String.noti_chan_urgent`設**緊急**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  設定`NotificationChannel`物件初始設定。
    例如，下列程式碼會設定`NotificationChannel`物件，讓張貼至這個頻道的通知將震動裝置和鎖定畫面上依預設會出現：

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  提交通知管理員的通知通道物件：

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```


### <a name="posting-to-a-notifications-channel"></a>張貼到通知通道

若要張貼通知通道的通知，執行下列作業：

1.  設定通知使用`Notification.Builder`，並傳入通道識別碼`SetChannelId`方法。 例如: 

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  建立及發行通知使用通知管理員[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/)方法：

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

您可以重複上述步驟建立的告知性訊息的另一個通知通道。 此第二個通道可以根據預設，停用震動，將預設鎖定螢幕可見度設定為`Private`，而且設定為通知重要性`Default`。

在動作中 Android Oreo 通知通道的完整程式碼範例，請參閱[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)範例; 此範例應用程式管理兩個通道，並設定其他通知選項。



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>除了基本的通知

通知預設為花俏基底的配置格式在 Android 中，但是您可以加強這個基本格式產生額外`Notification.Builder`方法呼叫。 在本節中，您將學習如何將大型相片圖示新增至您的通知，您會看到有關如何建立擴充的版面配置通知的範例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大型圖示格式

Android 的通知通常會顯示原始的應用程式圖示 （左邊的通知）。 不過，通知可以顯示影像或相片 (*大型圖示*) 而不是標準的小圖示。 例如，訊息的應用程式可以顯示相片的寄件者，而不是應用程式圖示。

以下是基本 Android 5.0 通知的範例&ndash;它會顯示只有的小型應用程式圖示：

![範例一般通知](local-notifications-images/13-sample-notification.png)

以下是顯示大型圖示通知修改後的螢幕擷取畫面&ndash;它會使用所建立的 Xamarin 程式碼猴子映像的圖示：

![範例大型圖示通知](local-notifications-images/14-large-icon-sample.png)

請注意，通知以大圖示格式呈現，小型應用程式圖示會顯示為在大型圖示右下角的徽章。

若要使用映像做為大型圖示通知中，您呼叫通知產生器 」 的[SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/)方法並傳入影像的點陣圖。 不同於`SetSmallIcon`，`SetLargeIcon`只接受一個點陣圖。 若要將影像檔轉換成點陣圖，您使用[BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/)類別。 例如: 

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

這個程式碼範例會開啟在映像檔**Resources/drawable/monkey_icon.png**、 將它轉換成點陣圖，並將傳遞至產生的點陣圖`Notification.Builder`。 一般而言，來源影像解析度大於小圖示&ndash;但更大。 影像太大，可能會導致不必要的調整大小作業，可能會延遲與張貼的通知。
如需有關在 Android 的通知圖示大小，請參閱[通知圖示](http://developer.android.com/design/style/iconography.html#notification)。


### <a name="big-text-style"></a>大文字樣式

*大文字*樣式是您用來顯示在通知中的長訊息展開版面配置範本。 所有展開的版面配置通知，例如大型文字通知最初顯示在壓縮的呈現格式：

![範例大文字通知](local-notifications-images/15-big-text-notification.png)

這種格式，會顯示只訊息的摘要，以兩個句點終止。 當使用者拖曳通知時，則會展開以顯示整個通知訊息：

![展開的大文字通知](local-notifications-images/16-big-text-expanded.png)

此擴充的配置格式也會包含摘要文字底部的通知。 最大高度*大文字*通知為 256 dp。

若要建立*大文字*通知，您具現化`Notification.Builder`物件，如往常一般，和具現化並加入[BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/)物件`Notification.Builder`物件。 例如: 

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

在此範例中，訊息文字和摘要文字會儲存在`BigTextStyle`物件 (`textStyle`) 傳遞至之前 `Notification.Builder.`


### <a name="image-style"></a>影像樣式

*映像*樣式 (也稱為*大圖片*樣式) 是一種展開的通知的格式，可用來顯示影像的通知本文。 例如，螢幕擷取畫面應用程式或相片應用程式可以使用*映像*通知提供給使用者通知的最後的樣式映像的擷取。 請注意，最大高度*映像*通知為 256 dp &ndash; Android 會重新調整影像以符合為此最大高度限制，在可用記憶體的限制範圍內。

如同所有擴充配置通知*映像*簡潔的格式顯示隨附的訊息文字的摘錄會先顯示其通知：

![壓縮映像通知會顯示任何映像](local-notifications-images/17-image-compact.png)

當使用者拖曳*映像*通知，它會展開以顯示影像。 例如，以下是先前通知的展開的版本：

![展開的影像的通知會顯示影像](local-notifications-images/18-image-expanded.png)

請注意，以精簡格式顯示通知時，它會顯示通知文字 (傳遞至通知產生器的文字`SetContentText`方法，如稍早所示)。 不過，當通知展開來顯示影像時，它會顯示在影像上方的摘要文字。

若要建立*映像*通知，您具現化`Notification.Builder`物件如往常一般，然後建立並插入[BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/)物件插入`Notification.Builder`物件。 例如: 

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

像`SetLargeIcon`方法`Notification.Builder`、 [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/)方法`BigPictureStyle`需要您想要通知的主體中顯示之影像的點陣圖。 在此範例中， [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32))方法`BitmapFactory`讀取映像檔位於**Resources/drawable/x_bldg.png**並將它轉換成點陣圖。

您也可以顯示映像不會封裝為資源。 比方說，下列範例程式碼從本機的 sd 記憶卡中載入影像，並顯示在*映像*通知：

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

在此範例中，映像檔位於 **/sdcard/Pictures/my-tshirt.jpg**會載入、 重新調整回其原始大小的一半，然後轉換為在通知中使用的點陣圖：

![通知中的範例 t 恤映像](local-notifications-images/19-tshirt-notification.png)

如果您事先不知道映像檔的大小，則最好將呼叫包裝至[BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/)例外狀況處理常式中&ndash;`OutOfMemoryError`影像太大時，可能會擲回例外狀況若要調整大小的 android。

如需有關載入和解碼大型點陣圖影像，請參閱[負載大型點陣圖有效率地](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently)。


### <a name="inbox-style"></a>收件匣樣式

*收件匣*格式為適用於通知的主體中顯示的文字 （例如電子郵件收件匣摘要） 的個別行展開版面配置範本。 *收件匣*格式通知第一次顯示以精簡的格式：

![範例壓縮收件匣通知](local-notifications-images/20-inbox-compact.png)

當使用者拖曳通知時，它會展開以顯示的電子郵件摘要，如以下螢幕擷取畫面所示：

![展開範例收件匣通知](local-notifications-images/21-inbox-expanded.png)

若要建立*收件匣*通知，您具現化`Notification.Builder`物件，如往常一般，並加入[InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/)物件`Notification.Builder`。 例如: 

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

若要將新的文字行加入至通知主體，呼叫[Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/)方法`InboxStyle`物件 (最大高度*收件匣*通知為 256 dp)。 請注意，不同於*大文字*樣式，*收件匣*樣式支援通知主體中的個別行文字。

您也可以使用*收件匣*樣式需要個別的程式碼行的文字顯示在擴充格式中任何通知。 例如，*收件匣*通知樣式可以用來結合多個暫止的通知至摘要通知&ndash;您可以更新單一*收件匣*樣式以新的通知通知內容各行 (請參閱[更新通知](#updating-a-notification)上面)，而不是比產生新的大部分類似通知的連續資料流。 如需這種方法的詳細資訊，請參閱[摘要說明您的通知](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications)。


## <a name="configuring-metadata"></a>設定中繼資料

`Notification.Builder` 包含您可以呼叫以設定您的通知，例如優先順序、 可見性和類別目錄的相關中繼資料的方法。 Android 使用這項資訊&mdash;使用者喜好設定以及&mdash;來判斷如何及何時要顯示通知。


### <a name="priority-settings"></a>優先順序設定

發佈通知時，通知的優先順序設定會決定兩種結果：

-   通知出現的位置相對於其他通知。
    比方說，高優先順序的通知會呈現上述較低的優先權通知，通知抽屜不論之前發行的每個通知。

-   是否顯示通知 Heads-up 通知格式 (Android 5.0 及更新版本)。 只有*高*和*最大*優先順序通知會顯示為抬頭通知。

Xamarin.Android 會定義下列列舉來設定通知優先順序：

-   `NotificationPriority.Max` &ndash; 提醒使用者注意到緊急或重大狀況 （例如，連入呼叫、 開啟所開啟的指示或緊急警示）。 在 Android 5.0 及更新版本的裝置，最高優先順序的通知會顯示抬頭格式。

-   `NotificationPriority.High` &ndash; 通知 （例如重要的電子郵件或即時聊天訊息抵達） 的重要事件的使用者。 在 Android 5.0 及更新版本的裝置，高優先順序的通知會顯示抬頭格式。

-   `NotificationPriority.Default` &ndash; 通知使用者的中型的重要性層級的條件。

-   `NotificationPriority.Low` &ndash; 使用者必須要收到通知 （例如，軟體更新的提醒或社交網路更新） 的非緊急的資訊。

-   `NotificationPriority.Min` &ndash; 如需更多資訊，使用者通知時，才檢視通知 （例如，位置或天氣資訊）。

若要設定通知的優先順序，請呼叫[SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/)方法`Notification.Builder`物件，傳遞的優先順序層級中。 例如: 

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下列範例、 高優先權通知、 「 重要訊息 ！ 」 會出現在通知抽屜頂端：

![範例高優先權的告知](local-notifications-images/22-hi-priority-drawer.png)

因為這是高優先順序通知時，它也會顯示為使用者的目前活動 畫面中 Android 5.0 以上抬頭通知：

![範例抬頭通知](local-notifications-images/23-heads-up-example.png)

在下一個範例中，低優先順序 」 視為一天 「 通知會顯示在電池較高優先權層級的通知：

![範例低優先權的通知](local-notifications-images/24-lo-priority-drawer.png)

因為 「 一天的思考 」 通知是低優先權的通知，則 Android 不會顯示它 Heads-up 格式。


### <a name="visibility-settings"></a>可見性設定

從 Android 5.0，開始*可視性*設定是可用來控制多少通知內容會出現在安全的鎖定畫面上。
Xamarin.Android 會定義下列列舉來設定通知的可見性：

-   `NotificationVisibility.Public` &ndash; 安全的鎖定畫面上，會顯示通知的完整內容。

-   `NotificationVisibility.Private` &ndash; 只有必要的資訊會顯示在安全的鎖定畫面 （例如 [通知] 圖示和張貼其應用程式名稱），但通知的詳細資料的其餘部分會隱藏。 所有通知都預設為`NotificationVisibility.Private`。

-   `NotificationVisibility.Secret` &ndash; 不會顯示在安全的鎖定畫面，甚至不能通知圖示。 通知內容才可使用使用者解除鎖定裝置。

若要設定通知，應用程式呼叫的可見性`SetVisibility`方法`Notification.Builder`物件，傳遞的可見性設定。 例如，此呼叫`SetVisibility`讓通知`Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

當`Private`張貼通知時，只有名稱和應用程式的圖示會顯示在安全的鎖定畫面上。 而非通知訊息，使用者會看到 「 解除鎖定您的裝置若要查看此通知 」:

![解除鎖定您裝置的通知訊息](local-notifications-images/25-lockscreen-private.png)

在此範例中， **NotificationsLab**是原始的應用程式的名稱。 鎖定畫面時才安全顯示通知此 redacted 的版本 （亦即，透過 PIN、 模式或密碼保護）&ndash;鎖定畫面並不安全，通知的完整內容是否在鎖定畫面。


### <a name="category-settings"></a>類別目錄設定

從 Android 5.0 開始，預先定義的類別可供進行排名及篩選通知。 Xamarin.Android 提供下列列舉，這些類別目錄：

-   `Notification.CategoryCall` &ndash; 電話來電。

-   `Notification.CategoryMessage` &ndash; 內送的文字訊息。

-   `Notification.CategoryAlarm` &ndash; 警示條件或計時器到期日。

-   `Notification.CategoryEmail` &ndash; 傳入的電子郵件訊息。

-   `Notification.CategoryEvent` &ndash; 行事曆事件。

-   `Notification.CategoryPromo` &ndash; 促銷訊息或公告。

-   `Notification.CategoryProgress` &ndash; 背景作業的進度。

-   `Notification.CategorySocial` &ndash; 社交網路的更新。

-   `Notification.CategoryError` &ndash; 背景作業或驗證處理序失敗。

-   `Notification.CategoryTransport` &ndash; 媒體播放更新。

-   `Notification.CategorySystem` &ndash; 保留供系統使用 （系統或裝置狀態）。

-   `Notification.CategoryService` &ndash; 表示背景服務正在執行。

-   `Notification.CategoryRecommendation` &ndash; 建議相關的訊息至目前執行的應用程式。

-   `Notification.CategoryStatus` &ndash; 裝置的相關資訊。

通知排序時，通知的優先順序的優先順序高於其分類設定。 高優先順序通知，例如將顯示為抬頭即使它屬於`Promo`類別目錄。 若要設定通知的類別，呼叫`SetCategory`方法`Notification.Builder`物件，傳入的分類設定。 例如: 

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*請勿打擾*（Android 5.0 中新增） 功能來篩選類別為基礎的通知。 例如，*請勿打擾*畫面**設定**電話通話和訊息的允許豁免通知使用者：

![不會干擾螢幕參數](local-notifications-images/26-do-not-disturb.png)

當使用者設定*請勿打擾*封鎖所有的插斷，除了撥打電話 （如上述螢幕擷取畫面所示），Android 讓通知使用的類別目錄設定`Notification.CategoryCall`裝置時的呈現處於*請勿打擾*模式。 請注意，`Notification.CategoryAlarm`通知並不會封鎖*請勿打擾*模式。


<a name="compatibility" />

## <a name="compatibility"></a>相容性

如果您在建立應用程式會也在較早版本 （早 API 層級 4），Android 上執行您將使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)類別而不是`Notification.Builder`。 當您建立具有通知`NotificationCompat.Builder`，Android 可確保基本通知內容正確顯示較舊的裝置上。 不過，因為某些進階的通知功能無法使用在舊版的 Android 上，您的程式碼必須明確地處理展開的通知樣式、 分類和可見性層級如下所述的相容性問題。

若要使用`NotificationCompat.Builder`在您的應用程式，您必須包含[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)專案中的 NuGet。

下列程式碼範例說明如何建立基本的通知使用`NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

如本範例所示，重要通知選項的方法呼叫都完全相同的`Notification.Builder`。 不過，您的程式碼必須處理向下相容性問題更複雜的通知 （下一節中所述）。

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications)範例示範如何使用`NotificationCompat.Builder`啟動通知的第二個活動。 此範例程式碼中會說明[Xamarin.Android 中使用本機通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)逐步解說。


### <a name="notification-styles"></a>通知樣式

若要建立*大文字*，*映像*，或*收件匣*樣式與通知`NotificationCompat.Builder`，您的應用程式必須使用這些樣式的相容性版本。 例如，若要使用*大文字*樣式，具現化`NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同樣地，您的應用程式可以使用`NotificationCompat.InboxStyle`和`NotificationCompat.BigPictureStyle`如*收件匣*和*映像*分別樣式。


### <a name="notification-priority-and-category"></a>通知優先順序和分類

`NotificationCompat.Builder` 支援`SetPriority`（從開始提供 Android 4.1） 的方法。 不過，`SetCategory`方法*不*支援`NotificationCompat.Builder`因為分類為 Android 5.0 中導入新通知的中繼資料系統的一部分。

若要支援舊版的 Android、 where`SetCategory`是無法使用，您的程式碼可以檢查應用程式開發介面層級，在執行階段有條件地呼叫`SetCategory`時的應用程式開發介面層級等於或大於 Android 5.0 (API level 21):

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此範例中，應用程式的**目標 Framework**設為 Android 5.0 和**最低的 Android 版本**設**Android 4.1 (API 層級 16)**。 因為`SetCategory`是可用應用程式開發介面層級 21 和更新版本中，此程式碼範例會呼叫`SetCategory`它時才可用&ndash;不會呼叫`SetCategory`時的應用程式開發介面層級小於
21.


### <a name="lockscreen-visibility"></a>鎖定畫面可見性

因為 Android 不支援 Android 5.0 (API level 21)，才能鎖定畫面通知`NotificationCompat.Builder`不支援`SetVisibility`方法。 以上所述的`SetCategory`，您的程式碼可以檢查應用程式開發介面層級，在執行階段和呼叫`SetVisiblity`它時才可用：

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>總結

本文說明如何在 Android 中建立本機的通知。 它所描述的通知結構，它說明如何使用`Notification.Builder`建立通知，在大圖示 樣式通知如何*大文字*，*映像*和*收件匣*格式、 如何設定通知中繼資料的設定，例如優先順序、 可見性和類別，以及如何啟動通知的活動。 本文也說明了如何使用新的抬頭、 鎖定畫面，這些通知設定和*請勿打擾*Android 5.0 中導入的功能。 最後，您學到如何使用`NotificationCompat.Builder`為了維持與舊版 Android 的通知相容性。

如需有關設計適用於 Android 的通知的指導方針，請參閱[通知](http://developer.android.com/preview/notifications.html)。


## <a name="related-links"></a>相關連結

- [NotificationsLab （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications （範例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Android 的逐步解說中的本機通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知](http://developer.android.com/design/patterns/notifications.html)
- [通知使用者](http://developer.android.com/training/notify-user/index.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
