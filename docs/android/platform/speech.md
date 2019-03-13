---
title: Android 的語音
description: 本文章涵蓋使用功能非常強大的 Android.Speech 命名空間的基本概念。 自開始，Android 已辨識語音及輸出為文字。 它是相當簡單的程序。 針對文字轉換語音，不過，程序會更為複雜，因為不僅語音引擎必須列入考量，但也語言可用及已安裝從文字轉換語音 (text-to-speech，TTS) 系統。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: e88f6e24cbf4c8b2f0c0486c6408e234e87066cc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104345"
---
# <a name="android-speech"></a>Android 的語音

_本文章涵蓋使用功能非常強大的 Android.Speech 命名空間的基本概念。自開始，Android 已辨識語音及輸出為文字。它是相當簡單的程序。針對文字轉換語音，不過，程序會更為複雜，因為不僅語音引擎必須列入考量，但也語言可用及已安裝從文字轉換語音 (text-to-speech，TTS) 系統。_

## <a name="speech-overview"></a>語音概觀

具有 「 了解 「 人類的語音和 enunciates 輸入的內容的系統 — 語音轉換文字和文字轉換語音，是不斷成長的區域內的行動裝置開發，因為自然的通訊，與我們的裝置在需求上升。 有許多情況下，有一項功能，將文字轉換語音，或反之亦然，是非常有用的工具，可併入 android 應用程式。

比方說，在行動電話使用，在駕駛時關閉 clamp，使用者想使用指針免費使用這種運作他們的裝置。 不同 Android 外型規格的過多，例如 Android Wear — 和不斷擴展其中包含能夠使用 Android 裝置 （例如平板電腦和注意板），建立較大的焦點 TTS 的絕佳應用程式。

Google 提供一組豐富的 Api Android.Speech 命名空間中使用開發人員，以涵蓋大部分的執行個體的 「 語音感知 」 （例如 for the blind 而設計的軟體） 的裝置。  命名空間包含允許文字轉譯成透過語音的機能`Android.Speech.Tts`，控制用來執行轉譯，以及許多的引擎`RecognizerIntent`s，而它允許語音轉換成文字。

雖然功能有語音能了解，會根據所使用的硬體的限制。 不可能的裝置已成功將解譯傳達給它，在每個可用的語言中的所有項目。

## <a name="requirements"></a>需求

沒有任何特殊的需求，如本指南中，您的裝置的麥克風和喇叭以外。

解譯語音的 Android 裝置的核心是使用`Intent`有對應`OnActivityResult`。
很重要，不過，辨識語音不了解，但解譯為文字。 差異是很重要的。

### <a name="the-difference-between-understanding-and-interpreting"></a>了解和解譯的差異

了解簡單定義是，您都能判斷的口吻和內容的真正意涵陳述的內容。 若要將只是表示取得文字，並在另一種形式對它們進行輸出。

請考慮下列簡單的範例是用於日常對話： 

<kbd>大家好，你好？</kbd>

沒有變化 （重點放在特定的單字或單字的部分），就可以很簡單的問題。 不過，如果緩慢的速度會套用至線條，接聽的人員將會偵測或發問不太高興，而且可能需要 喝采發問是開心。 如果將重點放在 「 是 」，要求的人員是通常比較有興趣的回應。

而不需要處理，相當強大的音訊的變化，以及某種程度的人工智慧 (AI)，用以了解的內容、 軟體無法開始了解什麼說的最簡單的電話可以是雙向轉換語音與文字。

## <a name="setting-up"></a>設定

之前使用語音系統，最好一律檢查並確定裝置有麥克風。 會有少許嘗試 Kindle 或 Google 的附註鍵台上執行應用程式，而不需要安裝麥克風的點。

下列程式碼範例示範查詢如果麥克風可用，而且如果不是，若要建立警示。 如果沒有麥克風可用在此時您會結束活動或停用來錄製語音功能。

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>建立目的

語音系統的對應方式使用特定類型的對應方式呼叫`RecognizerIntent`。 此意圖控制參數，包括時間等待無回應之前的記錄會被視為對任何其他的語言，辨識及輸出，以及要包含在任何文字一大堆`Intent`的強制回應對話方塊，做為方法的指示。 在此片段中，`VOICE`已`readonly int`中的識別所使用的`OnActivityResult`。

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>語音的轉換

解譯從語音的文字將會傳遞內`Intent`，這會傳回當活動已完成，而且透過存取`GetStringArrayListExtra(RecognizerIntent.ExtraResults)`。 這會傳回`IList<string>`，而是其索引可以是使用根據和顯示來電者意圖中要求的語言數目 (並以指定`RecognizerIntent.ExtraMaxResults`)。 為與任何清單，值得檢查，確保沒有要顯示的資料。

當接聽的傳回值`StartActivityForResult`，則`OnActivityResult`方法有提供。

