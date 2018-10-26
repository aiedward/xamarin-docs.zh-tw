---
title: Android 的音訊
description: Android OS 可廣泛支援的多媒體，包含音訊和視訊。 本指南著重在 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器和記錄器類別，以及低階音訊 API。 其中也涵蓋處理音訊廣播由其他應用程式的事件，讓開發人員可以建置行為良好的應用程式。
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 9b7e9354250881074fc6f0db5d97dc83e4d3fa77
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114661"
---
# <a name="android-audio"></a>Android 的音訊

_Android OS 可廣泛支援的多媒體，包含音訊和視訊。本指南著重在 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器和記錄器類別，以及低階音訊 API。其中也涵蓋處理音訊廣播由其他應用程式的事件，讓開發人員可以建置行為良好的應用程式。_


## <a name="overview"></a>總覽

現代的行動裝置已採用，前身為需要專用的設備部分功能&ndash;相機、 音樂播放機和視訊燒錄。 因為這個緣故，多媒體架構已成為行動 Api 中的頭等功能。

Android 提供多媒體廣泛的支援。 這篇文章會檢查使用 Android 中的音訊，並涵蓋下列主題

1.  **播放音訊與 MediaPlayer** &ndash;使用內建`MediaPlayer`類別來播放音訊，包括本機的音訊檔和資料流處理的音訊檔案`AudioTrack`類別。

2.  **錄製音效**&ndash;使用內建`MediaRecorder`來錄製音效的類別。

3.  **使用音訊通知**&ndash;建立些正確回應事件 （例如來電之內的項目） 的應用程式中使用音訊的通知，透過暫停或取消其音訊輸出。

4.  **使用 低階音訊**&ndash;播放音訊使用`AudioTrack`藉由直接寫入記憶體緩衝區的類別。 錄製音訊使用`AudioRecord`類別，並直接從記憶體緩衝區讀取。


## <a name="requirements"></a>需求

本指南會要求 Android 2.0 （API 層級 5） 或更高版本。 請注意，偵錯在 Android 上的音訊必須在裝置上。

就必須要求`RECORD_AUDIO`中的權限**AndroidManifest.XML**:

![所需的記錄與 Android 資訊清單的 [權限] 區段\_啟用音訊](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>播放音訊與 MediaPlayer 類別

播放音訊在 Android 中的最簡單方式是使用內建[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)類別。
`MediaPlayer` 藉由傳入的檔案路徑，可以播放本機或遠端檔案。 不過，`MediaPlayer`非常區分的狀態和錯誤的狀態呼叫其中一個方法會擲回例外狀況。 請務必互動`MediaPlayer`以避免發生錯誤，如下所述的順序。



### <a name="initializing-and-playing"></a>初始化及播放

播放音訊與`MediaPlayer`需要下列順序：

1. 新具現化[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)物件。

1. 設定檔案來播放經由[SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/)方法。

1. 呼叫[準備](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/)初始化播放程式的方法。

1. 呼叫[啟動](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/)方法來啟動音訊的播放。


下列程式碼範例說明這種使用方式：

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>暫停和繼續播放

可以藉由呼叫暫停播放[暫停](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/)方法：

```csharp
player.Pause();
```

若要繼續暫停的播放，請呼叫[啟動](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/)方法。
這將會繼續從播放暫停的位置：

```csharp
player.Start();
```

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/)在播放程式的方法結束進行中的播放：

```csharp
player.Stop();
```

當播放程式不再需要時，必須藉由呼叫釋放資源[發行](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/)方法：

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用資料錄音訊 MediaRecorder 類別

必然結果`MediaPlayer`是在 Android 中的錄製音訊[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)類別。 例如`MediaPlayer`，它會狀態區分，並透過數種狀態，使它可以在其中開始錄製的點會轉換。 若要錄製音訊，`RECORD_AUDIO`必須設定權限。 如需有關如何設定應用程式權限請參閱[使用 AndroidManifest.xml](~/android/platform/android-manifest.md)。


### <a name="initializing-and-recording"></a>初始化和記錄

錄製音訊的`MediaRecorder`需要下列步驟：

1. 新具現化[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)物件。

2. 指定哪些硬體裝置用來擷取透過音訊的輸入[SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/)方法。

