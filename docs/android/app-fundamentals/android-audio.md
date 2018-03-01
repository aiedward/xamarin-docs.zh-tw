---
title: "Android 的音訊"
description: "Android 作業系統提供更詳盡的支援多媒體，內含音訊和視訊。 本指南著重於 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器與記錄器類別及低階的音訊 API。 其中也涵蓋處理音訊事件廣播由其他應用程式，好讓開發人員可以建置行為良好的應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ea3fd7d73f104f7b9650431a5531fe4399a2630c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="android-audio"></a>Android 的音訊

_Android 作業系統提供更詳盡的支援多媒體，內含音訊和視訊。本指南著重於 Android 中的音訊，並涵蓋播放及錄製音訊，使用內建的音訊播放器與記錄器類別及低階的音訊 API。其中也涵蓋處理音訊事件廣播由其他應用程式，好讓開發人員可以建置行為良好的應用程式。_

<a name="Overview" />

## <a name="overview"></a>總覽

現代的行動裝置採用功能之前會需要專用的設備部份&ndash;數位相機、 播放音樂和視訊燒錄程式。 因為這個緣故，多媒體架構已成為行動應用程式開發介面中的第一級的功能。

Android 多媒體提供更詳盡的支援。 這篇文章會檢查使用 Android 中的音訊，並涵蓋下列主題

1.  **播放音訊與 MediaPlayer** &ndash;使用內建`MediaPlayer`類別來播放音效，包括本機的音訊檔案和資料流的音訊檔案`AudioTrack`類別。

2.  **錄製音效**&ndash;使用內建`MediaRecorder`錄製音訊的類別。

3.  **使用 音效通知**&ndash;使用音訊通知來建立行為良好事件 （例如電話來電） 正確回應的應用程式暫停或取消其音訊輸出。

4.  **使用 低階音訊**&ndash;播放音訊使用`AudioTrack`直接寫入記憶體緩衝區的類別。 錄製音訊使用`AudioRecord`類別，並直接從記憶體緩衝區讀取。


## <a name="requirements"></a>需求

本指南需要 Android 2.0 （API 層級 5） 或更高版本。 請注意，偵錯在 Android 上的音訊必須在裝置上。

要求需要`RECORD_AUDIO`中的權限**AndroidManifest.XML**:

![所需的 Android 與記錄資訊清單的權限區段\_啟用音訊](android-audio-images/image01.png)


<a name="Playing_Audio_with_the_MediaPlayer_Class" />

## <a name="playing-audio-with-the-mediaplayer-class"></a>播放音訊與 MediaPlayer 類別

播放音訊在 Android 的最簡單方式是使用內建[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)類別。
`MediaPlayer` 藉由傳遞的檔案路徑，可以播放本機或遠端的檔案。 不過，`MediaPlayer`非常區分的狀態，而且其中一個方法呼叫處於錯誤狀態將會導致擲回例外狀況。 請務必互動`MediaPlayer`為了避免發生錯誤，如下所述的順序。


<a name="Initializing_and_Playing" />

### <a name="initializing-and-playing"></a>初始化及播放

播放音訊與`MediaPlayer`需要下列順序：

1. 具現化新[MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)物件。

1. 設定檔案來播放經由[SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/)方法。

1. 呼叫[準備](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/)方法來初始化播放程式。

1. 呼叫[啟動](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/)方法啟動音訊播放。


下列程式碼範例將說明這種使用方式：

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

<a name="Suspending_and_Resuming_Playback" />

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

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/)在播放程式的方法會結束進行中的播放：

```csharp
player.Stop();
```

當播放程式不再需要時，必須藉由呼叫釋放資源[發行](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/)方法：

```csharp
player.Release();
```

<a name="Using_the_MediaRecorder_Class_to_Record_Audio" />


## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用要錄製音訊 MediaRecorder 類別

來推論`MediaPlayer`錄製音訊的 Android 是[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)類別。 像`MediaPlayer`，它會狀態區分，並透過它可以在該處開始錄製的點來取得數種狀態轉換。 若要錄製音訊，`RECORD_AUDIO`必須設定權限。 如需有關如何設定應用程式的指示權限請參閱[使用 AndroidManifest.xml](~/android/platform/android-manifest.md)。

<a name="Initializing_and_Recording" />

### <a name="initializing-and-recording"></a>初始化及錄製

錄製音訊與`MediaRecorder`需要下列步驟：

1. 具現化新[MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)物件。

2. 指定要擷取透過音訊的輸入使用的硬體裝置[SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/)方法。

