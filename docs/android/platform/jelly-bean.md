---
title: Jelly Bean 功能
description: 本檔將為 Android 4.1 中引進的開發人員提供新功能的概要說明。 這些功能包括：增強的通知、Android 橫樑的更新以共用大型檔案、多媒體更新、對等網路探索、動畫、新的許可權。
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 55969993a4cb3755f5a26e681ae21bf993307a0a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456882"
---
# <a name="jelly-bean-features"></a>Jelly Bean 功能

_本檔將為 Android 4.1 中引進的開發人員提供新功能的概要說明。這些功能包括：增強的通知、Android 橫樑的更新以共用大型檔案、多媒體更新、對等網路探索、動畫、新的許可權。_

## <a name="overview"></a>概觀

Android 4.1 (API 層級 16) （也稱為「Jelly Bean」）發行于2012年7月9日。 本文將為使用 Xamarin 的開發人員提供 Android 4.1 中一些新功能的高階簡介。 引進的一些新功能，就是啟動活動的動畫、攝影機的新音效，以及改善的應用程式堆疊流覽支援的增強功能。 您現在可以使用意圖來剪下和貼上。

Android 應用程式的穩定性已改進，可將相依性隔離到不穩定的內容提供者。 服務也可以隔離，使其只能由啟動這些服務的活動存取。

已針對使用 Bonjour、UPnP 或多播 DNS 服務的網路服務探索新增支援。 現在可以有更豐富的通知，其中包含格式化的文字、動作按鈕和大型影像。

最後，在 Android 4.1 中新增了數個新的許可權。

## <a name="requirements"></a>需求

若要使用 Jelly Bean 開發 Xamarin. Android 應用程式，需要 Xamarin. Android 4.2.6 或更高版本，以及 Android 4.1 (API 層級 16) 透過 Android SDK 管理員安裝，如下列螢幕擷取畫面所示：

