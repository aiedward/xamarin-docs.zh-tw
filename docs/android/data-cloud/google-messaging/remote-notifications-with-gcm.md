---
title: Google 雲端通訊的遠端通知
description: 本逐步解說提供如何使用 Google 雲端通訊在 Xamarin Android 應用程式中執行遠端通知（也稱為推播通知）的逐步說明。 其中說明您必須執行才能與 Google 雲端通訊（GCM）通訊的各種類別，它會說明如何設定 Android 資訊清單中的許可權以存取 GCM，並示範使用範例測試程式的端對端訊息。
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: a59f824e1d97966d8d0af92bc9bbcc8d80fcfa4d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249920"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Google 雲端通訊的遠端通知

> [!WARNING]
> 從2018年4月10日起，Google 已淘汰的 GCM。 下列檔和範例專案可能無法再進行維護。 Google 的 GCM 伺服器和用戶端 Api 將會在 2019 5 月29日之後移除。 Google 建議您將 GCM 應用程式遷移至 Firebase 雲端通訊（FCM）。 如需有關 GCM 淘汰和遷移的詳細資訊，請參閱[Google 雲端通訊-](https://developers.google.com/cloud-messaging/)已被取代。
>
> 若要開始使用 Firebase Cloud 通訊與 Xamarin 的遠端通知，請參閱[使用 FCM 的遠端通知](remote-notifications-with-fcm.md)。

_本逐步解說提供如何使用 Google 雲端通訊在 Xamarin Android 應用程式中執行遠端通知（也稱為推播通知）的逐步說明。其中說明您必須執行才能與 Google 雲端通訊（GCM）通訊的各種類別，它會說明如何設定 Android 資訊清單中的許可權以存取 GCM，並示範使用範例測試程式的端對端訊息。_

## <a name="gcm-notifications-overview"></a>GCM 通知總覽

在此逐步解說中，我們將建立使用 Google 雲端通訊（GCM）來執行遠端通知（也稱為*推播通知*）的 Xamarin Android 應用程式。 我們將會實行使用 GCM 進行遠端通訊的各種意圖和接聽程式服務，我們會使用模擬應用程式伺服器的命令列程式來測試我們的執行。 

繼續進行本逐步解說之前，您必須先取得使用 Google GCM 伺服器所需的認證;[Google 雲端通訊](~/android/data-cloud/google-messaging/google-cloud-messaging.md)中會說明此程式。 特別是，您需要有*API 金鑰*和傳送者*識別碼*，才能插入本逐步解說中所提供的範例程式碼。 

我們將使用下列步驟來建立具備 GCM 功能的 Xamarin 用戶端應用程式：

1. 安裝與 GCM 伺服器通訊所需的其他套件。
2. 設定應用程式許可權以存取 GCM 伺服器。
3. 執行程式碼以檢查 Google Play Services 是否存在。 
4. 針對註冊權杖，執行與 GCM 協商的註冊意圖服務。
5. 執行可接聽 GCM 註冊權杖更新的實例識別碼接聽程式服務。
6. 執行 GCM 接聽程式服務，以透過 GCM 接收來自應用程式伺服器的遠端訊息。

此應用程式將使用稱為*主題訊息*的新 GCM 功能。 在主題訊息中，應用程式伺服器會將訊息傳送至主題，而不是個別裝置的清單。 訂閱該主題的裝置可以接收主題訊息做為推播通知。 如需 GCM 主題訊息的詳細資訊，請參閱 Google 的[實施主題訊息](https://developers.google.com/cloud-messaging/topic-messaging)。 

當用戶端應用程式準備就緒時，我們將會執行命令C#行應用程式，透過 GCM 將推播通知傳送至用戶端應用程式。 

## <a name="walkthrough"></a>逐步解說

首先，讓我們建立一個新的空白解決方案，稱為 **[] remotenotifications**。 接下來，讓我們將新的 Android 專案新增至以**Android 應用程式**範本為基礎的解決方案。 讓我們來呼叫這個專案**ClientApp**。 （如果您不熟悉如何建立 Xamarin Android 專案，請參閱[Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md)）。**ClientApp**專案會包含會透過 GCM 接收遠端通知之 Xamarin. Android 用戶端應用程式的程式碼。 

### <a name="add-required-packages"></a>新增必要的套件

我們必須先安裝用來與 GCM 通訊的數個套件，才可以執行用戶端應用程式程式碼。 此外，我們還必須將 Google Play 商店應用程式新增至我們的裝置（如果尚未安裝）。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>新增 Xamarin Google Play Services GCM 封裝

若要從 Google 雲端通訊接收訊息，裝置上必須有[Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)架構。 若沒有此架構，Android 應用程式就無法接收來自 GCM 伺服器的訊息。 當 Android 裝置開機時，Google Play Services 會在背景中執行，並無訊息地接聽 GCM。 當這些訊息抵達時，Google Play Services 會將訊息轉換為意圖，然後將這些意圖廣播給已為其註冊的應用程式。 

在 Visual Studio 中，以滑鼠右鍵按一下 **參考 > 管理 NuGet 套件 ...** ;在 Visual Studio for Mac 中，以滑鼠右鍵按一下 **套件 > 新增封裝**...。搜尋**Xamarin Google Play Services-GCM** ，並將此套件安裝到**ClientApp**專案： 

[![安裝 Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

當您安裝**xamarin Google Play Services-GCM**時，會自動安裝**Xamarin Google Play Services 基底**。 如果您收到錯誤，請將專案的 [*最低 Android 至目標*] 設定變更為 [**使用 SDK 版本編譯**] 以外的值，然後再次嘗試安裝 NuGet。 

接著，編輯**MainActivity.cs**並新增下列`using`語句：

```csharp
using Android.Gms.Common;
using Android.Util;
```

這可讓程式碼使用 Google Play Services GMS 套件中的型別，並加入我們將用來追蹤交易與 GMS 的記錄功能。 

#### <a name="google-play-store"></a>Google Play 商店

若要從 GCM 接收訊息，必須在裝置上安裝 Google Play 商店應用程式。 （每當裝置上安裝 Google Play 應用程式時，也會一併安裝 Google Play 商店，因此它很可能已經安裝在您的測試裝置上）。若沒有 Google Play，Android 應用程式就無法接收來自 GCM 的訊息。 如果您尚未在裝置上安裝 Google Play 商店應用程式，請造訪[Google Play](https://support.google.com/googleplay)網站下載並安裝 Google Play。 

或者，您可以使用執行 Android 2.2 或更新版本的 Android 模擬器，而不是測試裝置（您不需要在 Android 模擬器上安裝 Google Play 商店）。 不過，如果您使用模擬器，則必須使用 Wi-fi 連線到 GCM，而且您必須在 Wi-fi 防火牆中開啟數個埠，如本逐步解說稍後所述。 

### <a name="set-the-package-name"></a>設定封裝名稱

在[Google 雲端通訊](~/android/data-cloud/google-messaging/google-cloud-messaging.md)中，我們為啟用 GCM 的應用程式指定了套件名稱（此套件名稱也可作為與我們的 API 金鑰和寄件者識別碼相關聯的*應用程式識別碼*）。 讓我們開啟**ClientApp**專案的屬性，並將封裝名稱設定為這個字串。 在此範例中，我們將套件名稱設定`com.xamarin.gcmexample`為：

[![設定封裝名稱](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

請注意，如果此套件名稱不*完全*符合我們在 Google 開發人員主控台中輸入的套件名稱，用戶端應用程式將無法從 GCM 接收註冊權杖。 

### <a name="add-permissions-to-the-android-manifest"></a>將許可權新增至 Android 資訊清單

Android 應用程式必須先設定下列許可權，才能接收來自 Google 雲端通訊的通知： 

- `com.google.android.c2dm.permission.RECEIVE`&ndash;授與應用程式的許可權，以註冊和接收來自 Google 雲端通訊的訊息。 （這是`c2dm`什麼意思？ 這代表_雲端到裝置的通訊_，這是 GCM 的現在已淘汰的前身。 
    GCM 仍然會`c2dm`在其許多許可權字串中使用）。 

- `android.permission.WAKE_LOCK`&ndash; （選擇性）在接聽訊息時，防止裝置 CPU 進入睡眠狀態。 

- `android.permission.INTERNET`&ndash;授與網際網路存取權，讓用戶端應用程式可以與 GCM 通訊。 

- *package_name 會向 Android* `.permission.C2D_MESSAGE`註冊應用程式，並要求許可權以獨佔方式接收所有的 C2D （雲端到裝置）訊息。 &ndash; *Package_name*前置詞與您的應用程式識別碼相同。 

我們會在 Android 資訊清單中設定這些許可權。 讓我們編輯**androidmanifest.xml** ，並將內容取代為下列 xml： 

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

在上述 XML 中，將*YOUR_PACKAGE_NAME*變更為用戶端應用程式專案的套件名稱。 例如，`com.xamarin.gcmexample`。 

### <a name="check-for-google-play-services"></a>檢查是否有 Google Play Services

在此逐步解說中，我們將使用 UI 中的單一來建立`TextView`一個簡單的應用程式。 此應用程式不會直接指出與 GCM 的互動。 相反地，我們會監看 [輸出] 視窗，以查看我們的應用程式如何與 GCM 交握，而我們會在通知紙匣收到新通知時加以檢查。 

首先，讓我們建立訊息區域的版面配置。 編輯**axml** ，並將內容取代為下列 XML： 

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

儲存**axml**並加以關閉。

當用戶端應用程式啟動時，我們想要確認 Google Play Services 可供使用，然後再嘗試聯繫 GCM。 編輯**MainActivity.cs** ，並將``count``執行個體變數宣告取代為下列執行個體變數宣告： 

```csharp
TextView msgText;
```

接下來，將下列方法新增至**MainActivity**類別： 

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

此程式碼會檢查裝置，以查看是否已安裝 Google Play Services APK。 如果未安裝，訊息區域中會顯示一則訊息，指示使用者從 Google Play 商店下載 APK （或在裝置的系統設定中啟用）。 因為我們想要在用戶端應用程式啟動時執行這項檢查，所以我們會在結尾`OnCreate`加入這個方法的呼叫。 

接下來，將`OnCreate`方法取代為下列程式碼：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

此程式碼會檢查 Google Play Services APK 是否存在，並將結果寫入訊息區域。 

讓我們完全重建並執行應用程式。 您應該會看到如下列螢幕擷取畫面所示的畫面： 

[![Google Play Services 可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

如果您沒有取得此結果，請確認您的裝置上已安裝 Google Play Services APK，而且**Xamarin GOOGLE PLAY SERVICES GCM**套件已新增至您的**ClientApp**專案（如先前所述）。 如果您收到組建錯誤，請嘗試清除方案，然後重新建立專案。 

接下來，我們將撰寫程式碼來聯絡 GCM，並取回註冊權杖。

### <a name="register-with-gcm"></a>向 GCM 註冊

在應用程式可以從應用程式伺服器接收遠端通知之前，它必須向 GCM 註冊並取回註冊權杖。 向 GCM 註冊應用程式的工作是由`IntentService`我們建立的所處理。 我們`IntentService`會執行下列步驟： 

1. 會使用[InstanceID](https://developers.google.com/instance-id/) API 來產生安全性權杖，以授權我們的用戶端應用程式存取應用程式伺服器。 傳回時，我們會從 GCM 取得註冊權杖。

2. 將註冊權杖轉送至應用程式伺服器（如果應用程式伺服器需要的話）。

3. 訂閱一或多個通知主題通道。

在我們執行此`IntentService`工作之後，我們會進行測試，以查看我們是否從 GCM 取得註冊權杖。

新增名為**RegistrationIntentService.cs**的新檔案，並將範本程式碼取代為下列內容：

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

在上述範例程式碼中，將*YOUR_SENDER_ID*變更為用戶端應用程式專案的寄件者識別碼。 若要取得專案的寄件者識別碼： 

1. 登入[Google Cloud 主控台](https://console.cloud.google.com/)，然後從下拉式功能表中選取您的專案名稱。 在針對專案顯示的 [**專案資訊**] 窗格中，按一下 [**移至專案設定**]：

    [![選取 XamarinGCM 專案](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2. 在 [**設定**] 頁面上，找出 [**專案編號** &ndash; ]，這是您專案的 [寄件者識別碼]：

    [![顯示的專案編號](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

我們想要在應用`RegistrationIntentService`程式開始執行時啟動我們。 編輯**MainActivity.cs**並修改`OnCreate` `RegistrationIntentService`方法，以便我們在檢查 Google Play Services 是否存在之後啟動： 

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

現在讓我們看一下的`RegistrationIntentService`各個區段，以瞭解其運作方式。 

首先，我們會使用`RegistrationIntentService`下列屬性來標注我們的服務，以指出系統不會將我們的服務具現化： 

```csharp
[Service (Exported = false)]
```

此`RegistrationIntentService`函式會將背景工作執行緒命名為*RegistrationIntentService* ，以便更輕鬆地進行調試。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

的核心功能`RegistrationIntentService`位於`OnHandleIntent`方法中。 讓我們逐步解說這段程式碼，以瞭解它如何向 GCM 註冊應用程式。

#### <a name="request-a-registration-token"></a>要求註冊權杖

`OnHandleIntent`會先呼叫 Google 的[InstanceID. GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法，以向 GCM 要求註冊權杖。 我們會將此程式碼`lock`包裝在中，以防止多個註冊意圖同時&ndash;發生`lock`的可能性，確保這些意圖會依序處理。 如果我們無法取得註冊權杖，則會擲回例外狀況，並記錄錯誤。 如果註冊成功， `token`會設定為我們從 GCM 傳回的註冊權杖： 

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

#### <a name="forward-the-registration-token-to-the-app-server"></a>將註冊權杖轉送至應用程式伺服器

如果我們取得註冊權杖（也就是未擲回任何例外狀況），我們`SendRegistrationToAppServer`會呼叫，將使用者的註冊權杖與應用程式所維護的伺服器端帳戶（如果有的話）產生關聯。 由於此實施取決於應用程式伺服器的設計，因此在此提供空的方法： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情況下，應用程式伺服器不需要使用者的註冊權杖;在此情況下，可以省略這個方法。 當註冊權杖傳送至應用程式伺服器時， `SendRegistrationToAppServer`應該維護布林值，以指出是否已將權杖傳送至伺服器。 如果此布林值為 false `SendRegistrationToAppServer` ，則會將權杖傳送至&ndash;應用程式伺服器，否則會在先前的呼叫中將權杖傳送至應用程式伺服器。 

#### <a name="subscribe-to-the-notification-topic"></a>訂閱通知主題

接下來，我們會`Subscribe`呼叫方法，向 GCM 指出我們想要訂閱通知主題。 在`Subscribe`中，我們會呼叫[GcmPubSub](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) ，以將我們的用戶端應用程式訂閱至`/topics/global`下的所有訊息：

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

如果我們收到通知訊息，應用程式`/topics/global`伺服器必須將其傳送給。 請注意，只要應用程式`/topics`伺服器和用戶端應用程式都同意這些名稱，下的主題名稱就可以是您想要的任何專案。 （在這裡，我們選擇了`global`名稱，表示我們想要接收應用程式伺服器所支援之所有主題的訊息）。 

如需伺服器端上 GCM 主題訊息的相關資訊，請參閱 Google 的傳送[訊息至主題](https://developers.google.com/cloud-messaging/topic-messaging)。 

#### <a name="implement-an-instance-id-listener-service"></a>執行實例識別碼接聽程式服務

註冊權杖是獨一無二且安全的;不過，用戶端應用程式（或 GCM）可能需要在應用程式重新安裝或發生安全性問題時重新整理註冊權杖。 基於這個理由，我們必須執行`InstanceIdListenerService` ，以回應 GCM 中的權杖重新整理要求。 

新增名為**InstanceIdListenerService.cs**的新檔案，並將範本程式碼取代為下列內容： 

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

使用`InstanceIdListenerService`下列屬性標注，以指出服務不會由系統具現化，而且它可以接收 GCM 註冊權杖（也稱為*實例識別碼*）重新整理要求： 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

服務`OnTokenRefresh`中的方法會`RegistrationIntentService`啟動，讓它可以攔截新的註冊權杖。

#### <a name="test-registration-with-gcm"></a>使用 GCM 測試註冊

讓我們完全重建並執行應用程式。 如果您成功地從 GCM 接收註冊權杖，註冊權杖應該會顯示在 [輸出] 視窗中。 例如： 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>處理下游訊息 

到目前為止，我們所執行的程式碼只是「設定」程式碼;它會檢查是否已安裝 Google Play Services，並與 GCM 和應用程式伺服器協商，以準備我們的用戶端應用程式以接收遠端通知。 不過，我們尚未執行實際接收和處理下游通知訊息的程式碼。 若要這麼做，我們必須執行*GCM*接聽程式服務。 此服務會從應用程式伺服器接收主題訊息，並在本機將它們廣播為通知。 在我們執行此服務之後，我們會建立一個測試程式，將訊息傳送至 GCM，讓我們可以查看我們的執行是否正常運作。 

#### <a name="add-a-notification-icon"></a>新增通知圖示

讓我們先新增一個小圖示，在通知啟動時出現在通知區域中。 您可以將[此圖示](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)複製到您的專案，或建立您自己的自訂圖示。 我們會將圖示檔案命名為**ic_stat_button_click** ，並將它複製到**Resources/可繪製**資料夾。 請記得使用 [**加入 > 現有專案**...]，在您的專案中包含此圖示檔。

#### <a name="implement-a-gcm-listener-service"></a>執行 GCM 接聽程式服務

新增名為**GcmListenerService.cs**的新檔案，並將範本程式碼取代為下列內容：

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

讓我們看一下我們`GcmListenerService`的每個章節，以瞭解其運作方式。 

首先，我們會`GcmListenerService`以屬性標注，以指出系統不會將此服務具現化，而且我們包含意圖篩選準則來表示它會接收 GCM 訊息： 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

從`GcmListenerService` GCM 接收訊息時`OnMessageReceived` ，會叫用方法。 這個方法會從傳入的中`Bundle`解壓縮訊息內容，記錄訊息內容（因此我們可以在 [輸出] 視窗中查看），然後呼叫`SendNotification`以使用接收的訊息內容來啟動本機通知： 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

方法會使用`Notification.Builder`來建立通知，然後使用`NotificationManager`來啟動通知。 `SendNotification` 實際上，這會將遠端通知訊息轉換成本機通知，以向使用者呈現。
如需使用`Notification.Builder`和`NotificationManager`的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

#### <a name="declare-the-receiver-in-the-manifest"></a>在資訊清單中宣告接收者

我們必須先在 Android 資訊清單中宣告 GCM 接聽程式，才可以從 GCM 接收訊息。 讓我們編輯**androidmanifest.xml** ，並以下列 xml `<application>`取代區段： 

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

在上述 XML 中，將*YOUR_PACKAGE_NAME*變更為用戶端應用程式專案的套件名稱。 在我們的逐步解說範例中，封裝`com.xamarin.gcmexample`名稱是。 

讓我們看看此 XML 中的每個設定有何作用：

|設定|描述|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|宣告我們的應用程式會執行可捕獲和處理傳入推播通知訊息的 GCM 接收者。|
|`com.google.android.c2dm.permission.SEND`|宣告只有 GCM 伺服器可以直接將訊息傳送至應用程式。|
|`com.google.android.c2dm.intent.RECEIVE`|意圖篩選準則廣告，我們的應用程式會處理來自 GCM 的廣播訊息。|
|`com.google.android.c2dm.intent.REGISTRATION`|意圖篩選廣告：我們的應用程式會處理新的註冊意圖（也就是我們已實作為實例識別碼接聽程式服務）。|

或者，您可以使用`GcmListenerService`這些屬性來裝飾，而不是在 XML 中指定它們。在此，我們會在**androidmanifest.xml**中指定它們，讓程式碼範例更容易遵循。 

### <a name="create-a-message-sender-to-test-the-app"></a>建立訊息寄件者以測試應用程式

讓我們將C#桌面主控台應用程式專案新增至方案，並將其稱為**MessageSender**。 我們將使用此主控台應用程式來模擬應用程式&ndash;伺服器，它會透過 GCM 將通知訊息傳送至**ClientApp** 。 

#### <a name="add-the-jsonnet-package"></a>新增 Json.NET 套件

在此主控台應用程式中，我們會建立 JSON 承載，其中包含我們想要傳送至用戶端應用程式的通知訊息。 我們將在**MessageSender**中使用**Json.NET**套件，讓您更輕鬆地建立 GCM 所需的 Json 物件。 在 Visual Studio 中，以滑鼠右鍵按一下 **參考 > 管理 NuGet 套件 ...** ;在 Visual Studio for Mac 中，以滑鼠右鍵按一下 **套件 > 新增封裝**...。 

讓我們搜尋**Json.NET**套件，並將它安裝在專案中： 

[![安裝 Json.NET 套件](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)

#### <a name="add-a-reference-to-systemnethttp"></a>新增對系統 .Net 的參考。 Http

我們也需要新增的參考`System.Net.Http` ，讓我們可以具現`HttpClient`化，以便將測試訊息傳送至 GCM。 在**MessageSender**專案中，以滑鼠右鍵按一下 [**參考] > [加入參考**] 並向下滾動，直到您看到 [ **System .net. Http**] 為止。 在 [ **System .net. Http** ] 旁加上核取記號，然後按一下 **[確定]** 。 

#### <a name="implement-code-that-sends-a-test-message"></a>執行傳送測試訊息的程式碼

在**MessageSender**中，編輯**Program.cs** ，並將內容取代為下列程式碼：

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

在上述程式碼中，將*YOUR_API_KEY*變更為用戶端應用程式專案的 API 金鑰。 

此測試應用程式伺服器會將下列 JSON 格式的訊息傳送至 GCM：

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

接著，GCM 會將此訊息轉送到您的用戶端應用程式。 讓我們建立**MessageSender** ，並開啟主控台視窗，我們可以從命令列執行它。

### <a name="try-it"></a>請嘗試

現在我們已經準備好測試用戶端應用程式。 如果您使用模擬器，或如果您的裝置透過 Wi-fi 與 GCM 通訊，您必須在防火牆上開啟下列 TCP 埠，以取得 GCM 訊息：5228、5229和5230。

啟動您的用戶端應用程式，並查看 [輸出] 視窗。 `RegistrationIntentService`成功從 GCM 接收註冊權杖之後，[輸出] 視窗應該會顯示具有記錄輸出的權杖，如下所示：

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此時，用戶端應用程式已準備好接收遠端通知訊息。 從命令列執行**MessageSender**程式，將 "Hello，Xamarin" 通知訊息傳送至用戶端應用程式。
如果您尚未建立**MessageSender**專案，請立即這麼做。

若要在 Visual Studio 下執行**MessageSender** ，請開啟命令提示字元，並變更為**MessageSender/bin/Debug**目錄，然後直接執行命令：

```cmd
MessageSender.exe
```

若要在 Visual Studio for Mac 下執行**MessageSender** ，請開啟終端機會話，變更為**MessageSender/bin/Debug**目錄，然後使用 mono 來執行**MessageSender .exe** 

```bash
mono MessageSender.exe
```

訊息可能需要一分鐘的時間，才能透過 GCM 傳播至您的用戶端應用程式。 如果成功接收訊息，我們應該會在 [輸出] 視窗中看到類似下列的輸出： 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外，您應該注意到通知紙匣中出現了新的通知圖示： 

[![裝置上出現通知圖示](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

當您開啟通知紙匣以查看通知時，您應該會看到我們的遠端通知：

[![隨即顯示通知訊息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

恭喜，您的應用程式已收到第一個遠端通知！

請注意，如果應用程式是強制停止的，則不會再收到 GCM 訊息。 若要在強制停止後繼續通知，必須手動重新開機應用程式。 如需此 Android 原則的詳細資訊，請參閱[在已停止的應用程式上啟動控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)和此[堆疊溢位文章](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

## <a name="summary"></a>總結

本逐步解說詳述在 Xamarin Android 應用程式中執行遠端通知的步驟。 文中說明如何安裝 GCM 通訊所需的其他套件，並說明如何設定應用程式許可權以存取 GCM 伺服器。 它提供範例程式碼，說明如何檢查是否存在 Google Play Services、如何執行註冊意圖服務和實例識別碼接聽程式服務，以與 GCM 協商以進行註冊權杖，以及如何執行 GCM 接聽程式接收和處理遠端通知訊息的服務。 最後，我們已實行命令列測試程式，透過 GCM 將測試通知傳送至用戶端應用程式。 

## <a name="related-links"></a>相關連結

- [GCM [] Remotenotifications （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