3. 設定輸出檔音訊格式使用[SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/)方法。 如需支援的音訊類型的清單，請參閱[Android 支援的媒體格式](http://developer.android.com/guide/appendix/media-formats.html)。

4. 呼叫[SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/)方法，以設定的音訊編碼類型。

5. 呼叫[SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/)方法，以指定的音訊資料寫入至輸出檔名稱。

6. 呼叫[準備](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/)方法來初始化記錄器。

7. 呼叫[啟動](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/)開始錄製的方法。


下列程式碼範例將說明此順序：

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

<a name="Stopping_recording" />

### <a name="stopping-recording"></a>停止錄製

若要停止錄製，請呼叫`Stop`方法`MediaRecorder`:

```csharp
recorder.Stop();
```

<a name="Cleaning_up" />


### <a name="cleaning-up"></a>清除已完成

一次`MediaRecorder`已停止，呼叫[重設](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/)方法，以將其放回閒置狀態：

```csharp
recorder.Reset();
```

當`MediaRecorder`已不再需要它必須釋放資源呼叫[發行](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/)方法：

```csharp
recorder.Release();
```

<a name="Managing_Audio_Notifications" />

## <a name="managing-audio-notifications"></a>管理音訊的通知

<a name="The_AudioManager_Class" />


### <a name="the-audiomanager-class"></a>AudioManager 類別

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)類別提供可讓應用程式知道音訊事件發生時的音訊通知的存取。 此服務也提供其他音訊的功能，例如磁碟區和響鈴模式控制存取。 `AudioManager`允許應用程式以處理控制音訊播放音訊的通知。

<a name="Managing_Audio_Focus" />


### <a name="managing-audio-focus"></a>管理音訊焦點

音訊裝置 （內建 media player 和錄製器） 的資源共用的所有執行中應用程式。

就概念而言，這是類似於其中只有一個應用程式具有鍵盤焦點，桌面的電腦上的應用程式： 以滑鼠按一下它，選取其中一個執行的應用程式之後, 鍵盤輸入，只會該應用程式。

音訊的焦點是類似的作法，並防止播放或錄音同時從多個應用程式。 它是比鍵盤焦點更複雜，因為它是您可選擇自願&ndash;應用程式可以忽略，它目前未不具有音訊的焦點，無論播放該事實&ndash;和不同類型的音訊焦點可能是因為要求。 例如，如果要求者只應該播放音訊的極短的時間，它可能會要求暫時性的焦點。

可能會立即授與音訊的焦點或一開始拒絕並授與更新版本。 例如，如果應用程式要求音訊焦點電話通話期間，它將會遭到拒絕，但焦點也可以授與完成撥號之後。 在此情況下，才能據以回應如果音訊焦點取出註冊接聽程式。 要求音訊的焦點用來判斷它在正常播放或錄音。

如需音訊焦點的詳細資訊，請參閱[管理音訊焦點](http://developer.android.com/training/managing-audio/audio-focus.html)。


<a name="Registering_the_Callback_for_Audio_Focus" />

#### <a name="registering-the-callback-for-audio-focus"></a>註冊回呼的音訊焦點

註冊`FocusChangeListener`從回呼`IOnAudioChangeListener`是很重要的一部分取得和釋放音訊的焦點。 這是焦點的因為可能會延遲到稍後的授與音訊。 例如，應用程式可能會要求在進行通話時播放音樂。 電話呼叫完成之前，將不會獲得音訊的焦點。

基於這個理由，回呼物件會傳遞為參數插入`GetAudioFocus`方法`AudioManager`，而且它是註冊回呼，這個呼叫。 如果一開始拒絕音訊的焦點，但稍後授與，通知應用程式叫用`OnAudioFocusChange`，回呼上。 相同的方法可用來告知音訊的焦點立即執行的應用程式。

當應用程式已經完成使用的音訊的資源時，它會呼叫`AbandonFocus`方法`AudioManager`，並再次將回呼中傳遞。 這取消登錄回呼，並釋放音訊的資源，使其他應用程式可能會取得音訊的焦點。


<a name="Requesting_Audio_Focus" />

#### <a name="requesting-audio-focus"></a>要求的音訊焦點

要求的音訊裝置資源所需的步驟如下：

1.  取得控制代碼`AudioManager`系統服務。

2.  建立回呼類別執行個體。

3.  藉由呼叫要求的音訊裝置資源`RequestAudioFocus`方法`AudioManager`。 參數為回呼物件、 音樂、 語音電話 （環形等） 的資料流類型和權限要求的存取類型 （音訊資源可以要求暫時或無限期的週期，例如）。

4.  授與要求時，如果`playMusic`立即叫用方法，並且開始播放音訊。

5.  如果要求遭到拒絕，會不採取任何進一步的動作。 在此情況下，如果稍後授與要求，將只能播放音訊。


下列程式碼範例顯示這些步驟：

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

<a name="Releasing_Audio_Focus" />

#### <a name="releasing-audio-focus"></a>釋放音訊焦點

曲目的播放完成時，`AbandonFocus`方法`AudioManager`叫用。 這可讓另一個應用程式獲得音訊裝置的資源。 如果他們註冊自己的接聽程式，其他應用程式會收到此音訊的焦點變更的通知。

<a name="Low_Level_Audio_API" />

## <a name="low-level-audio-api"></a>低層級的音訊 API

低層級的音訊 Api 提供更好的控制，透過音訊播放及錄製，因為它們直接互動記憶體緩衝區，而不使用檔案的 Uri。 有一些在這種方法時，最好的案例。 這類案例包括：

1.  加密時，從播放音訊檔案。

2.  當播放一連串的簡短的剪輯。

3.  音訊資料流處理。


 <a name="AudioTrack_Class" />


### <a name="audiotrack-class"></a>AudioTrack 類別

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)類別的低階音訊 Api 用於錄製內容和低階相當於`MediaPlayer`類別。

