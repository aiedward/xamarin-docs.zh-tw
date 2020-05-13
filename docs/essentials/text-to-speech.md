---
title: 'Xamarin.Essentials: Text-to-Speech'
description: Xamarin.Essentials 中的 TextToSpeech 類別可讓應用程式利用內建的文字轉換語音引擎將裝置的文字說出來，也可以查詢引擎可支援的可用語言。
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: abe591d67ea749de4ae9a2f8dadf4df07712691a
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149717"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: Text-to-Speech

**TextToSpeech** 類別可讓應用程式利用內建的文字轉換語音引擎將裝置的文字說出來，也可以查詢引擎可支援的可用語言。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>使用文字轉換語音

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

文字轉換語音透過呼叫 `SpeakAsync` 方法搭配文字與選擇性的參數運作，並且在語句完成後傳回。

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

此方法會接受選擇性的 `CancellationToken` 以在語句開始後停止語句。

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

// Cancel speech if a cancellation token exists & hasn't been already requested.
public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? true)
        return;

    cts.Cancel();
}
```

文字轉換語音會自動將來自相同執行緒的語音要求排入佇列。

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

如需更充分地掌控音訊說話的方式，您可透過 `SpeechOptions` 來設定音量、音調與地區設定。

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

以下是這些參數支援的值：

| 參數 | 最小值 | 最大值 |
| --- | :---: | :---: |
| 音調 | 0 | 2.0 |
| 磁碟區 | 0 | 1.0 |

### <a name="speech-locales"></a>語音地區設定

每個平台都支援不同的地區設定，以便以不同語言和重音符號來唸出文字。 平台擁有不同的代碼和指定地區設定的方式，因此 Xamarin.Essentials 提供了跨平台的 `Locale` 類別，和使用 `GetLocalesAsync` 來查詢它們的方法。

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>限制

- 如果跨多執行緒呼叫，則不保證會排入語句佇列。
- 尚未正式支援背景音訊播放。

## <a name="api"></a>API

- [TextToSpeech 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [TextToSpeech API 文件](xref:Xamarin.Essentials.TextToSpeech)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Text-to-Speech-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
