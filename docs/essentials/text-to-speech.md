---
title: Xamarin.Essentials 文字轉換語音
description: 從裝置和查詢可用的語言引擎可支援以尋求後的文字的文字轉換語音引擎利用內建應用程式 TextToSpeech 類別可讓。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b2c9ed50c48aee6343a20ddb28c49e1bd05d2153
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials 文字轉換語音

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**TextToSpeech**類別可讓應用程式可以利用內建文字轉換語音引擎從裝置和查詢可用的語言引擎可支援以尋求後的文字。

## <a name="using-text-to-speech"></a>使用文字轉換語音

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

文字轉換語音功能的運作方式是呼叫`SpeakAsync`文字及選擇性的參數與傳回 utterance 完成之後的方法。 

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

若要停止 utterance，一旦開始之後，這個方法會採用中選擇性的 CancellationToken。 
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

文字轉換語音自動會將佇列語音要求從相同的執行緒。 

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

進一步控制如何播放音訊隨同`SpeakSettings`，可讓您設定的磁碟區、 音調、 和地區設定。

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

支援這些參數的值如下：

| 參數 | 最低 | 最大值 |
| --- | :---: | :---: |
| 點數 | 0 | 2.0 |
| 磁碟區 | 0 | 1.0 |

### <a name="speech-locales"></a>語音地區設定

每個平台提供了多重語言和重音符號後的文字的讀出的地區設定。 每個平台都有不同的程式碼與方式來指定這個項目，這也是為什麼 Essentials 提供跨平台`Locale`類別和方法來查詢它們與`GetLocalesAsync`。

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

- 如果多個執行緒上呼叫，並不保證 utterance 佇列。
- 背景音訊播放未正式支援。

## <a name="api"></a>API

- [TextToSpeech 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [TextToSpeech API 文件](xref:Xamarin.Essentials.TextToSpeech)
