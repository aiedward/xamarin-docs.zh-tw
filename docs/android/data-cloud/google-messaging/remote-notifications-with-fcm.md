---
title: 遠端通知使用 Firebase 雲端傳訊
description: 本逐步解說提供如何使用 Firebase 雲端通訊實作遠端通知 （也稱為 「 推播通知 」） 的逐步說明，Xamarin.Android 應用程式中。 它說明如何實作所需的通訊使用 Firebase 雲端通訊 (FCM)，提供如何設定存取 FCM，Android 資訊清單的範例，並示範使用 Firebase 下游訊息處理的各種類別主控台。
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: de0e2c5ff10de9136c4cb5987c80ce22c7b18c4d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105541"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>遠端通知使用 Firebase 雲端傳訊

_本逐步解說提供如何使用 Firebase 雲端通訊實作遠端通知 （也稱為 「 推播通知 」） 的逐步說明，Xamarin.Android 應用程式中。它說明如何實作所需的通訊使用 Firebase 雲端通訊 (FCM)，提供如何設定存取 FCM，Android 資訊清單的範例，並示範使用 Firebase 下游訊息處理的各種類別主控台。_

## <a name="fcm-notifications-overview"></a>FCM 通知概觀

在本逐步解說中，基本的應用程式呼叫**FCMClient**將建立來說明必要的 FCM 訊息。 **FCMClient**會檢查 Google Play 服務的狀態、 從 FCM 收到註冊權杖，會顯示您從 Firebase 主控台中，傳送的遠端通知和訂閱主題訊息：

