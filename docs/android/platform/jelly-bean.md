---
title: 軟糖 Bean 功能
description: '本檔將針對 Android 4.1 中引進的開發人員提供新功能的高階總覽。 這些功能包括: 增強的通知、Android 橫樑更新以共用大型檔案、更新為多媒體、對等網路探索、動畫、新許可權。'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 614a0e3952db42d2587930b66bf71ce4c703d035
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524071"
---
# <a name="jelly-bean-features"></a>軟糖 Bean 功能

_本檔將針對 Android 4.1 中引進的開發人員提供新功能的高階總覽。這些功能包括: 增強的通知、Android 橫樑更新以共用大型檔案、更新為多媒體、對等網路探索、動畫、新許可權。_



## <a name="overview"></a>總覽

Android 4.1 (API 層級 16) (也稱為「軟糖 Bean」) 已于2012年7月9日發行。 本文將針對使用 Xamarin 的開發人員提供 Android 4.1 中一些新功能的高階簡介。 引進的部分新功能包括啟動活動的動畫、攝影機的新聲音, 以及改善應用程式堆疊導覽的支援。 現在可以使用意圖進行剪下和貼上。

Android 應用程式的穩定性已經過改善, 可讓您隔離相依性不穩定的內容提供者。 服務也可能會隔離, 使其只能由啟動它們的活動存取。

已針對使用 Bonjour、UPnP 或多播 DNS 服務的網路服務探索新增支援。 現在有可能會有更豐富的通知, 其中包含格式化文字、動作按鈕和大型影像。

最後, Android 4.1 中已新增數個新的許可權。



## <a name="requirements"></a>需求

若要使用軟糖 Bean 開發 Xamarin. Android 應用程式, 您必須透過 Android SDK Manager 安裝 Xamarin 4.2.6 或更高版本, 以及 Android 4.1 (API 層級 16), 如下列螢幕擷取畫面所示:

