---
title: 果凍豆功能
description: 本文檔將為 Android 4.1 中引入的開發人員提供新功能的高級概述。 這些功能包括:增強通知、更新 Android Beam 以共用大型檔、更新多媒體、對等網路發現、動畫、新許可權。
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027202"
---
# <a name="jelly-bean-features"></a>果凍豆功能

_本文檔將為 Android 4.1 中引入的開發人員提供新功能的高級概述。這些功能包括:增強通知、更新 Android Beam 以共用大型檔、更新多媒體、對等網路發現、動畫、新許可權。_

## <a name="overview"></a>概觀

Android 4.1 (API 級別 16), 也被稱為"果凍豆", 於 2012 年 7 月 9 日發佈。 本文將為使用 Xamarin.Android 的開發人員提供有關 Android 4.1 中某些新功能的高級介紹。 引入的一些新功能包括增強用於啟動活動的動畫、相機的新聲音以及改進對應用程式堆疊導航的支援。 現在可以有意圖剪切和粘貼。

通過隔離對不穩定內容提供商的依賴,Android 應用程式的穩定性得到了提高。 還可以隔離服務,以便僅由啟動服務的活動訪問它們。

已添加支援,以便使用 Bonjour、UPnP 或基於多播 DNS 的服務進行網路服務發現。 現在,具有格式化文本、操作按鈕和大圖像的更豐富的通知現在可以了。

最後,在 Android 4.1 中添加了幾個新許可權。

## <a name="requirements"></a>需求

要開發使用果凍豆的 Xamarin.Android 應用程式,需要 Xamarin.Android 4.2.6 或更高版本,並且 Android 4.1(API 16 級)通過 Android SDK 管理器安裝,如下螢幕拍攝所示:

