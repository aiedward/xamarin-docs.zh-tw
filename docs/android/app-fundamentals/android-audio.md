---
title: Android 音訊
description: Android OS 提供多媒體的廣泛支援，包括音訊和影片。 本指南著重于 Android 中的音訊，並涵蓋如何使用內建的音訊播放機和錄製器類別，以及低層級的音訊 API 來播放和錄製音訊。 它也涵蓋了使用其他應用程式廣播的音訊事件，讓開發人員可以建立行為良好的應用程式。
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: f8fb2c8b1d87ec1a02c0920cc4d3670e7e8f9cb0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456246"
---
# <a name="android-audio"></a>Android 音訊

_Android OS 提供多媒體的廣泛支援，包括音訊和影片。本指南著重于 Android 中的音訊，並涵蓋如何使用內建的音訊播放機和錄製器類別，以及低層級的音訊 API 來播放和錄製音訊。它也涵蓋了使用其他應用程式廣播的音訊事件，讓開發人員可以建立行為良好的應用程式。_

## <a name="overview"></a>概觀

新式行動裝置採用先前需要專用設備 &ndash; 攝影機、音樂播放機和影片燒錄片的功能。 因此，多媒體架構已成為行動 Api 的頂級功能。

Android 提供多媒體的廣泛支援。 本文探討如何在 Android 中使用音訊，並涵蓋下列主題

1. **使用 MediaPlayer** &ndash; 播放音訊使用內建  `MediaPlayer` 類別來播放音訊，包括本機音訊檔案，以及使用類別的資料流程處理音訊檔案 `AudioTrack` 。

2. **錄製音訊** &ndash; 使用內建 `MediaRecorder` 類別錄製音訊。

3. 使用**音訊通知** &ndash;使用音訊通知來建立正常運作的應用程式，以正確回應事件 (例如，透過暫停或取消其音訊輸出) 撥入電話電話。

4. 使用**低層級音訊** &ndash;`AudioTrack`直接寫入記憶體緩衝區以使用類別播放音訊。 使用類別錄製音訊 `AudioRecord` ，並直接從記憶體緩衝區讀取。

## <a name="requirements"></a>需求

本指南需要 Android 2.0 (API 層級 5) 或更高版本。 請注意，在 Android 上的偵錯工具必須在裝置上完成。

必須要求 `RECORD_AUDIO` **AndroidManifest.XML**中的許可權：

![啟用錄製音訊之 Android 資訊清單的必要許可權區段 \_](android-audio-images/image01.png)

## <a name="playing-audio-with-the-mediaplayer-class"></a>使用 MediaPlayer 類別播放音訊

在 Android 中播放音訊最簡單的方式就是使用內建的 [MediaPlayer](xref:Android.Media.MediaPlayer) 類別。
`MediaPlayer` 可以藉由傳入檔案路徑來播放本機或遠端檔案。 不過，很容易 `MediaPlayer` 區分狀態，而且在錯誤狀態下呼叫其中一個方法，將會擲回例外狀況。 請務必依照 `MediaPlayer` 下面所述的順序進行互動，以避免發生錯誤。

### <a name="initializing-and-playing"></a>初始化和播放

播放音訊 `MediaPlayer` 需要下列順序：

1. 具現化新的 [MediaPlayer](xref:Android.Media.MediaPlayer) 物件。

1. 設定要透過 [SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*) 方法播放的檔案。

1. 呼叫 [Prepare](xref:Android.Media.MediaPlayer.Prepare) 方法以初始化播放玩家。

1. 呼叫 [start](xref:Android.Media.MediaPlayer.Start) 方法以開始播放音訊。

下列程式碼範例說明此用法：

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

您可以藉由呼叫 [Pause](xref:Android.Media.MediaPlayer.Pause) 方法來暫停播放：

```csharp
player.Pause();
```

若要繼續暫停播放，請呼叫 [Start](xref:Android.Media.MediaPlayer.Start) 方法。
這會從播放中的暫停位置繼續：

```csharp
player.Start();
```

在播放程式上呼叫 [Stop](xref:Android.Media.MediaPlayer.Stop) 方法會結束進行中的播放：

```csharp
player.Stop();
```

當不再需要播放程式時，必須藉由呼叫 [Release](xref:Android.Media.MediaPlayer.Release) 方法來釋放資源：

```csharp
player.Release();
```

## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用 MediaRecorder 類別錄製音訊

在 `MediaPlayer` Android 中錄製音訊的必然結果是 [MediaRecorder](xref:Android.Media.MediaRecorder) 類別。 就像 `MediaPlayer` ，它會區分狀態，而且會轉換成數個狀態，以取得可開始錄製的時間點。 為了錄製音訊， `RECORD_AUDIO` 必須設定許可權。 如需如何設定應用程式許可權的指示，請參閱 [使用 AndroidManifest.xml](~/android/platform/android-manifest.md)。

### <a name="initializing-and-recording"></a>正在初始化和錄製

使用錄製音訊 `MediaRecorder` 需要下列步驟：

1. 具現化新的 [MediaRecorder](xref:Android.Media.MediaRecorder) 物件。

