---
title: 使用 Firebase 雲端傳訊進行遠端通知
description: 本逐步解說提供逐步解說，說明如何使用 Firebase 雲端通訊來實行在 Xamarin Android 應用程式中)  (也稱為「推播通知」的遠端通知。 它說明如何執行與 Firebase Cloud communication (FCM) 進行通訊所需的各種類別、提供如何設定 Android 資訊清單以存取 FCM 的範例，以及示範使用 Firebase 主控台的下游訊息。
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: 702ca70e220d8e4d28a1a2ddc6be40daae052d58
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455845"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>使用 Firebase 雲端傳訊進行遠端通知

_本逐步解說提供逐步解說，說明如何使用 Firebase 雲端通訊來實行在 Xamarin Android 應用程式中)  (也稱為「推播通知」的遠端通知。它說明如何執行與 Firebase Cloud communication (FCM) 進行通訊所需的各種類別、提供如何設定 Android 資訊清單以存取 FCM 的範例，以及示範使用 Firebase 主控台的下游訊息。_

## <a name="fcm-notifications-overview"></a>FCM 通知總覽

在此逐步解說中，將會建立名為 **FCMClient** 的基本應用程式，以說明 FCM 訊息的基本概念。 **FCMClient** 會檢查 Google Play Services 是否存在、接收來自 FCM 的註冊權杖、顯示您從 Firebase 主控台傳送的遠端通知，以及訂閱主題訊息：

[![應用程式的範例螢幕擷取畫面](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

將會探索下列主題區域：

1. 背景通知

2. 主題訊息

3. 前景通知

在此逐步解說期間，您將會以累加方式將功能新增至 **FCMClient** ，並在裝置或模擬器上執行，以瞭解其與 FCM 的互動方式。 您將使用記錄來見證 FCM 伺服器的即時應用程式交易，而您將會看到如何從您輸入至 Firebase 主控台通知 GUI 的 FCM 訊息產生通知。

## <a name="requirements"></a>需求

熟悉 Firebase 雲端通訊所能傳送的 [不同訊息類型](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) ，將會很有説明。 訊息的承載將會決定用戶端應用程式將如何接收及處理訊息。

繼續進行本逐步解說之前，您必須先取得使用 Google FCM 伺服器所需的認證; [Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm)中會說明此程式。
尤其是，您必須下載 **google-services.json** 檔案，才能與本逐步解說中所提供的範例程式碼搭配使用。 如果您尚未在 Firebase 主控台中建立專案 (或尚未下載檔案) ** 上的google-services.js** ，請參閱 [Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

若要執行範例應用程式，您將需要使用 Firebase 相容的 Android 測試裝置或模擬器。 Firebase 雲端通訊支援在 Android 4.0 或更高版本上執行的用戶端，而且這些裝置也必須安裝 Google Play 商店應用程式 (Google Play Services 9.2.1 或更新版本) 。 如果您的裝置上尚未安裝 Google Play 商店應用程式，請造訪 [Google Play](https://support.google.com/googleplay) 網站下載並安裝該應用程式。 或者，您可以使用 Android SDK 模擬器，並安裝 Google Play Services，而不是測試裝置 (如果您使用 Android SDK 模擬器) ，就不需要安裝 Google Play 商店。

## <a name="start-an-app-project"></a>啟動應用程式專案

若要開始，請建立新的空白 Xamarin. Android 專案，稱為 **FCMClient**。 如果您不熟悉如何建立 Xamarin Android 專案，請參閱 [Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md)。
建立新的應用程式之後，下一個步驟是設定套件名稱，並安裝數個將用來與 FCM 通訊的 NuGet 套件。

### <a name="set-the-package-name"></a>設定套件名稱

在 [Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)中，您已為啟用 FCM 的應用程式指定套件名稱。 此封裝名稱也會作為與[API 金鑰](firebase-cloud-messaging.md#fcm-in-action-api-key)相關聯的[*應用程式識別碼*](./firebase-cloud-messaging.md#fcm-in-action-app-id)。 將應用程式設定為使用此套件名稱：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 開啟 **FCMClient** 專案的屬性。

2. 在 [ **Android 資訊清單** ] 頁面中，設定封裝名稱。

在下列範例中，套件名稱會設定為 `com.xamarin.fcmexample` ：

[![設定套件名稱](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

當您更新 **Android 資訊清單**時，也請確認是否已 `Internet` 啟用許可權。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 開啟 **FCMClient** 專案的屬性。

2. 在 [ **Android 應用程式** ] 頁面中，設定套件名稱。

在下列範例中，套件名稱會設定為 `com.xamarin.fcmexample` ：

[![設定套件名稱](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

當您更新 **Android 資訊清單**時，也請確認是否已在 [ `INTERNET` **必要許可權** ] 下 (啟用許可權) 。

-----

> [!IMPORTANT]
> 如果此套件名稱與 Firebase 主控台中輸入的套件名稱不 *完全* 相符，用戶端應用程式將無法從 FCM 接收註冊權杖。

### <a name="add-the-xamarin-google-play-services-base-package"></a>新增 Xamarin Google Play Services 基底套件

因為 Firebase 雲端通訊相依于 Google Play Services，所以必須將 [xamarin Google Play Services 基底](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) NuGet 套件新增至 Xamarin. Android 專案。 您將需要29.0.0.2 版或更新版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，以滑鼠右鍵按一下 [ **參考] > 管理 NuGet 套件**...]。

2. 按一下 [**流覽**] 索引標籤，並搜尋 **>xamarin.googleplayservices.base。**

3. 將此套件安裝至 **FCMClient** 專案：

    [![安裝 Google Play Services 基底](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [ **封裝] > [新增套件**]。

2. 搜尋 **Xamarin.GooglePlayServices.Base**。

3. 將此套件安裝至 **FCMClient** 專案：

    [![安裝 Google Play Services 基底](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果您在安裝 NuGet 期間收到錯誤，請關閉 **FCMClient** 專案，再重新開啟，然後重試 nuget 安裝。

當您安裝 **>xamarin.googleplayservices.base**時，也會一併安裝所有必要的相依性。 編輯 **MainActivity.cs** 並加入下列 `using` 語句：

```csharp
using Android.Gms.Common;
```

這個語句會讓 `GoogleApiAvailability` **>xamarin.googleplayservices.base** 中的類別可供 **FCMClient** 程式碼使用。
`GoogleApiAvailability` 用來檢查 Google Play Services 是否存在。

### <a name="add-the-xamarin-firebase-messaging-package"></a>新增 Xamarin Firebase 訊息套件

若要接收來自 FCM 的訊息，必須將 [Xamarin Firebase 訊息](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 套件新增至應用程式專案。 若沒有這個套件，Android 應用程式就無法接收來自 FCM 伺服器的訊息。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中，以滑鼠右鍵按一下 [ **參考] > 管理 NuGet 套件**...]。

2. 搜尋**Firebase。**

3. 將此套件安裝至 **FCMClient** 專案：

    [![安裝 Xamarin Firebase 訊息](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [ **封裝] > [新增套件**]。

2. 搜尋**Firebase。**

3. 將此套件安裝至 **FCMClient** 專案：

    [![安裝 Xamarin Firebase 訊息](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

當您安裝 **Firebase**時，也會一併安裝所有必要的相依性。

接著，請編輯 **MainActivity.cs** 並加入下列 `using` 語句：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前兩個語句會讓 **Firebase** NuGet 套件中的類型可用於 **FCMClient** 程式碼。 **Util** 會新增將用來觀察具有 FMS 之交易的記錄功能。

### <a name="add-the-google-services-json-file"></a><a name="add-googleplayservices-json"></a>新增 Google Services JSON 檔案

下一步是將 **google-services.json** 檔案新增至專案的根目錄：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 將 **google-services.js** 複製到專案資料夾。

2. 將**google-services.js**加入至應用程式專案 (按一下 [**顯示****方案總管**中的所有檔案]，以滑鼠右鍵按一下 [ **google-services.js開啟**]，然後選取 [**包含在專案) 中**]。

3. 在 [方案總管]**** 視窗中選取 **google-services.json**。

4. 在 [ **屬性** ] 窗格中，將 [ **組建] 動作** 設為  **GoogleServicesJson**：

    [![將組建動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > 如果未顯示 [ **GoogleServicesJson** ] 組建動作，請儲存並關閉方案，然後再重新開啟。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 將 **google-services.js** 複製到專案資料夾。

2. 將 **google-services.js** 加入至應用程式專案。

3. 以滑鼠右鍵按一下 [ **google-services.js開啟**]。

4. 將 [ **組建] 動作** 設定為 **GoogleServicesJson**：

    [![將組建動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

當**google-services.json**加入至專案時 (並設定 [ **GoogleServicesJson** ] 組建動作) 時，組建程式會解壓縮用戶端識別碼和[API 金鑰](./firebase-cloud-messaging.md#fcm-in-action-api-key)，然後將這些認證新增至位於**obj/Debug/android/AndroidManifest.xml**的合併/產生的**AndroidManifest.xml** 。 此合併程式會自動新增連接至 FCM 伺服器所需的任何許可權和其他 FCM 元素。

## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>檢查 Google Play Services 並建立通知通道

Google 建議 Android 應用程式先檢查 Google Play Services APK 是否存在，再存取 Google Play Services 功能 (如需詳細資訊，請參閱 [檢查 Google Play Services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)) 。

首先會建立應用程式 UI 的初始配置。 編輯 **Resources/layout/Main. .axml** ，並將其內容取代為下列 XML：

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

這 `TextView` 會用來顯示指出是否已安裝 Google Play Services 的訊息。 將變更儲存至 **主要 .axml**。

編輯 **MainActivity.cs** ，並將下列執行個體變數新增至 `MainActivity` 類別：

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

變數 `CHANNEL_ID` 和 `NOTIFICATION_ID` 將用於 [`CreateNotificationChannel`](#create-notification-channel-code) 稍後將在此逐步解說中新增至的方法 `MainActivity` 。

在下列範例中， `OnCreate` 方法會先確認 Google Play Services 可供使用，然後應用程式才會嘗試使用 FCM 服務。
將下列方法新增至 `MainActivity` 類別：

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

此程式碼會檢查裝置，查看是否已安裝 Google Play Services APK。 如果未安裝，則會在中顯示一則訊息， `TextBox` 指示使用者從 Google Play 商店 (下載 APK，或在裝置的 [系統設定]) 中啟用它。

<a name="create-notification-channel-code"></a>在 Android 8.0 (API 層級 26) 或更高版本上執行的應用程式，必須建立 [_通知通道_](~/android/app-fundamentals/notifications/local-notifications.md) 以發佈其通知。  將下列方法新增至類別，以 `MainActivity` 建立通知通道 (如有必要) ：

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

`IsPlayServicesAvailable` 會在結束時呼叫， `OnCreate` 以便在每次應用程式啟動時執行 Google Play Services 檢查。 呼叫方法， `CreateNotificationChannel` 以確保執行 Android 8 或更新版本的裝置有通知通道。 如果您的應用程式具有 `OnResume` 方法，它也應該呼叫 `IsPlayServicesAvailable` `OnResume` 。 完整重建並執行應用程式。 如果一切都已正確設定，您應該會看到如下列螢幕擷取畫面所示的畫面：

[![應用程式指出 Google Play Services 可供使用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果您未收到此結果，請確認 Google Play Services APK 已安裝在您的裝置上 (如需詳細資訊，請參閱 [設定 Google Play Services](https://developers.google.com/android/guides/setup)) 。
也請確認您已將 FCMClient 專案新增至**您的** **FCMClient**專案，如先前所述。

## <a name="add-the-instance-id-receiver"></a>新增實例識別碼接收者

下一步是加入擴充的服務， `FirebaseInstanceIdService` 以處理 [Firebase 註冊權杖](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token)的建立、輪替和更新。 FCM 必須有此服務，才能將 `FirebaseInstanceIdService` 訊息傳送至裝置。 當 `FirebaseInstanceIdService` 服務新增至用戶端應用程式時，應用程式會自動接收 FCM 訊息，並在背景執行應用程式時，將其顯示為通知。

### <a name="declare-the-receiver-in-the-android-manifest"></a>在 Android 資訊清單中宣告接收者

編輯 **AndroidManifest.xml** ，並將下列 `<receiver>` 元素插入 `<application>` 區段中：

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

這個 XML 會執行下列動作：

- 宣告 `FirebaseInstanceIdReceiver` 可為每個應用程式實例提供 [唯一識別碼](https://developers.google.com/instance-id/) 的實作為。 此接收器也會驗證和授權動作。

- 宣告 `FirebaseInstanceIdInternalReceiver` 用來安全地啟動服務的內部執行。

- [應用程式識別碼](./firebase-cloud-messaging.md#fcm-in-action-app-id)會儲存在已[新增至專案](#add-googleplayservices-json)的**google-services.js**檔案中。 Xamarin. Android Firebase 系結會將權杖取代為 `${applicationId}` 應用程式識別碼; 用戶端應用程式不需要額外的程式碼來提供應用程式識別碼。

`FirebaseInstanceIdReceiver`是 `WakefulBroadcastReceiver` 接收 `FirebaseInstanceId` 和事件的， `FirebaseMessaging` 並將其傳遞至您衍生自的類別 `FirebaseInstanceIdService` 。

### <a name="implement-the-firebase-instance-id-service"></a>執行 Firebase 實例識別碼服務

使用 FCM 註冊應用程式的工作是由您提供的自訂服務所處理 `FirebaseInstanceIdService` 。
`FirebaseInstanceIdService` 執行下列步驟：

1. 使用 [實例識別碼 API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) 來產生安全性權杖，以授權用戶端應用程式存取 FCM 和應用程式伺服器。 傳回時，應用程式會從 FCM 取得 [註冊權杖](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) 。

2. 如果應用程式伺服器需要，則將註冊權杖轉送到應用程式伺服器。

新增名為 **MyFirebaseIIDService.cs** 的新檔案，並將其範本程式碼取代為下列內容：

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

這項服務 `OnTokenRefresh` 會在一開始建立或變更註冊權杖時，執行所叫用的方法。 當 `OnTokenRefresh` 執行時，它會從屬性抓取最新的權杖， `FirebaseInstanceId.Instance.Token` (FCM) 以非同步方式更新。 在此範例中，會記錄已重新整理的權杖，讓您可以在 [輸出] 視窗中加以查看：

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不常叫用：在下列情況下，它會用來更新權杖：

- 安裝或卸載應用程式時。

- 當使用者刪除應用程式資料時。

- 當應用程式清除實例識別碼時。

- 當令牌的安全性遭到入侵時。

根據 Google 的 [實例識別碼](https://developers.google.com/instance-id/guides/android-implementation) 檔，FCM 實例識別碼服務會要求應用程式定期重新整理權杖， (通常是每6個月) 。

`OnTokenRefresh` 也 `SendRegistrationToAppServer` 會呼叫，以將使用者的註冊權杖與伺服器端帳戶建立關聯， (如果應用程式所維護的任何) ：

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

因為這項實行取決於應用程式伺服器的設計，所以在此範例中會提供空的方法主體。 如果您的應用程式伺服器需要 FCM 註冊資訊，請修改 `SendRegistrationToAppServer` 以將使用者的 FCM 實例識別碼權杖與您應用程式所維護的任何伺服器端帳戶產生關聯。  (請注意，權杖對用戶端應用程式而言是不透明的。 ) 

當令牌傳送至應用程式伺服器時， `SendRegistrationToAppServer` 應維持布林值以指出權杖是否已傳送至伺服器。 如果此布林值為 false，則會 `SendRegistrationToAppServer` 將權杖傳送至應用程式伺服器 &ndash; ，否則權杖已在先前的呼叫中傳送至應用程式伺服器。 在某些情況下 (例如 `FCMClient`) 的範例中，應用程式伺服器不需要權杖，因此在此範例中不需要這個方法。

## <a name="implement-client-app-code"></a>執行用戶端應用程式程式碼

現在已有接收器服務，可以撰寫用戶端應用程式程式碼，以利用這些服務。 在下列各節中，會將按鈕新增至 UI，以記錄註冊權杖 (也稱為 *實例識別碼權杖*) ，然後在 `MainActivity` `Intent` 應用程式從通知中啟動時，新增更多程式碼以查看資訊：

[![新增至應用程式畫面的 [記錄權杖] 按鈕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>記錄權杖

此步驟中新增的程式碼僅供示範之 &ndash; 用：生產用戶端應用程式不需要記錄註冊權杖。 編輯 **Resources/layout/Main. .axml** ，並在 `Button` 元素之後立即新增下列宣告 `TextView` ：

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

將下列程式碼新增至 `MainActivity.OnCreate` 方法的結尾：

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

這段程式碼會在按下 [ **記錄 token** ] 按鈕時，將目前的權杖記錄至 [輸出] 視窗。

### <a name="handle-notification-intents"></a>處理通知意圖

當使用者按下從 **FCMClient**發出的通知時，會在額外專案中提供通知訊息隨附的任何資料 `Intent` 。 編輯 **MainActivity.cs** ，並將下列程式碼加入至方法的頂端 `OnCreate` (在呼叫) 之前 `IsPlayServicesAvailable` ：

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

`Intent`當使用者按下通知訊息時，就會引發應用程式的啟動程式，因此此程式碼會將任何伴隨的資料記錄 `Intent` 到 [輸出] 視窗中。 如果 `Intent` 必須引發不同的，則 `click_action` 通知訊息的欄位必須設定為， `Intent` (`Intent` 當) 未指定任何時，就會使用啟動器 `click_action` 。

## <a name="background-notifications"></a>背景通知

建立並執行 **FCMClient** 應用程式。 [ **記錄權杖** ] 按鈕隨即顯示：

[![[記錄 Token] 按鈕顯示](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

點擊 [ **記錄 Token** ] 按鈕。 IDE 輸出視窗中應該會顯示類似下列的訊息：

[![顯示在 [輸出] 視窗中的實例識別碼權杖](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

標記為 **token** 的長字串是您將貼到 Firebase 主控台中的實例識別碼權杖 &ndash; ，請選取並將此字串複製到剪貼簿。 如果您沒有看到實例識別碼權杖，請將下列這一行加入至方法的頂端， `OnCreate` 以確認已正確剖析 **google-services.js** ：

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

`google_app_id`記錄至 [輸出] 視窗的值應符合 `mobilesdk_app_id` **google-services.js**中記錄的值。

### <a name="send-a-message"></a>傳送訊息

登入 [Firebase 主控台](https://console.firebase.google.com)，選取您的專案，按一下 [ **通知**]，然後按一下 [ **傳送您的第一則訊息**]：

[![傳送第一個訊息按鈕](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

在 [ **撰寫訊息** ] 頁面上，輸入郵件內文，然後選取 [ **單一裝置**]。 從 IDE 輸出視窗中複製實例識別碼權杖，並將它貼入 Firebase 主控台的 [ **FCM 註冊權杖** ] 欄位：

[![撰寫訊息對話方塊](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

在 Android 裝置 (或模擬器) 上，藉由使用 [Android **總覽** ] 按鈕並觸及主畫面來背景應用程式。 當裝置準備就緒時，請在 Firebase 主控台中按一下 [ **傳送訊息** ]：

[![傳送訊息按鈕](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

顯示 [ **審核訊息** ] 對話方塊時，按一下 [ **傳送**]。
通知圖示應該會出現在裝置 (或模擬器) 的通知區域中：

[![顯示通知圖示](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

開啟 [通知] 圖示以查看訊息。 通知訊息應該是在 Firebase 主控台的 [ **郵件內文** ] 欄位中輸入的確切內容：

[![通知訊息會顯示在裝置上](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

請按一下通知圖示來啟動 **FCMClient** 應用程式。 `Intent`傳送至**FCMClient**的額外專案會列在 IDE 輸出視窗中：

[![從 key、message ID 和 collapse 索引鍵的意圖額外專案清單](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此範例中， **from** 索引鍵會設定為應用程式的 Firebase 專案編號 (在此範例中 `41590732`) ，而且 **collapse_key** 會設定為其套件名稱 (**fcmexample**) 。
如果您沒有收到訊息，請嘗試在裝置 (或模擬器) 上刪除 **FCMClient** 應用程式，然後重複上述步驟。

> [!NOTE]
> 如果您強制關閉應用程式，FCM 將會停止傳遞通知。 Android 可防止背景服務廣播不慎或不必要地啟動已停止應用程式的元件。  (如需此行為的詳細資訊，請參閱 [在已停止的應用程式上啟動控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)) 。基於這個原因，您必須在每次執行應用程式時手動將其卸載，然後從偵錯工具停止它， &ndash; 這會強制 FCM 產生新的權杖，以繼續接收訊息。

### <a name="add-a-custom-default-notification-icon"></a>新增自訂預設通知圖示

在上述範例中，通知圖示會設定為應用程式圖示。 下列 XML 會設定通知的自訂預設圖示。 Android 會針對未明確設定通知圖示的所有通知訊息，顯示這個自訂預設圖示。

若要加入自訂預設通知圖示，請將您的圖示新增至 **資源/可繪製** 目錄，編輯 **AndroidManifest.xml**，並將下列 `<meta-data>` 元素插入 `<application>` 區段中：

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此範例中，位於 **資源/可繪製/ic \_ stat stat \_ \_notification.png** 的通知圖示將會用來作為自訂預設通知圖示。 如果未在 **AndroidManifest.xml** 中設定自訂預設圖示，且通知承載中未設定任何圖示，則 Android 會使用應用程式圖示作為通知圖示， (如上面) 的通知圖示螢幕擷取畫面所示。

## <a name="handle-topic-messages"></a>處理主題訊息

目前為止所撰寫的程式碼會處理註冊權杖，並將遠端通知功能新增至應用程式。 下一個範例會新增可接聽 *主題訊息* 的程式碼，並將其轉送至使用者作為遠端通知。 主題訊息是傳送至一或多個訂閱特定主題之裝置的 FCM 訊息。 如需主題訊息的詳細資訊，請參閱 [主題訊息](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。

### <a name="subscribe-to-a-topic"></a>訂閱主題

編輯 **Resources/layout/Main. .axml** ，並 `Button` 緊接在上一個元素之後加入下列宣告 `Button` ：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

這個 XML 會將 [ **訂閱通知** ] 按鈕加入至版面配置。
編輯 **MainActivity.cs** ，並將下列程式碼加入至方法的結尾 `OnCreate` ：

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

這段程式碼會在版面配置中找出 [ **訂閱通知** ] 按鈕，並將它的 click 處理常式指派給呼叫的程式碼，並 `FirebaseMessaging.Instance.SubscribeToTopic` 在訂閱的主題中傳遞 _新聞_。 當使用者按下 [ **訂閱** ] 按鈕時，應用程式會訂閱 _新聞_ 主題。 在下一節中，會從 Firebase 主控台通知 GUI 傳送 _新聞_ 主題訊息。

### <a name="send-a-topic-message"></a>傳送主題訊息

卸載應用程式、重建應用程式，然後再次執行。 按一下 [ **訂閱通知** ] 按鈕：

[![訂閱通知按鈕](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果應用程式已成功訂閱，您應該會在 IDE 輸出視窗中看到 [ **成功同步** 處理] 主題：

[![[輸出] 視窗顯示主題同步成功訊息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

使用下列步驟來傳送主題訊息：

1. 在 Firebase 主控台中，按一下 [ **新增訊息**]。

2. 在 [ **撰寫訊息** ] 頁面上，輸入郵件內文，然後選取 [  **主題**]。

3. 在 [ **主題** ] 下拉式功能表中，選取內建主題 [  **新聞**：

    [![選取新聞主題](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. 在 Android 裝置 (或模擬器) 上，藉由使用 [Android **總覽** ] 按鈕並觸及主畫面來背景應用程式。

5. 當裝置準備就緒時，請在 Firebase 主控台中按一下 [ **傳送訊息** ]。

6. 檢查 IDE 輸出視窗，以查看記錄輸出中的 **/topics/news** ：

    [![顯示來自/topic/news 的訊息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

當您在 [輸出] 視窗中看到此訊息時，通知圖示也應該會出現在 Android 裝置的通知區域中。 開啟 [通知] 圖示以查看主題訊息：

[![主題訊息會顯示為通知](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果您沒有收到訊息，請嘗試在裝置 (或模擬器) 上刪除 **FCMClient** 應用程式，然後重複上述步驟。

## <a name="foreground-notifications"></a>前景通知

若要在 foregrounded apps 中收到通知，您必須執行 `FirebaseMessagingService` 。 這項服務也是接收資料承載和傳送上游訊息時的必要項。 下列範例說明如何執行服務，以擴充 `FirebaseMessagingService` &ndash; 產生的應用程式，在前景中執行時可以處理遠端通知。

### <a name="implement-firebasemessagingservice"></a>執行 FirebaseMessagingService

此 `FirebaseMessagingService` 服務負責接收和處理來自 Firebase 的訊息。 每個應用程式都必須將此型別設為子類別，並覆寫 `OnMessageReceived` 來處理傳入訊息。 當應用程式在前景時， `OnMessageReceived` 回呼一律會處理訊息。

> [!NOTE]
> 應用程式只能有10秒鐘的時間來處理傳入的 Firebase 雲端訊息。 任何花費超過此時間的工作，都應該使用程式庫（例如 [Android 工作](~/android/platform/android-job-scheduler.md) 排程器或 [Firebase 作業發送器](~/android/platform/firebase-job-dispatcher.md)）來排程背景執行。

新增名為 **MyFirebaseMessagingService.cs** 的新檔案，並將其範本程式碼取代為下列內容：

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

請注意， `MESSAGING_EVENT` 必須宣告意圖篩選準則，以便將新的 FCM 訊息導向至 `MyFirebaseMessagingService` ：

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

當用戶端應用程式從 FCM 接收訊息時，藉 `OnMessageReceived` `RemoteMessage` 由呼叫其方法，從傳入的物件中將訊息內容解壓縮 `GetNotification` 。 接著，它會記錄訊息內容，以便在 IDE 輸出視窗中加以查看：

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> 如果您在中設定中斷點 `FirebaseMessagingService` ，則偵錯工具不一定會因為 FCM 傳遞訊息的方式而遇到這些中斷點。

### <a name="send-another-message"></a>傳送另一則訊息

卸載應用程式、重建應用程式、再次執行，然後遵循下列步驟來傳送另一則訊息：

1. 在 Firebase 主控台中，按一下 [ **新增訊息**]。

2. 在 [ **撰寫訊息** ] 頁面上，輸入郵件內文，然後選取 [  **單一裝置**]。

3. 從 IDE 輸出視窗複製權杖字串，然後將它貼入 Firebase 主控台的 [ **FCM 註冊權杖** ] 欄位中，如同之前一樣。

4. 確定應用程式正在前景中執行，然後按一下 Firebase 主控台中的 [ **傳送訊息** ]：

    [![從主控台傳送另一則訊息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. 顯示 [ **審核訊息** ] 對話方塊時，按一下 [ **傳送**]。

6. 傳入訊息會記錄到 IDE 輸出視窗中：

    [![列印到輸出視窗的訊息內文](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)

### <a name="add-a-local-notification-sender"></a>新增本機通知寄件者

在此其餘範例中，會將內送 FCM 訊息轉換為應用程式在前景中執行時啟動的本機通知。 編輯 **MyFirebaseMessageService.cs** 並加入下列 `using` 語句：

```csharp
using FCMClient;
using System.Collections.Generic;
```

將下列方法新增至 `MyFirebaseMessagingService`：

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

為了區別此通知與背景通知，此程式碼會以與應用程式圖示不同的圖示來標記通知。 將「 [ic」 \_ stat 的 \_ ic \_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) 新增至 **資源/可繪製** 的，並將它包含在 **FCMClient** 專案中。

`SendNotification`方法會使用 `NotificationCompat.Builder` 來建立通知，並且 `NotificationManagerCompat` 用來啟動通知。 通知 `PendingIntent` 會保留，讓使用者開啟應用程式，並查看傳入的字串內容 `messageBody` 。 如需的詳細資訊 `NotificationCompat.Builder` ，請參閱 [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

`SendNotification`在方法的結尾呼叫方法 `OnMessageReceived` ：

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

由於這些變更的結果， `SendNotification` 會在應用程式在前景時，每次收到通知時執行，而且通知會出現在通知區域中。

當應用程式在背景時， [訊息](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) 的承載將會決定訊息的處理方式：

- **通知** &ndash; 訊息將會傳送至 **系統**匣。 本機通知會出現在該處。 當使用者按到通知時，應用程式將會啟動。
- **資料** &ndash; 訊息將由處理 `OnMessageReceived` 。
- **兩者** &ndash; 具有通知和資料承載的訊息將會傳遞至系統匣。 當應用程式啟動時，資料承載將會出現在 `Extras` `Intent` 用來啟動應用程式的中。

在此範例中，如果應用程式是背景執行，則 `SendNotification` 如果訊息具有資料承載，將會執行。 否則，在此逐步解說中稍早所述的背景通知 (將會啟動) 。

### <a name="send-the-last-message"></a>傳送最後一則訊息

卸載應用程式、重建應用程式、再次執行，然後使用下列步驟來傳送最後一則訊息：

1. 在 Firebase 主控台中，按一下 [ **新增訊息**]。

2. 在 [ **撰寫訊息** ] 頁面上，輸入郵件內文，然後選取 [ **單一裝置**]。

3. 從 IDE 輸出視窗複製權杖字串，然後將它貼入 Firebase 主控台的 [ **FCM 註冊權杖** ] 欄位中，如同之前一樣。

4. 確定應用程式正在前景中執行，然後按一下 Firebase 主控台中的 [ **傳送訊息** ]：

    [![傳送前景訊息](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

這次，在應用程式于前景中執行時，在 [輸出] 視窗中記錄的訊息也會封裝在新的通知中，通知 &ndash; 圖示會顯示在通知匣中：

[![前景訊息的通知圖示](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

當您開啟通知時，您應該會看到從 Firebase 主控台通知 GUI 傳送的最後一則訊息：

[![以前景圖示顯示的前景通知](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

## <a name="disconnecting-from-fcm"></a>中斷與 FCM 的連線

若要取消訂閱主題，請在[FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging)類別上呼叫[UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29)方法。 例如，若要取消訂閱先前訂閱的 _新聞_ 主題，可使用下列處理常式程式碼，將 **取消訂閱** 按鈕新增至配置：

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

若要從 FCM 完全取消註冊裝置，請在[FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)類別上呼叫[DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29)方法來刪除實例識別碼。 例如：

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

這個方法呼叫會刪除實例識別碼和與其相關聯的資料。 如此一來，將會定期將 FCM 資料傳送至裝置。

## <a name="troubleshooting"></a>疑難排解

以下說明使用 Firebase 雲端通訊與 Xamarin 時可能發生的問題和因應措施。

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp 未初始化

在某些情況下，您可能會看到下列錯誤訊息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

這是已知的問題，您可以藉由清除方案並重建專案 (**建立 > 乾淨方案**， **建立 > 重建解決方案**) 來解決此問題。

## <a name="summary"></a>摘要

本逐步解說詳述在 Xamarin Android 應用程式中執行 Firebase 雲端通訊遠端通知的步驟。 說明如何安裝 FCM 通訊所需的必要套件，並說明如何設定 Android 資訊清單以存取 FCM 伺服器。 其中提供的範例程式碼說明如何檢查 Google Play Services 是否存在。 它示範如何針對註冊權杖執行與 FCM 協調的實例識別碼接聽程式服務，並說明此程式碼如何在背景執行應用程式時建立背景通知。 它說明如何訂閱主題訊息，並提供訊息接聽程式服務的範例，此服務可在應用程式于前景執行時，用來接收及顯示遠端通知。

## <a name="related-links"></a>相關連結

- [FCMNotifications (範例) ](/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [關於 FCM 訊息](https://firebase.google.com/docs/cloud-messaging/concept-options)