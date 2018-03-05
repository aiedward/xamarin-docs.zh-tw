---
title: "軟糖 Bean 功能"
description: "本文件將適用於 Android 4.1 中導入的開發人員提供的新功能的高階概觀。 這些功能包括： 增強式 Android 資料交換 」 共用大型檔案、 多媒體、 對等網路探索、 動畫、 新的權限更新至更新的通知。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/14/2017
ms.openlocfilehash: 2e54bfc4bea3955dc80a747c4ecce485b78ada1d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="jelly-bean-features"></a>軟糖 Bean 功能

_本文件將適用於 Android 4.1 中導入的開發人員提供的新功能的高階概觀。這些功能包括： 增強式 Android 資料交換 」 共用大型檔案、 多媒體、 對等網路探索、 動畫、 新的權限更新至更新的通知。_

<a name="Overview" />


## <a name="overview"></a>總覽

Android 4.1 (API 層級 16)，也稱為 「 軟糖 Bean，「 已於 2012 年 7 月 9 日發行。 這篇文章會提供開發人員使用 Xamarin.Android 高層級的一些新功能，於 Android 4.1 的簡介。 這些新引進的功能有些啟動活動時，新的音效的相機和改良的應用程式堆疊巡覽支援動畫的增強功能。 您就可以剪下並貼上的對應方式。

若要找出的相依性不穩定的內容提供者的功能改進 Android 應用程式的穩定性。 服務也可能會隔離，使其只能存取啟動它們的活動。

已新增支援的網路服務探索使用 Bonjour、 UPnP 或多點傳送的 DNS 為基礎的服務。 它現在可能會更豐富已格式化文字、 動作按鈕和大型影像的通知。

最後 Android 4.1 已加入數個新的權限。

 <a name="Requirements" />


## <a name="requirements"></a>需求

若要開發 Xamarin.Android 應用程式使用軟糖 Bean 需要 Xamarin.Android 4.2.6 或較高和 Android 4.1 (API 層級 16) 安裝透過 Android SDK Manager 中的下列螢幕擷取畫面所示：

