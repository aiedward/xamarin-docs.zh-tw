---
title: "遠端通知 Firebase 與雲端通訊"
description: "本逐步解說 Xamarin.Android 應用程式中提供的逐步說明如何使用 Firebase Cloud Messaging 實作遠端 （也稱為推播通知） 的通知。 它說明如何實作所需的通訊與 Firebase 雲端傳訊 (FCM)、 提供如何設定存取權 FCM，Android 資訊清單的範例和示範如何使用 Firebase 下游訊息處理的各種類別主控台。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7e107fc9ced62027bc793c97a388344d74becd66
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>遠端通知 Firebase 與雲端通訊

_本逐步解說 Xamarin.Android 應用程式中提供的逐步說明如何使用 Firebase Cloud Messaging 實作遠端 （也稱為推播通知） 的通知。它說明如何實作所需的通訊與 Firebase 雲端傳訊 (FCM)、 提供如何設定存取權 FCM，Android 資訊清單的範例和示範如何使用 Firebase 下游訊息處理的各種類別主控台。_

## <a name="fcm-notifications-overview"></a>FCM 通知概觀

在本逐步解說中，基本的應用程式呼叫**FCMClient**將建立來說明 FCM 傳訊 essentials。 **FCMClient**會檢查 Google Play 服務的狀態、 從 FCM 接收註冊權杖、 顯示遠端 Firebase 主控台中，從您傳送的通知，並訂閱主題訊息：