在下列範例中，`textBox`是`TextBox`用於輸出項目有指定。 它同樣可用來將傳遞至解譯器，並從該處某種形式的文字、 應用程式可以比較的文字和應用程式的另一部分的分支。

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>文字轉換語音

文字轉換語音相當的反向語音文字並不依賴兩個主要的元件;文字轉換語音引擎安裝在裝置上，安裝的語言。

主要，Android 裝置隨附的預設安裝的 Google TTS 服務 」 和 「 至少一種語言。 這建立裝置第一次設定，並會根據裝置的當時的位置時 （比方說，在德國設定手機將會安裝到安裝德文的語言，而其中一個地區會有美式英文）。

### <a name="step-1---instantiating-texttospeech"></a>步驟 1-具現化 TextToSpeech

`TextToSpeech` 可能需要最多 3 個參數前, 兩個是第三個必要項目為選擇性 (`AppContext`， `IOnInitListener`， `engine`)。 接聽程式用來與引擎正在使用 Android 的文字轉換語音引擎任何數目的繫結至的服務和失敗的測試。 在最少，裝置將會有 Google 自己的引擎。

### <a name="step-2---finding-the-languages-available"></a>步驟 2-尋找可用的語言

`Java.Util.Locale`類別包含有用的方法呼叫`GetAvailableLocales()`。 這份語音引擎支援的語言可以先通過已安裝的語言。

它是毫無用處來產生 「 了解 「 語言的清單。 永遠都會有預設的語言 （使用者設定第一次設定時其裝置的語言），因此在此範例中`List<string>`具有 「 預設 」 的第一個參數，清單的其餘部分將會根據結果的區域分布`textToSpeech.IsLanguageAvailable(locale)`。

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

此程式碼會呼叫[TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/)若要測試是否為特定地區設定的語言套件已存在於裝置上。 這個方法會傳回[LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/)，表示是否可以使用傳入的地區設定的語言。 如果`LanguageAvailableResult`表示的語言是`NotSupported`，則沒有聲音的封裝 （即使是針對下載），您可以使用該語言。 如果`LanguageAvailableResult`設為`MissingData`，則可能如下所述步驟 4 中下載新的語言套件。

### <a name="step-3---setting-the-speed-and-pitch"></a>步驟 3-設定速度及字距等性質

Android 可讓使用者藉由改變 alter 語音的聲音`SpeechRate`和`Pitch`（速度及音調的語音速率）。 這是從 0 到 1，以 「 標準 」 是兩個 1 的語音。

### <a name="step-4---testing-and-loading-new-languages"></a>步驟 4： 測試和載入新的語言

下載新的語言透過執行`Intent`。 此意圖的結果會導致[OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/)要叫用方法。 不同於語音轉換文字範例 (使用哪個[RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/)做為`PutExtra`參數來`Intent`)，測試和載入`Intent`是`Action`-基礎：

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash;啟動的活動從平台`TextToSpeech`引擎，以確認適當的安裝和裝置上的語言資源的可用性。

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash;啟動會提示使用者下載所需語言的活動。

下列程式碼範例說明如何使用這些動作進行測試的語言資源，並下載新的語言：

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` 語言資源的可用性測試。 `OnActivityResult` 這項測試完成時，會叫用。 如果要下載的語言資源需要`OnActivityResult`引發`TextToSpeech.Engine.ActionInstallTtsData`動作以啟動活動，可讓使用者下載所需的語言。 請注意這個`OnActivityResult`實作並不會檢查`Result`程式碼，因為在這個簡化的範例中，決定已建立，必須先下載的語言套件。

`TextToSpeech.Engine.ActionInstallTtsData`動作的原因**Google TTS 語音資料**活動，以呈現給使用者，選擇要下載的語言：

![Google TTS 語音資料活動](speech-images/01-google-tts-voice-data.png)

為例，使用者可能會挑選法文，並按一下 [下載] 圖示來下載法文的語音資料：

![下載法文語言的範例](speech-images/02-selecting-french.png)

下載完成之後，會自動進行安裝，此資料。


### <a name="step-5---the-ioninitlistener"></a>步驟 5-IOnInitListener

若要能夠將文字轉換語音，介面方法的轉換活動`OnInit`具有實作 (這是指定的具現化的第二個參數`TextToSpeech`類別)。 這會初始化接聽程式，並測試結果。

接聽程式應該測試兩者`OperationResult.Success`和`OperationResult.Failure`最小值。
下列範例會示範這項：

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>總結

本指南中，我們已討論過文字轉語音及語音轉換文字和可能的方法，如何將它們包含在自己的應用程式的基本概念。 雖然它們並未涵蓋每個特定的案例，您現在應該有基本了解如何解譯語音、 如何安裝新的語言，以及如何增加您的應用程式的 inclusivity。



## <a name="related-links"></a>相關連結

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [文字轉換語音 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [語音轉換文字 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Android.Speech 命名空間](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Android.Speech.Tts 命名空間](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
