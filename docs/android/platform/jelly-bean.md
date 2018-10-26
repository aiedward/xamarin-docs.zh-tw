---
title: Jelly Bean 功能
description: 本文件將 Android 4.1 中導入開發人員提供的新功能的高階概觀。 這些功能包括： 增強式通知，Android 的資料交換 」 共用大型檔案、 多媒體、 對等網路探索、 動畫、 新的權限的更新的更新。
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9e83c9a8c1e2740596a981598cafbbfb65e2caf2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119237"
---
# <a name="jelly-bean-features"></a>Jelly Bean 功能

_本文件將 Android 4.1 中導入開發人員提供的新功能的高階概觀。這些功能包括： 增強式通知，Android 的資料交換 」 共用大型檔案、 多媒體、 對等網路探索、 動畫、 新的權限的更新的更新。_



## <a name="overview"></a>總覽

Android 4.1 (API 層級 16)，也稱為"Jelly Bean，「 已於 2012 年 7 月 9 日發行。 這篇文章會使用 Xamarin.Android 開發人員提供的一些 Android 4.1 的新功能的高階簡介。 有些推出這些新功能是啟動活動，如相機、 新的音效，並改善應用程式堆疊巡覽支援動畫的增強功能。 您現可以剪下並貼上包含 」 用途。

Android 應用程式的穩定性改進能夠找出的相依性不穩定的內容提供者。 服務也可能隔離，使其僅可供存取啟動它們的活動。

支援已新增網路服務探索使用 Bonjour、 UPnP 或多點傳送的 DNS 為基礎的服務。 您現在可更豐富文字、 動作按鈕和大型映像已格式化的通知。

最後幾個新的權限已加入於 Android 4.1。



## <a name="requirements"></a>需求

若要開發 Xamarin.Android 應用程式，使用 Jelly Bean 需要 Xamarin.Android 4.2.6 或更高的及 Android 4.1 (API 層級 16) 透過 Android SDK 管理員安裝如下列螢幕擷取畫面所示：