[![在 Android SDK Manager 中選取 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png)

 <a name="What's_New" />


## <a name="whats-new"></a>新功能

 <a name="Animations" />


### <a name="animations"></a>Animations

活動可能會透過使用縮放動畫或自訂動畫啟動`ActivityOptions`類別。 為支援這些動畫，提供下列新方法：

-   `MakeScaleUpAnimation` – 這會建立從開始位置和大小，在螢幕上的活動視窗向上延展的動畫。
-   `MakeThumbnailScaleUpAnimation` – 這會根據從螢幕上的指定位置的縮圖影像，以建立向上延展的動畫。
-   `MakeCustomAnimation` – 這會從應用程式中的資源建立動畫。 沒有一個動畫，適用於開啟 [活動] 時，另一個活動就會停止。


新`TimeAnimator`類別會提供介面`TimeAnimator.ITimeListener`，可以通知應用程式，每次在範圍內變更的動畫。 例如，請考慮下列實作`TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

若要使用類別的執行個體現在`TimeAnimator`建立時，而且會設定接聽程式：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

做為`TimeAnimator`執行個體正在執行，將會叫用`ITimeAnimator.ITimeListener`，哪個然後將記錄檔如何長動畫已經執行，而且多久它已在上次方法時叫用。

 <a name="Application_Stack_Navigation" />


### <a name="application-stack-navigation"></a>應用程式堆疊巡覽

Android 4.1 改善應用程式堆疊瀏覽 Android 3.0 中引進。 藉由指定`ParentName`屬性`ActivityAttribute`，Android 可以開啟適當的父活動，當使用者按下[向上按鈕](http://developer.android.com/design/patterns/navigation.html#up-vs-back)動作列-Android 將會執行個體化所指定的活動`ParentName`屬性。 這可讓應用程式保留的活動將指定的工作階層。

對於大部分的應用程式設定`ParentName`活動是足夠的資訊適用於 Android 的瀏覽應用程式的堆疊; 提供正確的行為Android 會藉由建立一系列的對應方式為每個父活動合成必要上一頁堆疊。 不過，因為這是假造的應用程式堆疊，每個綜合活動不會自然活動之後所儲存的狀態。 若要提供儲存綜合父活動的狀態，活動可能會覆寫`OnPrepareNavigationUpTaskStack`方法。 這個方法會接收`TaskStackBuilder`Android 將用來建立上一頁堆疊的執行個體將會有的意圖集合物件。 活動可以修改這些對應方式，以便建立綜合活動時，它將會收到適當的狀態資訊。

更複雜的情況下，新的方法上有活動類別，可用來處理上巡覽的行為，並建構上一頁堆疊：

-   `OnNavigateUp` – 藉由覆寫這個方法是能夠執行自訂動作時<span class="ui">向上</span>按下按鍵時。
-   `NavigateUpTo` – 呼叫這個方法會導致應用程式目前的活動從瀏覽至所指定的意圖指定的活動。
-   `ParentActivityIntent` – 這用來取得將會啟動目前活動之父活動的意圖。
-   `ShouldUpRecreateTask` – 此方法用來查詢綜合上一頁堆疊是否必須建立來瀏覽到父系活動。 傳回`true`如果必須建立綜合堆疊。 
-   `FinishAffinity` – 呼叫這個方法將會完成目前的活動及中的所有活動其下目前的工作具有相同的工作親和性。
-   `OnCreateNavigateUpTaskStack` – 需要對具有完整控制權綜合堆疊的建立方式時，會覆寫此方法。


 <a name="Camera" />


### <a name="camera"></a>觀景窗

新的介面， `Camera.IAutoFocusMoveCallback`，可用來偵測自動焦點已啟動或停止移動時。 下列程式碼片段中，可以看到這個新介面的範例：

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

新的類別`MediaActionSound`提供一組 API，以便產生不同的媒體動作的音效。 有幾個動作，可能會發生相機、 這些列舉所定義`Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – 此專注完成時，便會播放的聲音。
-   `MediaActionSoundType.ShutterClick` – 取得靜態影像圖片時，就會播放此音效。
-   `MediaActionSoundType.StartVideoRecording` – 使用此聲音指示視訊錄製的開頭。
-   `MediaActionSoundType.StopVideoRecording` – 表示結束的視訊錄製，就會播放此音效。


使用方式的範例`MediaActionSound`類別可以看到在下列程式碼片段：

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

 <a name="Connectivity" />


### <a name="connectivity"></a>連線能力

 <a name="Android_Beam" />


#### <a name="android-beam"></a>Android 資料交換

Android 資料交換是一種 NFC 基礎技術，可讓兩個的 Android 裝置，來與對方進行通訊。 Android 4.1 傳送大型檔案提供更好的支援。 使用新的方法時`NfcAdapter.SetBeamPushUris()`Android 會替代傳輸機制 （例如藍芽） 之間切換以達到快速傳輸速度。

 <a name="Network_Services_Discovery" />


#### <a name="network-services-discovery"></a>網路服務探索

Android 4.1 包含新的 API 的 DNS 為基礎的多點傳送的服務探索。
這可讓應用程式偵測，並透過 Wi-fi 連線到其他裝置，例如印表機、 相機或媒體裝置。 這些新的 API 位於`Android.Net.Nsd`封裝。

若要建立可供其他服務，服務`NsdServiceInfo`類別用來建立物件，定義服務的屬性。 此物件然後提供給`NsdManager.RegisterService()`以及實作`NsdManager.ResolveListener`。 實作`NsdManager.ResolveListener`用於註冊成功的通知，以及取消登錄服務。

若要探索網路，與實作服務`Nsd.DiscoveryListener`傳遞至`NsdManager.discoverServices()`。

 <a name="Network_Usage" />


#### <a name="network-usage"></a>網路使用量

新的方法，`ConnectivityManager.IsActiveNetworkMetered`可讓裝置檢查是否已連接到計量付費網路。 這個方法可以用來協助管理資料使用量是精確地通知使用者，可能昂貴的費用資料作業。

 <a name="WiFi_Direct_Service_Discovery" />


#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服務探索

`WifiP2pManager`類別來支援 Android 4.0 中導入*zeroconf*。 Zeroconf （零設定網路功能） 是一組技術，可讓裝置 （電腦、 印表機、 電話），自動連線到網路的人為的網路業者或特殊組態伺服器介入。

在軟糖 Bean`WifiP2pManager`可以找出附近的裝置使用*Bonjour*或*Upnp*。 Bonjour 是 zeroconf Apple 的實作。 Upnp 設定也支援 zeroconf 的網路通訊協定。 下列方法加入至`WiFiP2pManager`來支援 Wi-fi 服務探索：

-   `AddLocalService()` – 此方法可透過 Wi-fi 宣佈為服務應用程式，對等所探索的。
-   `AddServiceRequest(` ) – 此方法是將服務探索要求傳送至架構。 它用來初始化 Wi-fi 服務探索。
-   `SetDnsSdResponseListeners()` – 此方法用來登錄接收 Bonjour 探索要求的回應叫用的回呼。
-   `SetUpnpServiceResponseListener()` – 此方法用來註冊要接收的回應探索要求 Upnp 上叫用的回呼。


 <a name="Content_Providers" />