[![應用程式的範例螢幕擷取畫面](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

將會探索下列主題區：

1.  背景通知

2.  主題的訊息

3.  前景通知

在這個逐步解說中，您會以累加方式將功能新增至**FCMClient** ，裝置或模擬器，以了解它如何與 FCM 互動上加以執行。 您將使用記錄來見證的 FCM 伺服器的即時應用程式交易，並從您輸入 Firebase 主控台通知 GUI 的 FCM 訊息產生通知的方式，您會看到。

## <a name="requirements"></a>需求


所以會有幫助您熟悉[不同類型的訊息](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages)可傳送的 Firebase 雲端通訊。 用戶端應用程式會接收和處理訊息的方式，將會決定訊息的承載。

您可以繼續進行本逐步解說之前，您必須取得所需的認證，以使用 Google 的 FCM 伺服器;會說明此程序[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm)。
特別是，您必須下載**google-services.json**来使用本逐步解說的範例程式碼檔案。 如果您有您尚未建立專案在 Firebase 主控台 (或如果尚未下載**google-services.json**檔案)，請參閱[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

若要執行範例應用程式，您必須測試的 Android 裝置或使用 Firebase 相容的模擬器。 Firebase 雲端通訊支援執行 Android 4.0 或更新版本中，用戶端，而且這些裝置也必須安裝 Google Play 商店應用程式 （Google Play Services 9.2.1 或更新版本）。 如果您還沒有安裝在裝置上的 Google Play 商店應用程式，請瀏覽[Google Play](https://support.google.com/googleplay)網站下載並安裝它。 或者，您可以使用 Android SDK 模擬器使用 Google Play 服務安裝而不是測試裝置 （您不必安裝 Google Play 商店，如果您使用 Android SDK 模擬器）。

## <a name="start-an-app-project"></a>啟動應用程式專案

若要開始，建立新的空白 Xamarin.Android 專案名**FCMClient**。 如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。
建立新的應用程式之後下, 一個步驟是設定封裝名稱，並安裝數個將用於使用 fcm 後通訊的 NuGet 套件。

### <a name="set-the-package-name"></a>設定封裝名稱

在  [Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)，指定 FCM 功能的應用程式的套件名稱。 此封裝名稱也會成為[*應用程式識別碼*](./firebase-cloud-messaging.md#fcm-in-action-app-id)相關聯[API 金鑰](firebase-cloud-messaging.md#fcm-in-action-api-key)。 設定應用程式使用此封裝名稱：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  開啟的內容**FCMClient**專案。

2.  在  **Android 資訊清單**頁面上，設定封裝名稱。

在下列範例中，套件名稱設定為`com.xamarin.fcmexample`:

[![設定封裝名稱](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

雖然您正在**Android 資訊清單**，也請確定`Internet`權限已啟用。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  開啟的內容**FCMClient**專案。

2.  在  **Android 應用程式**頁面上，設定封裝名稱。

在下列範例中，套件名稱設定為`com.xamarin.fcmexample`:

[![設定封裝名稱](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

而您要更新**Android 資訊清單**，也請務必`INTERNET`權限已啟用 (在**必要權限**)。

-----

> [!IMPORTANT]
> 用戶端應用程式將無法從 FCM 收到註冊權杖，如果此套件名稱並不會*完全*符合輸入的 Firebase 主控台中的封裝名稱。

### <a name="add-the-xamarin-google-play-services-base-package"></a>新增 Xamarin Google Play 服務基底套件

Firebase 雲端通訊取決於 Google Play 服務，因為[Xamarin Google Play 服務-基底](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/)NuGet 套件必須新增至 Xamarin.Android 專案。 您需要新版 29.0.0.2 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 套件...**.

2.  按一下 **瀏覽**索引標籤，然後搜尋**Xamarin.GooglePlayServices.Base**。

3.  安裝此套件到**FCMClient**專案：

    [![安裝 Google Play 服務基底](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  在 Visual Studio for Mac，請以滑鼠右鍵按一下**封裝 > 新增套件...**.

2.  搜尋**Xamarin.GooglePlayServices.Base**。

3.  安裝此套件到**FCMClient**專案：

    [![安裝 Google Play 服務基底](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果您的 NuGet 的安裝期間收到錯誤，請關閉**FCMClient**專案、 開啟它，然後重試 NuGet 安裝。

當您安裝**Xamarin.GooglePlayServices.Base**，也會安裝所有必要的相依性。 編輯**MainActivity.cs**並新增下列`using`陳述式：

```csharp
using Android.Gms.Common;
```

此陳述式會產生`GoogleApiAvailability`類別內**Xamarin.GooglePlayServices.Base**能夠**FCMClient**程式碼。
`GoogleApiAvailability` 用來檢查 Google Play 服務存在。

### <a name="add-the-xamarin-firebase-messaging-package"></a>將 Xamarin Firebase 通訊的封裝

若要從 FCM，接收訊息[Xamarin Firebase-傳訊](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/)NuGet 套件必須新增至應用程式專案。 沒有這個套件中，Android 應用程式從 FCM 伺服器無法接收訊息。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 套件...**.

2. 搜尋**Xamarin.Firebase.Messaging**。

3.  安裝此套件到**FCMClient**專案：

    [![安裝 Xamarin Firebase 傳訊](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  在 Visual Studio for Mac，請以滑鼠右鍵按一下**封裝 > 新增套件...**.

2.  請檢查**顯示發行前版本套件**並搜尋**Xamarin.Firebase.Messaging**。

3.  安裝此套件到**FCMClient**專案：

    [![安裝 Xamarin Firebase 傳訊](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

當您安裝**Xamarin.Firebase.Messaging**，也會安裝所有必要的相依性。

接著，編輯**MainActivity.cs**並新增下列`using`陳述式：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前兩個陳述式進行中的型別**Xamarin.Firebase.Messaging** NuGet 套件可**FCMClient**程式碼。 **Android.Util**新增將用來觀察與 FM 交易的記錄功能。

### <a name="add-googleplayservices-json"></a>新增 Google Services JSON 檔案

下一個步驟是新增**google-services.json**至您的專案根目錄的檔案：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  複製**google-services.json**到專案資料夾。

2.  新增**google-services.json**應用程式專案 (按一下**顯示所有檔案**中**方案總管 中**，以滑鼠右鍵按一下**google-services.json**，然後選取**加入至專案**)。

3.  選取 [ **google-services.json**中**方案總管] 中**視窗。

4.  在 **屬性**窗格中，將**建置動作**來**GoogleServicesJson**:

    [![將建置動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > 如果**GoogleServicesJson**建置動作不會顯示，儲存並關閉方案，然後重新開啟它。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  複製**google-services.json**到專案資料夾。

2.  新增**google-services.json**應用程式專案。

3.  以滑鼠右鍵按一下**google-services.json**。

4.  設定**建置動作**要**GoogleServicesJson**:

    [![將建置動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

當**google-services.json**加入至專案 (而**GoogleServicesJson**建置動作設定)，建置程序會擷取用戶端識別碼和[API 金鑰](./firebase-cloud-messaging.md#fcm-in-action-api-key)，然後將這些認證加入至合併/產生**AndroidManifest.xml** ，位於**obj/Debug/android/AndroidManifest.xml**。 此合併處理會自動加入任何權限和其他 FCM 項目所需的 FCM 伺服器的連線。


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>檢查 Google Play 服務，並建立通知通道

Google 建議的 Android 應用程式檢查是否存在的 Google Play Services APK 之前存取 Google Play 服務功能 (如需詳細資訊，請參閱 <<c0> [ 檢查 Google Play 服務](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play))。

會先建立應用程式的 ui 的初始版面配置。 編輯**Resources/layout/Main.axml**並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

這`TextView`會用來顯示訊息，指出是否已安裝 Google Play 服務。 變更儲存到**Main.axml**。


編輯**MainActivity.cs**並加入下列的執行個體變數，以`MainActivity`類別：

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

變數`CHANNEL_ID`並`NOTIFICATION_ID`將使用在方法中[ `CreateNotificationChannel` ](#create-notification-channel-code) ，將會新增至`MainActivity`以便稍後在本逐步解說。


在下列範例中，`OnCreate`方法會驗證應用程式會嘗試使用 FCM 服務之前，是使用 Google Play 服務。
將下列方法加入`MainActivity`類別：

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

此程式碼會檢查裝置，以檢查是否已安裝 Google Play Services APK。 如果未安裝，訊息會顯示在`TextBox`，指示使用者從 Google Play 商店下載 APK （或啟用裝置的系統設定 中）。

<a name="create-notification-channel-code"></a>Android 8.0 （API 層級 26） 或更高版本執行的應用程式必須建立[_通知通道_](~/android/app-fundamentals/notifications/local-notifications.md)發佈其通知。  將下列方法加入`MainActivity`類別會建立通知通道 （如有必要）：

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

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

以下列程式碼取代 `OnCreate` 方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` 在結尾處呼叫`OnCreate`，讓 Google Play 服務可讓您檢查每個執行時間應用程式會啟動。 此方法`CreateNotificationChannel`被呼叫，以確保通知通道存在為執行 Android 8 的裝置或更高版本。 如果您的應用程式`OnResume`方法，它應該呼叫`IsPlayServicesAvailable`從`OnResume`以及。 完全重建，並執行應用程式。 如果所有的設定正確，您應該會看到一個畫面，看起來像下列螢幕擷取畫面：

[![應用程式可讓您指出 Google Play 服務可用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果您未獲得此結果，請確認您的裝置上已安裝 Google Play Services APK (如需詳細資訊，請參閱 <<c0> [ 設定 Google Play 服務](https://developers.google.com/android/guides/setup))。
也請確認您已新增**Xamarin.Google.Play.Services.Base**封裝到您**FCMClient**專案稍早所述。


## <a name="add-the-instance-id-receiver"></a>新增執行個體識別碼接收者

下一個步驟是新增一項服務，擴充`FirebaseInstanceIdService`處理的建立、 輪替和更新[Firebase 註冊權杖](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)。 `FirebaseInstanceIdService`服務是必要的 FCM，若要能夠將訊息傳送至裝置。 當`FirebaseInstanceIdService`服務新增至用戶端應用程式中，應用程式會自動收到 FCM 訊息和其顯示為通知，每當應用程式在背景時。

### <a name="declare-the-receiver-in-the-android-manifest"></a>宣告在 Android 資訊清單接收器

編輯**AndroidManifest.xml** ，並將下列`<receiver>`項目`<application>`區段：

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

這段 XML 會執行下列動作：

-   宣告`FirebaseInstanceIdReceiver`實作，可提供[唯一識別項](https://developers.google.com/instance-id/)每個應用程式執行個體。 此收件者也會驗證並授權動作。

-   宣告的內部`FirebaseInstanceIdInternalReceiver`實作，用來安全地啟動服務。

-   [應用程式識別碼](./firebase-cloud-messaging.md#fcm-in-action-app-id)會儲存在**google-services.json**檔案有[加入至專案](#add-googleplayservices-json)。 Xamarin.Android Firebase 繫結將會取代語彙基元`${applicationId}`的應用程式識別碼; 沒有額外的程式碼所用戶端應用程式提供應用程式識別碼。

`FirebaseInstanceIdReceiver`是`WakefulBroadcastReceiver`接收`FirebaseInstanceId`並`FirebaseMessaging`事件，並將其類別衍生自`FirebaseInstanceIdService`。

### <a name="implement-the-firebase-instance-id-service"></a>實作 Firebase 執行個體識別碼服務

向 FCM 註冊應用程式的工作由自訂`FirebaseInstanceIdService`您提供的服務。
`FirebaseInstanceIdService` 執行下列步驟：

1.  會使用[執行個體識別碼 API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID)產生安全性權杖，授權用戶端應用程式存取 FCM 和應用程式伺服器。 應用程式，取得上一步[註冊權杖](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)從 FCM。

2.  如果應用程式伺服器時需要它，會將轉送至應用程式伺服器的註冊權杖。

加入新的檔名**MyFirebaseIIDService.cs**和它的範本程式碼取代為下列：

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

此服務會實作`OnTokenRefresh`一開始建立或變更註冊權杖時，會叫用的方法。 當`OnTokenRefresh`執行時，它會擷取最新的權杖從`FirebaseInstanceId.Instance.Token`屬性 （這會由 FCM 以非同步方式更新）。 在此範例中，如此就可以檢視 [輸出] 視窗中，會記錄重新整理的權杖：

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不常叫用： 它用來更新在下列情況下的權杖：

-   當應用程式已安裝或解除安裝。

-   當使用者刪除應用程式資料。

-   當應用程式清除執行個體識別碼。

-   當安全性權杖的已遭入侵。

根據 Google[執行個體識別碼](https://developers.google.com/instance-id/guides/android-implementation)文件，FCM 執行個體識別碼服務會要求應用程式重新整理其權杖定期 （一般而言，每 6 個月）。

`OnTokenRefresh` 也會呼叫`SendRegistrationToAppServer`關聯使用者的註冊權杖與伺服器端有更多的帳戶 （如果有的話），由維護應用程式：

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

因為此實作取決於應用程式伺服器的設計，在此範例中提供空白的方法主體。 如果您的應用程式伺服器需要 FCM 註冊資訊，請修改`SendRegistrationToAppServer`維護您的應用程式的任何伺服器端帳戶相關聯使用者的 FCM 執行個體識別碼權杖。 （請注意，語彙基元，用戶端應用程式。）

當權杖傳送至應用程式伺服器，`SendRegistrationToAppServer`應該維護的布林值，指出是否將權杖傳送至伺服器。 如果為 false，這個布林值`SendRegistrationToAppServer`會將權杖傳送至應用程式伺服器&ndash;語彙基元，否則為已傳送前一次呼叫中的應用程式伺服器。 在某些情況下 (這類`FCMClient`範例)，應用程式伺服器不需要的語彙基元; 因此，此方法不需要這個範例。

## <a name="implement-client-app-code"></a>實作用戶端應用程式程式碼

既然接收器服務準備就緒之後，您就可以寫入用戶端應用程式程式碼充分利用這些服務。 在下列章節中，按鈕會加入 ui 好記錄註冊權杖 (也稱為*執行個體識別碼權杖*)，以及更多的程式碼加入至`MainActivity`若要檢視`Intent`時從啟動應用程式資訊通知：

[![記錄檔的語彙基元 按鈕新增至應用程式畫面](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>記錄檔的語彙基元

在此步驟中新增的程式碼僅供示範之用&ndash;實際執行用戶端應用程式會有不需要記錄註冊權杖。 編輯**Resources/layout/Main.axml**並新增下列`Button`宣告之後立即`TextView`項目：

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

請在 `MainActivity.OnCreate` 方法的結尾新增下列程式碼：

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

這段程式碼會記錄目前的語彙基元至 [輸出] 視窗時**記錄檔語彙基元**點選按鈕時。

### <a name="handle-notification-intents"></a>處理通知的對應方式

當使用者點選通知，從發出**FCMClient**伴隨該通知的任何資料，訊息會提供在`Intent`額外項目。 編輯**MainActivity.cs**並將下列程式碼加入至頂端`OnCreate`方法 (呼叫前面`IsPlayServicesAvailable`):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

應用程式啟動器`Intent`當使用者點選通知訊息，因此這段程式碼會記錄任何隨附資料時會引發`Intent`到輸出視窗。 如果不同的話`Intent`必須引發`click_action`通知訊息的欄位必須設為可`Intent`(啟動器`Intent`時不會使用`click_action`指定)。


## <a name="background-notifications"></a>背景通知

建置並執行**FCMClient**應用程式。 **記錄檔語彙基元**按鈕會顯示：

[![顯示記錄檔的語彙基元 按鈕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

點選**記錄檔語彙基元** 按鈕。 IDE 的 [輸出] 視窗中，應該會顯示訊息，如下所示：

[![在 [輸出] 視窗中顯示的執行個體識別碼權杖](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

長字串加**語彙基元**是您會將它貼到 Firebase 主控台中的執行個體識別碼權杖&ndash;選取，然後將此字串複製到剪貼簿。 如果看不到執行個體識別碼權杖，可將下行新增至頂端`OnCreate`方法以確認**google-services.json**正確剖析：

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id`記錄到 [輸出] 視窗的值應該符合`mobilesdk_app_id`值記錄在**google-services.json**。

### <a name="send-a-message"></a>傳送訊息

登入[Firebase 主控台](https://console.firebase.google.com)，選取您的專案，按一下**通知**，然後按一下**傳送您的第一個訊息**:

[![傳送您的第一個訊息 按鈕](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

在 **撰寫訊息**頁面上，輸入訊息文字，然後選取**單一裝置**。 從 IDE 的 [輸出] 視窗複製的執行個體識別碼權杖，並將它貼至**FCM 註冊權杖**Firebase 主控台中的欄位：

[![撰寫訊息對話方塊](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Android 裝置 （或模擬器上），請依序點選 Android 背景應用程式**概觀**按鈕，然後觸碰 首頁 螢幕。 裝置就緒時，請按一下**SEND MESSAGE**在 Firebase 主控台中：

[![傳送郵件 按鈕](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

當**檢閱訊息** 對話方塊隨即出現，請按一下**傳送**。
[通知] 圖示應該會出現在通知區域的裝置 （或模擬器）：

[![會顯示通知圖示](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

開啟 [通知] 圖示，以檢視訊息。 通知訊息應會有什麼輸入**訊息文字**Firebase 主控台中的欄位：

[![在裝置上顯示通知訊息](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

點選 [通知] 圖示以啟動**FCMClient**應用程式。 `Intent`傳送至的額外項目**FCMClient** IDE 的 [輸出] 視窗中所列：

[![從索引鍵、 訊息識別碼和摺疊機碼的意圖的額外項目清單](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此範例中，**從**機碼設為應用程式的 Firebase 專案編號 (在此範例中， `41590732`)，而**collapse_key**設定為它的套件名稱 (**com.xamarin.fcmexample**)。
如果您沒有收到一則訊息，請嘗試刪除**FCMClient**裝置 （或模擬器） 上的應用程式，然後重複上述步驟。


> [!NOTE]
> 如果您強制關閉應用程式、 FCM 將會停止傳遞通知。 Android 會防止不小心或不必要地啟動元件已停止的應用程式的背景服務廣播。 (如需有關此行為的詳細資訊，請參閱 <<c0> [ 啟動已停止的應用程式上的控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)。)基於這個理由，就必須手動解除安裝應用程式每次方法，您可以執行它，並停止偵錯工作階段從&ndash;這會強制 FCM，才能產生新的權杖，以便將繼續接收訊息。

### <a name="add-a-custom-default-notification-icon"></a>新增自訂的預設通知圖示

在上述範例中，[通知] 圖示設為應用程式圖示。 下列 XML 程式碼會設定自訂的預設圖示通知。 Android 會顯示所有的通知訊息，其中的通知圖示未明確設定此自訂的預設圖示。

若要新增自訂的預設通知圖示，新增您要的圖示**資源/drawable**目錄中，編輯**AndroidManifest.xml**，並插入下列`<meta-data>`項目`<application>`一節：

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此範例中，[通知] 圖示，位於**資源/drawable/ic\_stat\_ic\_notification.png**將用於自訂的預設通知圖示。 如果在未設定自訂的預設圖示**AndroidManifest.xml**且無圖示設定通知承載中，Android 會使用應用程式圖示為通知圖示 （如通知圖示上方螢幕擷取畫面所示）。

## <a name="handle-topic-messages"></a>處理主題的訊息

到目前為止所撰寫的程式碼會處理註冊權杖，並將遠端通知功能加入至應用程式。 下一步 的範例會將程式碼，它會接聽*主題訊息*並將它們轉送到使用者做為遠端的通知。 主題的訊息是 FCM 訊息傳送給訂閱特定主題的一或多個裝置。 如需有關主題的訊息的詳細資訊，請參閱[主題傳訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

### <a name="subscribe-to-a-topic"></a>訂閱主題

編輯**Resources/layout/Main.axml**並新增下列`Button`緊接先前的宣告`Button`項目：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

這個 XML 會新增**通知訂閱**版面配置 按鈕。
編輯**MainActivity.cs**並將下列程式碼新增至結尾`OnCreate`方法：

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

此程式碼會找出**通知訂閱**版面配置中的按鈕並將其 click 處理常式指派給呼叫程式碼`FirebaseMessaging.Instance.SubscribeToTopic`，在訂閱的主題中，傳遞_新聞_。 當使用者點選**Subscribe**  按鈕，應用程式訂閱_新聞_主題。 在下一節_新聞_將從 Firebase 主控台通知 GUI 傳送主題訊息。

### <a name="send-a-topic-message"></a>傳送主題訊息

解除安裝應用程式，請重建它，再執行一次。 按一下 **訂閱通知**按鈕：

[![訂閱通知按鈕](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果已成功訂閱應用程式，您應該會看到**主題的同步處理成功**在 IDE 中輸出視窗：

[![輸出視窗會顯示主題成功的同步處理的訊息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

您可以使用下列步驟來傳送主題訊息：

1.  在 Firebase 主控台中，按一下**新訊息**。

2.  在 **撰寫訊息**頁面上，輸入訊息文字，然後選取**主題**。

3.  在 **主題**下拉功能表選取內建的主題，**新聞**:

    [![選取 新聞主題](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  Android 裝置 （或模擬器上），請依序點選 Android 背景應用程式**概觀**按鈕，然後觸碰 首頁 螢幕。

5.  裝置就緒時，請按一下**SEND MESSAGE**在 Firebase 主控台中。

6.  檢查 IDE 輸出視窗來查看 **/主題/新聞**的記錄輸出：

    [![會顯示從 /topic/news 訊息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

在 [輸出] 視窗中看到此訊息，通知圖示也應該會在 Android 裝置上的通知區域中出現。 開啟 [通知] 圖示，以檢視主題訊息：

[![主題的訊息會顯示為通知](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果您沒有收到一則訊息，請嘗試刪除**FCMClient**裝置 （或模擬器） 上的應用程式，然後重複上述步驟。

## <a name="foreground-notifications"></a>前景通知

若要接收通知 foregrounded 應用程式中，您必須實作`FirebaseMessagingService`。 此服務也是必要的接收資料裝載，並將訊息上游傳送。 下列範例說明如何實作服務，其`FirebaseMessagingService`&ndash;產生的應用程式將能夠在前景執行時，處理遠端通知。

### <a name="implement-firebasemessagingservice"></a>實作 FirebaseMessagingService

`FirebaseMessagingService`服務會負責接收和處理從 Firebase 訊息。 每個應用程式必須子類別，這個型別和覆寫`OnMessageReceived`處理內送的訊息。 當應用程式處於前景，`OnMessageReceived`回呼永遠會處理訊息。

> [!NOTE]
> 應用程式只能有 10 秒要處理內送的 Firebase 雲端訊息。 任何超過這應該會排定這類使用程式庫的背景執行的工作[Android 工作排程器](~/android/platform/android-job-scheduler.md)或[Firebase 作業發送器](~/android/platform/firebase-job-dispatcher.md)。

加入新的檔名**MyFirebaseMessagingService.cs**和它的範本程式碼取代為下列：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

請注意，`MESSAGING_EVENT`必須宣告意圖篩選，以便新的 FCM 訊息被導向至`MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

當用戶端應用程式從 FCM 收到訊息`OnMessageReceived`擷取訊息內容傳入`RemoteMessage`物件，藉由呼叫其`GetNotification`方法。 接下來，它記錄的訊息內容，使它可以在 IDE 的 [輸出] 視窗中檢視：

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> 如果您在中設定中斷點`FirebaseMessagingService`、 偵錯工作階段可能會或可能不會叫用這些中斷點因為 FCM 將訊息傳遞。


### <a name="send-another-message"></a>傳送另一則訊息

解除安裝應用程式、 加以重新建置、 執行一次，並遵循下列步驟來傳送其他訊息：

1.  在 Firebase 主控台中，按一下**新訊息**。

2.  在 **撰寫訊息**頁面上，輸入訊息文字，然後選取**單一裝置**。

3.  從 IDE 的 [輸出] 視窗複製權杖字串，並將它貼至**FCM 註冊權杖**Firebase 主控台與之前的欄位。

4.  請確定應用程式正在執行前景，然後按一下  **SEND MESSAGE**在 Firebase 主控台中：

    [![從主控台傳送另一則訊息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  當**檢閱訊息** 對話方塊隨即出現，請按一下**傳送**。

6.  內送訊息會記錄至 IDE 的 [輸出] 視窗中︰

    [![訊息內文列印到輸出視窗](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>新增本機通知寄件者

在其餘範例中，內送的 FCM 訊息會轉換成應用程式在前景執行時啟動的本機通知。 編輯**MyFirebaseMessageService.cs**並新增下列`using`陳述式：

```csharp
using FCMClient;
using System.Collections.Generic;
```

將下列方法加入`MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>
```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

為了區別來自背景通知此通知，此程式碼將標記通知以不同於應用程式圖示的圖示。 將檔案加入[ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png)來**資源/drawable**並將它併入**FCMClient**專案.

`SendNotification`方法會使用` NotificationCompat.Builder`若要建立通知，和`NotificationManagerCompat`用來啟動的通知。 通知會保留`PendingIntent`，可讓使用者開啟應用程式，並檢視傳入的字串內容`messageBody`。 如需詳細資訊`NotificationCompat.Builder`，請參閱 <<c2> [ 本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

呼叫`SendNotification`方法的結尾處`OnMessageReceived`方法：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

基於這些變更，`SendNotification`會在應用程式位於前景，而通知會出現在通知區域中，接收到通知時執行。

應用程式時於背景[訊息的承載](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages)會決定如何處理訊息：

* **通知**&ndash;會將訊息傳送至**系統匣**。 本機通知會在該處顯示。 在使用者點選通知時將會啟動應用程式。
* **資料**&ndash;訊息將由處理`OnMessageReceived`。
* **兩者**&ndash;通知 」 和 「 資料裝載的訊息會傳遞至系統匣。 當應用程式啟動時，資料裝載會出現在`Extras`的`Intent`用來啟動應用程式。

在此範例中，如果應用程式在背景，`SendNotification`訊息具有資料裝載會執行。 否則，就會啟動 （稍早在本逐步解說中說明） 背景通知。

### <a name="send-the-last-message"></a>傳送最後一則訊息

解除安裝應用程式、 加以重新建置、 執行一次，然後使用下列步驟來傳送最後一則訊息：

1.  在 Firebase 主控台中，按一下**新訊息**。

2.  在 **撰寫訊息**頁面上，輸入訊息文字，然後選取**單一裝置**。

3.  從 IDE 的 [輸出] 視窗複製權杖字串，並將它貼至**FCM 註冊權杖**Firebase 主控台與之前的欄位。

4.  請確定應用程式正在執行前景，然後按一下  **SEND MESSAGE**在 Firebase 主控台中：

    [![前景訊息傳送](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

此時，已登入 [輸出] 視窗的訊息也封裝在新的通知&ndash;通知圖示會出現在通知紙匣，而在前景中執行應用程式：

[![前景訊息的通知圖示](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

當您開啟通知時，您應該會看到從 Firebase 主控台通知 GUI 已傳送的最後一個訊息：

[![前景與前景圖示一起顯示的通知](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>從 FCM 中斷連線

若要取消訂閱主題，呼叫[UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29)方法[FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging)類別。 例如，若要取消訂閱_新聞_主題稍早，訂閱**取消訂閱**按鈕可能會新增到下列的處理常式程式碼配置：

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

若要取消註冊該裝置從 FCM 完全，請呼叫來刪除執行個體識別碼[DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29)方法[FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)類別。 例如: 

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

這個方法呼叫刪除的執行個體識別碼和與其相關聯的資料。 如此一來，定期傳送到裝置的 FCM 資料就會中止。


## <a name="troubleshooting"></a>疑難排解

以下描述問題和搭配 Xamarin.Android 使用 Firebase 雲端通訊時可能發生的因應措施。

### <a name="firebaseapp-is-not-initialized"></a>未初始化 FirebaseApp

在某些情況下，您可能會看到此錯誤訊息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

這是您可以藉由清除方案並重建專案解決的已知的問題 (**建置 > 清除方案**，**建置 > 重建方案**)。 如需詳細資訊，請參閱此[論壇討論](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process)。


## <a name="summary"></a>總結

本逐步解說的詳細步驟在 Xamarin.Android 應用程式中實作 Firebase 雲端通訊的遠端通知。 它說明如何安裝必要的套件所需的 FCM 通訊，它說明如何設定的 Android 資訊清單的 FCM 伺服器的存取權。 它提供範例程式碼，說明如何檢查 Google Play 服務存在。 它示範了如何實作會交涉使用 FCM 註冊權杖，執行個體識別碼接聽程式服務，它說明如何這段程式碼會建立背景通知應用程式在背景時。 它說明如何訂閱主題訊息，它提供用來接收並顯示遠端通知，應用程式在前景執行時的訊息的接聽程式服務的範例實作。


## <a name="related-links"></a>相關連結

- [FCMNotifications （範例）](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)
