---
title: Android 音訊
description: Android OS 為多媒體提供廣泛的支援，同時包含音訊和影片。 本指南著重于 Android 中的音訊，並說明如何使用內建的音訊播放機和錄製器類別，以及低層級的音訊 API 來播放和錄製音訊。 它也涵蓋了使用其他應用程式廣播的音訊事件，讓開發人員可以建立效能良好的應用程式。
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: ac59bcbb062dc9dae784d18054048f50094c2145
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018075"
---
# <a name="android-audio"></a>Android 音訊

_Android OS 為多媒體提供廣泛的支援，同時包含音訊和影片。本指南著重于 Android 中的音訊，並說明如何使用內建的音訊播放機和錄製器類別，以及低層級的音訊 API 來播放和錄製音訊。它也涵蓋了使用其他應用程式廣播的音訊事件，讓開發人員可以建立效能良好的應用程式。_

## <a name="overview"></a>總覽

新式行動裝置採用的功能，先前會有必要的設備，&ndash; 攝影機、音樂播放機和錄影機。 因此，多媒體架構已成為行動 Api 中的第一類功能。

Android 提供多媒體的廣泛支援。 本文會探討如何使用 Android 中的音訊，並涵蓋下列主題

1. 使用內建的 `MediaPlayer` 類別播放音訊，以**MediaPlayer** &ndash; 播放音訊，包括使用 `AudioTrack` 類別的本機音訊檔案和資料流程音訊檔案。

2. 使用內建 `MediaRecorder` 類別**錄製**音訊 &ndash; 記錄音訊。

3. 使用音訊通知 &ndash; 使用音訊通知來建立**運作**正常的應用程式，藉由暫停或取消其音訊輸出，正確回應事件（例如來電）。

4. 使用**低層級的音訊**&ndash; 透過 `AudioTrack` 類別直接寫入記憶體緩衝區來播放音訊。 使用 `AudioRecord` 類別錄製音訊，並直接從記憶體緩衝區讀取。

## <a name="requirements"></a>需求

本指南需要 Android 2.0 （API 層級5）或更高版本。 請注意，您必須在裝置上完成 Android 上的偵錯工具音訊。

必須要求**androidmanifest.xml**中的 `RECORD_AUDIO` 許可權：

![已啟用記錄\_音訊之 Android 資訊清單的必要許可權區段](android-audio-images/image01.png)

## <a name="playing-audio-with-the-mediaplayer-class"></a>使用 MediaPlayer 類別播放音訊

在 Android 中播放音訊最簡單的方式，就是使用內建的[MediaPlayer](xref:Android.Media.MediaPlayer)類別。
`MediaPlayer` 可以藉由傳入檔案路徑來播放本機或遠端檔案。 不過，`MediaPlayer` 非常區分狀態，而且在錯誤狀態下呼叫其中一個方法，會導致擲回例外狀況。 請務必依照下面所述順序與 `MediaPlayer` 互動，以避免發生錯誤。

### <a name="initializing-and-playing"></a>初始化和播放

使用 `MediaPlayer` 播放音訊需要下列順序：

1. 具現化新的[MediaPlayer](xref:Android.Media.MediaPlayer)物件。

1. 設定要透過[SetDataSource](xref:Android.Media.MediaPlayer.SetDataSource*)方法播放的檔案。

1. 呼叫[Prepare](xref:Android.Media.MediaPlayer.Prepare)方法以初始化播放人員。

1. 呼叫[start](xref:Android.Media.MediaPlayer.Start)方法以開始播放音訊。

下列程式碼範例說明這種用法：

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

您可以藉由呼叫[Pause](xref:Android.Media.MediaPlayer.Pause)方法來暫停播放：

```csharp
player.Pause();
```

若要繼續暫停播放，請呼叫[Start](xref:Android.Media.MediaPlayer.Start)方法。
這會從播放中的暫停位置繼續：

```csharp
player.Start();
```

呼叫播放程式上的[Stop](xref:Android.Media.MediaPlayer.Stop)方法會結束進行中的播放：

```csharp
player.Stop();
```

當不再需要播放程式時，必須呼叫[Release](xref:Android.Media.MediaPlayer.Release)方法來釋放資源：

```csharp
player.Release();
```

## <a name="using-the-mediarecorder-class-to-record-audio"></a>使用 MediaRecorder 類別錄製音訊

在 Android 中錄製音訊 `MediaPlayer` 的必然結果是[MediaRecorder](xref:Android.Media.MediaRecorder)類別。 如同 `MediaPlayer`，它會區分狀態，並會透過數個狀態轉換，以到達可開始錄製的時間點。 若要錄製音訊，必須設定 [`RECORD_AUDIO`] 許可權。 如需如何設定應用程式許可權的指示，請參閱[使用 androidmanifest.xml](~/android/platform/android-manifest.md)。

### <a name="initializing-and-recording"></a>初始化和記錄

使用 `MediaRecorder` 錄製音訊需要下列步驟：

1. 具現化新的[MediaRecorder](xref:Android.Media.MediaRecorder)物件。