### <a name="content-providers"></a>內容提供者

`ContentResolver`類別已收到新的方法， `AcquireUnstableContentProvider`。 這個方法可讓應用程式來取得 「 不穩定"的內容提供者。 一般來說，當應用程式取得內容提供者，且該內容提供者會損毀，因此將應用程式。 使用這個方法呼叫中，應用程式將不會損毀如果損毀的內容提供者。 相反地，`Android.OS.DeadObjectionException`從內容提供者上的呼叫以通知離開內容的提供者的應用程式將會擲回。 「 不穩定"的內容提供者時，與從其他應用程式內容提供者互動而言較不可能有問題的程式碼，從另一個應用程式將會影響另一個應用程式。

 <a name="Copy_and_Paste_With_Intents" />


### <a name="copy-and-paste-with-intents"></a>複製和貼上的對應方式

`Intent`類別現在可以有`ClipData`透過與其相關聯物件`Intent.ClipData`屬性。 這個方法可讓從剪貼簿的額外資料傳輸以進行操作。 執行個體`ClipData`可以包含一或多個`ClipData.Item`。 `ClipData.Item`下列類型的項目：

-   **文字**– 這是任何文字字串中的，可能是 HTML，或跨越任何支援的內建的 Android 樣式的格式的字串。
-  **意圖**– 任一`Intent`物件。
-   **Uri** – 這可以是任何 URI，例如 HTTP 書籤或內容提供者的 URI。


 <a name="Isolated_Services" />


### <a name="isolated-services"></a>隔離的服務

外掛式主控的服務是自己特殊的處理序下執行，並具有自己的任何權限的服務。 只與服務通訊時，啟動服務，並透過服務 API 繫結至它。 它是可以宣告為外掛式主控服務的屬性設定`IsolatedProcess="true"`中`ServiceAttribute`，adorns 服務類別。

 <a name="Media" />


### <a name="media"></a>媒體

新`Android.Media.MediaCodec`類別會提供 API，以低階媒體轉碼器。 應用程式可以查詢以找出裝置上的低階轉碼器的系統。

新`Android.Media.Audiofx.AudioEffect`已加入支援其他音訊擷取音訊預先處理子類別：

-   `Android.Media.Audiofx.AcousticEchoCanceler` – 這個類別用於前置處理音訊訊號移除遠端合作對象從擷取的音訊信號。 例如，移除語音通訊應用程式的回應。
-   `Android.Media.Audiofx.AutomaticGainControl` – 這個類別用來將擷取的訊號正規化提高或降低輸入的訊號，使輸出訊號是常數。
-   `Android.Media.Audiofx.NoiseSuppressor` – 這個類別將會移除背景噪音，從擷取的信號。


並非所有的裝置將會支援這些效果。 此方法`AudioEffect.IsAvailable`應該由應用程式有問題的音訊效果是否支援在執行應用程式的裝置上呼叫。