[![範例應用程式的螢幕擷取畫面](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

下列主題領域將瀏覽：

1.  背景通知

2.  主題的訊息

3.  前景通知

在此逐步解說中，您將會以累加方式將功能加入至**FCMClient**上裝置或模擬器，以了解它如何與 FCM 互動和執行。 您將用於見證 FCM 伺服器後，即時應用程式交易記錄，您將會觀察從輸入到 Firebase 主控台通知 GUI FCM 訊息都會產生通知。 

## <a name="requirements"></a>需求

您可以繼續進行本逐步解說之前，您必須取得使用 Google FCM 伺服器; 所需的認證此程序中會說明[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 特別是，您必須下載**google services.json**来使用本逐步解說的範例程式碼檔案。 如果您有尚未建立專案中 Firebase 主控台 (或如果尚未下載**google services.json**檔案)，請參閱[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

若要執行範例應用程式，您必須測試 Android 裝置或模擬器與 Firebase 變為。 Firebase Cloud Messaging 支援執行 Android 2.3 (Gingerbread) 或更高版本，用戶端，而且這些裝置也必須安裝 Google Play 商店應用程式 （Google Play 服務 9.2.1 或更新版本）。 如果您還沒有安裝在裝置上的 Google Play 商店應用程式，請瀏覽[Google Play](https://support.google.com/googleplay)網站下載並安裝它。 或者，您可以使用 Android SDK 模擬器執行 Android 2.3 或更新版本，而不是 （您不需要安裝 Google Play 商店，如果您使用 Android SDK 模擬器） 測試裝置。 

## <a name="start-an-app-project"></a>啟動應用程式專案

若要開始，建立新的空白 Xamarin.Android 專案呼叫**FCMClient**。 如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。 建立新的應用程式之後下, 一個步驟是設定封裝名稱，並安裝數個將用於與 FCM 通訊的 NuGet 封裝。 

### <a name="set-the-package-name"></a>設定封裝名稱

在[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)，指定 FCM 啟用應用程式的封裝名稱。 此封裝的名稱也可做為*應用程式識別碼*API 金鑰與相關聯。 設定應用程式使用此封裝名稱：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  開啟的內容**FCMClient**專案。 

2.  在**Android 資訊清單**頁面上，設定封裝名稱。 

在下列範例中，封裝名稱設定為`com.xamarin.fcmexample`: 

[![設定封裝名稱](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

當您更新**Android 資訊清單**，也請檢查以確定`Internet`權限已啟用。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  開啟的內容**FCMClient**專案。 

2.  在**Android 應用程式**頁面上，設定封裝名稱。 

在下列範例中，封裝名稱設定為`com.xamarin.fcmexample`: 

[![設定封裝名稱](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

當您更新**Android 資訊清單**，也請檢查以確定`INTERNET`權限已啟用 (下**必要的權限**)。 

-----

請注意，用戶端應用程式將無法從 FCM 接收的註冊權杖，如果不存在此封裝名稱*完全*符合到 Firebase 主控台輸入的封裝名稱。 

### <a name="add-the-xamarin-google-play-services-base-package"></a>新增 Xamarin Google Play 服務的基本封裝

因為 Firebase Cloud Messaging 取決於 Google Play 服務[Xamarin Google Play 服務的基底](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/)NuGet 套件必須加入至 Xamarin.Android 專案。 您將需要版本 29.0.0.2 或更新版本。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 封裝...**. 

2.  按一下**瀏覽**索引標籤上，並搜尋**Xamarin.GooglePlayServices.Base**。 

3.  安裝此套件的**FCMClient**專案： 

    [![安裝 Google Play 服務的基底](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**. 

2.  搜尋**Xamarin.GooglePlayServices.Base**。 

3.  安裝此套件的**FCMClient**專案： 

    [![安裝 Google Play 服務的基底](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果您安裝 NuGet 期間收到錯誤，請關閉**FCMClient**專案、 開啟它，然後重試 NuGet 安裝。 

當您安裝**Xamarin.GooglePlayServices.Base**，也會安裝所有必要的相依性。 編輯**Weatherapp**並加入下列`using`陳述式： 

```csharp
using Android.Gms.Common;
```

此陳述式會產生`GoogleApiAvailability`類別**Xamarin.GooglePlayServices.Base**可**FCMClient**程式碼。 
`GoogleApiAvailability` 用來檢查 Google Play 服務存在。 

### <a name="add-the-xamarin-firebase-messaging-package"></a>新增訊息封裝 Xamarin Firebase

從 FCM，接收訊息[Xamarin Firebase-傳訊](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/)NuGet 套件必須新增至應用程式專案。 沒有這個套件，Android 應用程式無法從 FCM 伺服器接收訊息。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 封裝...**. 

2.  請檢查**包含發行前版本**並搜尋**Xamarin.Firebase.Messaging**。 

3.  安裝此套件的**FCMClient**專案： 

    [![安裝 Xamarin Firebase 傳訊](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**. 

2.  請檢查**顯示發行前版本的封裝**並搜尋**Xamarin.Firebase.Messaging**。 

3.  安裝此套件的**FCMClient**專案： 

    [![安裝 Xamarin Firebase 傳訊](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
當您安裝**Xamarin.Firebase.Messaging**，也會安裝所有必要的相依性。

接著，編輯**Weatherapp**並加入下列`using`陳述式：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前兩個陳述式進行中的型別**Xamarin.Firebase.Messaging** NuGet 封裝可**FCMClient**程式碼。 **Android.Util**加入將用來觀察 FMS 與交易的記錄功能。 


### <a name="add-the-google-services-json-file"></a>將 Google 服務的 JSON 檔案

下一個步驟是加入**google services.json**檔案至您的專案的根目錄： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  複製**google services.json**到專案資料夾。

2.  新增**google services.json**應用程式專案 (按一下**顯示所有檔案**中**方案總管 中**，以滑鼠右鍵按一下**google services.json**，然後選取**加入至專案**)。 

3.  選取**google services.json**中**方案總管 中**視窗。

4.  在**屬性**] 窗格中，設定**建置動作**至**GoogleServicesJson** (如果**GoogleServicesJson**未顯示 [建置動作，儲存並關閉方案，然後再重新開啟）：

    [![將建置動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  複製**google services.json**到專案資料夾。

2.  新增**google services.json**應用程式專案。 

3.  以滑鼠右鍵按一下**google services.json**。

4.  設定**建置動作**至**GoogleServicesJson**: 

    [![將建置動作設定為 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

當**google services.json**加入至專案 (而**GoogleServicesJson**建置動作設定)，建置程序會擷取用戶端識別碼和 API 金鑰，並接著將這些認證新增至合併 /產生**AndroidManifest.xml** ，位於**obj/Debug/android/AndroidManifest.xml**。 此合併處理會自動加入任何權限和其他 FCM 項目所需的 FCM 伺服器的連線。 


## <a name="check-for-google-play-services"></a>Google Play 服務的核取

將第一次建立的應用程式的 UI 初始配置。 編輯**Resources/layout/Main.axml** ，並以下列 XML 取代其內容： 

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

這`TextView`會用來顯示訊息，指出是否已安裝 Google Play 服務。 變更儲存到**Main.axml**。 編輯**Weatherapp**並加入下列執行個體變數宣告，以`MainActivity`類別： 

```csharp
TextView msgText;
```

Google 建議的 Android 應用程式的 Google 播放服務 APK 存在之前先檢查存取 Google Play 服務的功能 (如需詳細資訊，請參閱[檢查 Google Play 服務](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play))。 在下列範例中，`OnCreate`方法將會確認應用程式嘗試使用 FCM 服務之前，會使用 Google Play 服務。 將下列方法加入`MainActivity`類別： 

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

此程式碼會檢查以查看是否已安裝 Google 播放服務 APK 裝置。 如果未安裝，訊息會顯示在`TextBox`，指示使用者從 Google Play 商店下載 APK （或啟用裝置的系統設定中）。 

以下列程式碼取代 `OnCreate` 方法： 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` 呼叫的結尾`OnCreate`以便 Google Play 服務可讓您檢查每個執行時間應用程式會啟動。 如果您的應用程式具有`OnResume`方法，它應該呼叫`IsPlayServicesAvailable`從`OnResume`以及。 完全重新建置並執行應用程式。 如果所有的設定正確，您應該會看到的畫面看起來像下列螢幕擷取畫面： 

[![應用程式指出 Google Play 服務為可用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果您沒有得到此結果，確認 Google 播放服務 APK 已安裝在裝置上 (如需詳細資訊，請參閱[Google Play 服務上設定](https://developers.google.com/android/guides/setup))。 也請確認您已經加入**Xamarin.Google.Play.Services.Base**封裝到您**FCMClient**稍早所述的專案。


## <a name="add-the-instance-id-receiver"></a>加入執行個體識別碼接收器

下一個步驟是加入擴充服務`FirebaseInstanceIdService`控制代碼建立，旋轉，以及 Firebase 註冊權杖的更新。 (如需詳細資訊註冊權杖，請參閱[註冊 FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。)`FirebaseInstanceIdService`做是為了要能夠將訊息傳送至裝置的 FCM 服務。 當`FirebaseInstanceIdService`服務會加入至用戶端應用程式中，應用程式會自動接收 FCM 訊息，並顯示它們以通知應用程式 backgrounded。 

### <a name="declare-the-receiver-in-the-android-manifest"></a>宣告 Android 資訊清單中的收件者

編輯**AndroidManifest.xml**下列並插入`<receiver>`項目`<application>`> 一節： 

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

-   宣告`FirebaseInstanceIdReceiver`實作，提供[唯一識別項](https://developers.google.com/instance-id/)每個應用程式執行個體。 此收件者也會驗證並授權的動作。 

-   宣告內部`FirebaseInstanceIdInternalReceiver`用來安全地啟動服務的實作。

`FirebaseInstanceIdReceiver`是`WakefulBroadcastReceiver`接收`FirebaseInstanceId`和`FirebaseMessaging`事件，並傳遞至類別衍生自`FirebaseInstanceIdService`。 

### <a name="implement-the-firebase-instance-id-service"></a>實作 Firebase 執行個體識別碼服務

應用程式登錄 FCM 的工作由自訂`FirebaseInstanceIdService`您提供的服務。 
`FirebaseInstanceIdService` 執行下列步驟： 

1.  使用[執行個體識別碼 API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID)產生授權存取 FCM 和應用程式伺服器的用戶端應用程式的安全性 token。 傳回的是，應用程式註冊語彙基元從取回 FCM。 

2.  如果應用程式伺服器要求，會將轉送應用程式伺服器的註冊權杖。

加入新的檔案稱為**MyFirebaseIIDService.cs**和其範本程式碼替換成下列： 

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

此服務會實作`OnTokenRefresh`最初建立或變更註冊語彙基元時所叫用方法。 當`OnTokenRefresh`執行時，它會擷取最新的語彙基元從`FirebaseInstanceId.Instance.Token`屬性 （這會由 FCM 以非同步方式更新）。 在此範例中，如此就可以檢視 [輸出] 視窗中，就會記錄重新整理的權杖： 

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不常叫用： 它用來更新的權杖，在下列情況： 

-   當應用程式已安裝或解除安裝。

-   當使用者刪除應用程式資料。 

-   當應用程式會清除執行個體識別碼。

-   當已遭洩漏的安全性語彙基元。

根據 Google[執行個體識別碼](https://developers.google.com/instance-id/guides/android-implementation)文件，FCM 執行個體識別碼服務會要求應用程式重新整理其語彙基元定期 （一般而言，每 6 個月）。 

`OnTokenRefresh` 也會呼叫`SendRegistrationToAppServer`關聯使用者的註冊權杖與伺服器端的帳戶 （如果有的話），由維護應用程式： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

因為此實作，取決於應用程式伺服器的設計，此範例中提供空白的方法主體。 如果您的應用程式伺服器需要 FCM 註冊資訊，請修改`SendRegistrationToAppServer`以與維護您的應用程式的任何伺服器端帳戶關聯的使用者 FCM 執行個體的 ID 語彙基元。 （請注意，語彙基元不透明用戶端應用程式。） 

當權杖傳送至應用程式伺服器，`SendRegistrationToAppServer`應該維持布林值，指出是否將權杖傳送到伺服器。 如果這個布林值為 false，`SendRegistrationToAppServer`將權杖傳送至應用程式伺服器&ndash;語彙基元，否則已傳送到先前呼叫的應用程式伺服器。 在某些情況下 (這類`FCMClient`範例)，應用程式伺服器不需要的語彙基元; 因此，這個方法不需要此範例中。 

## <a name="implement-client-app-code"></a>實作用戶端應用程式程式碼

既然接收器服務已就緒，可以寫入用戶端應用程式程式碼充分利用這些服務。 在下列章節中，按鈕會加入至 UI，以記錄的註冊權杖 (也稱為*的執行個體的 ID 語彙基元*)，而且更多程式碼加入至`MainActivity`檢視`Intent`資訊從應用程式啟動時通知： 

[![記錄檔加入至應用程式 畫面的語彙基元按鈕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>記錄檔語彙基元

在此步驟中加入的程式碼僅供示範之用&ndash;實際執行用戶端應用程式就不需要記錄註冊語彙基元。 編輯**Resources/layout/Main.axml**並加入下列`Button`宣告之後立即`TextView`項目： 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

編輯**Weatherapp**並加入下列成員宣告以`MainActivity`類別：

```csharp
const string TAG = "MainActivity";
```

將下列程式碼加入至結尾`OnCreate`方法： 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

此程式碼 [輸出] 視窗中記錄目前的語彙基元時**記錄語彙基元**所點選按鈕。 

### <a name="handle-notification-intents"></a>處理通知的對應方式

當使用者點選通知從發出**FCMClient**伴隨該通知的任何資料、 訊息可在`Intent`額外項目。 編輯**Weatherapp**並將下列程式碼加入至頂端`OnCreate`方法 (呼叫之前`IsPlayServicesAvailable`): 

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

應用程式啟動器`Intent`使用者點選通知訊息，因此這段程式碼會記錄任何隨附的資料時，會引發`Intent`到輸出視窗。 如果不同`Intent`必須引發`click_action`通知訊息的欄位必須設為， `Intent` (啟動器`Intent`時沒有使用`click_action`指定)。 


## <a name="background-notifications"></a>背景通知

建置並執行**FCMClient**應用程式。 **記錄語彙基元**按鈕會顯示：

[![會顯示記錄檔的語彙基元按鈕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

點選**記錄語彙基元** 按鈕。 IDE 的 [輸出] 視窗中，應該會顯示訊息如下所示： 

[![在 [輸出] 視窗中顯示的執行個體 ID 語彙基元](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

加上長字串**語彙基元**會貼入 Firebase 主控台的執行個體 ID 語彙基元&ndash;選取，然後將此字串複製到剪貼簿。 如果看不到執行個體的 ID 語彙基元，請將下列行加入頂端`OnCreate`方法可讓您確認**google services.json**正確剖析：

```csharp
Log.Debug(TAG, "google app id: " + Resource.String.google_app_id);
```

`google_app_id`記錄到 [輸出] 視窗的值應該符合`mobilesdk_app_id`值記錄在**google services.json**。 

### <a name="send-a-message"></a>傳送訊息

登入[Firebase 主控台](https://console.firebase.google.com)，選取您的專案，按一下**通知**，然後按一下**傳送您的第一個訊息**: 

[![傳送您的第一個訊息 按鈕](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

在**撰寫訊息**頁面上，輸入訊息文字，並選取**單一裝置**。 從 IDE 的 [輸出] 視窗複製執行個體的 ID 語彙基元，並將它貼入**FCM 註冊語彙基元**Firebase 主控台的欄位： 

[![撰寫訊息對話方塊](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Android 裝置 （或模擬器上），背景應用程式藉由點選 Android**概觀**按鈕和碰觸的主畫面。 在裝置準備好時，請按一下**傳送訊息**Firebase 主控台中： 

[![傳送郵件 按鈕](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

當**檢閱訊息** 對話方塊隨即出現，請按一下**傳送**。
通知圖示應該會出現在通知區域的裝置 （或模擬器）： 

[![顯示通知圖示](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

開啟 檢視訊息的通知圖示。 通知訊息應剛好什麼輸入到**訊息文字**Firebase 主控台的欄位： 

[![在裝置上會顯示通知訊息](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

點選通知圖示以返回**FCMClient**應用程式。 `Intent`額外項目傳送至**FCMClient** IDE 的 [輸出] 視窗中所列： 

[![從索引鍵、 訊息識別碼和摺疊機碼的意圖額外項目清單](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此範例中，**從**機碼設定成 Firebase 專案的應用程式 (在此範例中， `41590732`)，而**collapse_key**設為它的套件名稱 (**com.xamarin.fcmexample**)。 如果您不會收到一則訊息，請嘗試刪除**FCMClient**裝置 （或模擬器） 上的應用程式，然後重複上述步驟。 


> [!NOTE]
> 如果您強制關閉應用程式，將會停止 FCM，傳送通知。 Android 防止意外或不必要地啟動元件已停止的應用程式的背景服務廣播。 (如需這個行為的詳細資訊，請參閱[啟動已停止的應用程式上的控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)。)基於這個理由，就必須手動解除安裝應用程式每次執行此程式碼及停止偵錯工作階段從&ndash;這會強制 FCM 才能產生新的權杖，以便將繼續接收訊息。

### <a name="add-a-custom-default-notification-icon"></a>加入自訂的預設通知圖示

在上述範例中，通知圖示會設定為應用程式圖示。 下列 XML 會設定自訂的預設圖示通知。 Android 顯示所有通知訊息，其中的通知圖示未明確設定此自訂的預設圖示。 

若要加入自訂的預設通知圖示，加入您的圖示來**資源/drawable**目錄中，編輯**AndroidManifest.xml**，插入下列`<meta-data>`項目插入`<application>`> 一節： 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此範例中，[通知] 圖示，位於**資源/drawable/ic\_stat\_ic\_notification.png**用作自訂預設通知圖示。 如果未在設定自訂的預設圖示**AndroidManifest.xml**而且無圖示設定通知裝載中，Android 會使用通知圖示作為應用程式圖示 （如通知圖示上方螢幕擷取畫面所示）。 

## <a name="handle-topic-messages"></a>控制代碼的主題訊息

到目前為止所撰寫的程式碼會處理註冊權杖，並將遠端通知功能加入至應用程式。 下一個範例會加入程式碼，它會接聽*主題訊息*並轉送至使用者做為遠端的通知。 主題的訊息是 FCM 訊息傳送至訂閱特定主題的一個或多個裝置。 如需主題訊息的詳細資訊，請參閱[主題傳訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

### <a name="subscribe-to-a-topic"></a>訂閱的主題

編輯**Resources/layout/Main.axml**並加入下列`Button`之後立即先前宣告`Button`項目：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

這個 XML 加入**訂閱通知**版面配置 按鈕。
編輯**Weatherapp**並將下列程式碼加入至結尾`OnCreate`方法： 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

此程式碼會找出**訂閱通知**版面配置中的按鈕並將其 click 處理常式指派給呼叫的程式碼`FirebaseMessaging.Instance.SubscribeToTopic`傳遞給已訂閱的主題，_新聞_。 當使用者點選**訂閱**按鈕時，應用程式訂閱_新聞_主題。 下一節，_新聞_主題訊息將傳送從 Firebase 主控台通知 GUI。 

### <a name="send-a-topic-message"></a>傳送主題訊息

解除安裝應用程式、 加以重新建置，並執行一次。 按一下**訂閱通知**按鈕：

[![訂閱通知按鈕](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果應用程式訂閱已成功，您應該會看到**主題同步成功**在 IDE 中輸出視窗： 

[![輸出 視窗會顯示主題成功的同步訊息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

傳送主題訊息中使用下列步驟：

1.  在 Firebase 主控台中，按一下 **新訊息**。 

2.  在**撰寫訊息**頁面上，輸入訊息文字，並選取**主題**。 

3.  在**主題**下拉功能表選取內建的主題，**新聞**: 

    [![選取新聞主題](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  Android 裝置 （或模擬器上），背景應用程式藉由點選 Android**概觀**按鈕和碰觸的主畫面。 

5.  在裝置準備好時，請按一下**傳送訊息**Firebase 主控台中。 

6.  檢查 IDE 輸出視窗來查看**/主題/新聞**中的記錄檔輸出： 

    [![顯示從 /topic/news 訊息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

在 [輸出] 視窗中，會看到此訊息，通知圖示也應該會在 Android 裝置上的通知區域中出現。 開啟通知圖示，以檢視主題訊息： 

[![主題會出現此訊息當做通知](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果您不會收到一則訊息，請嘗試刪除**FCMClient**裝置 （或模擬器） 上的應用程式，然後重複上述步驟。 

## <a name="foreground-notifications"></a>前景通知

若要接收通知 foregrounded 應用程式中，您必須實作`FirebaseMessagingService`。 此服務也是需要接收資料裝載和傳送上游的訊息。 下列範例說明如何實作會擴充服務`FirebaseMessagingService`&ndash;產生的應用程式將能夠在前景執行時處理遠端的通知。 

### <a name="implement-firebasemessagingservice"></a>實作 FirebaseMessagingService

`FirebaseMessagingService`服務包括`OnMessageReceived`您撰寫來處理內送訊息的方法。 請注意，`OnMessageReceived`通知訊息會叫用*只*在前景執行的應用程式時。 當在背景中執行應用程式時，自動產生的通知會顯示 （如本逐步解說稍早所示）。 

加入新的檔案稱為**MyFirebaseMessagingService.cs**和其範本程式碼替換成下列： 

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

請注意，`MESSAGING_EVENT`必須宣告意圖篩選，使新 FCM 訊息被導向至`MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

當用戶端應用程式接收訊息時從 FCM，`OnMessageReceived`擷取來自傳入的訊息內容`RemoteMessage`物件藉由呼叫其`GetNotification`方法。 接下來，它記錄訊息內容，如此就可以在 IDE 的 [輸出] 視窗中檢視： 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> 如果您在中設定中斷點`FirebaseMessagingService`、 您偵錯工作階段可能會或可能不會因為 FCM 將訊息傳遞達到這些中斷點。
 

### <a name="send-another-message"></a>傳送另一則訊息

解除安裝應用程式、 加以重新建置、 執行一次，並遵循下列步驟來傳送其他訊息：

1.  在 Firebase 主控台中，按一下 **新訊息**。 

2.  在**撰寫訊息**頁面上，輸入訊息文字，並選取**單一裝置**。 

3.  從 IDE 的 [輸出] 視窗複製語彙基元字串，並將它貼入**FCM 註冊語彙基元**Firebase 主控台與之前的欄位。 

4.  請確認應用程式正在執行在前景，然後按一下 **傳送訊息**Firebase 主控台中： 

    [![從主控台傳送另一則訊息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  當**檢閱訊息** 對話方塊隨即出現，請按一下**傳送**。

6.  內送的訊息記錄到 IDE 的 [輸出] 視窗：

    [![訊息本文列印到輸出視窗](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>新增本機通知寄件者

其餘在本例中，連入 FCM 訊息將會轉換成本機應用程式在前景執行時啟動的通知。 編輯**MyFirebaseMessageService.cs**並加入下列`using`陳述式： 

```csharp
using FCMClient;
using System.Collections.Generic;
```

將下列方法加入`MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

為了區別來自背景通知此通知，此程式碼將標記通知以不同於 applicatiion 圖示的圖示。 新增[ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png)至**資源/drawable**並將它併入**FCMClient**專案。 

`SendNotification`方法會使用`Notification.Builder`來建立的通知，以及`NotificationManager`用來啟動的通知。 通知會保存`PendingIntent`，可讓使用者開啟應用程式和檢視傳入的字串內容`messageBody`。 根據您想要的目標應用程式的 Android 版本，您可能想要使用`NotificationCompat.Builder`改為。 如需有關`Notification.Builder`，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。 

呼叫`SendNotification`方法結尾處`OnMessageReceived`方法： 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

因為這些變更，而`SendNotification`每當應用程式位於前景，並通知會出現在通知區域中時，收到通知，將會執行。 如果應用程式 backgrounded，`SendNotification`將不會執行，而是會啟動 （稍早在本逐步解說中所述） 背景通知。 

### <a name="send-the-last-message"></a>傳送最後一則訊息

解除安裝應用程式，請加以重新建置、 執行一次，然後使用下列步驟來傳送最後一則訊息：

1.  在 Firebase 主控台中，按一下 **新訊息**。 

2.  在**撰寫訊息**頁面上，輸入訊息文字，並選取**單一裝置**。 

3.  從 IDE 的 [輸出] 視窗複製語彙基元字串，並將它貼入**FCM 註冊語彙基元**Firebase 主控台與之前的欄位。 

4.  請確認應用程式正在執行在前景，然後按一下 **傳送訊息**Firebase 主控台中： 

    [![傳送前景訊息](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

此時，[輸出] 視窗中已記錄的訊息也封裝在新的通知&ndash;通知圖示會出現在通知紙匣，而在前景執行的應用程式： 

[![前景訊息的通知圖示](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

當您開啟通知時，您應該會看到從 Firebase 主控台通知 GUI 已傳送的最後一個訊息： 

[![前景與前景圖示一起顯示的通知](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

 
## <a name="troubleshooting"></a>疑難排解

以下描述問題和因應措施使用 Xamarin.Android Firebase Cloud Messaging 時可能會發生。

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp 未初始化

在某些情況下，您可能會看到此錯誤訊息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

這是您可以清除方案並重建專案解決的已知的問題 (**建置 > 清除方案**，**建置 > 重建方案**)。 如需詳細資訊，請參閱此[論壇討論](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process)。


## <a name="summary"></a>總結

本逐步解說詳細實作 Firebase Cloud Messaging 遠端通知 Xamarin.Android 應用程式中的步驟。 它描述如何安裝必要的封裝所需的 FCM 通訊，而它說明如何設定 Android FCM 伺服器的存取權的資訊清單。 提供範例程式碼說明如何檢查的 Google Play 服務存在，該檔案。 它示範如何實作與 FCM 交涉的註冊權杖中，執行個體識別碼接聽程式服務，它說明如何這段程式碼會建立背景通知而為 backgrounded 應用程式。 它說明如何訂閱主題訊息，它提供用來接收和應用程式在前景執行時，顯示遠端的通知訊息的接聽程式服務的實作範例。 


## <a name="related-links"></a>相關連結

- [FCMNotifications （範例）](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
