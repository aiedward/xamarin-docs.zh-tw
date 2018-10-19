---
title: 實作文字轉換語音
description: 這篇文章說明如何使用 Xamarin.Forms DependencyService 類別來呼叫每個平台的原生的文字轉換語音 API。
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6d1948214b97a1b536b07b6420c32e4d27124518
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "38997539"
---
# <a name="implementing-text-to-speech"></a>實作文字轉換語音

當您建立使用的跨平台應用程式，這篇文章會引導您[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)存取原生的文字轉換語音 Api:

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何建立共用程式碼中的介面。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在適用於 iOS 的原生程式碼中實作的介面。
- **[Android 的實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[UWP 實作](#WindowsImplementation)** &ndash;了解如何在原生程式碼適用於通用 Windows 平台 (UWP) 中實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`呼叫原生實作共用的程式碼。

應用程式使用`DependencyService`會有下列結構：

![](text-to-speech-images/tts-diagram.png "DependencyService 應用程式結構")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，表達您打算實作的功能的共用程式碼中建立的介面。 此範例中，介面會包含單一方法， `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

針對這個介面，在共用的程式碼中撰寫程式碼，可讓 Xamarin.Forms 應用程式存取語音 Api，每個平台。

> [!NOTE]
> 實作介面的類別必須具有無參數的建構函式，才能使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 的實作

每個平台專屬的應用程式專案中，就必須實作的介面。 請注意，此類別具有無參數建構函式，讓`DependencyService`可以建立新的執行個體。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

`[assembly]`屬性會註冊類別的實作作為`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 的實作

Android 的程式碼是更複雜的 iOS 版本比： 它需要實作的類別繼承自 Android 專用`Java.Lang.Object`並實作`IOnInitListener`以及介面。 它也需要存取目前的 Android 內容，由`MainActivity.Instance`屬性。

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

`[assembly]`屬性會註冊類別的實作作為`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台實作

通用 Windows 平台有語音 API 在`Windows.Media.SpeechSynthesis`命名空間。 唯一要注意的是要記得勾選**麥克風**功能中的資訊，否則存取語音，Api 會被封鎖。

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

`[assembly]`屬性會註冊類別的實作作為`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在我們可以撰寫並測試共用的程式碼存取的文字轉換語音的介面。 這個簡單的頁面包含一個按鈕，就會觸發的語音功能。 它會使用`DependencyService`若要取得的執行個體`ITextToSpeech`介面&ndash;在執行階段，這個執行個體將會是具有完整存取權的原生 SDK 的平台特定實作。

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

在 iOS、 Android 或 UWP 上執行此應用程式，並按下按鈕會導致您在每個平台上使用原生的語音 SDK 以向應用程式。

 ![iOS 和 Android 的文字轉換語音按鈕](text-to-speech-images/running.png "文字轉換語音範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService （範例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)