<a name="Initializing_and_Playing" />

#### <a name="initializing-and-playing"></a>初始化及播放

若要播放音訊的新執行個體`AudioTrack`必須具現化。 引數清單傳遞至[建構函式](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist)指定如何播放音訊緩衝區中包含的範例。 引數為：

1.  資料流類型&ndash;語音、 鈴聲、 音樂、 系統或警示。

2.  頻率&ndash;Hz 中表示的取樣率。

3.  通道組態&ndash;Mono 或立體聲。

4.  音訊格式&ndash;8 位元或 16 位元編碼方式。

5.  緩衝區大小&ndash;以位元組為單位。

6.  緩衝模式&ndash;串流或靜態。


建構完成之後，[播放](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/)方法`AudioTrack`叫用時，若要設定最多開始播放。 寫入的音訊緩衝區`AudioTrack`開始播放：

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

<a name="Pausing_and_Stopping_the_Playback" />

#### <a name="pausing-and-stopping-the-playback"></a>暫停和停止播放

呼叫[暫停](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/)方法，以暫停播放：

```csharp
audioTrack.Pause();
```

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/)方法將會永久結束播放：

```csharp
audioTrack.Stop();
```

<a name="Cleaning_up" />

#### <a name="cleanup"></a>清除

當`AudioTrack`已不再需要它必須釋放資源呼叫[發行](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```

<a name="The_AudioRecord_Class" />

### <a name="the-audiorecord-class"></a>AudioRecord 類別

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)類別就相當於`AudioTrack`錄製的一邊。 像`AudioTrack`，它會使用記憶體緩衝區直接，來取代檔案和 Uri。 它要求`RECORD_AUDIO`使用權限設定資訊清單中。

<a name="Initializing_and_Recording" />

#### <a name="initializing-and-recording"></a>初始化及錄製

第一個步驟是建構新[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)物件。 引數清單傳遞至[建構函式](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist)提供所有記錄所需的資訊。 不同於在`AudioTrack`，其中的引數是主要是列舉型別中的對等引數`AudioRecord`都是整數。 它們包括：

1.  硬體可用例如麥克風音訊輸入的來源。

2.  資料流類型&ndash;語音、 鈴聲、 音樂、 系統或警示。

3.  頻率&ndash;Hz 中表示的取樣率。

4.  通道組態&ndash;Mono 或立體聲。

5.  音訊格式&ndash;8 位元或 16 位元編碼方式。

6.  緩衝區大小的位元組


一次`AudioRecord`建構時，其[StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/)叫用方法。 它現在已準備好開始錄製。 `AudioRecord`持續讀取輸入，音訊緩衝區，並將這項輸入出音訊檔案。

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

<a name="Stopping_the_Recording" />

#### <a name="stopping-the-recording"></a>停止錄製

呼叫[停止](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/)方法結束錄製：

```csharp
audRecorder.Stop();
```

<a name="Clean_Up" />

#### <a name="cleanup"></a>清除

當`AudioRecord`物件不再需要請呼叫其[發行](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/)方法與其相關聯的資源全部釋出：

```csharp
audRecorder.Release();
```

<a name="Summary" />

## <a name="summary"></a>總結

Android OS 所提供的功能強大的架構來播放、 燒錄與管理音訊。 本文涵蓋如何播放及錄製音訊，使用高階`MediaPlayer`和`MediaRecorder`類別。 接下來，它會探索如何使用音訊通知共用音訊裝置不同的應用程式之間的資源。 最後，它處理方式來播放及錄製音訊，使用低層級 Api，哪個介面直接與記憶體緩衝區。


## <a name="related-links"></a>相關連結

- [使用與音訊 （範例）](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Media Player](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [媒體錄製器](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [音訊管理員](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [音訊播放軌](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [音訊錄製器](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