[![在 Android SDK 管理器中選擇 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>新功能

### <a name="animations"></a>Animations

可以使用縮放動畫或使用`ActivityOptions`類啟動活動或自定義動畫。 提供以下新方法來支援這些動畫:

- `MakeScaleUpAnimation`• 這將創建一個動畫,從螢幕上的開始位置和大小向上縮放活動視窗。
- `MakeThumbnailScaleUpAnimation`• 這將創建一個動畫,該動畫從螢幕上的指定位置從縮略圖圖像向上縮放。
- `MakeCustomAnimation`• 這將從應用程式中的資源創建動畫。 活動何時打開,另一個動畫用於活動停止時。

新`TimeAnimator`類提供了一個`TimeAnimator.ITimeListener`介面 ,可在動畫中每次幀更改時通知應用程式。 例如,請考慮以下實`TimeAnimator.ITimeListener`作 :

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

現在要使用 類,將創建`TimeAnimator`一個實例,並設置偵聽器:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

`TimeAnimator`實例運行時,它將調用`ITimeAnimator.ITimeListener`,然後記錄動畫師運行的時間以及自上次調用該方法以來的運行時間。

### <a name="application-stack-navigation"></a>應用程式堆疊導覽

Android 4.1 改進了 Android 3.0 中引入的應用程序堆疊導航。 通過指定`ParentName`屬性`ActivityAttribute`, 當使用者按下操作列上的[Up 按鈕](https://developer.android.com/design/patterns/navigation.html#up-vs-back)時,Android 可以打開正確的父活動`ParentName`- Android 將實例化 屬性指定的活動。 這允許應用程式保留生成給定任務的活動層次結構。

對於大多數設置`ParentName`活動的應用程式是足夠的資訊,Android提供正確的行為來導航應用程式堆疊;Android 將通過為每個父活動創建一系列意圖來合成必要的后堆疊。 但是,由於這是一個人工應用程式堆疊,因此每個合成活動將沒有自然活動將具有的保存狀態。 要向合成父活動提供保存的狀態,活動可能會覆蓋該方法`OnPrepareNavigationUpTaskStack`。 此方法接收的`TaskStackBuilder`實例將包含 Android 將用於創建後堆疊的意向物件的集合。 活動可能會修改這些意圖,以便在創建合成活動時,它將接收適當的狀態資訊。

對於更複雜的方案,活動類上有一些新方法,可用於處理 Up 導航的行為並構造後堆疊:

- `OnNavigateUp`• 通過重寫此方法,可以在按下**Up**按鈕時執行自定義操作。
- `NavigateUpTo`• 調用此方法將導致應用程式從當前活動導航到給定意圖指定的活動。
- `ParentActivityIntent`• 這用於獲取將啟動當前活動的父活動的意圖。
- `ShouldUpRecreateTask`• 此方法用於查詢是否必須創建合成後堆疊才能導航到父活動。 如果`true`必須創建合成堆疊,則返回。 
- `FinishAffinity`• 調用此方法將完成當前任務中具有相同任務相關性的當前活動及其下的所有活動。
- `OnCreateNavigateUpTaskStack`• 當需要完全控制合成堆疊的創建方式時,將重寫此方法。

### <a name="camera"></a>相機

有一個新的介面,`Camera.IAutoFocusMoveCallback`可用於偵測自動對焦何時啟動或停止移動。 此新介面的範例可以在以下代碼段中看到:

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

新類`MediaActionSound`提供了一組 API,用於為各種媒體操作生成聲音。 攝像機可以執行多個操作,這些操作由枚舉`Android.Media.MediaActionSoundType`定義:

- `MediaActionSoundType.FocusComplete`• 對焦完成後播放的此聲音。
- `MediaActionSoundType.ShutterClick`• 拍攝靜止圖像圖片時,將播放此聲音。
- `MediaActionSoundType.StartVideoRecording`• 此聲音表示視頻錄製的開始。
- `MediaActionSoundType.StopVideoRecording`• 將播放此聲音以指示視頻錄製結束。

如何使用類`MediaActionSound`的範例可以在以下代碼段中看到:

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

Android 光束是基於 NFC 的技術,允許兩個 Android 設備相互通信。 Android 4.1 為大型檔案的傳輸提供更好的支援。 使用新方法時`NfcAdapter.SetBeamPushUris()`,Android 將在備用傳輸機制(如藍牙)之間切換,以實現快速傳送速率。

#### <a name="network-services-discovery"></a>網路服務發現

Android 4.1 包含用於基於多播 DNS 的服務發現的新 API。
這允許應用程式通過Wi-Fi檢測並連接到其他設備,如印表機、攝影機和媒體設備。 這些新的 API 在`Android.Net.Nsd`套件中 。

要創建可能由其他服務使用的服務,類`NsdServiceInfo`用於創建將定義服務屬性的物件。 然後,`NsdManager.RegisterService()`此物件會使用一起提供給`NsdManager.ResolveListener`。 的`NsdManager.ResolveListener`實現用於通知成功的註冊和取消註冊服務。

探索網路上的服務,並實現`Nsd.DiscoveryListener`傳遞到`NsdManager.discoverServices()`。

#### <a name="network-usage"></a>網路使用量

新方法`ConnectivityManager.IsActiveNetworkMetered`允許設備檢查其是否已連接到按流量計費的網路。 此方法可用於通過準確地通知用戶數據操作可能收取的費用來説明管理數據使用方式。

#### <a name="wifi-direct-service-discovery"></a>WiFi 直接服務發現

該`WifiP2pManager`類在Android 4.0中引入,以支援*零聯盟*。 Zeroconf(零配置網路)是一組技術,允許設備(電腦、印表機、電話)在人工網路運營商或特殊配置伺服器的干預下自動連接到網路。

在果凍豆,`WifiP2pManager`可以發現附近的設備使用*邦珠*或*Upnp。* 邦珠是蘋果實施零聯盟。 Upnp 是一組網路協定,也支援零聯盟。 新增到`WiFiP2pManager`支援 Wi-Fi 服務發現的以下方法:

- `AddLocalService()`• 此方法用於通過 Wi-Fi 將應用程式作為服務進行公告,供對等體發現。
- `AddServiceRequest(`) • 此方法將服務發現請求發送到框架。 它用於初始化 Wi-Fi 服務發現。
- `SetDnsSdResponseListeners()`• 此方法用於註冊在接收來自 Bonjour 的發現請求的回應時調用的回調。
- `SetUpnpServiceResponseListener()`• 此方法用於註冊在接收對發現請求 Upnp 的回應時調用的回調。

### <a name="content-providers"></a>內容提供者

類別`ContentResolver`已收到新方法`AcquireUnstableContentProvider`。 此方法允許應用程式獲取"不穩定"的內容提供程式。 通常,當應用程式獲取內容提供程式,並且內容提供程式崩潰時,應用程式也會崩潰。 使用此方法調用時,如果內容提供程式崩潰,應用程式不會崩潰。 相反,`Android.OS.DeadObjectionException`將從對內容提供商的調用中引發,以通知應用程式內容提供程式已消失。 當與其他應用程式的內容提供程式互動時,「不穩定」的內容提供程式非常有用 - 來自另一個應用程式的錯誤代碼不太可能影響另一個應用程式。

### <a name="copy-and-paste-with-intents"></a>複製並貼上意圖

類`Intent`現在`Intent.ClipData`可以通過`ClipData`屬性具有 與其關聯的物件。 此方法允許出於意圖傳輸剪貼簿中的額外數據。 的`ClipData`實體可以包含一個或多個`ClipData.Item`。 `ClipData.Item`是以下類型的專案:

- **文本**– 這是任何文字字串,HTML 或任何字串,其格式受內置 Android 樣式範圍支援。
- **意圖**`Intent`= 任何物件。
- **Uri** – 可以是任何 URI,例如 HTTP 書籤或內容提供程式的 URI。

### <a name="isolated-services"></a>隔離服務

隔離服務是在自己的特殊進程下運行且沒有其自身許可權的服務。 與服務的唯一通信是在啟動服務並通過服務 API 綁定到該服務時。 可以通過在 修飾服務類的`IsolatedProcess="true"``ServiceAttribute`屬性中 設置 屬性來聲明服務為隔離。

### <a name="media"></a>媒體

新`Android.Media.MediaCodec`類向低級媒體編解碼器提供 API。 應用程式可以查詢系統,以瞭解設備上可用的低級編解碼器。

新增了新的`Android.Media.Audiofx.AudioEffect`子類,以支援對捕獲的音訊進行其他音訊預處理:

- `Android.Media.Audiofx.AcousticEchoCanceler`• 此類用於預處理音訊,以便從捕獲的音訊信號中刪除遠端方的信號。 例如,從語音通信應用程式中刪除回聲。
- `Android.Media.Audiofx.AutomaticGainControl`• 此類用於通過增增或降低輸入信號使輸出信號恆定來使捕獲的信號標準化。
- `Android.Media.Audiofx.NoiseSuppressor`• 此類將從捕獲的信號中刪除背景雜訊。

並非所有設備都支援這些效果。 應用程式應`AudioEffect.IsAvailable`呼叫該方法,以檢視運行該應用程式的裝置是否支援有問題的音訊效果。

類`MediaPlayer`現在支援`SetNextMediaPlayer()`使用 方法進行無間隙播放。 此新方法指定目前的媒體播放器完成播放時啟動的下一個 MediaPlayer。

以下新類別提供標準機制和 UI,用於選擇媒體將在何處播放:

- `MediaRouter`• 此類允許應用程式控制媒體通道從設備路由到外部揚聲器或其他設備。
- `MediaRouterActionProvider`和`MediaRouteButton`* 這些類有助於提供一致的 UI 來選擇和播放媒體。

### <a name="notifications"></a>通知

Android 4.1 允許應用程式通過顯示通知來提高靈活性和控制力。 應用程式現在可以向用戶顯示更大更好的通知。 新方法`NotificationBuilder.SetStyle()`允許在通知上設置三個新樣式之一:

- `Notification.BigPictureStyle`• 這是一個幫助程式類,它將生成具有圖像的通知。 下圖顯示了具有大圖像的通知範例:

 [![大圖片樣式通知的範例螢幕截圖](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`• 這是一個幫助程式類,它將生成具有多行文本(如電子郵件)的通知。 可在以下螢幕截圖中看到此新通知樣式的範例:

 [![BigTextStyle 通知的範例螢幕擷取](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`• 這是一個幫助程式類,它將產生包含字串清單的通知,例如電子郵件中的代碼段,如以下螢幕截圖所示:

 [![通知的示例屏幕截圖。](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

當通知使用正常或更大的樣式時,通知消息底部可以添加最多兩個操作按鈕。
以下螢幕截圖中可以看到這方面的範例,其中操作按鈕在通知底部可見:

 [![通知訊息下方顯示的操作按鈕的範例螢幕擷取](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

類`Notification`已收到新的常量,允許開發人員為通知指定五個優先順序級別之一。 這些可以在使用`Priority`屬性 的通知上設置。

### <a name="permissions"></a>權限

新增以下新權限:

- `READ_EXTERNAL_STORAGE`- 應用程式需要唯讀取對外部儲存的存取。 目前,默認情況下,所有應用程式都具有讀取訪問許可權,但 Android 的未來版本將需要應用程式顯式請求讀取訪問。
- `READ_USER_DICTIONARY`- 允許讀取存取使用者的單字典。
- `READ_CALL_LOG`- 允許應用程式通過讀取呼叫日誌來獲取有關傳入和傳出呼叫的資訊。
- `WRITE_CALL_LOG`- 允許應用程式寫入電話上的呼叫日誌。
- `WRITE_USER_DICTIONARY`- 允許應用程式寫入使用者的單字字典。

需要注意的重要更改`READ_EXTERNAL_STORAGE`- 目前此許可權由 Android 自動授予。 將來版本的 Android 將需要應用程式在授予許可權之前請求此許可權。

## <a name="summary"></a>總結

本文介紹了 Android 4.1(API 級別 16)中提供的一些新 API。 它突出顯示了動畫的一些更改,並為活動的啟動進行了動畫處理,並引入了新的 API,用於使用 Bonjour 或 UPnP 等協議進行網路發現其他設備。 API 的其他更改也突出顯示,例如通過意圖剪切和粘貼數據的能力、使用獨立服務的能力或"不穩定"的內容提供程式。

然後,本文繼續介紹通知的更新,並討論了 Android 4.1 引入的一些新許可權

## <a name="related-links"></a>相關連結

- [時間動畫範例(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [安卓 4.1 API](https://developer.android.com/about/versions/android-4.1.html)
- [工作與後堆疊](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [帶「後退」和「向上」的導航](https://developer.android.com/design/patterns/navigation.html)
