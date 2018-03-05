---
title: "遠端使用 Google 雲端訊息的通知"
description: "本逐步解說 Xamarin.Android 應用程式中提供的逐步說明如何使用 Google Cloud Messaging 實作遠端 （也稱為推播通知） 的通知。 它會描述您必須實作以進行通訊與 Google Cloud Messaging (GCM) 的各種類別，說明如何在 Android 的 GCM，存取資訊清單設定的權限，它會示範端對端訊息的範例測試程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: dda7597456421aa4ae401f56ed6cfc7983df29c5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>遠端使用 Google 雲端訊息的通知

_本逐步解說 Xamarin.Android 應用程式中提供的逐步說明如何使用 Google Cloud Messaging 實作遠端 （也稱為推播通知） 的通知。它會描述您必須實作以進行通訊與 Google Cloud Messaging (GCM) 的各種類別，說明如何在 Android 的 GCM，存取資訊清單設定的權限，它會示範端對端訊息的範例測試程式。_

## <a name="gcm-notifications-overview"></a>GCM 通知概觀

在此逐步解說中，我們將建立使用 Google Cloud Messaging (GCM) 實作遠端通知的 Xamarin.Android 應用程式 (也稱為*推播通知*)。 我們會實作使用 GCM 適用於遠端訊息的各種方式及接聽程式服務，我們將測試會模擬應用程式伺服器的命令列程式實作。 

