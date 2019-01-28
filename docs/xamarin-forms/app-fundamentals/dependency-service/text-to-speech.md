---
title: 實作文字轉換語音功能
description: 本文說明如何使用 Xamarin.Forms DependencyService 類別來呼叫每個平台的原生文字轉換語音 API。
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: af40dc99dffd6e04ad0d408aea58b23c5446461e
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479754"
---
# <a name="implementing-text-to-speech"></a>實作文字轉換語音功能

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

本文會引導您建立使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 存取原生文字轉換語音 API 的跨平台應用程式：

- **[建立介面](#Creating_the_Interface)** &ndash; 了解如何在共用程式碼中建立介面。
- **[iOS 實作](#iOS_Implementation)** &ndash; 了解如何以適用於 iOS 的原生程式碼來實作介面。
- **[Android 實作](#Android_Implementation)** &ndash; 了解如何在適用於 Android 的原生程式碼中實作介面。
- **[UWP 實作](#WindowsImplementation)** &ndash; 了解如何在適用於通用 Windows 平台 (UWP) 的原生程式碼中實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 從共用程式碼呼叫原生實作。

使用 `DependencyService` 的應用程式會有下列結構：

![](text-to-speech-images/tts-diagram.png "DependencyService 應用程式結構")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，在共用程式碼中建立介面，表示您打算實作的功能。 針對此範例，介面包括一個單一方法 `Speak`：

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

以共用程式碼撰寫這個介面的程式碼時，可讓 Xamarin.Forms 應用程式存取每個平台上的語音 API。

> [!NOTE]
> 實作介面的類別必須具有無參數建構函式，才能使用 `DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

介面必須實作在每個平台特定應用程式專案中。 請注意，此類別具有無參數的建構函式，以便讓 `DependencyService` 可以建立新的執行個體。

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

`[assembly]` 屬性會將類別註冊為 `ITextToSpeech` 介面的實作，這表示 `DependencyService.Get<ITextToSpeech>()` 可在共用程式碼中用來建立其執行個體。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 實作

Android 程式碼比 iOS 版本更為複雜。 它也需要存取由 `MainActivity.Instance` 屬性公開的目前 Android 內容：

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    internal static MainActivity Instance { get; private set; }

    protected override void OnCreate(Bundle bundle)
    {
        ...
    }
}
```

它也需要實作類別以繼承自 Android 特定的 `Java.Lang.Object` 並實作 `IOnInitListener` 介面。

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

`[assembly]` 屬性會將類別註冊為 `ITextToSpeech` 介面的實作，這表示 `DependencyService.Get<ITextToSpeech>()` 可在共用程式碼中用來建立其執行個體。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台實作

通用 Windows 平台在 `Windows.Media.SpeechSynthesis` 命名空間具有語音 API。 唯一要注意的是，請記得勾選資訊清單中的**麥克風**功能，否則對語音 API 的存取會受到封鎖。

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

`[assembly]` 屬性會將類別註冊為 `ITextToSpeech` 介面的實作，這表示 `DependencyService.Get<ITextToSpeech>()` 可在共用程式碼中用來建立其執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，我們可以撰寫並測試存取文字轉換語音介面的共用程式碼。 此簡單頁面包含可觸發語音功能的按鈕。 會使用 `DependencyService` 來取得 `ITextToSpeech` 介面的執行個體 &ndash; 在執行階段，這個執行個體將會是具有原生 SDK 完整存取權的平台特定實作。

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

在 iOS、Android 或 UWP 上執行此應用程式並按下按鈕，應用程式即會使用各平台上的原生語音 SDK 向您說話。

 ![iOS 和 Android 文字轉換語音](text-to-speech-images/running.png "文字轉換語音範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService (範例)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
