---
title: 使用 Google 雲端通訊的遠端通知
description: 本逐步解說提供如何使用 Google Cloud Messaging 實作遠端通知 （也稱為 「 推播通知 」） 的逐步說明，Xamarin.Android 應用程式中。 它描述各種通訊與 Google 雲端通訊 (GCM)，您必須實作的類別，它說明如何設定權限以存取 GCM，Android 資訊清單，它會示範端對端傳訊範例測試程式。
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: be96683a2e63ed802169543dcee55a3431e42130
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528802"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>使用 Google 雲端通訊的遠端通知

_本逐步解說提供如何使用 Google Cloud Messaging 實作遠端通知 （也稱為 「 推播通知 」） 的逐步說明，Xamarin.Android 應用程式中。它描述各種通訊與 Google 雲端通訊 (GCM)，您必須實作的類別，它說明如何設定權限以存取 GCM，Android 資訊清單，它會示範端對端傳訊範例測試程式。_

> [!NOTE]
> GCM 已被取代[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)(FCM)。
> GCM 伺服器和用戶端 Api[已被取代](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)和將無法再使用立即於 2019 年 4 月 11 日。

## <a name="gcm-notifications-overview"></a>GCM 通知概觀

在本逐步解說中，我們將建立來實作遠端通知使用 Google 雲端通訊 (GCM) 的 Xamarin.Android 應用程式 (也稱為*推播通知*)。 我們將實作使用 GCM，適用於遠端訊息的各種意圖和接聽程式服務，我們將測試我們的實作會模擬應用程式伺服器的命令列程式。 