[![在 Android SDK 管理員中選取 Android 4。1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>新功能



### <a name="animations"></a>Animations

您可以使用 [縮放動畫] 或 [ `ActivityOptions`自訂動畫] 來啟動活動, 方法是使用類別。 提供下列新方法以支援這些動畫:

- `MakeScaleUpAnimation`–這會建立動畫, 從螢幕上的開始位置和大小相應增加活動時段。
- `MakeThumbnailScaleUpAnimation`–這會建立一個動畫, 從螢幕上指定的位置相應增加縮圖影像。
- `MakeCustomAnimation`–這會從應用程式中的資源建立動畫。 當活動開啟時, 會有一個動畫, 另一個則用於活動停止的時間。


新`TimeAnimator`的類別會提供介面`TimeAnimator.ITimeListener` , 每次在動畫中變更畫面格時, 就會通知應用程式。 例如, 請考慮下列`TimeAnimator.ITimeListener`的執行:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

現在若要使用類別, `TimeAnimator`會建立的實例, 並設定接聽程式:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

當實例正在執行時, 它`ITimeAnimator.ITimeListener`會叫用, 接著會記錄 animator 的執行時間, 以及自上一次叫用方法以來的時間長度。 `TimeAnimator`



### <a name="application-stack-navigation"></a>應用程式堆疊導覽

Android 4.1 改善了 Android 3.0 中引進的應用程式堆疊導覽。 藉由指定`ParentName`的屬性`ActivityAttribute`, 當使用者按下動作列上的 [上一層][按鈕](https://developer.android.com/design/patterns/navigation.html#up-vs-back)時, android 就可以開啟適當的父活動-android `ParentName`會將屬性所指定的活動具現化。 這可讓應用程式保留進行指定工作的活動階層。

對於大部分的應用程式`ParentName` , 在活動上設定的足夠資訊, 可讓 Android 針對流覽應用程式堆疊提供正確的行為;Android 會針對每個父活動建立一系列意圖, 以合成必要的後端堆疊。 不過, 因為這是人工應用程式堆疊, 所以每個綜合活動都不會有自然活動所具有的儲存狀態。 若要將儲存的狀態提供給綜合父活動, 活動可能會`OnPrepareNavigationUpTaskStack`覆寫方法。 這個方法會接收`TaskStackBuilder`一個實例, 其中會有一組 Android 將用來建立後端堆疊的意圖物件。 活動可能會修改這些意圖, 以便在建立綜合活動時, 將會收到適當的狀態資訊。

針對更複雜的案例, 活動類別上有新的方法可用來處理向上導覽並建立後端堆疊的行為:

- `OnNavigateUp`–藉由覆寫這個方法, 您可以在按下 [**向上**] 按鈕時執行自訂動作。
- `NavigateUpTo`-呼叫這個方法會導致應用程式從目前的活動流覽至指定意圖所指定的活動。
- `ParentActivityIntent`–這是用來取得將啟動目前活動之父活動的意圖。
- `ShouldUpRecreateTask`–這個方法是用來查詢是否必須建立綜合後端堆疊, 才能導覽至父活動。 `true`如果必須建立綜合堆疊, 則傳回。 
- `FinishAffinity`-呼叫這個方法將會在目前的工作中, 完成目前的活動和其底下的所有活動, 並具有相同的工作相似性。
- `OnCreateNavigateUpTaskStack`–當需要完全控制如何建立綜合堆疊時, 會覆寫這個方法。




### <a name="camera"></a>觀景窗

有一個新的介面, `Camera.IAutoFocusMoveCallback`可用來偵測自動焦點已開始或停止移動的時間。 此新介面的範例可在下列程式碼片段中看到:

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

新的類別`MediaActionSound`會提供一組 API, 以產生各種媒體動作的音效。 相機有數個可能發生的動作, 這些都是由列舉`Android.Media.MediaActionSoundType`所定義:

- `MediaActionSoundType.FocusComplete`–完成焦點時所播放的音效。
- `MediaActionSoundType.ShutterClick`–當拍攝靜止影像圖片時, 將會播放此音效。
- `MediaActionSoundType.StartVideoRecording`–使用此音效表示影片錄製開始。
- `MediaActionSoundType.StopVideoRecording`–將會播放此音效, 指出影片錄製結束。


在下列程式碼片段中, 可以`MediaActionSound`看到如何使用類別的範例:

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

Android 橫樑是一種以 NFC 為基礎的技術, 可讓兩個 Android 裝置彼此通訊。 Android 4.1 針對大型檔案的傳輸提供更佳的支援。 使用新的方法`NfcAdapter.SetBeamPushUris()`時, Android 會在替代傳輸機制 (例如藍牙) 之間切換, 以達到快速傳送速率。



#### <a name="network-services-discovery"></a>網絡服務探索

Android 4.1 包含新的 API, 適用于多播 DNS 服務探索。
這可讓應用程式偵測並透過 Wi-fi 連線到其他裝置, 例如印表機、攝影機和媒體裝置。 這些新的 API 位於`Android.Net.Nsd`套件中。

若要建立其他服務可能取用的服務, 則`NsdServiceInfo`會使用類別來建立將定義服務屬性的物件。 然後, 這個物件會與`NsdManager.RegisterService()`的`NsdManager.ResolveListener`執行一起提供。 的`NsdManager.ResolveListener`執行是用來通知成功註冊, 以及取消註冊服務。

探索網路上的服務, 並`Nsd.DiscoveryListener`將傳遞至。 `NsdManager.discoverServices()`



#### <a name="network-usage"></a>網路使用量

新的方法`ConnectivityManager.IsActiveNetworkMetered`可讓裝置檢查是否已連線到計量付費網路。 這個方法可用來藉由正確地通知使用者, 資料作業的費用可能會很高, 而協助管理資料的使用。



#### <a name="wifi-direct-service-discovery"></a>WiFi Direct 服務探索

類別是在 Android 4.0 中引進, 以支援*zeroconf。* `WifiP2pManager` Zeroconf (零設定網路) 是一組技術, 可讓裝置 (電腦、印表機、手機) 自動連線到網路, 並介入人力網路操作員或特殊設定伺服器。

在軟糖 Bean 中`WifiP2pManager` , 可以使用*Bonjour*或*Upnp*來探索附近的裝置。 Bonjour 是 Apple 的 zeroconf 實行。 Upnp 是一組也支援 zeroconf 的網路通訊協定。 已將下列方法新增至`WiFiP2pManager` , 以支援 wi-fi 服務探索:

- `AddLocalService()`–此方法是用來透過 Wi-fi 宣告應用程式即服務, 以進行對等的探索。
- `AddServiceRequest(`) –此方法會將服務探索要求傳送至架構。 它是用來初始化 Wi-fi 服務探索。
- `SetDnsSdResponseListeners()`–此方法用來註冊接收來自 Bonjour 之探索要求的回應時, 所要叫用的回呼。
- `SetUpnpServiceResponseListener()`–這個方法是用來註冊要在接收對探索要求的回應時叫用的回呼。




### <a name="content-providers"></a>內容提供者

類別已收到新的`AcquireUnstableContentProvider`方法。 `ContentResolver` 這個方法可讓應用程式取得「不穩定」的內容提供者。 一般來說, 當應用程式取得內容提供者, 而且該內容提供者損毀時, 應用程式就會出現。 使用此方法呼叫時, 如果內容提供者損毀, 應用程式將不會當機。 相反地`Android.OS.DeadObjectionException` , 會從內容提供者的呼叫擲回, 以通知應用程式內容提供者已消失。 當與其他應用程式的內容提供者互動時, 「不穩定」的內容提供者很有用–另一個應用程式的錯誤程式碼較不可能會影響另一個應用程式。



### <a name="copy-and-paste-with-intents"></a>使用意圖複製並貼上

類別現在可以透過`Intent.ClipData`屬性, 將物件與其相關聯。`ClipData` `Intent` 這個方法可讓剪貼簿中的額外資料與意圖一起傳輸。 的`ClipData`實例可以包含一或多個`ClipData.Item`。 `ClipData.Item`是下列類型的專案:

- **Text** –這是任何文字字串, HTML 或內建 Android 樣式範圍支援其格式的任何字串。
- **意圖**-任何`Intent`物件。
- **Uri** –這可以是任何 Uri, 例如 HTTP 書簽或內容提供者的 uri。




### <a name="isolated-services"></a>隔離的服務

隔離服務是以自己的特殊進程執行的服務, 而且沒有自己的許可權。 只有在啟動服務並透過服務 API 系結至該服務時, 才是唯一與服務的通訊。 您可以在裝飾服務類別的`IsolatedProcess="true"` `ServiceAttribute`中設定屬性, 以將服務宣告為隔離。


### <a name="media"></a>媒體

新`Android.Media.MediaCodec`的類別提供低層級媒體編解碼器的 API。 應用程式可以查詢系統, 找出裝置上可用的低層級編解碼器。

已加入`Android.Media.Audiofx.AudioEffect`新的子類別, 以支援已捕捉音訊的其他音訊前置處理:

- `Android.Media.Audiofx.AcousticEchoCanceler`–此類別用於前置處理音訊, 以從已捕捉的音訊信號移除遠端合作物件的信號。 例如, 從語音通訊應用程式中移除回應。
- `Android.Media.Audiofx.AutomaticGainControl`–這個類別是用來藉由增加或減少輸入信號, 讓輸出信號保持不變, 藉以正規化已捕捉的信號。
- `Android.Media.Audiofx.NoiseSuppressor`–這個類別會從捕捉信號中移除背景雜訊。


並非所有裝置都支援這些效果。 應用程式`AudioEffect.IsAvailable`應該呼叫方法, 以查看執行應用程式的裝置是否支援問題中的音訊效果。

類別現在支援`SetNextMediaPlayer()`使用方法的 gapless 播放。 `MediaPlayer` 這個新方法會指定當目前的媒體播放機完成播放時, 要啟動的下一個 MediaPlayer。

下列新類別提供標準的機制和 UI, 可用於選取媒體的播放位置:

- `MediaRouter`–此類別可讓應用程式控制從裝置到外部喇叭或其他裝置的媒體通道路由。
- `MediaRouterActionProvider`和`MediaRouteButton` –這些類別可協助提供一致的 UI 來選取和播放媒體。




### <a name="notifications"></a>通知

Android 4.1 可讓應用程式更有彈性和控制顯示通知。 應用程式現在可以向使用者顯示更大且更好的通知。 新的方法`NotificationBuilder.SetStyle()`可讓您在通知上設定新三個新樣式的其中一個:

- `Notification.BigPictureStyle`–這是協助程式類別, 會產生會在其中包含影像的通知。 下圖顯示具有大影像的通知範例:


 [![BigPictureStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`–這是協助程式類別, 會產生會有多行文字的通知, 例如電子郵件。 此新通知樣式的範例可在下列螢幕擷取畫面中看到:


 [![BigTextStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`–這是協助程式類別, 會產生包含字串清單的通知, 例如來自電子郵件訊息的程式碼片段, 如下列螢幕擷取畫面所示:


 [![InboxStyle 通知的範例螢幕擷取畫面](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

當通知使用正常或較大的樣式時, 可以在通知訊息底部新增最多兩個動作按鈕。
您可以在下列螢幕擷取畫面中看到這種情況的範例, 其中動作按鈕會顯示在通知的底部:

 [![在通知訊息底下顯示之動作按鈕的範例螢幕擷取畫面](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

`Notification`類別已收到新的常數, 可讓開發人員為通知指定五個優先權層級的其中一個。 這些可以使用`Priority`屬性在通知上設定。



### <a name="permissions"></a>權限

已新增下列新許可權:

- `READ_EXTERNAL_STORAGE`-應用程式需要外部儲存體的唯讀存取權。 根據預設, 所有應用程式都有讀取權限, 但未來版本的 Android 會要求應用程式明確要求讀取權限。
- `READ_USER_DICTIONARY`-允許對使用者的字組字典進行讀取存取。
- `READ_CALL_LOG`-允許應用程式藉由讀取通話記錄來取得傳入和撥出電話的相關資訊。
- `WRITE_CALL_LOG`-允許應用程式寫入電話上的通話記錄。
- `WRITE_USER_DICTIONARY`-允許應用程式寫入使用者的單字字典。


要注意`READ_EXTERNAL_STORAGE`的重要變更-目前, Android 會自動授與此許可權。 Android 的未來版本將會要求應用程式在授與許可權之前要求此許可權。



## <a name="summary"></a>總結

本文介紹 Android 4.1 (API 層級 16) 中提供的一些新 API。 它會反白顯示動畫的一些變更, 並以動畫方式啟動活動, 並引進新的 API, 以使用 Bonjour 或 UPnP 等通訊協定來探索其他裝置的網路。 API 的其他變更也會反白顯示, 例如能夠透過意圖來剪下和貼上資料、使用隔離服務或「不穩定」內容提供者的能力。

本文接著會介紹通知的更新, 並討論一些已使用 Android 4.1 引進的新許可權


## <a name="related-links"></a>相關連結

- [時間動畫範例 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Android 4.1 Api](https://developer.android.com/about/versions/android-4.1.html)
- [工作和後端堆疊](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [使用上一頁和 Up 導覽](https://developer.android.com/design/patterns/navigation.html)