[![在 Android SDK 管理員中選取 Android 4。1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>新功能

### <a name="animations"></a>動畫

活動可能會使用縮放動畫或自訂動畫來啟動，方法是使用 `ActivityOptions` 類別。 以下是提供支援這些動畫的新方法：

- `MakeScaleUpAnimation` –這會建立一個動畫，從畫面上的開始位置和大小向上調整活動時段。
- `MakeThumbnailScaleUpAnimation` –這會建立動畫，從螢幕上指定位置的縮圖影像向上調整。
- `MakeCustomAnimation` –這會從應用程式中的資源建立動畫。 活動開啟時有一個動畫，另一個則用於活動停止的時間。

新 `TimeAnimator` 類別所提供的介面 `TimeAnimator.ITimeListener` ，可在每次動畫中的框架變更時通知應用程式。 例如，請考慮下列的實作為 `TimeAnimator.ITimeListener` ：

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

現在要使用類別， `TimeAnimator` 會建立的實例，並設定接聽程式：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

當實例正在執行 `TimeAnimator` 時，它會叫 `ITimeAnimator.ITimeListener` 用，然後記錄 animator 已執行的時間長度，以及自從上次叫用方法之後所經過的時間長度。

### <a name="application-stack-navigation"></a>應用程式堆疊導覽

Android 4.1 改善了 Android 3.0 中所引進的應用程式堆疊導覽。 藉由指定的 `ParentName` 屬性 `ActivityAttribute` ，當使用者按下動作列上的 [ [上移] 按鈕](https://developer.android.com/design/patterns/navigation.html#up-vs-back) 時，android 可以開啟適當的父活動-android 會將屬性指定的活動具現化 `ParentName` 。 這可讓應用程式保留執行指定工作的活動階層。

針對大部分的應用程式， `ParentName` 在活動上設定的足夠資訊，讓 Android 能夠提供正確的行為來流覽應用程式堆疊;Android 會為每個父活動建立一系列意圖，以合成必要的回堆疊。 不過，因為這是人工智慧應用程式堆疊，所以每個綜合活動都不會有自然活動會有的儲存狀態。 為了將儲存的狀態提供給綜合父活動，活動可能會覆寫 `OnPrepareNavigationUpTaskStack` 方法。 這個方法會接收一個 `TaskStackBuilder` 實例，此實例會有一個意圖物件的集合，而這些物件會讓 Android 用來建立後端堆疊。 活動可能會修改這些意圖，如此一來，當綜合活動建立時，就會收到適當的狀態資訊。

在更複雜的案例中，活動類別上有新的方法，可用來處理向上流覽和建立後端堆疊的行為：

- `OnNavigateUp` –藉由覆寫這個方法，您可以在按下 [ **向上** ] 按鈕時執行自訂動作。
- `NavigateUpTo` –呼叫這個方法會導致應用程式從目前的活動流覽至指定意圖所指定的活動。
- `ParentActivityIntent` –這是用來取得將啟動目前活動之父活動的意圖。
- `ShouldUpRecreateTask` –此方法可用來查詢是否必須建立綜合回堆疊，才能流覽至父活動。 `true`如果必須建立綜合堆疊，則傳回。 
- `FinishAffinity` –呼叫這個方法將會完成目前的活動，並在其下方的所有活動都具有相同的工作親和性。
- `OnCreateNavigateUpTaskStack` –必要時，會覆寫這個方法，以取得如何建立綜合堆疊的完整控制權。

### <a name="camera"></a>相機

有一個新的介面， `Camera.IAutoFocusMoveCallback` 可用來偵測自動焦點何時開始或停止移動。 您可以在下列程式碼片段中看到這個新介面的範例：

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

新類別 `MediaActionSound` 提供一組 API，用於為各種媒體動作產生音效。 攝影機有幾個可能發生的動作，這些動作是由列舉所定義 `Android.Media.MediaActionSoundType` ：

- `MediaActionSoundType.FocusComplete` –焦點完成時所播放的音效。
- `MediaActionSoundType.ShutterClick` –拍攝靜止影像圖片時，將會播放這個音效。
- `MediaActionSoundType.StartVideoRecording` –這個音效是用來表示影片錄製的開始。
- `MediaActionSoundType.StopVideoRecording` –將會播放此音效以指出錄影的結尾。

您 `MediaActionSound` 可以在下列程式碼片段中看到如何使用類別的範例：

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

#### <a name="android-beam"></a>Android Beam

Android 橫樑是以 NFC 為基礎的技術，可讓兩個 Android 裝置彼此通訊。 Android 4.1 針對大型檔案的傳輸提供更好的支援。 使用新方法時， `NfcAdapter.SetBeamPushUris()` Android 會切換替代傳輸機制 (例如藍牙) ，以達到快速傳送速率。

#### <a name="network-services-discovery"></a>網路服務探索

Android 4.1 包含新的 API，適用于多播 DNS 服務探索。
這可讓應用程式透過 Wi-fi 偵測並聯機到其他裝置，例如印表機、攝影機和媒體裝置。 這些新的 API 位於套件中 `Android.Net.Nsd` 。

若要建立其他服務可能取用的服務，此 `NsdServiceInfo` 類別會用來建立將定義服務屬性的物件。 接著會提供這個物件和的 `NsdManager.RegisterService()` 實作為 `NsdManager.ResolveListener` 。 的實 `NsdManager.ResolveListener` 作為用來通知成功註冊及取消註冊服務。

探索網路上的服務，並將 `Nsd.DiscoveryListener` 傳遞至的執行 `NsdManager.discoverServices()` 。

#### <a name="network-usage"></a>網路使用量

新的方法， `ConnectivityManager.IsActiveNetworkMetered` 可讓裝置檢查是否已連線到計量付費網路。 您可以使用這個方法來協助管理資料使用，方法是精確地通知使用者資料作業可能會有昂貴的費用。

#### <a name="wifi-direct-service-discovery"></a>WiFi Direct 服務探索

`WifiP2pManager`類別是在 Android 4.0 中引進以支援*zeroconf*。 Zeroconf (零設定網路) 是一組技術，可讓 (電腦、印表機、手機) 的裝置自動連線到網路，並介入人為網路操作員或特殊設定伺服器。

在 Jelly Bean 中， `WifiP2pManager` 可以使用 *Bonjour* 或 *Upnp*探索附近的裝置。 Bonjour 是 Apple 的 zeroconf 執行。 Upnp 是一組同時支援 zeroconf 的網路通訊協定。 將下列方法新增至 `WiFiP2pManager` 以支援 wi-fi 服務探索：

- `AddLocalService()` –此方法會使用 Wi-fi 宣告應用程式即服務，以供對等進行探索。
- `AddServiceRequest(` ) –此方法會將服務探索要求傳送至架構。 它是用來初始化 Wi-fi 服務探索。
- `SetDnsSdResponseListeners()` –此方法用來登錄接收來自 Bonjour 之探索要求的回應時，所要叫用的回呼。
- `SetUpnpServiceResponseListener()` –此方法可用來註冊在接收對探索要求 Upnp 的回應時，所要叫用的回呼。

### <a name="content-providers"></a>內容提供者

`ContentResolver`類別已收到新的方法 `AcquireUnstableContentProvider` 。 此方法可讓應用程式取得「不穩定」的內容提供者。 一般情況下，當應用程式取得內容提供者，且該內容提供者損毀時，應用程式就會正常運作。 使用這個方法呼叫時，如果內容提供者損毀，應用程式將不會損毀。 相反 `Android.OS.DeadObjectionException` 地，將會從內容提供者的呼叫擲回，以通知應用程式內容提供者已消失。 當與其他應用程式的內容提供者互動時，「不穩定」的內容提供者會很有用，因為另一個應用程式的錯誤程式碼不太可能會影響另一個應用程式。

### <a name="copy-and-paste-with-intents"></a>使用意圖複製和貼上

`Intent`類別現在可以透過屬性來建立與其 `ClipData` 相關聯的物件 `Intent.ClipData` 。 此方法可讓剪貼簿中的額外資料與意圖一起傳送。 的實例 `ClipData` 可包含一或多個 `ClipData.Item` 。 `ClipData.Item`是下列類型的專案：

- **Text** –這是任何文字字串，也就是 HTML，或是內建 Android 樣式範圍所支援的任何格式字串。
- **意圖** -任何  `Intent` 物件。
- **Uri** –這可以是任何 Uri，例如 HTTP 書簽或內容提供者的 uri。

### <a name="isolated-services"></a>隔離服務

隔離的服務是一項服務，它會在自己的特殊進程下執行，而且沒有自己的許可權。 唯一與服務的通訊是透過服務 API 啟動服務並系結至該服務。 您可以藉由 `IsolatedProcess="true"` 在中設定裝飾服務類別的屬性，將服務宣告為隔離 `ServiceAttribute` 。

### <a name="media"></a>媒體

新的 `Android.Media.MediaCodec` 類別會將 API 提供給低層級媒體編解碼器。 應用程式可以查詢系統，以找出裝置上可用的低層級編解碼器。

已加入新的子 `Android.Media.Audiofx.AudioEffect` 類別，以支援捕獲音訊的額外音訊前置處理：

- `Android.Media.Audiofx.AcousticEchoCanceler` –這個類別可用於前置處理音訊，以從已捕捉的音訊信號中移除遠端方的信號。 例如，從語音通訊應用程式移除 echo。
- `Android.Media.Audiofx.AutomaticGainControl` –這個類別是用來藉由提升或降低輸入信號來正規化捕捉的信號，讓輸出信號保持不變。
- `Android.Media.Audiofx.NoiseSuppressor` –這個類別會從捕捉的信號中移除背景雜音。

並非所有裝置都支援這些效果。 `AudioEffect.IsAvailable`應用程式應該呼叫此方法，以查看執行應用程式的裝置是否支援問題中的音訊效果。

`MediaPlayer`類別現在支援使用方法 gapless 播放 `SetNextMediaPlayer()` 。 這個新方法會指定當目前的 media player 完成播放時，要啟動的下一個 MediaPlayer。

下列新類別提供標準機制和 UI，供您選取要播放媒體的位置：

- `MediaRouter` –這個類別可讓應用程式控制從裝置到外部喇叭或其他裝置的媒體通道路由。
- `MediaRouterActionProvider` 和  `MediaRouteButton` –這些類別可協助提供一致的 UI 來選取及播放媒體。

### <a name="notifications"></a>通知

Android 4.1 可讓應用程式以更多的彈性和控制權來顯示通知。 應用程式現在可以對使用者顯示更大且更好的通知。 新方法可 `NotificationBuilder.SetStyle()` 讓您在通知上設定新的三種新樣式之一：

- `Notification.BigPictureStyle` –這是一個協助程式類別，將會產生其中有影像的通知。 下圖顯示具有大影像的通知範例：

 [![BigPictureStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle` –這是一個 helper 類別，會產生包含多行文字的通知，例如電子郵件。 您可以在下列螢幕擷取畫面中看到這個新通知樣式的範例：

 [![BigTextStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle` –這是一個 helper 類別，會產生包含字串清單的通知，例如電子郵件訊息中的程式碼片段，如下列螢幕擷取畫面所示：

 [![通知的範例螢幕擷取畫面。 InboxStyle 通知](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

當通知使用一般或較大的樣式時，最多可以在通知訊息底部新增兩個動作按鈕。
您可以在下列螢幕擷取畫面中看到這種情況的範例，在此螢幕擷取畫面中，您可以在通知底部看到動作按鈕：

 [![通知訊息下方顯示之動作按鈕的範例螢幕擷取畫面](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification`類別已收到新的常數，可讓開發人員為通知指定五個優先權層級的其中一個。 您可以使用屬性，在通知上設定這些 `Priority` 屬性。

### <a name="permissions"></a>權限

已新增下列新的許可權：

- `READ_EXTERNAL_STORAGE` -應用程式需要外部儲存體的唯讀存取權。 目前，所有的應用程式都有預設的讀取權限，但未來的 Android 版本將需要應用程式明確要求讀取存取權。
- `READ_USER_DICTIONARY` -允許讀取存取使用者的字組字典。
- `READ_CALL_LOG` -可讓應用程式藉由讀取通話記錄來取得傳入和撥出電話的相關資訊。
- `WRITE_CALL_LOG` -允許應用程式寫入電話上的通話記錄檔。
- `WRITE_USER_DICTIONARY` -允許應用程式寫入使用者的單字字典。

要注意的重要變更 `READ_EXTERNAL_STORAGE` -目前，此許可權是由 Android 自動授與。 Android 的未來版本將要求應用程式在授與許可權之前要求此許可權。

## <a name="summary"></a>摘要

本文引進了一些可在 Android 4.1 (API 層級 16) 中使用的新 API。 它會反白顯示動畫的一些變更，並以動畫顯示活動的啟動，並引進新的 API，以使用 Bonjour 或 UPnP 等通訊協定來探索其他裝置的網路。 API 的其他變更也會反白顯示，例如透過意圖剪下和貼上資料的能力、使用隔離服務或「不穩定」內容提供者的能力。

本文接著開始介紹通知的更新，並討論一些已在 Android 4.1 中引進的新許可權

## <a name="related-links"></a>相關連結

- [時間動畫範例 (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 Api](https://developer.android.com/about/versions/android-4.1.html)
- [工作和後端堆疊](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [使用上一頁和向上流覽](https://developer.android.com/design/patterns/navigation.html)