請注意，Firebase 雲端通訊 (FCM) 是新版 GCM &ndash; Google 強烈建議使用 FCM，而不是 GCM。 如果您目前使用 GCM，升級至 FCM 建議。 如需 FCM 的詳細資訊，請參閱[Firebase 雲端通訊](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

您可以繼續進行本逐步解說之前，您必須取得所需的認證，以使用 Google 的 GCM 伺服器;會說明此程序[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)。 特別是，您必須*API 金鑰*並*寄件者識別碼*以插入本逐步解說的範例程式碼。 

若要建立已啟用 GCM 的 Xamarin.Android 用戶端應用程式，我們將使用下列步驟：

1.  安裝與 GCM 伺服器進行通訊所需的其他套件。
2.  設定 GCM 伺服器的存取權的應用程式權限。
3.  實作程式碼來檢查 Google Play 服務存在。 
4.  實作註冊意圖的服務，會交涉向 GCM 註冊權杖。
5.  實作執行個體識別碼接聽程式服務，它會接聽來自 GCM 註冊權杖的更新。
6.  實作應用程式伺服器透過 GCM 接收遠端訊息的 GCM 接聽程式服務。

此應用程式會使用新的 GCM 功能，稱為*主題傳訊*。 在主題傳訊中，應用程式伺服器會傳送訊息至主題，而不是個別裝置的清單。 該主題訂閱的裝置可以接收主題訊息為推播通知。 如需 GCM 主題傳訊的詳細資訊，請參閱 Google[實作主題傳訊](https://developers.google.com/cloud-messaging/topic-messaging)。 

當用戶端應用程式就緒時，我們將實作命令列C#將推播通知傳送至 GCM 透過用戶端應用程式的應用程式。 

## <a name="walkthrough"></a>逐步解說

若要開始，讓我們建立一個新的空白解決方案，稱為**RemoteNotifications**。 接下來，讓我們將新的 Android 專案加入至此方案，根據**Android 應用程式**範本。 讓我們來呼叫此專案**ClientApp**。 (如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)**ClientApp**專案會包含接收遠端通知 GCM 透過 Xamarin.Android 用戶端應用程式的程式碼。 

### <a name="add-required-packages"></a>新增必要的套件

我們可以實作我們的用戶端應用程式程式碼之前，我們必須安裝與 GCM 通訊，我們將使用的數個封裝。 此外，我們必須新增 Google Play 商店應用程式，到我們的裝置，如果尚未安裝。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>加入 Xamarin Google Play Services GCM 封裝

若要從 Google Cloud Messaging、 接收訊息[Google Play 服務](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)framework 必須存在於裝置上。 如果沒有這個架構中，Android 應用程式無法接收來自 GCM 伺服器的訊息。 Google Play 服務在背景中執行時在 Android 裝置的電源已開啟，以無訊息模式接聽來自 GCM 的訊息。 這些訊息到達時，Google Play 服務會將訊息對應方式轉換，然後再廣播這些意圖，以便他們已註冊的應用程式。 

在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 套件...**; 在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**.搜尋**Xamarin Google Play 服務-GCM**並安裝此封裝**ClientApp**專案： 

[![安裝 Google Play 服務](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

當您安裝**Xamarin Google Play 服務-GCM**， **Xamarin Google Play 服務-基底**會自動安裝。 如果您收到錯誤，將專案的變更*最低 Android 目標*以外的值設定**使用的 SDK 版本進行編譯**並再試一次 NuGet 安裝。 

接著，編輯**MainActivity.cs**並新增下列`using`陳述式：

```csharp
using Android.Gms.Common;
using Android.Util;
```

這可讓類型在 Google Play Services GMS 套件中提供給我們的程式碼，並新增記錄功能，我們將用來追蹤與 GMS 我們交易。 

#### <a name="google-play-store"></a>Google Play 商店

若要從 GCM 接收訊息，Google Play 商店應用程式必須安裝在裝置上。 （只要在裝置上安裝 Google Play 應用程式，Google Play 商店也會安裝，因此很可能已經被安裝在您的測試裝置上。）沒有 Google Play 的 Android 應用程式無法接收來自 GCM 的訊息。 如果您還沒有安裝在裝置上的 Google Play 商店應用程式，請瀏覽[Google Play](https://support.google.com/googleplay)網站下載並安裝 Google Play。 

或者，您可以使用 Android 模擬器執行 Android 2.2 或更新版本，而不是測試裝置 （您不必安裝在 Android 模擬器上的 Google Play 商店）。 不過，如果您使用模擬器，您必須使用 Wi-fi 連線到 GCM，並在此逐步解說稍後所述，您必須在 Wi-fi 防火牆中開啟數個連接埠。 

### <a name="set-the-package-name"></a>設定封裝名稱

在  [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)，我們指定我們的 GCM 啟用應用程式的套件名稱 (此封裝名稱也可做為*應用程式識別碼*與我們的 API 金鑰和寄件者識別碼相關聯)。 讓我們開啟的內容**ClientApp**專案，然後將這個字串中的封裝名稱。 在此範例中，設定封裝名稱為`com.xamarin.gcmexample`:

[![設定封裝名稱](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

請注意，用戶端應用程式將無法接收自 GCM 註冊權杖，如果此套件名稱並不會*完全*符合我們輸入 Google 開發人員主控台中的封裝名稱。 

### <a name="add-permissions-to-the-android-manifest"></a>新增 Android 資訊清單的權限

Android 應用程式必須設定才能從 Google Cloud Messaging 接收通知的下列權限： 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; 授與我們註冊，並接收來自 Google 雲端通訊的訊息的應用程式的權限。 (用途`c2dm`表示？ 這代表_雲端到裝置傳訊_，也就是現在已被取代前置任務至 GCM。 
    仍會使用 GCM`c2dm`許多其權限字串中。) 

-   `android.permission.WAKE_LOCK` &ndash; （選擇性）防止裝置 CPU 進入睡眠時接聽訊息。 

-   `android.permission.INTERNET` &ndash; 授與網際網路存取，讓用戶端應用程式能夠與 GCM 通訊。 

-   *package_name* `.permission.C2D_MESSAGE` &ndash;向 Android 的應用程式，並要求權限以獨佔方式收到所有 C2D （雲端到裝置） 訊息。 *Package_name*前置詞相當於您的應用程式識別碼。 

在 Android 資訊清單中，我們會設定這些權限。 讓我們編輯**AndroidManifest.xml** ，並以下列 XML 取代內容： 

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

在上述 XML 中，變更*YOUR_PACKAGE_NAME*用戶端應用程式專案的封裝名稱。 例如， `com.xamarin.gcmexample` 。 

### <a name="check-for-google-play-services"></a>檢查 Google Play 服務

此逐步解說中，我們建立基本應用程式使用單一`TextView`在 UI 中。 此應用程式並未直接指出 GCM 的互動。 相反地，我們會監看 [輸出] 視窗，以查看如何使用 GCM，我們的應用程式交握抵達時，我們會查看新的通知的通知匣。 

首先，讓我們建立的訊息區域的配置。 編輯**Resources.layout.Main.axml** ，並以下列 XML 取代內容： 

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

儲存**Main.axml**並關閉它。

當用戶端應用程式啟動時，我們想要確認我們嘗試連絡 GCM 之前，可以使用 Google Play 服務。 編輯**MainActivity.cs** ，並取代``count``執行個體與下列的執行個體變數宣告的變數宣告： 

```csharp
TextView msgText;
```

接下來，新增下列方法加入**MainActivity**類別： 

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

此程式碼會檢查裝置，以檢查是否已安裝 Google Play Services APK。 如果未安裝，訊息會顯示在訊息區域，以指示使用者從 Google Play 商店下載 APK （或啟用裝置的系統設定 中）。 因為我們想要執行這項檢查，用戶端應用程式啟動時，我們將新增至這個方法的呼叫結尾`OnCreate`。 


接下來，將`OnCreate`為下列程式碼的方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

此程式碼會檢查 Google Play Services APK 的狀態，並將結果寫入至訊息的區域。 

讓我們完全重建，並執行應用程式。 您應該會看到一個畫面，看起來像下列螢幕擷取畫面： 

[![Google Play 服務可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

如果您未獲得此結果，確認在您的裝置，確認已安裝 Google Play Services APK **Xamarin Google Play 服務-GCM**封裝會新增至您**ClientApp**專案所述稍早。 如果您收到建置錯誤，請嘗試清除方案，並再次建置專案。 

接下來，我們將撰寫程式碼來連絡 GCM 註冊權杖。

### <a name="register-with-gcm"></a>向 GCM 註冊

應用程式可以從應用程式伺服器接收遠端通知之前，它必須向 GCM 註冊，並取得註冊權杖。 向 GCM 註冊我們的應用程式的工作由`IntentService`我們建立的。 我們`IntentService`會執行下列步驟： 

1.  會使用[InstanceID](https://developers.google.com/instance-id/)產生授與我們的用戶端應用程式，來存取應用程式伺服器的安全性權杖的 API。 我們的註冊權杖從取得 GCM。

2.  （如果應用程式伺服器會需要它），會將轉送至應用程式伺服器的註冊權杖。

3.  訂閱一或多個通知主題通道。

我們把它實作之後`IntentService`，我們將測試看看如果我們回到從取得註冊權杖 GCM。

加入新的檔名**RegistrationIntentService.cs**並以下列內容取代範本程式碼：


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

在上述的範例程式碼，變更*YOUR_SENDER_ID*至用戶端應用程式專案的寄件者識別碼。 若要取得您的專案中的寄件者識別碼： 

1.  登入[Google 雲端主控台](https://console.cloud.google.com/)，然後從下拉式功能表中選取您的專案名稱。 在 **專案資訊**窗格中顯示為您的專案中，按一下 **移至 專案設定**:

    [![選取 XamarinGCM 專案](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  在 **設定**頁面上，找出**專案編號**&ndash;這是您專案的寄件者識別碼：

    [![顯示的專案編號](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

我們想要啟動我們`RegistrationIntentService`我們的應用程式開始執行時。 編輯**MainActivity.cs** ，並修改`OnCreate`方法，讓我們`RegistrationIntentService`之後我們會檢查是否有 Google Play 服務已啟動： 

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

現在讓我們看看每個區段`RegistrationIntentService`若要了解其運作方式。 

首先，我們加上註解我們`RegistrationIntentService`與要指出我們的服務不是要具現化系統的下列屬性： 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService`建構函式名稱的背景工作執行緒*RegistrationIntentService*進行偵錯更容易。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

核心功能`RegistrationIntentService`位於`OnHandleIntent`方法。 讓我們逐步解說這段程式碼，以查看如何向 GCM 註冊我們的應用程式。


##### <a name="request-a-registration-token"></a>要求註冊權杖

`OnHandleIntent` 會先呼叫 Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法來要求來自 GCM 的註冊權杖。 將在這段程式碼`lock`防範可能會同時發生多個註冊意圖&ndash;`lock`可確保這些意圖會循序處理。 如果我們無法取得註冊權杖，會擲回例外狀況，我們會記錄錯誤。 如果註冊成功，`token`設為我們得到來自 GCM 註冊權杖： 

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

##### <a name="forward-the-registration-token-to-the-app-server"></a>註冊權杖轉送給應用程式伺服器

如果我們收到註冊權杖 （也就是沒有擲回例外狀況），我們呼叫`SendRegistrationToAppServer`關聯使用者的註冊權杖與伺服器端有更多的帳戶 （如果有的話），由維護我們的應用程式。 因為此實作取決於應用程式伺服器的設計，此處提供的空白方法： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情況下，應用程式伺服器不需要使用者的註冊權杖;在此情況下，您可以省略這個方法。 當註冊權杖傳送至應用程式伺服器，`SendRegistrationToAppServer`應該維護的布林值，指出是否將權杖傳送至伺服器。 如果為 false，這個布林值`SendRegistrationToAppServer`會將權杖傳送至應用程式伺服器&ndash;語彙基元，否則為已傳送前一次呼叫中的應用程式伺服器。 


##### <a name="subscribe-to-the-notification-topic"></a>訂閱通知主題

接下來，我們呼叫我們`Subscribe`方法，以表示至我們想要訂閱通知主題的 GCM。 在  `Subscribe`，我們稱之為[GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;)訂閱我們的用戶端應用程式下的所有訊息的 API `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

應用程式伺服器必須傳送通知訊息給`/topics/global`如果我們來接收它們。 請注意，本主題下名稱`/topics`可以是任何想要的話，只要應用程式伺服器和用戶端應用程式都同意這些名稱。 (這裡，我們選擇名稱`global`指出我們想要接收訊息的應用程式伺服器所支援的所有主題。) 

GCM 主題傳訊的伺服器端上的相關資訊，請參閱 Google[傳送訊息至主題](https://developers.google.com/cloud-messaging/topic-messaging)。 


#### <a name="implement-an-instance-id-listener-service"></a>實作執行個體識別碼接聽程式服務

註冊權杖是唯一且安全的;不過，用戶端應用程式 （或 GCM），可能需要重新整理應用程式重新安裝或安全性問題的事件註冊語彙基元中。 基於這個理由，我們必須實作`InstanceIdListenerService`，回應來自 GCM 權杖重新整理要求。 

加入新的檔名**InstanceIdListenerService.cs**並以下列內容取代範本程式碼： 

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

標註`InstanceIdListenerService`具有下列屬性以指出服務是不具現化系統，且能夠接收 GCM 註冊權杖 (也稱為*執行個體識別碼*) 重新整理要求： 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh`方法，在我們的服務會啟動`RegistrationIntentService`，讓它可以攔截新的註冊權杖。


#### <a name="test-registration-with-gcm"></a>測試向 GCM 註冊

讓我們完全重建，並執行應用程式。 如果您已成功收到 GCM 註冊權杖，註冊權杖，應該會顯示在 [輸出] 視窗中。 例如:  

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>處理下游的訊息 

到目前為止，我們已實作的程式碼是只有 「 設定 」 程式碼;它會檢查 Google Play 服務是否已安裝，並會交涉使用 GCM 和應用程式伺服器，以接收遠端通知時，準備我們的用戶端應用程式。 不過，我們尚未實作程式碼，實際上會接收並處理下游的通知訊息。 若要這樣做，我們必須實作*GCM 接聽程式服務*。 此服務接收應用程式伺服器中的主題訊息，並在本機廣播它們做為告知。 我們實作這項服務後，我們將建立測試程式，以將訊息傳送至 GCM，以便我們可以看到如果我們的實作能正常運作。 


#### <a name="add-a-notification-icon"></a>新增通知圖示

讓我們先新增啟動我們的通知時，會出現在通知區域中的小圖示。 您可以複製[這個圖示](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)至您的專案或建立您自己的自訂圖示。 我們會將檔案命名為圖示**ic_stat_button_click.png**將它複製到**可繪製資源/** 資料夾。 請務必使用**新增 > 現有項目...** 在您的專案中包含此圖示檔案。


#### <a name="implement-a-gcm-listener-service"></a>實作 GCM 接聽程式服務

加入新的檔名**GcmListenerService.cs**並以下列內容取代範本程式碼：

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

讓我們看看每一節我們`GcmListenerService`若要了解其運作方式。 

首先，我們加上註解`GcmListenerService`，表示此服務並不會具現化系統，並納入了意圖篩選，以指出它接收 GCM 訊息屬性： 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

當`GcmListenerService`收到來自 GCM，訊息`OnMessageReceived`叫用方法。 這個方法會擷取從傳入的訊息內容`Bundle`，記錄訊息內容 （因此我們可以在 [輸出] 視窗中檢視），並呼叫`SendNotification`啟動接收到的訊息內容的本機通知： 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification`方法會使用`Notification.Builder`來建立該通知上方，然後使用`NotificationManager`啟動的通知。 實際上，這會將遠端通知訊息轉換成要呈現給使用者的本機通知。
如需使用詳細資訊`Notification.Builder`並`NotificationManager`，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。


#### <a name="declare-the-receiver-in-the-manifest"></a>宣告資訊清單中的接收器

我們可以從 GCM 接收訊息之前，我們必須宣告 GCM 接聽程式在 Android 資訊清單中。 讓我們編輯**AndroidManifest.xml** ，並取代`<application>`區段包含下列 XML: 

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

在上述 XML 中，變更*YOUR_PACKAGE_NAME*用戶端應用程式專案的封裝名稱。 在我們逐步解說的範例，是封裝名稱`com.xamarin.gcmexample`。 

讓我們看看此 XML 中的每個設定的用途：

|設定|描述|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|會宣告我們的應用程式實作 GCM 接收者會擷取並處理內送的推播通知訊息。|
|`com.google.android.c2dm.permission.SEND`|宣告只有 GCM 伺服器，可以將訊息傳送至應用程式直接。|
|`com.google.android.c2dm.intent.RECEIVE`|意圖篩選廣告，我們的應用程式會處理來自 GCM 的廣播的訊息。|
|`com.google.android.c2dm.intent.REGISTRATION`|廣告，我們的應用程式會處理新的註冊對應方式的意圖篩選 （也就是我們已實作的執行個體識別碼接聽程式服務）。|

或者，您可以裝飾`GcmListenerService`使用這些屬性，而不是 XML; 中指定它們在這裡我們指定它們**AndroidManifest.xml** ，讓您更輕鬆地遵循程式碼範例。 


### <a name="create-a-message-sender-to-test-the-app"></a>建立測試應用程式的訊息寄件者

讓我們新增C#桌面主控台應用程式專案加入方案，並稱之為**MessageSender**。 我們將使用此主控台應用程式來模擬應用程式伺服器&ndash;它會傳送通知訊息給**ClientApp**透過 GCM。 


#### <a name="add-the-jsonnet-package"></a>新增 Json.NET 封裝

在此主控台應用程式中，我們要建置 JSON 承載，其中包含我們想要傳送至用戶端應用程式的通知訊息。 我們將使用**Json.NET**封裝中**MessageSender**讓您更輕鬆地建置需要 GCM 的 JSON 物件。 在 Visual Studio 中，以滑鼠右鍵按一下**參考 > 管理 NuGet 套件...**; 在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝 > 新增套件...**. 

讓我們搜尋**Json.NET**封裝，並將它安裝在專案中： 

[![安裝 Json.NET 封裝](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>加入對 System.Net.Http 的參考

我們也需要將參考加入`System.Net.Http`以便我們可以具現化`HttpClient`將我們的測試訊息傳送至 GCM。 在  **MessageSender**專案，以滑鼠右鍵按一下**參考 > 加入參考**向下捲動直到您看到**System.Net.Http**。 核取記號旁邊**System.Net.Http**然後按一下**確定**。 


#### <a name="implement-code-that-sends-a-test-message"></a>實作傳送測試訊息的程式碼

在  **MessageSender**，編輯**Program.cs**並以下列程式碼取代內容：

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

在上述程式碼中，變更*YOUR_API_KEY* API 金鑰的用戶端應用程式專案。 

此測試應用程式伺服器會將下列 JSON 格式訊息傳送至 GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM，反而會將轉送此訊息至用戶端應用程式。 讓我們來建置**MessageSender**並開啟主控台視窗中，我們可以從命令列執行它。



### <a name="try-it"></a>請嘗試

現在我們已經準備好要測試我們的用戶端應用程式。 如果您使用模擬器，或如果您的裝置在透過 Wi-fi 與 GCM 通訊，您必須透過取得的 GCM 訊息在防火牆上開啟下列 TCP 通訊埠： 5228、 5229 和 5230。

啟動您的用戶端應用程式，並查看 [輸出] 視窗。 之後`RegistrationIntentService`成功接收，註冊自 GCM 語彙基元時，[輸出] 視窗應該會顯示此語彙基元，類似於下列的記錄輸出：

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此時用戶端應用程式已準備好接收遠端通知訊息。 從命令列中，執行**MessageSender.exe**程式，將"Hello，Xamarin 」 的通知訊息傳送至用戶端應用程式。
如果您尚未建立**MessageSender**專案，請現在建立。

若要執行**MessageSender.exe**下方 Visual Studio 中，開啟 命令提示字元，將變更為**MessageSender/bin/Debug**目錄，然後執行命令直接：

```cmd
MessageSender.exe
```

若要執行**MessageSender.exe**在 Visual Studio for Mac 中，開啟 終端機工作階段，將變更**MessageSender/bin/Debug**目錄中，並使用 mono 執行**MessageSender.exe** 

```bash
mono MessageSender.exe
```

它可能需要一分鐘的時間才能傳播到 GCM 和用戶端應用程式降回訊息。 如果已成功接收訊息時，我們應該會看到類似於 [輸出] 視窗中的下列輸出： 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外，您應該會注意到，通知系統匣中出現新的 [通知] 圖示： 

[![通知圖示會出現在裝置上](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

當您開啟以檢視通知的通知紙匣時，您應該會看到我們遠端通知：

[![會顯示通知訊息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

恭喜，您的應用程式已收到其第一個遠端通知 ！

請注意，是否應用程式強制停止，不會再將收到 GCM 訊息。 若要強制停止後繼續通知，應用程式必須以手動方式重新啟動。 如需有關此 Android 的原則的詳細資訊，請參閱[啟動已停止的應用程式上的控制項](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)，而這[堆疊溢位 post](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

 
## <a name="summary"></a>總結

本逐步解說的詳細步驟在 Xamarin.Android 應用程式中實作遠端通知。 它說明如何安裝 GCM 通訊所需的其他套件，它說明如何設定 GCM 伺服器的存取權的應用程式權限。 它提供範例程式碼，說明如何檢查 Google Play 服務存在、 如何實作註冊意圖服務和執行個體識別碼接聽程式服務，會交涉向 GCM 註冊權杖，以及如何實作 GCM 接聽程式接收和處理遠端通知訊息的服務。 最後，我們會實作命令列測試程式，以將測試通知傳送至 GCM 透過用戶端應用程式。 


## <a name="related-links"></a>相關連結

- [GCM RemoteNotifications （範例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