請注意，Firebase 雲端傳訊 (FCM) 是新版本的 GCM &ndash; Google 強烈建議使用 FCM，而不是 GCM。 如果您目前使用 GCM，升級至 FCM 建議。 如需 FCM 的詳細資訊，請參閱[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

您可以繼續進行本逐步解說之前，您必須取得使用 Google 的 GCM 伺服器; 所需的認證此程序中會說明[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)。 特別是，您將需要*API 金鑰*和*寄件者識別碼*本逐步解說的範例程式碼中插入。 

若要建立 GCM 啟用 Xamarin.Android 用戶端應用程式，我們將使用下列步驟：

1.  安裝額外的 GCM 伺服器進行通訊所需的封裝。
2.  設定 GCM 伺服器的存取權的應用程式權限。
3.  實作程式碼會檢查 Google Play 服務存在。 
4.  實作會交涉使用 GCM 註冊語彙基元註冊意圖服務。
5.  實作會接聽來自 GCM 註冊權杖更新的執行個體識別碼接聽程式服務。
6.  實作會從應用程式伺服器透過 GCM 接收遠端訊息的 GCM 接聽程式服務。

此應用程式會使用新的 GCM 功能，又稱為*主題傳訊*。 在主題傳訊中，應用程式伺服器傳送訊息給主題，而不是個別裝置的清單。 該主題訂閱的裝置可以接收主題訊息為推播通知。 如需 GCM 主題傳訊的詳細資訊，請參閱 Google[實作主題傳訊](https://developers.google.com/cloud-messaging/topic-messaging)。 

用戶端應用程式就緒時，我們會實作命令列 C# 應用程式，將推播通知傳送至 GCM 透過我們的用戶端應用程式。 

## <a name="walkthrough"></a>逐步解說

若要開始，我們來建立新的空白方案，稱為**RemoteNotifications**。 接下來，讓我們加入新的 Android 專案加入至此方案，根據**Android 應用程式**範本。 這個專案，我們稱為**ClientApp**。 (如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)**ClientApp**專案會包含接收遠端透過 GCM 通知的 Xamarin.Android 用戶端應用程式的程式碼。 

### <a name="add-required-packages"></a>將必要的封裝

我們可以實作我們的用戶端應用程式程式碼之前，我們必須安裝與 GCM 進行通訊，我們將使用的數個封裝。 此外，我們必須加入 Google Play 商店應用程式到我們的裝置，如果尚未安裝。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>將 Xamarin Google Play 服務 GCM 封裝

從 Google Cloud Messaging 接收訊息[Google Play 服務](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)架構必須存在於裝置上。 沒有這個架構，Android 應用程式無法從 GCM 伺服器接收訊息。 Google Play 服務在背景中執行 Android 的裝置已開啟電源，而接聽來自 GCM 的訊息。 這些訊息到達時，Google Play 服務會將訊息轉換成對應方式，然後再廣播這些對應已註冊他們的應用程式的方式。 

在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 封裝...**; 在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**.搜尋**Xamarin Google Play 服務-GCM**並安裝此套件的**ClientApp**專案： 

[ ![安裝 Google Play 服務](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png)

當您安裝**Xamarin Google Play 服務-GCM**， **Xamarin Google Play 服務的基底**會自動安裝。 如果您收到錯誤，請變更專案的*最小值 Android 目標*以外的值設定**使用 SDK 版本編譯**，然後再試一次 NuGet 安裝。 

接著，編輯**Weatherapp**並加入下列`using`陳述式：

```csharp
using Android.Gms.Common;
using Android.Util;
```

這樣可讓類型 Google 播放服務 GMS 套件提供給我們的程式碼，並將我們會用來追蹤與 GMS 我們交易的記錄功能。 

#### <a name="google-play-store"></a>Google Play 商店

若要從 GCM 接收訊息，Google Play 商店應用程式必須安裝在裝置上。 （每當裝置上安裝 Google Play 應用程式，Google Play 商店也會安裝，因此可能已經被安裝測試裝置上。）不含 Google Play Android 應用程式無法接收來自 GCM 的訊息。 如果您還沒有安裝在裝置上的 Google Play 商店應用程式，請瀏覽[Google Play](https://support.google.com/googleplay)網站下載並安裝 Google Play。 

或者，您可以使用 Android 模擬器中執行 Android 2.2 或更新版本，而不是 （您不需要在 Android 模擬器上安裝 Google Play 商店） 測試裝置。 不過，如果您使用模擬器，您必須使用 Wi-fi 連線至 GCM 並稍後在本逐步解說中所述，您必須在 Wi-fi 防火牆中開啟數個連接埠。 

### <a name="set-the-package-name"></a>設定封裝名稱

在[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)，我們指定我們 GCM 啟用的應用程式的封裝名稱 (此封裝的名稱也可做為*應用程式識別碼*與我們的 API 金鑰與傳送者識別碼相關聯)。 讓我們來開啟的內容**ClientApp**專案，並設定封裝名稱，這個字串。 在此範例中，設定封裝名稱為`com.xamarin.gcmexample`:

[ ![設定封裝名稱](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png)

請注意，用戶端應用程式將無法接收來自 GCM 註冊語彙基元，如果此封裝的名稱並不會*完全*符合我們輸入 Google 開發人員主控台中的封裝名稱。 

### <a name="add-permissions-to-the-android-manifest"></a>加入 Android 資訊清單的權限

Android 應用程式必須設定才能從 Google Cloud Messaging 接收通知的下列權限： 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; 授與我們的應用程式來註冊及接收來自 Google Cloud Messaging 訊息的權限。 (為何`c2dm`表示？ 這代表_雲端到裝置訊息_，也就是至 GCM 現在已被取代的前置項。 
    GCM 仍會使用`c2dm`的許多其權限的字串。) 

-   `android.permission.WAKE_LOCK` &ndash; （選擇性）防止睡眠模式，但接聽訊息時，將裝置從 CPU。 

-   `android.permission.INTERNET` &ndash; 授與存取網際網路，讓用戶端應用程式能夠與 GCM 通訊。 

-   *package_name* `.permission.C2D_MESSAGE` &ndash; android 註冊此應用程式，並要求以獨佔方式接收所有 C2D 權限 （雲端到裝置） 訊息。 *Package_name*前置詞相當於您的應用程式識別碼。 

Android 資訊清單中，我們會設定這些權限。 開始編輯**AndroidManifest.xml**並以下列 XML 取代內容： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

在上述 XML 中，變更*YOUR_PACKAGE_NAME*到用戶端應用程式專案的封裝名稱。 例如，`com.xamarin.gcmexample`。 

### <a name="check-for-google-play-services"></a>Google Play 服務的核取

這個逐步解說中，我們建立基本應用程式使用單一`TextView`UI 中。 此應用程式未直接指出 GCM 互動。 相反地，我們將觀看 [輸出] 視窗，以查看如何使用 GCM，我們的應用程式交握和到達時，我們會檢查新通知的通知紙匣。 

首先，讓我們來建立訊息區域的版面配置。 編輯**Resources.layout.Main.axml**並以下列 XML 取代內容： 

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

儲存**Main.axml**並將它關閉。

當用戶端應用程式啟動時，我們想要在我們嘗試連絡 GCM 確認 Google Play 服務可以使用。 編輯**Weatherapp**和取代``count``執行個體變數宣告，但下列執行個體變數宣告： 

```csharp
TextView msgText;
```

接下來，加入下列方法加入**MainActivity**類別： 

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
            msgText.Text = "Sorry, this device is not supported";
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

此程式碼會檢查以查看是否已安裝 Google 播放服務 APK 裝置。 如果未安裝，訊息會顯示區域中的訊息會指示使用者從 Google Play 商店下載 APK （或啟用裝置的系統設定中）。 因為我們想要執行這項檢查，用戶端應用程式啟動時，我們會將呼叫此方法的結尾`OnCreate`。 


接下來，取代`OnCreate`方法取代下列程式碼：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

這個程式碼會檢查 Google 播放服務 APK 狀態，並將結果寫入訊息區域。 

讓我們完全重新建置並執行應用程式。 您應該會看到的畫面看起來像下列螢幕擷取畫面： 

[ ![Google Play 服務為可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png)

如果您沒有得到此結果，確認 Google 播放服務 APK 已安裝您的裝置，而且該**Xamarin Google Play 服務-GCM**封裝加入至您**ClientApp**所述的專案稍早。 如果您收到建置錯誤，請嘗試清除方案，並再次建置專案。 

接下來，我們要撰寫程式碼來連絡 GCM 和取回註冊語彙基元。

### <a name="register-with-gcm"></a>GCM 註冊

應用程式可以接收來自應用程式伺服器的遠端通知之前，它必須使用 GCM 註冊，並回到註冊語彙基元。 我們的應用程式登錄 GCM 的工作由`IntentService`我們建立的。 我們`IntentService`執行下列步驟： 

1.  使用[InstanceID](https://developers.google.com/instance-id/) API 來產生授權我們的用戶端應用程式來存取應用程式伺服器的安全性權杖。 傳回的是，我們註冊語彙基元從取回 GCM。

2.  （如果應用程式伺服器需要的話），會轉送到應用程式伺服器的註冊權杖。

3.  訂閱一個或多個通知主題通道。

我們會實作這之後`IntentService`，我們將測試以查看是否我們註冊語彙基元從取回 GCM。

加入新的檔案稱為**RegistrationIntentService.cs**和範本程式碼替換成下列：


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

在上述範例程式碼中，變更*YOUR_SENDER_ID*寄件者識別碼的用戶端應用程式專案。 若要為您的專案中取得寄件者識別碼： 

1.  登入[Google 雲端主控台](https://console.cloud.google.com/)從提取下功能表中選取您的專案名稱。 在**專案資訊**為您的專案，顯示] 窗格中按一下**移至 [專案設定**:

    [![選取 XamarinGCM 專案](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png)

2.  在**設定**頁面上，尋找**專案編號**&ndash;這是寄件者識別碼，為您的專案：

    [![顯示的專案數目](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png)

我們想要啟動我們`RegistrationIntentService`我們的應用程式開始執行時。 編輯**Weatherapp**和修改`OnCreate`方法，讓我們`RegistrationIntentService`後，我們會檢查是否存在的 Google Play 服務啟動時： 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

現在讓我們看看的每個區段`RegistrationIntentService`了解其運作方式。 

首先，我們加上註解我們`RegistrationIntentService`具有下列屬性來指出，我們的服務不會具現化系統： 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService`建構函式名稱的背景工作執行緒*RegistrationIntentService*進行偵錯更容易。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

核心功能`RegistrationIntentService`位於`OnHandleIntent`方法。 讓我們逐步解說以查看它如何與 GCM 註冊我們的應用程式的這段程式碼。


##### <a name="request-a-registration-token"></a>要求註冊權杖

`OnHandleIntent` 會先呼叫 Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法來要求來自 GCM 註冊語彙基元。 我們將包裝在這段程式碼`lock`防止多個註冊對應方式同時發生的可能性&ndash;`lock`可確保這些方式會依序處理。 如果我們無法取得註冊權杖時，會擲回例外狀況，我們會記錄錯誤。 如果登錄成功，`token`設得到回自 GCM 註冊權杖： 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>應用程式伺服器轉送給註冊憑證

如果我們取得註冊權杖 （也就是沒有擲回例外狀況），我們稱之為`SendRegistrationToAppServer`關聯使用者的註冊權杖與伺服器端的帳戶 （如果有的話），由維護我們的應用程式。 因為此實作，取決於應用程式伺服器的設計，此處提供的空白方法： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情況下，應用程式伺服器不需要使用者的註冊權杖。在此情況下，您可以省略這個方法。 當註冊權杖傳送至應用程式伺服器，`SendRegistrationToAppServer`應該維持布林值，指出是否將權杖傳送到伺服器。 如果這個布林值為 false，`SendRegistrationToAppServer`將權杖傳送至應用程式伺服器&ndash;語彙基元，否則已傳送到先前呼叫的應用程式伺服器。 


##### <a name="subscribe-to-the-notification-topic"></a>訂閱通知主題

接下來，呼叫我們`Subscribe`方法，以表示至 GCM 我們想要訂閱通知主題。 在`Subscribe`，我們稱之為[GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;)訂閱我們的用戶端應用程式底下的所有訊息的應用程式開發介面`/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

應用程式伺服器必須傳送通知訊息給`/topics/global`如果我們要接收訊息。 請注意，本主題名稱下`/topics`可以是任何項目，只要應用程式伺服器和用戶端應用程式同意這些名稱。 (在這裡，我們選擇名稱`global`表示我們想要在應用程式伺服器所支援的所有主題上接收訊息。) 

GCM 主題傳訊的伺服器端上的相關資訊，請參閱 Google[傳送訊息的主題](https://developers.google.com/cloud-messaging/topic-messaging)。 


#### <a name="implement-an-instance-id-listener-service"></a>實作的執行個體識別碼的接聽程式服務

註冊語彙基元是唯一且安全的。不過，用戶端應用程式 （或 GCM） 可能需要重新整理應用程式重新安裝或安全性問題時的註冊權杖。 基於這個理由，我們必須實作`InstanceIdListenerService`，回應來自 GCM 權杖重新整理要求。 

加入新的檔案稱為**InstanceIdListenerService.cs**和範本程式碼替換成下列： 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

加上註解`InstanceIdListenerService`具有下列屬性指出服務就是不具現化系統，而且它可以接收 GCM 註冊權杖 (也稱為*執行個體識別碼*) 重新整理要求： 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh`我們服務中的方法會啟動`RegistrationIntentService`，讓它可以攔截新的註冊權杖。


#### <a name="test-registration-with-gcm"></a>測試 GCM 的註冊

讓我們完全重新建置並執行應用程式。 如果您已成功收到 GCM 註冊語彙基元，註冊語彙基元應該顯示在 [輸出] 視窗中。 例如:  

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>處理下游訊息 

到目前為止，我們已經實作的程式碼是只有 「 安裝 」 程式碼;它會檢查以查看 Google Play 服務已安裝且與 GCM 和應用程式伺服器，準備我們的用戶端應用程式接收遠端通知交涉。 不過，我們還沒有實作程式碼會確實接收及處理下游的通知訊息。 若要這樣做，我們必須實作*GCM 接聽服務*。 這個服務接收應用程式伺服器中的主題訊息，並在本機以通知廣播它們。 我們會實作此服務之後，我們將建立測試的程式，將訊息傳送至 GCM，以便我們可以看到如果我們實作能正常運作。 


#### <a name="add-a-notification-icon"></a>加入通知圖示

讓我們先加入我們的通知啟動時，會出現在通知區域中的小圖示。 您可以複製[這個圖示](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)至您的專案或建立您自己自訂的圖示。 圖示檔案**ic_stat_button_click.png**並將它複製到**資源/drawable**資料夾。 請務必使用**新增 > 現有項目...**專案中包含這個圖示檔。


#### <a name="implement-a-gcm-listener-service"></a>實作 GCM 接聽程式服務

加入新的檔案稱為**GcmListenerService.cs**和範本程式碼替換成下列：

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

讓我們看看的每個區段，我們`GcmListenerService`了解其運作方式。 

首先，我們加上註解`GcmListenerService`，指出此服務並不會具現化系統，而且我們包括意圖，表示它會接收 GCM 訊息篩選的屬性： 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

當`GcmListenerService`GCM，從接收訊息`OnMessageReceived`叫用方法。 這個方法會擷取訊息的內容從傳入的`Bundle`，記錄訊息內容 （因此我們可以在 [輸出] 視窗中檢視），並呼叫`SendNotification`啟動本機通知接收的訊息內容： 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification`方法會使用`Notification.Builder`來建立該通知上方，然後使用`NotificationManager`啟動的通知。 實際上，這會將遠端通知訊息轉換成要呈現給使用者的本機通知。
如需有關使用`Notification.Builder`和`NotificationManager`，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。


#### <a name="declare-the-receiver-in-the-manifest"></a>收件者資訊清單中的宣告

我們可以從 GCM 接收訊息之前，我們必須宣告 Android 資訊清單中的 GCM 接聽程式。 開始編輯**AndroidManifest.xml**和取代`<application>`> 一節，以下列 XML: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

在上述 XML 中，變更*YOUR_PACKAGE_NAME*到用戶端應用程式專案的封裝名稱。 在逐步解說範例中，封裝名稱是`com.xamarin.gcmexample`。 

讓我們看看此 XML 中的每個設定的作用：

<table>
    <thead>
        <tr>
            <th>設定</th>
            <th>描述</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>com.google.android.gms.gcm.GcmReceiver</code></td>
            <td>宣告我們的應用程式實作 GCM 接收器，以便擷取和處理內送的推播通知訊息。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.permission.SEND</code></td>
            <td>宣告，只有 GCM 伺服器可以直接傳送訊息給應用程式。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.intent.RECEIVE</code></td> 
            <td>意圖廣告，我們的應用程式會處理來自 GCM 的廣播的訊息的篩選條件。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.intent.REGISTRATION</code></td>
            <td>意圖篩選通知，我們的應用程式會處理新的註冊方式 （也就是我們已經實作的執行個體識別碼接聽程式服務）。</td>
        </tr>
    </tbody>
</table>

或者，您可以在其中裝飾`GcmListenerService`這些屬性，而不是 XML; 在這裡我們指定在**AndroidManifest.xml** ，讓您更輕鬆地遵循程式碼範例。 


### <a name="create-a-message-sender-to-test-the-app"></a>建立測試應用程式的訊息寄件者

讓我們將 C# 桌面的主控台應用程式專案加入方案，並呼叫它**MessageSender**。 我們將使用此主控台應用程式來模擬應用程式伺服器&ndash;它會傳送通知訊息給**ClientApp**透過 GCM。 


#### <a name="add-the-jsonnet-package"></a>新增 Json.NET 封裝

在此主控台應用程式中，我們要建置的 JSON 承載，包含我們想要傳送給用戶端應用程式的通知訊息。 我們將使用**Json.NET**封裝中**MessageSender**讓您更輕鬆地建立所需的 GCM 的 JSON 物件。 在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 封裝...**; 在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**. 

我們來搜尋**Json.NET**封裝，並將它安裝在專案中： 

[ ![安裝 Json.NET 封裝](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png)


#### <a name="add-a-reference-to-systemnethttp"></a>將參考加入 System.Net.Http

我們還需要將參考加入`System.Net.Http`，以便我們可以具現化`HttpClient`將我們的測試訊息傳送至 GCM。 在**MessageSender**專案，以滑鼠右鍵按一下**參考 > 加入參考**向下捲動，直到您看到**System.Net.Http**。 核取記號旁**System.Net.Http**按一下**確定**。 


#### <a name="implement-code-that-sends-a-test-message"></a>傳送測試訊息的實作程式碼

在**MessageSender**，編輯**Program.cs**並取代為下列程式碼的內容：

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

在上述程式碼中，變更*YOUR_API_KEY*至用戶端應用程式專案的應用程式開發介面索引鍵。 

此測試應用程式伺服器下列 JSON 格式將訊息傳送至 GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM，依次轉送至用戶端應用程式此訊息。 讓我們來建置**MessageSender**開啟主控台視窗，我們可以從命令列執行它。


<a name="tryit" />

### <a name="try-it"></a>請嘗試

現在我們已經準備好測試我們的用戶端應用程式。 如果您使用模擬器，或如果您的裝置透過 Wi-fi 通訊的 GCM，您必須透過取得 GCM 訊息在防火牆上開啟下列 TCP 連接埠： 5228、 5229 和 5230。

啟動用戶端應用程式，並查看 [輸出] 視窗。 之後`RegistrationIntentService`成功接收，註冊來自 GCM 語彙基元時，[輸出] 視窗應該會顯示語彙基元，類似於下列的記錄輸出：

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此時用戶端應用程式已準備好接收遠端的通知訊息。 從命令列執行**MessageSender.exe**程式，將"Hello，Xamarin"通知訊息傳送至用戶端應用程式。
如果您尚未建立**MessageSender**專案，請立刻執行。

若要執行**MessageSender.exe**在 Visual Studio 中，開啟 命令提示字元，將變更為**MessageSender/bin/Debug**目錄，並執行命令直接：

```cmd
MessageSender.exe
```

若要執行**MessageSender.exe**下適用於 Mac 的 Visual Studio，開啟 終端機工作階段，將變更為**MessageSender/bin/Debug**目錄和執行使用 mono **MessageSender.exe** 

```bash
mono MessageSender.exe
```

它可能需要一分鐘的時間傳播到 GCM 和回向您的用戶端應用程式訊息。 如果已成功接收訊息時，我們應該會看到類似於 [輸出] 視窗中的下列輸出： 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外，您應該會注意到新的通知圖示已出現在通知紙匣： 

[ ![Notiication 圖示會出現在裝置上](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png)

當您開啟檢視的通知的通知紙匣時，您應該會看到我們遠端通知：

[ ![會顯示通知訊息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png)

恭喜，您的應用程式接收到其第一個遠端通知 ！

請注意，是否應用程式會強制停止，不會再將收到 GCM 訊息。 若要繼續通知強制停止後，應用程式必須用手動方式重新啟動。 如需此 Android 原則的詳細資訊，請參閱[啟動已停止的應用程式上的控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)和這[堆疊溢位 post](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

 
## <a name="summary"></a>總結

本逐步解說詳細 Xamarin.Android 應用程式中實作遠端通知的步驟。 它描述如何安裝 GCM 通訊所需的其他封裝，而它說明如何設定 GCM 伺服器的存取權的應用程式權限。 它提供說明如何檢查 Google Play 服務存在、 如何實作註冊意圖服務和執行個體識別碼接聽程式服務，會與 GCM 交涉的註冊權杖，以及如何實作 GCM 接聽程式的範例程式碼服務會接收及處理遠端通知訊息。 最後，我們會實作要將測試通知傳送至 GCM 透過我們的用戶端應用程式的命令列測試程式。 


## <a name="related-links"></a>相關連結

- [GCM RemoteNotifications （範例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