2. 透過 [SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*) 方法，指定要用來捕捉音訊輸入的硬體裝置。

3. 使用 [SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*) 方法設定輸出檔音訊格式。 如需支援的音訊類型清單，請參閱 [Android 支援的媒體格式](https://developer.android.com/guide/appendix/media-formats.html)。

4. 呼叫 [SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*) 方法來設定音訊編碼類型。

5. 呼叫 [SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*) 方法，以指定寫入音訊資料的輸出檔名稱。

6. 呼叫 [Prepare](xref:Android.Media.MediaRecorder.Prepare) 方法以初始化錄製器。

7. 呼叫 [start](xref:Android.Media.MediaRecorder.Start) 方法以開始錄製。

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

### <a name="stopping-recording"></a>停止錄製

若要停止錄製，請 `Stop` 在上呼叫方法 `MediaRecorder` ：

```csharp
recorder.Stop();
```

### <a name="cleaning-up"></a>清除

一旦 `MediaRecorder` 停止之後，請呼叫 [Reset](xref:Android.Media.MediaRecorder.Reset) 方法使其回到閒置狀態：

```csharp
recorder.Reset();
```

當不再 `MediaRecorder` 需要時，必須藉由呼叫 [Release](xref:Android.Media.MediaRecorder.Release) 方法來釋放其資源：

```csharp
recorder.Release();
```

## <a name="managing-audio-notifications"></a>管理音訊通知

### <a name="the-audiomanager-class"></a>AudioManager 類別

[AudioManager](xref:Android.Media.AudioManager)類別可讓您存取音訊通知，讓應用程式知道音訊事件發生的時間。 這種服務也提供其他音訊功能的存取權，例如音量和響鈴模式控制項。 `AudioManager`可讓應用程式處理音訊通知，以控制音訊播放。

### <a name="managing-audio-focus"></a>管理音效焦點

裝置的音訊資源 (內建的播放程式和錄製器) 由所有執行中的應用程式共用。

從概念上來說，這類似于桌上型電腦上只有一個應用程式具有鍵盤焦點的應用程式：選取其中一個執行中的應用程式之後，只要按一下滑鼠按鍵，鍵盤輸入就只會進入該應用程式。

音訊焦點是一種類似的概念，可防止多個應用程式同時播放或錄製音訊。 它比鍵盤焦點更複雜，因為它是自願 &ndash; 的，應用程式可以忽略它目前沒有音訊焦點，也不會播放， &ndash; 因為有不同類型的音訊焦點可以要求。 例如，如果要求者只預期播放音訊一小段時間，它可能會要求暫時性焦點。

音訊焦點可能會立即授與，或一開始拒絕並授與。 例如，如果應用程式在通話期間要求音訊焦點，將會遭到拒絕，但在撥打電話之後，可能會被授與焦點。 在此情況下，會註冊接聽程式，以便在音訊焦點離開時據以回應。 要求音訊焦點是用來判斷播放或錄製音訊是否沒問題。

如需有關音訊焦點的詳細資訊，請參閱 [管理音訊焦點](https://developer.android.com/training/managing-audio/audio-focus.html)。

#### <a name="registering-the-callback-for-audio-focus"></a>註冊音訊焦點的回呼

從註冊 `FocusChangeListener` 回呼 `IOnAudioChangeListener` 是取得和放開音訊焦點的重要部分。 這是因為對音訊焦點的授與可能會延後到較晚的時間。 例如，應用程式可能會要求在通話進行時播放音樂。 在通話完成之前，將不會授與音訊焦點。

基於這個理由，回呼物件會以參數的形式傳遞至的 `GetAudioFocus` 方法中 `AudioManager` ，這是註冊回呼的呼叫。 如果一開始會拒絕音訊焦點但稍後再授與，則會 `OnAudioFocusChange` 在回呼上叫用應用程式。 您可以使用相同的方法來告訴應用程式，音訊焦點即將被移除。

當應用程式使用完音訊資源時，它會呼叫的 `AbandonFocus` 方法 `AudioManager` ，並再次在回呼中傳遞。 這會取消註冊回呼並釋出音訊資源，讓其他應用程式可以取得音訊焦點。

#### <a name="requesting-audio-focus"></a>要求音訊焦點

要求裝置音訊資源所需的步驟如下：

1. 取得系統服務的控制碼 `AudioManager` 。

2. 建立回呼類別的實例。

3. 在上呼叫方法，以要求裝置的音訊資源 `RequestAudioFocus` `AudioManager` 。 這些參數包括回呼物件、串流類型 (音樂、語音通話、環形等 ) 以及所要求之存取權的類型 (音訊資源可以短暫要求或在不限時間內進行要求，例如) 。

4. 如果授與要求，則 `playMusic` 會立即叫用方法，並開始播放音訊。

5. 如果要求遭到拒絕，則不會採取進一步的動作。 在此情況下，只有在稍後授與要求時，才會播放音訊。

下列程式碼範例會顯示這些步驟：

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

#### <a name="releasing-audio-focus"></a>放開音訊焦點

播放軌的播放完成時，會叫用 `AbandonFocus` 上的方法 `AudioManager` 。 這可讓另一個應用程式得到裝置的音訊資源。 如果其他應用程式已註冊自己的接聽程式，則會收到此音訊焦點變更的通知。

## <a name="low-level-audio-api"></a>低層級音訊 API

低層級音訊 Api 可讓您更有效地控制音訊播放和錄製，因為它們會直接與記憶體緩衝區互動，而不是使用檔案 Uri。 在某些情況下，最好採用此方法。 這類案例包括：

1. 從加密的音訊檔案播放時。

2. 播放連續的短剪輯時。

3. 音訊串流。

### <a name="audiotrack-class"></a>AudioTrack 類別

[AudioTrack](xref:Android.Media.AudioTrack)類別會使用低層級的音訊 api 進行錄製，而且是類別的低層級對等專案 `MediaPlayer` 。

#### <a name="initializing-and-playing"></a>初始化和播放

若要播放音訊，必須具現化的新實例 `AudioTrack` 。 傳遞至函式的引數清單 [會指定如何](xref:Android.Media.AudioTrack) 播放緩衝區中包含的音訊範例。 引數如下：

1. 串流類型 &ndash; 語音、鈴聲、音樂、系統或警報。

2. &ndash;取樣率的頻率，以 Hz 表示。

3. 通道設定 &ndash; Mono 或身歷聲。

4. 音訊格式 &ndash; 8 位或16位編碼。

5. 緩衝區大小 &ndash; （以位元組為單位）。

6. 緩衝區模式 &ndash; 資料流程或靜態。

在結構之後，會叫 [用的 Play](xref:Android.Media.AudioTrack.Play) 方法 `AudioTrack` ，將其設定為開始播放。 將音訊緩衝區寫入至會 `AudioTrack` 開始播放：

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

呼叫 [pause](xref:Android.Media.AudioTrack.Pause) 方法以暫停播放：

```csharp
audioTrack.Pause();
```

呼叫 [Stop](xref:Android.Media.AudioTrack.Stop) 方法將會永久終止播放：

```csharp
audioTrack.Stop();
```

#### <a name="cleanup"></a>清理

當不再 `AudioTrack` 需要時，必須透過呼叫 [Release](xref:Android.Media.AudioTrack.Release)來釋放其資源：

```csharp
audioTrack.Release();
```

### <a name="the-audiorecord-class"></a>AudioRecord 類別

[AudioRecord](xref:Android.Media.AudioRecord)類別在錄製端相當于 `AudioTrack` 。 就像一樣 `AudioTrack` ，它會直接使用記憶體緩衝區來取代檔案和 uri。 它需要在 `RECORD_AUDIO` 資訊清單中設定許可權。

#### <a name="initializing-and-recording"></a>正在初始化和錄製

第一個步驟是建立新的 [AudioRecord](xref:Android.Media.AudioRecord) 物件。 傳遞至函式的引數清單 [會提供錄製](xref:Android.Media.AudioRecord) 所需的所有資訊。 不同于 `AudioTrack` ，其中引數大部分是列舉，在中的對等引數 `AudioRecord` 是整數。 這些包括：

1. 硬體音訊輸入來源，例如麥克風。

2. 串流類型 &ndash; 語音、鈴聲、音樂、系統或警報。

3. &ndash;取樣率的頻率，以 Hz 表示。

4. 通道設定 &ndash; Mono 或身歷聲。

5. 音訊格式 &ndash; 8 位或16位編碼。

6. 緩衝區大小-以位元組為單位

一旦建立之後 `AudioRecord` ，就會叫用其 [StartRecording](xref:Android.Media.AudioRecord.StartRecording) 方法。 現在已準備好開始錄製。 `AudioRecord`持續讀取音訊緩衝區以進行輸入，並將此輸入寫出至音訊檔案。

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

呼叫 [Stop](xref:Android.Media.AudioRecord.Stop) 方法會終止錄製：

```csharp
audRecorder.Stop();
```

#### <a name="cleanup"></a>清理

當 `AudioRecord` 不再需要物件時，呼叫其 [發行](xref:Android.Media.AudioRecord.Release) 方法會釋放與其相關聯的所有資源：

```csharp
audRecorder.Release();
```

## <a name="summary"></a>摘要

Android 作業系統提供強大的架構來播放、錄製和管理音訊。 本文涵蓋如何使用高階和類別來播放和錄製音訊 `MediaPlayer` `MediaRecorder` 。 接下來，它會探討如何使用音訊通知，在不同的應用程式之間共用裝置的音訊資源。 最後，它會處理如何使用低層級 Api 來播放和錄製音訊，而這些 Api 是直接與記憶體緩衝區互動的介面。

## <a name="related-links"></a>相關連結

- [使用音訊 (範例) ](/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [媒體播放器](xref:Android.Media.MediaPlayer)
- [媒體錄製器](xref:Android.Media.MediaRecorder)
- [音訊管理員](xref:Android.Media.AudioManager)
- [音訊播放軌](xref:Android.Media.AudioTrack)
- [音訊錄製器](xref:Android.Media.AudioRecord)