3. 設定輸出檔音訊格式使用[SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/)方法。 如需支援的音訊類型的清單，請參閱[Android 支援的媒體格式](http://developer.android.com/guide/appendix/media-formats.html)。

4. 呼叫[SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/)方法來設定編碼類型的音訊。

5. 呼叫[SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/)方法，以指定的音訊資料寫入至輸出檔名稱。

6. 呼叫[準備](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/)方法來初始化記錄器。

7. 呼叫[啟動](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/)開始錄製的方法。


下列程式碼範例說明此順序：

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>正在停止記錄

若要停止錄製，請呼叫`Stop`方法`MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>清除

一次`MediaRecorder`已停止，呼叫[重設](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/)方法，以將它放回其閒置狀態：

```csharp
recorder.Reset();
```

當`MediaRecorder`已不再需要其必須釋出資源藉由呼叫[發行](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/)方法：

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>管理音訊的通知



### <a name="the-audiomanager-class"></a>AudioManager 類別

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)類別提供存取權可讓應用程式知道音訊事件發生時的音訊通知。 此服務也提供其他音訊的功能，例如磁碟區和響鈴模式控制存取。 `AudioManager`允許應用程式處理控制音訊播放的音訊通知。



### <a name="managing-audio-focus"></a>管理音訊的焦點

由所有執行中應用程式所共用的裝置 （內建的播放器和記錄器） 音訊資源。

就概念而言，這是類似應用程式，其中只有一個應用程式具有鍵盤焦點的桌面電腦上： 選取之後執行的應用程式的其中一個滑鼠按一下它，鍵盤輸入只會傳到該應用程式。

音訊的焦點是類似的方式，並且防止多個應用程式，或同時錄製音訊的播放。 它是比鍵盤焦點更複雜的因為它是自願&ndash;應用程式可以忽略這件事，它目前未不具有音訊著重和無論播放&ndash;和不同類型的音訊的焦點，可能會因為要求。 例如，如果要求者只預期要播放音訊的極短的時間，它可能會要求暫時性的焦點。

可能會立即授與音訊的焦點或一開始拒絕並授與更新版本。 例如，如果應用程式要求音訊焦點在撥打電話時，它將會遭到拒絕，但焦點可能也會授與完成通話。 在此情況下，接聽程式會註冊才能據以回應，如果音訊的焦點會消失。 要求音訊的焦點用來判斷它正常播放或錄音。

如需音訊焦點的詳細資訊，請參閱[管理音訊焦點](http://developer.android.com/training/managing-audio/audio-focus.html)。



#### <a name="registering-the-callback-for-audio-focus"></a>音訊的焦點註冊回呼

註冊`FocusChangeListener`回呼`IOnAudioChangeListener`是很重要的一部分取得和釋放音訊的焦點。 這是因為所授與的音訊焦點可能延後到稍後的時間。 例如，應用程式可能會要求在進行通話時播放音樂。 撥打電話完成之前，將不會具有音訊的焦點。

基於這個理由，回呼物件會傳遞為參數插入`GetAudioFocus`方法的`AudioManager`，因此註冊回呼，這個呼叫。 如果音訊的焦點是一開始被拒，但稍後授與，應用程式會收到通知，藉由叫用`OnAudioFocusChange`回呼。 相同的方法用來告知應用程式，音訊的焦點被剝奪的。

當應用程式完成使用音訊的資源時，它會呼叫`AbandonFocus`方法的`AudioManager`，並再次將傳遞回呼中。 這可以用來移除回呼，並釋放音訊的資源，以便其他應用程式可能會取得音訊的焦點。



#### <a name="requesting-audio-focus"></a>要求的音訊焦點

要求裝置的音訊的資源所需的步驟如下：

1.  取得的控制代碼`AudioManager`系統服務。

2.  建立回呼類別的執行個體。

3.  要求的音訊裝置資源，藉由呼叫`RequestAudioFocus`方法`AudioManager`。 參數是回呼物件、 資料流類型 （音樂、 語音通話、 信號等） 和權限要求的存取類型 （音訊的資源可以要求暫時或無限期的期間內，例如）。

4.  授與要求時，如果`playMusic`立即叫用方法，並且開始播放音訊。

5.  如果要求遭到拒絕，則會不採取任何進一步的動作。 在此情況下，如果稍後授與要求，將只播放音訊。


下列程式碼範例示範這些步驟：

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>釋放音訊的焦點

曲目播放完畢`AbandonFocus`方法`AudioManager`叫用。 這可讓另一個應用程式，以取得裝置的音訊的資源。 如果已註冊他們自己的接聽程式，其他應用程式會收到此音訊的焦點變更的通知。


## <a name="low-level-audio-api"></a>低層級的音訊 API

低階音訊 Api 會提供更多的控制音訊的播放和錄音，因為它們會直接接觸記憶體緩衝區，而不使用檔案的 Uri。 有某些情況下，這種方法最好的地方。 這類案例包括：

1.  加密時，從播放音訊檔案。

2.  當播放連續的短片。

3.  音訊串流。


### <a name="audiotrack-class"></a>AudioTrack 類別

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)類別的記錄，使用低階音訊 Api 和相當低層級的`MediaPlayer`類別。


#### <a name="initializing-and-playing"></a>初始化及播放

若要播放音訊的新執行個體`AudioTrack`必須具現化。 引數清單傳遞至[建構函式](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist)指定如何播放音訊緩衝區中包含的範例。 引數為：

1.  Stream 型別&ndash;語音、 鈴聲、 音樂、 系統或警示。

2.  頻率&ndash;以 Hz 的取樣率。

3.  通道組態&ndash;Mono 或立體聲。

4.  音訊格式&ndash;8 位元或 16 位元編碼方式。

5.  緩衝區大小&ndash;以位元組為單位。

6.  緩衝模式&ndash;串流或靜態。


建構完成之後，[播放](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/)方法`AudioTrack`叫用時，若要設定最多開始播放。 撰寫要的音訊緩衝區`AudioTrack`開始播放：

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>暫停和停止播放

呼叫[暫停](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/)方法，以暫停播放：

```csharp
audioTrack.Pause();
```

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/)方法將會永久終止播放：

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>清除

當`AudioTrack`已不再需要其必須釋出資源藉由呼叫[發行](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>AudioRecord 類別

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)類別相當於`AudioTrack`錄製一端。 例如`AudioTrack`，它會使用記憶體緩衝區直接，來取代檔案和 Uri。 它要求`RECORD_AUDIO`使用權限設定資訊清單中。


#### <a name="initializing-and-recording"></a>初始化和記錄

第一個步驟是建構新[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)物件。 引數清單傳遞至[建構函式](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist)提供用於記錄所需的所有資訊。 不同於在`AudioTrack`，其中的引數是大量列舉，在對等的引數`AudioRecord`都是整數。 它們包括：

1.  硬體可用例如麥克風音訊輸入的來源。

2.  Stream 型別&ndash;語音、 鈴聲、 音樂、 系統或警示。

3.  頻率&ndash;以 Hz 的取樣率。

4.  通道組態&ndash;Mono 或立體聲。

5.  音訊格式&ndash;8 位元或 16 位元編碼方式。

6.  緩衝區大小的位元組


一次`AudioRecord`建構時，其[StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/)叫用方法。 您現已準備好開始錄製。 `AudioRecord`持續讀取輸入，音訊緩衝區，並將此輸入出音訊檔案。

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>停止錄製

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/)方法結束錄製：

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>清除

當`AudioRecord`不再需要物件，呼叫其[發行](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/)方法會釋放與它相關聯的所有資源：

```csharp
audRecorder.Release();
```


## <a name="summary"></a>總結

Android 作業系統提供強大的架構，來播放、 燒錄和管理音訊。 這篇文章將探討如何播放錄製的音訊使用高階`MediaPlayer`和`MediaRecorder`類別。 接下來，它會探索如何使用音訊的通知來共用音訊裝置不同的應用程式之間的資源。 最後，其處理方式若要播放及錄製音訊，使用低層級的 Api，其與 tfs 直接記憶體緩衝區。


## <a name="related-links"></a>相關連結

- [使用與音訊 （範例）](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [媒體錄製器](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [音訊管理員](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [音訊資料軌](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [音訊錄製器](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