`MediaPlayer`類別現可支援與 gapless 播放`SetNextMediaPlayer()`方法。 這個新的方法指定為啟動時的目前媒體播放機完成其播放下一步的 MediaPlayer。

下列新類別會提供標準的機制和 UI 選取播放媒體：

-   `MediaRouter` – 這個類別可讓應用程式控制外接式喇叭的裝置或其他裝置的媒體通道的路由。
-   `MediaRouterActionProvider` 和`MediaRouteButton`– 這些類別可提供一致的 UI 選取和播放媒體。


 <a name="Notifications" />


### <a name="notifications"></a>通知

Android 4.1 允許應用程式更具靈活性和控制項時顯示通知。 應用程式，現在可以向使用者顯示更大更好的通知。 新的方法，`NotificationBuilder.SetStyle()`新增三個新樣式的其中一個允許要在通知設定：

-   `Notification.BigPictureStyle` – 這是一個 helper 類別，也會產生中會有影像的通知。 下圖顯示一則通知大映像的範例：


 [ ![BigPictureStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image2.png)](jelly-bean-images/image2.png)

-   `Notification.BigTextStyle` – 這是會產生將會有多行文字，例如電子郵件通知的協助程式類別。 下列螢幕擷取畫面中，可以看到這個新的通知樣式的範例：


 [ ![BigTextStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image3.png)](jelly-bean-images/image3.png)

-   `Notification.InboxStyle` – 這是一個 helper 類別，也會產生包含的字串，例如電子郵件訊息，從程式碼片段清單的通知，此螢幕擷取畫面所示：


 [ ![Notification.InboxStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image4.png)](jelly-bean-images/image4.png)

您可新增最多兩個動作按鈕底部的通知訊息，通知使用標準或更大的樣式時。
顯示底部的通知動作按鈕的下列螢幕擷取畫面中，可以看到此動作的範例：

 [ ![範例螢幕擷取畫面顯示下列通知訊息的動作按鈕](jelly-bean-images/image5.png)](jelly-bean-images/image5.png)

`Notification`類別已收到新的常數，可讓開發人員指定為通知的五個優先權層級的其中一個。 這些可以設定通知使用`Priority`屬性。

 <a name="Permissions" />


### <a name="permissions"></a>權限

已加入下列新的權限：

-   `READ_EXTERNAL_STORAGE` -應用程式需要唯讀的存取外部儲存體。 所有應用程式目前會根據預設，具有讀取存取權，但未來版本的 Android 需要應用程式明確要求讀取權限。
-   `READ_USER_DICTIONARY` -允許讀取權限至使用者的字組的字典。
-   `READ_CALL_LOG` -允許應用程式讀取呼叫記錄檔，取得傳入和傳出呼叫的相關資訊。
-   `WRITE_CALL_LOG` -允許應用程式的手機上寫入呼叫記錄檔。
-   `WRITE_USER_DICTIONARY` -可讓應用程式寫入至使用者的字組的字典。


要注意的重大變更`READ_EXTERNAL_STORAGE`– 目前這個權限會自動授與 android。 Android 的未來版本將需要應用程式要求此權限，才能授與權限。

 <a name="Summary" />


## <a name="summary"></a>總結

這篇文章導入了一些新 API 的 Android 4.1 (API 層級 16) 中可用。 它會反白顯示的一些變更動畫和建立動畫的啟動活動，並導入的新 API 的其他裝置使用 Bonjour 或 UPnP 通訊協定的網路探索。 其他變更的 api 會像是剪下並貼上資料對應方式，透過使用隔離的服務或 「 不穩定的 「 內容提供者的能力，反白顯示。

這篇文章然後引入更新通知，發生在上，並討論一些 Android 4.1 引進了新的權限


## <a name="related-links"></a>相關連結

- [時間動畫範例 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 Api](http://developer.android.com/about/versions/android-4.1.html)
- [工作和上一頁堆疊](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [前及向上巡覽](http://developer.android.com/design/patterns/navigation.html)
