---
title: Android 上的本機通知
description: 本節說明如何在 Xamarin 中執行本機通知。 其中說明 Android 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 617c04e2f40af535fb381362a389524d693fad0b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303936"
---
# <a name="local-notifications-on-android"></a>Android 上的本機通知

_本節說明如何在 Xamarin 中執行本機通知。其中說明 Android 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。_

## <a name="local-notifications-overview"></a>本機通知總覽

Android 提供兩個系統控制的區域，以向使用者顯示通知圖示和通知資訊。 第一次發佈通知時，其圖示會顯示在*通知區域*中，如下列螢幕擷取畫面所示：

![裝置上的範例通知區域](local-notifications-images/01-notification-shade.png)

若要取得通知的詳細資料，使用者可以開啟通知抽屜（這會展開每個通知圖示以顯示通知內容），並執行與通知相關聯的任何動作。 下列螢幕擷取畫面顯示對應于上方所示通知區域的*通知抽屜*：

[顯示三個通知的 ![範例通知抽屜](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android 通知會使用兩種類型的版面配置：

- ***基底版面***配置 &ndash; 精簡、固定的呈現格式。

- ***展開的版面***配置 &ndash; 可以擴充到較大大小以顯示詳細資訊的簡報格式。

下列各節將說明這些版面配置類型（以及如何建立它們）。

> [!NOTE]
> 本指南著重于[Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)的[NotificationCompat api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) 。 這些 Api 會確保最大的 Android 4.0 回溯相容性（API 層級14）。

### <a name="base-layout"></a>基本版面配置

所有 Android 通知都是以基本版面配置格式為基礎，其中至少包含下列元素：

1. 表示原始應用程式的*通知圖示*，如果應用程式支援不同類型的通知，則為通知類型。

2. 通知*標題*，或寄件者的名稱（如果通知是個人訊息）。

3. 通知訊息。

4. *時間戳記*。

如下圖所示，會顯示這些元素：

[通知元素的 ![位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

基本配置的高度限制為64個密度獨立圖元（dp）。 Android 預設會建立此基本通知樣式。

（選擇性）通知可以顯示代表應用程式或寄件者相片的大型圖示。 當 Android 5.0 和更新版本的通知中使用大型圖示時，小的通知圖示會顯示為大圖示上的徽章：

![簡單的通知相片](local-notifications-images/04-simple-notification-photo.png)

從 Android 5.0 開始，通知也會出現在鎖定畫面上：

[![範例鎖定畫面通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

使用者可以按兩下鎖定螢幕通知來解除鎖定裝置，並跳至發出該通知的應用程式，或滑動以關閉通知。 應用程式可以設定通知的可見度層級以控制鎖定畫面上顯示的內容，而使用者可以選擇是否允許在鎖定畫面通知中顯示機密內容。

Android 5.0 引進了稱為「*列印頭*」的高優先順序通知簡報格式。 標題通知會從畫面頂端向下滑動幾秒鐘，然後重新執行至通知區域：

[![範例標題通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

標頭通知可讓系統 UI 將重要資訊放在使用者前方，而不會中斷目前正在執行之活動的狀態。

Android 包含通知中繼資料的支援，可讓您以智慧方式排序和顯示通知。 通知中繼資料也會控制通知在鎖定畫面上的呈現方式，以及如何以標題的格式顯示。 應用程式可以設定下列類型的通知中繼資料：

- **優先順序**&ndash; 優先順序等級會決定通知的呈現方式和時機。 例如，在 Android 5.0 中，高優先順序的通知會顯示為「標題通知」。

- **可見度**&ndash; 指定當通知出現在鎖定畫面上時，要顯示多少通知內容。

- **類別**&ndash; 會通知系統如何在各種情況下處理通知，例如裝置處於「*請勿打擾*」模式時。

> [!NOTE]
> **可見度**和**類別**是在 android 5.0 中引進，而且在舊版 android 中無法使用。 從 Android 8.0 開始，[通知通道](#notif-chan)會用來控制向使用者呈現通知的方式。

### <a name="expanded-layouts"></a>擴充的版面配置

從 Android 4.1 開始，您可以使用擴充的版面配置樣式來設定通知，讓使用者展開通知的高度，以查看更多內容。 例如，下列範例說明已展開的版面配置通知模式：

![已簽訂合約通知](local-notifications-images/07-contracted-notification.png)

當此通知展開時，會顯示整個訊息：

![展開的通知](local-notifications-images/08-expanded-notification.png)

Android 針對單一事件通知支援三種擴充的版面配置樣式：

- 在「已合約」模式中的***Big Text*** &ndash; 會顯示訊息第一行的摘錄，後面接著兩個句號。 在展開模式中，會顯示整個訊息（如上述範例所示）。

- ***收件***匣 &ndash; 在合約模式中，會顯示新訊息的數目。 在 [擴充] 模式中，會顯示第一封電子郵件訊息，或收件匣中的郵寄清單。

- ***影像***&ndash; 在簽約模式中，只會顯示郵件內文。 在展開模式中，會顯示文字和影像。

[除了基本通知](#beyond-the-basic-notification)（本文稍後）說明如何建立*大文字*、*收件*匣和*影像*通知。

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>通知通道

從 Android 8.0 （Oreo）開始，您可以使用 [*通知通道*] 功能，針對您要顯示的每一種通知類型，建立使用者可自訂的通道。 通知通道可讓您將通知分組，讓張貼至通道的所有通知呈現相同的行為。 例如，您可能會有一個通知通道，適用于需要立即注意的通知，以及用於告知性訊息的個別「安靜」通道。

隨 Android Oreo 安裝的**YouTube**應用程式會列出兩個通知類別：**下載通知**和**一般通知**：

[Android Oreo 中 YouTube 的 ![通知畫面](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

每一個類別都會對應至一個通知通道。 YouTube 應用程式會執行**下載通知**通道和**一般通知**通道。 使用者可以按一下 [**下載通知**]，這會顯示應用程式下載通知通道的 [設定] 畫面：

[![下載 YouTube 應用程式的通知畫面](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

在此畫面中，使用者可以執行下列動作來修改**下載**通知通道的行為：

- 將 [重要性] 層級設為 [**緊急**]、[**高**]、[**中**] 或 [**低**]，以設定音效和視覺中斷的層級。

- 開啟或關閉通知點。

- 開啟或關閉閃爍的燈。

- 在鎖定畫面上顯示或隱藏通知。

- 覆寫 [**請勿打擾**] 設定。

**一般通知**通道具有類似的設定：

[![YouTube 應用程式的一般通知畫面](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

請注意，您無法完全控制通知通道與使用者的互動方式 &ndash; 使用者可以修改裝置上任何通知通道的設定，如上述螢幕擷取畫面所示。 不過，您可以設定預設值（如下所述）。 如下列範例所示，新的通知通道功能讓您可以讓使用者更精細地控制不同類型的通知。

## <a name="notification-creation"></a>建立通知

若要在 Android 中建立通知，您可以使用 NotificationCompat[中的.](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) [Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder)類別。 此類別可讓您在較舊版本的 Android 上建立和發佈通知。
也會討論 `NotificationCompat.Builder`。

`NotificationCompat.Builder` 提供在通知中設定各種選項的方法，例如：

- 內容，包括標題、郵件內文和通知圖示。

- 通知的樣式，例如「*大文字*」、「*收件*匣」或「*影像*樣式」。

- 通知的優先順序： [最低]、[低]、[預設]、[高] 或 [最大值]。 在 Android 8.0 和更新版本上，優先順序是透過[_通知通道_](#notification-channels)來設定。

- 鎖定畫面上的通知可見度： [公用]、[私人] 或 [秘密]。

- 分類中繼資料，可協助 Android 分類和篩選通知。

- 選擇性的意圖，表示在點擊通知時要啟動的活動。

- 通知將發佈于的通知通道識別碼（Android 8.0 和更新版本）。

在建立器中設定這些選項之後，您會產生包含設定的通知物件。 若要發佈通知，請將此通知物件傳遞給*通知管理員*。 Android 提供[NotificationManager](xref:Android.App.NotificationManager)類別，負責發佈通知並向使用者顯示。 您可以從任何內容（例如活動或服務）取得此類別的參考。

### <a name="creating-a-notification-channel"></a>建立通知通道

在 Android 8.0 上執行的應用程式必須為其通知建立通知通道。 通知通道需要下列三項資訊：

- 識別碼字串，對於將識別通道的封裝而言是唯一的。
- 將向使用者顯示的通道名稱。  名稱必須介於1到40個字元之間。
- 通道的重要性。

應用程式必須檢查其正在執行的 Android 版本。
執行 Android 8.0 以前版本的裝置不應建立通知通道。 下列方法是如何在活動中建立通知通道的其中一個範例：

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

每次建立活動時，都應該建立通知通道。 針對 `CreateNotificationChannel` 方法，應該在活動的 `OnCreate` 方法中呼叫它。

### <a name="creating-and-publishing-a-notification"></a>建立和發佈通知

若要在 Android 中產生通知，請遵循下列步驟：

1. 執行個體化 `NotificationCompat.Builder` 物件。

2. 呼叫 `NotificationCompat.Builder` 物件上的各種方法來設定通知選項。

3. 呼叫 `NotificationCompat.Builder` 物件的[Build](xref:Android.App.Notification.Builder.Build)方法，以具現化通知物件。

4. 呼叫通知管理員的 [[通知](xref:Android.App.NotificationManager.Notify*)] 方法來發佈通知。

針對每個通知，您至少必須提供下列資訊：

- 小型圖示（24x24 的 dp 大小）

- 簡短標題

- 通知的文字

下列程式碼範例說明如何使用 `NotificationCompat.Builder` 來產生基本通知。 請注意，`NotificationCompat.Builder` 方法支援[方法連結](https://en.wikipedia.org/wiki/Method_chaining);也就是說，每個方法都會傳回 builder 物件，因此您可以使用最後一個方法呼叫的結果來叫用下一個方法呼叫：

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

在此範例中，會具現化稱為 `builder` 的新 `NotificationCompat.Builder` 物件，以及要使用之通知通道的識別碼。 系統會設定通知的標題和文字，並從 [資源]/[可**繪製/ic_notification .png**] 載入通知圖示。 呼叫通知產生器的 `Build` 方法會使用這些設定來建立通知物件。 下一步是呼叫通知管理員的 `Notify` 方法。 若要尋找通知管理員，您可以呼叫 `GetSystemService`，如上所示。

`Notify` 方法接受兩個參數：通知識別碼和通知物件。 通知識別碼是唯一的整數，可識別您應用程式的通知。 在此範例中，通知識別碼設定為零（0）;不過，在實際執行的應用程式中，您會想要為每個通知提供唯一識別碼。 在 `Notify` 的呼叫中重複使用先前的識別碼值，會導致最後一個通知遭到覆寫。

當此程式碼在 Android 5.0 裝置上執行時，它會產生如下列範例所示的通知：

![範例程式碼的通知結果](local-notifications-images/09-hello-world.png)

通知圖示會顯示在通知的左側 &ndash; 此圓形 &ldquo;i&rdquo; 具有 Alpha 色板，讓 Android 可以在其後方繪製灰色的圓形背景。 您也可以提供沒有 Alpha 色板的圖示。 若要將相片影像顯示為圖示，請參閱本主題稍後的[大型圖示格式](#large-icon-format)。

時間戳記會自動設定，但您可以藉由呼叫通知產生器的[SetWhen](xref:Android.App.Notification.Builder.SetWhen*)方法來覆寫此設定。 例如，下列程式碼範例會將時間戳記設定為目前時間：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>啟用音效和震動

如果您想要讓通知也播放音效，您可以呼叫 notification builder 的[SetDefaults](xref:Android.App.Notification.Builder.SetDefaults*)方法，並傳入 `NotificationDefaults.Sound` 旗標：

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

此 `SetDefaults` 的呼叫會導致裝置在發佈通知時播放音效。 如果您想要讓裝置震動，而不是播放音效，您可以將 `NotificationDefaults.Vibrate` 傳遞至 `SetDefaults.` 若要讓裝置播放音效並震動裝置，您可以將這兩個旗標傳遞至 `SetDefaults`：

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果您啟用音效而不指定播放音效，Android 會使用預設的系統通知音效。 不過，您可以藉由呼叫通知產生器的[SetSound](xref:Android.App.Notification.Builder.SetSound*)方法，來變更將播放的音效。 例如，若要使用通知來播放警示音效（而不是預設的通知音效），您可以從[RingtoneManager](xref:Android.Media.RingtoneManager)取得警示音效的 URI，並將它傳遞給 `SetSound`：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，您可以針對您的通知使用系統預設的鈴聲音效：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

建立通知物件之後，您可以在通知物件上設定通知屬性（而不是透過 `NotificationCompat.Builder` 方法，事先加以設定）。 例如，您可以直接修改通知的 [[預設值](xref:Android.App.Notification.Defaults)] 屬性的位旗標，而不是呼叫 `SetDefaults` 方法來啟用通知的震動。

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

此範例會在發佈通知時，讓裝置震動。

### <a name="updating-a-notification"></a>更新通知

如果您想要在發佈通知之後更新其內容，您可以重複使用現有的 `NotificationCompat.Builder` 物件來建立新的通知物件，並使用最後一個通知的識別碼發佈此通知。 例如：

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此範例中，現有的 `NotificationCompat.Builder` 物件是用來建立具有不同標題和訊息的新通知物件。
新的通知物件是使用先前通知的識別碼發佈，而這會更新先前發佈之通知的內容：

![已更新通知](local-notifications-images/12-updated-notification.png)

先前通知的主體會重複使用 &ndash; 只有標題和通知的文字會在通知顯示于通知選單時變更。 標題文字會從「範例通知」變更為「已更新通知」，而郵件內文會從「Hello World！」變更為 這是我的第一個通知！」 [變更為此訊息]。

通知會保持可見，直到發生三件事之一為止：

- 使用者關閉通知（或點擊 [*全部清除*]）。

- 應用程式會呼叫 `NotificationManager.Cancel`，傳入通知發行時所指派的唯一通知識別碼。

- 應用程式會呼叫 `NotificationManager.CancelAll`。

如需有關更新 Android 通知的詳細資訊，請參閱[修改通知](https://developer.android.com/training/notify-user/managing.html#Updating)。

### <a name="starting-an-activity-from-a-notification"></a>從通知啟動活動

在 Android 中，與*動作*相關聯的通知通常會在使用者按下通知時啟動的活動 &ndash;。 此活動可以位於另一個應用程式中，甚至是另一個工作。 若要將動作新增至通知，請建立[PendingIntent](xref:Android.App.PendingIntent)物件，並將 `PendingIntent` 與通知相關聯。 「`PendingIntent`」是一種特殊的意圖，可讓收件者應用程式以傳送應用程式的許可權執行預先定義的程式碼片段。 當使用者按下通知時，Android 會啟動 `PendingIntent`所指定的活動。

下列程式碼片段說明如何使用會啟動原始應用程式活動的 `PendingIntent` 來建立通知，`MainActivity`：

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

這段程式碼與上一節中的通知碼非常類似，不同之處在于 `PendingIntent` 會新增至通知物件。 在此範例中，`PendingIntent` 會在傳遞至通知產生器的[SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*)方法之前，與原始應用程式的活動產生關聯。 `PendingIntentFlags.OneShot` 旗標會傳遞至 `PendingIntent.GetActivity` 方法，因此 `PendingIntent` 只能使用一次。 當此程式碼執行時，會顯示下列通知：

![第一個動作通知](local-notifications-images/10-first-action-notification.png)

點擊此通知會讓使用者回到原始活動。

在生產應用程式中，當使用者按下通知活動中的 [**上一頁**] 按鈕時，您的應用程式必須處理*後端堆疊*（如果您不熟悉 Android 工作和後端堆疊，請參閱工作[和後端堆疊](https://developer.android.com/guide/components/tasks-and-back-stack.html)）。
在大部分情況下，向外流覽通知活動應會將使用者移出應用程式，並返回主畫面。 若要管理後端堆疊，您的應用程式會使用[TaskStackBuilder](xref:Android.App.TaskStackBuilder)類別來建立具有後端堆疊的 `PendingIntent`。

另一個實際的考慮是，原始活動可能需要將資料傳送至通知活動。 例如，通知可能表示已到達文字訊息，而通知活動（訊息流覽畫面）需要訊息的識別碼，才能向使用者顯示訊息。 建立 `PendingIntent` 的活動可以使用[PutExtra](xref:Android.Content.Intent.PutExtra*)方法，將資料（例如字串）新增至意圖，以便將此資料傳遞至通知活動。

下列程式碼範例說明如何使用 `TaskStackBuilder` 來管理後端堆疊，並包含如何將單一訊息字串傳送至稱為 `SecondActivity`之通知活動的範例：

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

在此程式碼範例中，應用程式是由兩個活動所組成： `MainActivity` （其中包含上述通知程式碼），而 `SecondActivity`則是使用者在點擊通知之後所看到的畫面。 執行此程式碼時，會顯示簡單的通知（類似先前的範例）。 點擊通知會將使用者帶到 [`SecondActivity`] 畫面：

![第二個活動螢幕擷取畫面](local-notifications-images/11-second-activity.png)

字串訊息（傳遞至意圖的 `PutExtra` 方法）會透過這行程式碼在 `SecondActivity` 中抓取：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

這個抓取的訊息「MainActivity！」中的問候語會顯示在 [`SecondActivity`] 畫面中，如上述螢幕擷取畫面所示。 當使用者在 `SecondActivity`中按 [**上一步**] 按鈕時，流覽會從應用程式移至應用程式啟動前的畫面。

如需建立擱置意圖的詳細資訊，請參閱[PendingIntent](xref:Android.App.PendingIntent)。

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>超出基本通知

通知會預設為 Android 中的簡單基底版面配置格式，但您可以藉由提出額外的 `NotificationCompat.Builder` 方法呼叫來增強此基本格式。 在本節中，您將瞭解如何將大型相片圖示新增至您的通知，您會看到如何建立擴充配置通知的範例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大型圖示格式

Android 通知通常會顯示原始應用程式的圖示（在通知的左邊）。 不過，通知可以顯示影像或相片（*大型圖示*），而不是標準小圖示。 例如，訊息應用程式可能會顯示寄件者的相片，而不是應用程式圖示。

以下是基本 Android 5.0 通知的範例 &ndash; 它只會顯示小型應用程式圖示：

![範例一般通知](local-notifications-images/13-sample-notification.png)

以下是在修改它以顯示大型圖示 &ndash; 其使用從 Xamarin 程式碼的影像建立的圖示時的通知螢幕擷取畫面：

![範例大型圖示通知](local-notifications-images/14-large-icon-sample.png)

請注意，當以大型圖示格式呈現通知時，小型應用程式圖示會在大型圖示的右下角顯示為徽章。

若要在通知中使用影像做為大圖示，您可以呼叫通知產生器的[SetLargeIcon](xref:Android.App.Notification.Builder.SetLargeIcon*)方法，並傳入影像的點陣圖。 不同于 `SetSmallIcon`，`SetLargeIcon` 只接受點陣圖。 若要將影像檔案轉換成點陣圖，您可以使用[BitmapFactory](xref:Android.Graphics.BitmapFactory)類別。 例如：

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

此範例程式碼會開啟**資源/可繪製/monkey_icon**的影像檔案，並將其轉換成點陣圖，並將產生的點陣圖傳遞給 `NotificationCompat.Builder`。 通常，來源影像解析度會大於小圖示 &ndash;，但不會大到多。 太大的影像可能會造成不必要的調整大小作業，而可能會延遲張貼通知。

### <a name="big-text-style"></a>Big text 樣式

*Big Text*樣式是一個展開的版面配置範本，可用來在通知中顯示長訊息。 就像所有已展開的版面配置通知一樣，一開始會以精簡的呈現格式來顯示大文字通知：

![範例 Big Text 通知](local-notifications-images/15-big-text-notification.png)

使用此格式時，只會顯示訊息的摘錄，並以兩個週期結束。 當使用者向下拖曳通知時，它會展開以顯示整個通知訊息：

![擴充的 Big Text 通知](local-notifications-images/16-big-text-expanded.png)

這個展開的版面配置格式也包含通知底部的摘要文字。 *Big Text*通知的最大高度為 256 dp。

若要建立*Big Text*通知，您可以像之前一樣具現化 `NotificationCompat.Builder` 物件，然後將[BigTextStyle](xref:Android.App.Notification.BigTextStyle)物件具現化並新增至 `NotificationCompat.Builder` 物件。 範例如下：

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

在此範例中，郵件內文和摘要文字會先儲存在 `BigTextStyle` 物件（`textStyle`）中，然後才會傳遞至 `NotificationCompat.Builder.`

### <a name="image-style"></a>影像樣式

*影像*樣式（也稱為「*大圖片*」樣式）是展開的通知格式，可讓您用來在通知主體中顯示影像。 例如，螢幕擷取畫面應用程式或相片應用程式可以使用*影像*通知樣式，為使用者提供最後所捕獲影像的通知。 請注意，*影像*通知的最大高度為 256 Dp &ndash; Android 會在可用記憶體的限制內調整影像大小，使其符合此最大高度限制。

就像所有展開的版面配置通知一樣，*影像*通知會先以精簡格式顯示，以顯示隨附郵件內文的摘錄：

![Compact 影像通知未顯示任何影像](local-notifications-images/17-image-compact.png)

當使用者向下拖曳*影像*通知時，它會展開以顯示影像。 例如，以下是先前通知的擴充版本：

![展開的影像通知顯示影像](local-notifications-images/18-image-expanded.png)

請注意，當通知以精簡格式顯示時，會顯示通知文字（傳遞給通知產生器 `SetContentText` 方法的文字，如先前所示）。 不過，當通知展開以顯示影像時，它會在影像上方顯示摘要文字。

若要建立*影像*通知，您可以像之前一樣具現化 `NotificationCompat.Builder` 物件，然後建立[BigPictureStyle](xref:Android.App.Notification.BigPictureStyle)物件並將其插入 `NotificationCompat.Builder` 物件中。 例如：

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

就像 `NotificationCompat.Builder`的 `SetLargeIcon` 方法一樣，`BigPictureStyle` 的[BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*)方法需要您想要在通知主體中顯示之影像的點陣圖。 在此範例中，`BitmapFactory` 的[DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*)方法會讀取位於**Resources/繪製/x_bldg .png**的影像檔案，並將它轉換成點陣圖。

您也可以顯示未封裝為資源的影像。 例如，下列範例程式碼會從本機 SD 記憶卡載入影像，並將其顯示在*影像*通知中：

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

在此範例中，會載入位於 **/sdcard/Pictures/my-tshirt.jpg**的影像檔案，並將其大小調整為其原始大小的一半，然後轉換成點陣圖以用於通知中：

![通知中的 T 恤影像範例](local-notifications-images/19-tshirt-notification.png)

如果您事先不知道影像檔案的大小，最好將呼叫[包裝在例外](xref:Android.Graphics.BitmapFactory.DecodeFile*)狀況處理常式中，&ndash; 如果影像太大而無法調整大小，就可能擲回 `OutOfMemoryError` 例外狀況。

如需載入和解碼大型點陣圖影像的詳細資訊，請參閱[有效率地載入大型點陣圖](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently)。

### <a name="inbox-style"></a>收件匣樣式

*收件*匣格式是一種展開的版面配置範本，可在通知本文中顯示個別的文字行（例如電子郵件收件匣摘要）。 *收件*匣格式通知會先以精簡格式顯示：

![範例 compact 收件匣通知](local-notifications-images/20-inbox-compact.png)

當使用者向下拖曳通知時，它會展開以顯示電子郵件摘要，如下列螢幕擷取畫面所示：

![擴充的收件匣通知範例](local-notifications-images/21-inbox-expanded.png)

若要建立*收件*匣通知，您可以像之前一樣具現化 `NotificationCompat.Builder` 物件，並將[InboxStyle](xref:Android.App.Notification.InboxStyle)物件新增至 `NotificationCompat.Builder`。 範例如下：

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

若要將新的文字行新增至通知主體，請呼叫 `InboxStyle` 物件的[Addline](xref:Android.App.Notification.InboxStyle.AddLine*)方法（*收件*匣通知的最大高度為 256 dp）。 請注意，與*大型文字*樣式不同的是，*收件*匣樣式在通知主體中支援個別的文字行。

您也可以使用 [*收件*匣] 樣式，針對需要以展開的格式顯示個別文字行的任何通知。 例如，*收件*匣通知樣式可用來將多個擱置中的通知結合成摘要通知 &ndash; 您可以用新的通知內容行更新單一*收件*匣樣式通知（請參閱上方的[更新通知](#updating-a-notification)），而不是產生新的連續串流，而不是產生最類似的通知。

## <a name="configuring-metadata"></a>設定中繼資料

`NotificationCompat.Builder` 包含您可以呼叫的方法，以設定通知的相關中繼資料，例如優先順序、可見度和分類。 Android 會使用此資訊 &mdash; 和使用者喜好設定 &mdash; 來決定如何和何時顯示通知。

### <a name="priority-settings"></a>優先順序設定

在 Android 7.1 和更低版本上執行的應用程式，需要直接在通知本身設定優先順序。 通知的優先順序設定會決定發佈通知時的兩個結果：

- 通知會出現在與其他通知相關的位置。
    例如，高優先順序的通知會顯示在 [通知] 選單中較低優先順序的通知上方，不論每個通知發佈的時間為何。

- 通知是否以標題通知格式顯示（Android 5.0 和更新版本）。 只有 [*高*] 和 [*最高*優先順序] 通知會顯示為 [標題通知]。

Xamarin 會定義下列設定通知優先順序的列舉：

- `NotificationPriority.Max` &ndash; 會將使用者警示到緊急或嚴重的情況（例如，撥入電話、輪流指示或緊急警示）。 在 Android 5.0 和更新版本的裝置上，最高優先順序的通知會以標題向上的格式顯示。

- `NotificationPriority.High` &ndash; 會通知使用者重要的事件（例如重要的電子郵件或即時聊天訊息的抵達）。 在 Android 5.0 和更新版本的裝置上，高優先順序的通知會以標題向上的格式顯示。

- `NotificationPriority.Default` &ndash; 會通知使用者有中等重要性層級的條件。

- `NotificationPriority.Low` &ndash; 使用者需要通知的非緊急資訊（例如軟體更新提醒或社交網路更新）。

- `NotificationPriority.Min` &ndash; 的背景資訊，使用者只會在查看通知時注意到（例如位置或天氣資訊）。

若要設定通知的優先順序，請呼叫 `NotificationCompat.Builder` 物件的[SetPriority](xref:Android.App.Notification.Builder.SetPriority*)方法，並傳入優先權層級。 例如：

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下列範例中，高優先順序的通知「重要訊息！」 出現在通知抽屜的頂端：

![範例高優先順序通知](local-notifications-images/22-hi-priority-drawer.png)

由於這是高優先順序的通知，因此也會在 Android 5.0 的使用者目前活動畫面上方顯示為外顯通知：

![範例標頭通知](local-notifications-images/23-heads-up-example.png)

在下一個範例中，低優先順序的「每日考慮」通知會顯示在較高優先權的電池等級通知底下：

![低優先順序的範例通知](local-notifications-images/24-lo-priority-drawer.png)

因為「考慮時間」通知是低優先順序的通知，所以 Android 不會將它顯示為列印頭格式。

> [!NOTE]
> 在 Android 8.0 和更新版本上，通知通道和使用者設定的優先順序會決定通知的優先順序。

### <a name="visibility-settings"></a>可見度設定

從 Android 5.0 開始，*可見度*設定可用來控制要在安全鎖定畫面上顯示多少通知內容。
Xamarin 會定義下列設定通知可見度的列舉：

- `NotificationVisibility.Public` &ndash; 通知的完整內容會顯示在安全鎖定畫面上。

- `NotificationVisibility.Private` &ndash; 只有重要資訊會顯示在安全鎖定畫面上（例如通知圖示和張貼的應用程式名稱），但會隱藏通知詳細資料的其餘部分。 所有通知都會預設為 `NotificationVisibility.Private`。

- `NotificationVisibility.Secret` &ndash; 安全鎖定畫面上不會顯示任何內容，甚至是通知圖示。 只有在使用者解除鎖定裝置之後，才能使用通知內容。

若要設定通知的可見度，應用程式會呼叫 `NotificationCompat.Builder` 物件的 `SetVisibility` 方法，並傳入可見度設定。 例如，`SetVisibility` 的這個呼叫會使通知 `Private`：

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

張貼 `Private` 通知時，只有應用程式的名稱和圖示會顯示在安全鎖定畫面上。 使用者會看到「解除鎖定您的裝置以查看此通知」，而不是通知訊息：

![解除鎖定您的裝置通知訊息](local-notifications-images/25-lockscreen-private.png)

在此範例中， **NotificationsLab**是原始應用程式的名稱。 只有當鎖定畫面安全（也就是透過 PIN、模式或密碼保護）時，才會顯示此編校版本的通知 &ndash; 如果鎖定螢幕不安全，則通知的完整內容可在鎖定畫面上取得。

### <a name="category-settings"></a>類別設定

從 Android 5.0 開始，預先定義的類別可用於排名和篩選通知。 Xamarin 會針對這些類別提供下列列舉：

- `Notification.CategoryCall` &ndash; 撥打電話。

- `Notification.CategoryMessage` &ndash; 傳入文字訊息。

- `Notification.CategoryAlarm` &ndash; 警示條件或計時器到期日。

- `Notification.CategoryEmail` &ndash; 傳入的電子郵件訊息。

- `Notification.CategoryEvent` &ndash; 行事曆事件。

- `Notification.CategoryPromo` &ndash; 促銷訊息或廣告。

- `Notification.CategoryProgress` &ndash; 背景作業的進度。

- `Notification.CategorySocial` &ndash; 社交網路更新。

- `Notification.CategoryError` 背景作業或驗證進程 &ndash; 失敗。

- `Notification.CategoryTransport` &ndash; 媒體播放更新。

- `Notification.CategorySystem` &ndash; 保留供系統使用（系統或裝置狀態）。

- `Notification.CategoryService` &ndash; 表示背景服務正在執行。

- `Notification.CategoryRecommendation` &ndash; 與目前正在執行之應用程式相關的建議訊息。

- `Notification.CategoryStatus` 裝置的 &ndash; 資訊。

當通知已排序時，通知的優先順序會優先于其類別設定。 例如，高優先順序的通知將會顯示為標題，即使它屬於 `Promo` 類別也一樣。 若要設定通知的類別，您可以呼叫 `NotificationCompat.Builder` 物件的 `SetCategory` 方法，傳入分類設定。 例如：

```csharp
builder.SetCategory (Notification.CategoryCall);
```

[*請勿打擾*] 功能（Android 5.0 中的新功能）會根據類別篩選通知。 例如，[**設定**] 中的 [*不要打擾*] 畫面可讓使用者豁免通話和訊息的通知：

![不要打擾畫面參數](local-notifications-images/26-do-not-disturb.png)

當使用者設定「*請勿打擾*」封鎖電話以外的所有中斷（如上述螢幕擷取畫面所示）時，Android 允許在裝置處於「不*打擾*」模式時，顯示 [類別] 設定為 [`Notification.CategoryCall`] 的通知。 請注意，在 [*請勿打擾*] 模式中，不會封鎖 `Notification.CategoryAlarm` 通知。

[LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)範例會示範如何使用 `NotificationCompat.Builder` 從通知啟動第二個活動。 在[使用 Xamarin 中的本機通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)逐步解說中會說明此範例程式碼。

### <a name="notification-styles"></a>通知樣式

若要使用 `NotificationCompat.Builder`建立*大型文字*、*影像*或*收件*匣樣式通知，您的應用程式必須使用這些樣式的相容性版本。 例如，若要使用*Big Text*樣式，請將 `NotificationCompat.BigTextstyle`具現化：

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同樣地，您的應用程式也可以分別使用 `NotificationCompat.InboxStyle` 和 `NotificationCompat.BigPictureStyle` 來取得*收件*匣和*影像*樣式。

### <a name="notification-priority-and-category"></a>通知優先順序和分類

`NotificationCompat.Builder` 支援 `SetPriority` 方法（從 Android 4.1 開始提供）。 不過，`NotificationCompat.Builder`*不*支援 `SetCategory` 方法，因為類別是 Android 5.0 中引進之新通知中繼資料系統的一部分。

若要支援較舊版本的 Android，其中 `SetCategory` 無法使用，您的程式碼可以在執行時間檢查 API 層級，以在 API 層級等於或大於 Android 5.0 時有條件地呼叫 `SetCategory` （API 層級21）：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此範例中，應用程式的**目標 Framework**設定為 android 5.0，**最低 android 版本**則設為**Android 4.1 （API 層級16）** 。 由於 `SetCategory` 在 API 層級21和更新版本中提供，因此此範例程式碼只會在可用時呼叫 `SetCategory` &ndash; 當 API 層級小於21時，就不會呼叫 `SetCategory`。

### <a name="lock-screen-visibility"></a>鎖定畫面可見度

因為 Android 在 Android 5.0 （API 層級21）之前不支援鎖定螢幕通知，所以 `NotificationCompat.Builder` 不支援 `SetVisibility` 方法。 如上述 `SetCategory`所述，您的程式碼可以在執行時間檢查 API 層級，並且只有在可用時才呼叫 `SetVisiblity`：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>摘要

本文說明如何在 Android 中建立本機通知。 文中說明了通知的剖析，說明如何使用 `NotificationCompat.Builder` 來建立通知、如何以大型圖示、*大文字*、*影像*和*收件*匣格式來設定通知的樣式、如何設定通知中繼資料設定（例如優先順序、可見度和分類），以及如何從通知啟動活動。 本文也說明了這些通知設定如何搭配新的標題、鎖定畫面，以及 Android 5.0 中引進的「*請勿打擾*」功能使用。 最後，您已瞭解如何使用 `NotificationCompat.Builder` 來維護與舊版 Android 的通知相容性。

如需設計 Android 通知的相關指導方針，請參閱[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。

## <a name="related-links"></a>相關連結

- [NotificationsLab （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Android 中的本機通知逐步解說](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知使用者](https://developer.android.com/training/notify-user/index.html)
- [發出](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