2. 透過[SetAudioSource](xref:Android.Media.MediaRecorder.SetAudioSource*)方法，指定要用來捕獲音訊輸入的硬體裝置。

3. 使用[SetOutputFormat](xref:Android.Media.MediaRecorder.SetOutputFormat*)方法來設定輸出檔案的音訊格式。 如需支援的音訊類型清單，請參閱[Android 支援的媒體格式](https://developer.android.com/guide/appendix/media-formats.html)。

4. 呼叫[SetAudioEncoder](xref:Android.Media.MediaRecorder.SetAudioEncoder*)方法以設定音訊編碼類型。

5. 呼叫[SetOutputFile](xref:Android.Media.MediaRecorder.SetOutputFile*)方法，指定要寫入音訊資料的輸出檔名稱。

6. 呼叫[Prepare](xref:Android.Media.MediaRecorder.Prepare)方法來初始化錄製器。

7. 呼叫[start](xref:Android.Media.MediaRecorder.Start)方法以開始錄製。

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

### <a name="stopping-recording"></a>停止錄製

若要停止錄製，請在 `MediaRecorder`上呼叫 `Stop` 方法：

```csharp
recorder.Stop();
```

### <a name="cleaning-up"></a>清理

一旦 `MediaRecorder` 停止，請呼叫[Reset](xref:Android.Media.MediaRecorder.Reset)方法，讓它回到閒置狀態：

```csharp
recorder.Reset();
```

當不再需要 `MediaRecorder` 時，其資源必須藉由呼叫[Release](xref:Android.Media.MediaRecorder.Release)方法來釋放：

```csharp
recorder.Release();
```

## <a name="managing-audio-notifications"></a>管理音訊通知

### <a name="the-audiomanager-class"></a>AudioManager 類別

[AudioManager](xref:Android.Media.AudioManager)類別提供音訊通知的存取，可讓應用程式在發生音訊事件時知道。 此服務也提供其他音訊功能的存取權，例如音量和響鈴模式控制。 此 `AudioManager` 可讓應用程式處理音訊通知以控制音訊播放。

### <a name="managing-audio-focus"></a>管理音訊焦點

裝置的音訊資源（內建的播放程式和錄製器）會由所有執行中的應用程式共用。

在概念上，這類似于只有一個應用程式具有鍵盤焦點的桌上型電腦上的應用程式：選取其中一個執行中的應用程式之後，鍵盤輸入才會進入該應用程式。

音訊焦點是一種類似的想法，可防止多個應用程式同時播放或錄製音訊。 它比鍵盤焦點更為複雜，因為它是自願的 &ndash; 應用程式可能會忽略目前沒有音訊焦點和播放，而不 &ndash;，而且因為有不同類型的音訊焦點可以要求。 例如，如果要求者只預期在很短的時間播放音訊，它可能會要求暫時性焦點。

音訊焦點可能會立即授與或一開始拒絕，並于稍後授與。 例如，如果應用程式在撥打電話期間要求音訊焦點，則會遭到拒絕，但當通話完成後，焦點可能會被授與。 在此情況下，會註冊接聽程式，以便在將音訊焦點移開時，據以回應。 要求音訊焦點是用來判斷是否可以播放或錄製音訊。

如需有關音訊焦點的詳細資訊，請參閱[管理音訊焦點](https://developer.android.com/training/managing-audio/audio-focus.html)。

#### <a name="registering-the-callback-for-audio-focus"></a>為音訊焦點註冊回呼

從 `IOnAudioChangeListener` 註冊 `FocusChangeListener` 回呼是取得和釋放音訊焦點的重要部分。 這是因為授與音訊焦點的時間可能會延遲到稍後為止。 例如，應用程式可能會要求在進行通話時播放音樂。 在通話完成之前，不會授與音訊焦點。

基於這個理由，回呼物件會當做參數傳遞至 `AudioManager`的 `GetAudioFocus` 方法中，而這就是註冊回呼的這個呼叫。 如果一開始拒絕音訊焦點，但後來被授與，則在回呼上叫用 `OnAudioFocusChange`，就會通知應用程式。 相同的方法會用來告訴應用程式正在取出音訊焦點。

當應用程式完成使用音訊資源時，它會呼叫 `AudioManager`的 `AbandonFocus` 方法，然後再次傳入回呼。 這會取消註冊回呼並釋放音訊資源，讓其他應用程式可以取得音訊焦點。

#### <a name="requesting-audio-focus"></a>要求音訊焦點

要求裝置的音訊資源所需執行的步驟如下：

1. 取得 `AudioManager` 系統服務的控制碼。

2. 建立回呼類別的實例。

3. 藉由在 `AudioManager` 上呼叫 `RequestAudioFocus` 方法，來要求裝置的音訊資源。 這些參數是回呼物件、資料流程類型（音樂、語音通話、響鈴等）和所要求的存取權類型（例如，可以短暫要求音訊資源，或不限時間）。

4. 如果已授與要求，則會立即叫用 `playMusic` 方法，而音訊會開始播放。

5. 如果要求遭到拒絕，則不會採取進一步的動作。 在此情況下，只有在稍後授與要求時，才會播放音訊。

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

#### <a name="releasing-audio-focus"></a>釋放音訊焦點

播放軌播放完成時，會叫用 `AudioManager` 上的 `AbandonFocus` 方法。 這可讓另一個應用程式取得裝置的音訊資源。 如果其他應用程式已註冊自己的接聽項，則會收到此音訊焦點變更的通知。

## <a name="low-level-audio-api"></a>低層級的音訊 API

低層級的音訊 Api 可讓您更有效地控制音訊播放和錄製，因為它們會直接與記憶體緩衝區互動，而不是使用檔案 Uri。 在某些情況下，最好採用此方法。 這類案例包括：

1. 從加密的音訊檔案播放時。

2. 播放連續的短剪輯時。

3. 音訊串流。

### <a name="audiotrack-class"></a>AudioTrack 類別

[AudioTrack](xref:Android.Media.AudioTrack)類別會使用低層級的音訊 api 進行錄製，而且是 `MediaPlayer` 類別的低層級對等。

#### <a name="initializing-and-playing"></a>初始化和播放

若要播放音訊，必須具現化 `AudioTrack` 的新實例。 傳入此函式的引數清單[會指定如何](xref:Android.Media.AudioTrack)播放包含在緩衝區中的音訊範例。 引數為：

1. 串流類型 &ndash; 語音、鈴聲、音樂、系統或鬧鐘。

2. 頻率 &ndash; 以 Hz 表示的取樣率。

3. 頻道設定 &ndash; Mono 或身歷聲。

4. 音訊格式 &ndash; 8 位或16位編碼。

5. 緩衝區大小 &ndash; 以位元組為單位。

6. 緩衝區模式 &ndash; 資料流程或靜態。

在結構之後，會叫用 `AudioTrack` 的[Play](xref:Android.Media.AudioTrack.Play)方法，將其設定為開始播放。 將音訊緩衝區寫入 `AudioTrack` 會開始播放：

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

呼叫[pause](xref:Android.Media.AudioTrack.Pause)方法以暫停播放：

```csharp
audioTrack.Pause();
```

呼叫[Stop](xref:Android.Media.AudioTrack.Stop)方法將會永久終止播放：

```csharp
audioTrack.Stop();
```

#### <a name="cleanup"></a>清除

當不再需要 `AudioTrack` 時，其資源必須藉由呼叫[Release](xref:Android.Media.AudioTrack.Release)來釋放：

```csharp
audioTrack.Release();
```

### <a name="the-audiorecord-class"></a>AudioRecord 類別

[AudioRecord](xref:Android.Media.AudioRecord)類別等同于錄製端上的 `AudioTrack`。 如同 `AudioTrack`，它會直接使用記憶體緩衝區來取代檔案和 Uri。 它需要在資訊清單中設定 `RECORD_AUDIO` 許可權。

#### <a name="initializing-and-recording"></a>初始化和記錄

第一個步驟是建立新的[AudioRecord](xref:Android.Media.AudioRecord)物件。 傳入此函式的引數清單[會提供記錄](xref:Android.Media.AudioRecord)所需的所有資訊。 不同于 `AudioTrack`，其中引數主要是列舉，`AudioRecord` 中的對等引數是整數。 它們包括：

1. 硬體音訊輸入來源，例如麥克風。

2. 串流類型 &ndash; 語音、鈴聲、音樂、系統或鬧鐘。

3. 頻率 &ndash; 以 Hz 表示的取樣率。

4. 頻道設定 &ndash; Mono 或身歷聲。

5. 音訊格式 &ndash; 8 位或16位編碼。

6. 緩衝區大小（以位元組為單位）

一旦建立 `AudioRecord` 之後，就會叫用其[StartRecording](xref:Android.Media.AudioRecord.StartRecording)方法。 現在已準備好開始錄製。 `AudioRecord` 會持續讀取音訊緩衝區以進行輸入，並將此輸入寫出至音訊檔案。

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

呼叫[Stop](xref:Android.Media.AudioRecord.Stop)方法會終止記錄：

```csharp
audRecorder.Stop();
```

#### <a name="cleanup"></a>清除

當不再需要 `AudioRecord` 物件時，呼叫其[Release](xref:Android.Media.AudioRecord.Release)方法會釋放與它相關聯的所有資源：

```csharp
audRecorder.Release();
```

## <a name="summary"></a>總結

Android OS 提供強大的架構來播放、錄製和管理音訊。 本文涵蓋如何使用高階 `MediaPlayer` 和 `MediaRecorder` 類別來播放和錄製音訊。 接下來，它會探索如何使用音訊通知，在不同的應用程式之間共用裝置的音訊資源。 最後，它會處理如何使用低層級的 Api 來播放和錄製音訊，而這是直接與記憶體緩衝區互動的介面。

## <a name="related-links"></a>相關連結

- [使用音訊（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/example-workingwithaudio)
- [媒體播放機](xref:Android.Media.MediaPlayer)
- [媒體錄製器](xref:Android.Media.MediaRecorder)
- [音訊管理員](xref:Android.Media.AudioManager)
- [音訊播放軌](xref:Android.Media.AudioTrack)
- [音訊錄製器](xref:Android.Media.AudioRecord)
