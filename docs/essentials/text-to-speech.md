---
title: Xamarin.Essentials： 文字轉換語音
description: 應用程式中要說出後的文字，來自裝置和查詢引擎可以支援的可用語言的文字轉換語音引擎利用內建的 Xamarin.Essentials 讓 TextToSpeech 類別。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ba822870edafce44140caa66b01f4da242fb7779
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353609"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials： 文字轉換語音

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**TextToSpeech**類別可讓您在要說出後的文字，來自裝置和查詢引擎可以支援的可用語言的文字轉換語音引擎利用內建應用程式。

## <a name="using-text-to-speech"></a>使用文字轉換語音

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

文字轉換語音功能的運作方式是呼叫`SpeakAsync`與文字和選擇性參數，然後傳回完成 [utterance] 之後的方法。 

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) =>
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

這個方法會採用選擇性`CancellationToken`一旦開始之後，停止 [utterance]。

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

文字轉換語音將會自動排入佇列語音要求，從相同的執行緒。

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>語音設定

更充分掌控如何唸音訊如備份與`SpeakSettings`，可讓您設定的磁碟區、 音調、 和地區設定。

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

以下是支援的值，這些參數：

| 參數 | 最低 | 最大值 |
| --- | :---: | :---: |
| 音調 | 0 | 2.0 |
| 磁碟區 | 0 | 1.0 |

### <a name="speech-locales"></a>語音的地區設定

每個平台提供說出後的文字，以多種語言和重音符號的地區設定。 每個平台各有不同的程式碼與方式來指定此項目，這也是為什麼 Essentials 提供跨平台`Locale`類別和以查詢的方式`GetLocalesAsync`。

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>限制

- 如果跨多個執行緒呼叫，不保證 utterance 佇列。
- 背景音訊播放未正式支援。

## <a name="api"></a>API

- [TextToSpeech 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [TextToSpeech API 文件](xref:Xamarin.Essentials.TextToSpeech)