[![選取 Android SDK 管理員中的 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>新功能



### <a name="animations"></a>Animations

活動可能會使用顯示比例動畫或自訂動畫來啟動`ActivityOptions`類別。 若要支援這些動畫提供下列新方法：

-   `MakeScaleUpAnimation` – 這會建立相應增加從開始位置和大小，在螢幕上的活動時段的動畫。
-   `MakeThumbnailScaleUpAnimation` – 這會從螢幕上的指定位置的縮圖映像建立的動畫，相應增加。
-   `MakeCustomAnimation` – 這會從應用程式中的資源建立動畫。 沒有針對該活動便會開啟一個動畫，而另一個活動時停止。


新`TimeAnimator`類別提供的介面`TimeAnimator.ITimeListener`，可以通知應用程式，每次在範圍內變更的動畫。 例如，請考慮下列實作`TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

若要使用的類別的執行個體現在`TimeAnimator`建立時，並設定接聽程式：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

作為`TimeAnimator`執行個體正在執行，它會叫用`ITimeAnimator.ITimeListener`，這將會再記錄如何動畫有長久以來一直執行，以及多久它如已在上次方法叫用。



### <a name="application-stack-navigation"></a>應用程式堆疊導覽

Android 4.1 改善 Android 3.0 中導入的應用程式堆疊導覽。 藉由指定`ParentName`的屬性`ActivityAttribute`，Android 可以開啟適當的父活動，當使用者按下[向上按鈕](http://developer.android.com/design/patterns/navigation.html#up-vs-back)動作列-Android 將會執行個體化所指定的活動`ParentName`屬性。 這可讓應用程式，以保留的活動將指定的工作階層。

對於大部分的應用程式設定`ParentName`活動資訊就足以讓 Android 導覽應用程式堆疊，提供正確的行為Android 會藉由建立一系列的對應方式為每個父活動合成必要上一頁堆疊。 不過，因為這是人的應用程式堆疊，每個綜合活動不會自然的活動會有儲存的狀態。 若要提供綜合的父活動的已儲存的狀態，活動可能會覆寫`OnPrepareNavigationUpTaskStack`方法。 這個方法會接收`TaskStackBuilder`會有一組的意圖的執行個體物件，Android 會使用建立上一頁堆疊。 活動可以修改這些意圖，以便建立綜合活動時，它會收到適當的狀態資訊。

更複雜的情況下，新的方法上有活動類別，可用來處理上巡覽的行為，並建構上一頁堆疊：

-   `OnNavigateUp` – 藉由覆寫這個方法是能夠執行自訂動作時<span class="ui">向上</span>按下按鈕時。
-   `NavigateUpTo` -呼叫這個方法會導致應用程式目前的活動從瀏覽至所指定的意圖指定的活動。
-   `ParentActivityIntent` – 這用來取得意圖，將會啟動目前活動之父活動。
-   `ShouldUpRecreateTask` – 此方法用以查詢來瀏覽到父活動是否必須建立綜合的上一頁堆疊。 傳回`true`如果必須建立綜合的堆疊。 
-   `FinishAffinity` -呼叫這個方法會完成目前活動與中的所有活動其下目前的工作具有相同的工作親和性。
-   `OnCreateNavigateUpTaskStack` – 此方法會覆寫時，就必須擁有完整控制權的綜合堆疊的建立方式。




### <a name="camera"></a>觀景窗

沒有新的介面， `Camera.IAutoFocusMoveCallback`，可用來偵測時自動焦點已啟動或停止移動。 下列程式碼片段中，就可以看到這個新介面的範例：

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

新的類別`MediaActionSound`提供一組 API，以便產生音效的各種媒體動作。 有可能發生的相機，這些會列舉所定義的數個動作`Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – 將焦點放完成時，就會播放此音效。
-   `MediaActionSoundType.ShutterClick` – 建立靜態影像圖片時，就會播放此音效。
-   `MediaActionSoundType.StartVideoRecording` – 此音效會使用指示的視訊錄製開頭。
-   `MediaActionSoundType.StopVideoRecording` – 將播放此音效，指出結尾的視訊錄製。


使用方式的範例`MediaActionSound`類別中所見下列程式碼片段：

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```



### <a name="connectivity"></a>連線能力



#### <a name="android-beam"></a>Android 資料交換

Android 資料交換是一種 NFC 基礎技術，讓兩個彼此通訊的 Android 裝置。 Android 4.1 移轉大型檔案提供更好的支援。 使用新的方法時`NfcAdapter.SetBeamPushUris()`Android 會替代傳輸機制 （例如藍芽） 之間切換以達到快速傳輸速度。



#### <a name="network-services-discovery"></a>網路服務探索

Android 4.1 包含新的 API 的多點傳送以 DNS 為基礎的服務探索。
這可讓應用程式偵測，並透過 Wi-fi 連線到其他裝置，例如印表機、 相機或媒體裝置。 這些新的 API 位於`Android.Net.Nsd`封裝。

若要建立可供其他服務，服務`NsdServiceInfo`類別用來建立物件，將會定義服務的屬性。 此物件則提供給`NsdManager.RegisterService()`連同實作`NsdManager.ResolveListener`。 實作`NsdManager.ResolveListener`用來註冊成功的通知，以及取消註冊服務。

若要探索的網路和實作上的服務`Nsd.DiscoveryListener`傳遞至`NsdManager.discoverServices()`。



#### <a name="network-usage"></a>網路使用量

新的方法，`ConnectivityManager.IsActiveNetworkMetered`可讓裝置以檢查是否已連接到計量付費網路。 這個方法可用來協助您管理資料使用量是準確地通知使用者，可能昂貴的費用，進行資料作業。



#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服務探索

`WifiP2pManager`類別來支援 Android 4.0 中導入*zeroconf*。 Zeroconf （零設定網路功能） 是一組技術，可讓裝置 （電腦、 印表機、 電話），自動連線到網路，而人為的網路操作員或特殊的組態伺服器的介入。

在 Jelly Bean`WifiP2pManager`可以找出使用的裝置附近*Bonjour*或是*Upnp*。 Bonjour 是 zeroconf Apple 的實作。 Upnp 網路通訊協定，也支援 zeroconf 的設定。 下列方法新增至`WiFiP2pManager`支援 Wi-fi 服務探索：

-   `AddLocalService()` – 此方法可透過 Wi-fi 宣佈為服務應用程式，探索的對等。
-   `AddServiceRequest(` ) – 此方法是將服務探索要求傳送至架構。 它用來初始化 Wi-fi 服務探索。
-   `SetDnsSdResponseListeners()` – 此方法用來註冊要接收 Bonjour 探索要求的回應上叫用的回呼。
-   `SetUpnpServiceResponseListener()` – 此方法用來註冊要接收的回應探索要求 Upnp 上叫用的回呼。




### <a name="content-providers"></a>內容提供者

`ContentResolver`類別已收到新的方法， `AcquireUnstableContentProvider`。 這個方法可讓應用程式取得 「 不穩定 」 的內容提供者。 一般來說，當應用程式取得內容的提供者，且該內容的提供者損毀，因此將應用程式。 此方法的呼叫，取代應用程式將不會損毀如果損毀，內容提供者。 相反地，`Android.OS.DeadObjectionException`從內容提供者上的呼叫以通知應用程式內容提供者已結束離開，就會擲回。 「 不穩定 」 的內容提供者是很有用，與從其他應用程式的內容提供者互動時一樣，它是比較不可能從另一個應用程式的不良程式碼將會影響另一個應用程式。



### <a name="copy-and-paste-with-intents"></a>複製並貼上包含 」 用途

`Intent`類別現在可以有`ClipData`透過相關聯的物件`Intent.ClipData`屬性。 這個方法可讓額外的資料，從 [剪貼簿] 中，把即將傳輸的目的。 執行個體`ClipData`可以包含一或多個`ClipData.Item`。 `ClipData.Item`下列類型的項目：

-   **文字**– 這是任何文字，其中一個 HTML 字串，或跨越任何內建 Android 樣式所支援之格式的字串。
-  **意圖**– 任一`Intent`物件。
-   **Uri** – 這可以是任何 URI，例如 HTTP 書籤或內容提供者的 URI。




### <a name="isolated-services"></a>隔離的服務

獨立的服務是一項服務，它自己特殊的處理序下執行，而且有自己的沒有權限。 與服務的唯一通訊時，啟動服務，並透過服務 API 的繫結至它。 可設定屬性來宣告做為獨立的服務`IsolatedProcess="true"`在`ServiceAttribute`，裝飾的服務類別。


### <a name="media"></a>媒體

新`Android.Media.MediaCodec`類別提供低階的媒體轉碼器的 API。 應用程式可以查詢系統以找出在裝置上可用的最低層級轉碼器。

新`Android.Media.Audiofx.AudioEffect`子類別已新增以支援其他音訊擷取音訊預先處理：

-   `Android.Media.Audiofx.AcousticEchoCanceler` – 這個類別的前置處理音訊來移除遠端合作對象擷取到的音訊訊號的訊號。 比方說，移除語音通訊應用程式的回應。
-   `Android.Media.Audiofx.AutomaticGainControl` – 這個類別用來將擷取的訊號標準化藉由提升或降低輸入的訊號，以便輸出訊號保持不變。
-   `Android.Media.Audiofx.NoiseSuppressor` – 這個類別將會移除背景噪音，從擷取的信號。


並非所有裝置將都支援這些效果。 此方法`AudioEffect.IsAvailable`應該由應用程式來查看有問題的音訊效果是否支援執行應用程式在裝置上呼叫。

`MediaPlayer`類別現可支援 gapless 播放`SetNextMediaPlayer()`方法。 這個新的方法指定下一步 以啟動目前的媒體播放器完成其播放 MediaPlayer。

下列的新類別會提供標準的機制和 UI 選取會播放媒體：

-   `MediaRouter` – 這個類別可讓應用程式來控制從裝置到外接式喇叭或其他裝置的媒體通道的路由。
-   `MediaRouterActionProvider` 和`MediaRouteButton`– 這些類別可協助提供一致的 UI 選取和播放媒體。




### <a name="notifications"></a>通知

更多的彈性和控制顯示通知，android 4.1 可讓應用程式。 應用程式現在可以向使用者顯示更大更好的通知。 新的方法，`NotificationBuilder.SetStyle()`要在通知設定可讓其中一個新的三個新的樣式：

-   `Notification.BigPictureStyle` – 這是一個 helper 類別，也會產生中會有映像的通知。 下圖顯示具有巨量的映像的通知的範例：


 [![BigPictureStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle` – 這是一個 helper 類別，也會產生具有多行文字，例如電子郵件通知。 下列螢幕擷取畫面中，就可以看到這個新的通知樣式的範例：


 [![BigTextStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle` – 這是一個 helper 類別，將會產生包含一份字串，例如電子郵件訊息，從程式碼片段的通知，此螢幕擷取畫面所示：


 [![Notification.InboxStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

就可以通知使用標準或更大的樣式時，底部的通知訊息中新增最多兩個動作按鈕。
這個範例可以看到下列的螢幕擷取畫面，其中動作按鈕可底部的通知：

 [![範例螢幕擷取畫面顯示下列通知訊息的動作按鈕](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification`類別已收到新的常數，可讓開發人員指定其中一個通知的五個優先權層級。 這些可以設定通知使用`Priority`屬性。



### <a name="permissions"></a>權限

已新增下列新的權限：

-   `READ_EXTERNAL_STORAGE` -應用程式需要唯讀的存取到外部儲存體。 所有應用程式目前會根據預設，擁有讀取權限，但 Android 的未來版本將會需要應用程式明確要求的讀取權限。
-   `READ_USER_DICTIONARY` -允許讀取權限加入使用者的 word 字典。
-   `READ_CALL_LOG` -允許應用程式取得傳入和傳出呼叫的相關資訊，請閱讀 通話記錄。
-   `WRITE_CALL_LOG` -可讓應用程式將寫入電話的通話記錄。
-   `WRITE_USER_DICTIONARY` -允許的應用程式，以寫入使用者的 word 字典。


要注意的重要變更`READ_EXTERNAL_STORAGE`– 目前此權限會自動授與 android。 Android 的未來版本將會需要應用程式要求此權限，才能授與權限。



## <a name="summary"></a>總結

這篇文章介紹一些新 API 的 Android 4.1 (API 層級 16) 中所提供。 它會反白顯示的一些變更的動畫建立動畫的活動中，啟動，並導入的新 API 的其他裝置使用 Bonjour 或 UPnP 等通訊協定的網路探索。 Api 的其他變更反白顯示，例如剪下並貼上資料對應方式，透過能夠使用外掛式的服務或 「 不穩定 」 的內容提供者的能力。

這篇文章再出在引進的更新通知，並討論了一些 Android 4.1 的新權限


## <a name="related-links"></a>相關連結

- [時間動畫範例 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 Api](http://developer.android.com/about/versions/android-4.1.html)
- [工作和上一頁堆疊](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [使用上一頁] 和 [向上巡覽](http://developer.android.com/design/patterns/navigation.html)
