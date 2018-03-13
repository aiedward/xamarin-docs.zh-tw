---
title: "實作文字轉換語音"
description: "使用 DependencyService 呼叫每個平台的原生文字轉換語音 API 應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6ac9ca7bae517602c33729134eb0bd48359afbc7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-text-to-speech"></a>實作文字轉換語音

當您建立跨平台應用程式使用，本文將引導您[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/)存取原生文字轉換語音的應用程式開發介面：

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何建立共用的程式碼中的介面。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在原生 iOS 程式碼中實作介面。
- **[Android 實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[Windows 實作](#WindowsImplementation)** &ndash;深入了解如何在原生程式碼中實作的介面，適用於 Windows Phone 和通用 Windows 平台 (UWP)。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`來呼叫原生實作共用的程式碼。

應用程式使用`DependencyService`會有下列結構：

![](text-to-speech-images/tts-diagram.png "DependencyService 應用程式結構")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，建立介面，在共用程式碼中表達您打算實作的功能。 例如，介面包含單一方法， `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

撰寫程式碼中的共用程式碼是此介面可讓 Xamarin.Forms 應用程式存取每個平台上的語音應用程式開發介面。

> [!NOTE]
> 實作介面的類別都必須有參數的建構函式，才能使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

必須實作介面，每個平台專屬的應用程式專案中。 請注意，此類別的無參數建構函式，讓`DependencyService`可以建立新的執行個體。

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

`[assembly]`屬性做為實作的註冊類別`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 的實作

Android 程式碼是 iOS 版本比更為複雜： 它需要實作的類別繼承自特定的 Android`Java.Lang.Object`並實作`IOnInitListener`以及介面。 它也需要存取目前的 Android 內容，由`MainActivity.Instance`屬性。

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

`[assembly]`屬性做為實作的註冊類別`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="WindowsImplementation" />

## <a name="windows-phone-and-universal-windows-platform-implementation"></a>Windows Phone 和通用 Windows 平台實作

Windows Phone 和通用 Windows 平台有中的語音 API`Windows.Media.SpeechSynthesis`命名空間。 唯一需要注意是要記得刻度**麥克風**功能中的資訊，否則存取遭到封鎖的應用程式開發介面的語音。

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

`[assembly]`屬性做為實作的註冊類別`ITextToSpeech`介面，這表示`DependencyService.Get<ITextToSpeech>()`可以共用的程式碼中用來建立它的執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在我們可以撰寫並測試共用的程式碼存取的文字轉換語音的介面。 這個簡單的頁面包含觸發語音功能的按鈕。 它會使用`DependencyService`來取得執行個體的`ITextToSpeech`介面&ndash;在執行階段，這個執行個體將會具有完整存取權的原生 SDK 的平台特定實作。

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

在 iOS、 Android 或 Windows 平台和按下按鈕將會導致就您在每個平台上使用原生語音 SDK 的應用程式上執行此應用程式。

 ![iOS 和 Android 的文字轉換語音按鈕](text-to-speech-images/running.png "則文字轉換語音範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService （範例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [文字轉換語音活頁簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
