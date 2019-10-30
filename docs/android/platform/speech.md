---
title: Android 語音
description: 本文涵蓋使用功能非常強大的 Android. Speech 命名空間的基本概念。 自開始，Android 已能夠辨識語音並將其輸出為文字。 這是一個相當簡單的程式。 不過，針對文字轉換語音，此程式更牽涉到，因為語音引擎不僅必須列入考慮，也可能是從文字轉換語音（TTS）系統中提供及安裝的語言。
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019760"
---
# <a name="android-speech"></a>Android 語音

_本文涵蓋使用功能非常強大的 Android. Speech 命名空間的基本概念。自開始，Android 已能夠辨識語音並將其輸出為文字。這是一個相當簡單的程式。不過，針對文字轉換語音，此程式更牽涉到，因為語音引擎不僅必須列入考慮，也可能是從文字轉換語音（TTS）系統中提供及安裝的語言。_

## <a name="speech-overview"></a>語音總覽

在行動裝置開發中使用「瞭解」「人類語音」和 enunciates 所輸入內容的系統（語音轉換文字和文字轉換語音），是行動開發的一大成長區域，因為與我們的裝置的自然通訊需求上升。 在許多情況下，有一項功能可以將文字轉換成語音，反之亦然，是一個非常有用的工具，可併入您的 android 應用程式中。

例如，當行動電話上的夾具在駕駛時使用，使用者想要以無人參與的方式操作其裝置。 不同的 Android 外型規格（例如 Android 磨損）眾多，以及能夠使用 Android 裝置（例如平板電腦和便箋板）的已擴大包含，都已在絕佳的 TTS 應用程式上建立更大的焦點。

Google 為開發人員提供了一組豐富的 Android. Speech 命名空間 Api，以涵蓋讓裝置「語音感知」（例如專為盲人設計的軟體）的大多數實例。  命名空間包含的功能，可讓文字透過 `Android.Speech.Tts`轉譯成語音、控制用來執行轉譯的引擎，以及可讓語音轉換成文字的多個 `RecognizerIntent`。

雖然有工具可讓您瞭解語音，但根據使用的硬體有一些限制。 裝置不太可能會以每種語言都能成功解讀所有的內容。

## <a name="requirements"></a>需求

除了您的裝置具有麥克風和喇叭以外，本指南沒有特殊需求。

Android 裝置解讀語音的核心是使用具有對應 `OnActivityResult`的 `Intent`。
不過，請務必辨識不了解語音，而是將其轉譯為文字。 差異很重要。

### <a name="the-difference-between-understanding-and-interpreting"></a>瞭解與解讀之間的差異

有一個簡單的瞭解定義，就是您能夠以音調和內容來判斷所說的實際意義。 若只要解讀，就能採用單字，並以另一種形式輸出。

請考慮下列在日常對話中使用的簡單範例：

<kbd>喂，你好嗎？</kbd>

如果沒有變化（強調放在特定單字或單字的部分），這就是一個簡單的問題。 不過，如果行的速度較慢，接聽者將會偵測到該使用者不太滿意，而且可能需要歡呼，或是有人 unwell。 如果重點放在「是」，則詢問的人員通常會對回應感興趣。

若沒有相當強大的音訊處理來使用變化，以及某種程度的人工智慧（AI）來瞭解內容，則軟體甚至無法開始瞭解說出什麼，這就是簡單的手機可以將語音轉換成文字。

## <a name="setting-up"></a>設定

使用語音系統之前，一定要先檢查以確保裝置具有麥克風。 在 Kindle 或 Google 記事 pad 上執行您的應用程式時，沒有安裝麥克風的情況很少。

下列程式碼範例示範如何查詢麥克風是否可用，如果沒有，則會建立警示。 如果此時沒有可用的麥克風，您可以結束活動，或停用錄製語音的功能。

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

### <a name="creating-the-intent"></a>建立意圖

語音系統的目的是使用特定類型的意圖，稱為 `RecognizerIntent`。 這個意圖控制大量的參數，包括等候無回應的時間長度，直到記錄被視為超過、辨識和輸出的任何其他語言，以及要包含在 `Intent`的強制回應對話方塊上做為指令的任何文字。 在此程式碼片段中，`VOICE` 是在 `OnActivityResult`中用來辨識的 `readonly int`。

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

### <a name="conversion-of-the-speech"></a>語音轉換

從語音解讀的文字將會在 `Intent`中傳遞，這會在活動完成並透過 `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`存取時傳回。 這會傳回 `IList<string>`，根據呼叫者意圖中要求的語言數目（並在 `RecognizerIntent.ExtraMaxResults`中指定），可以使用並顯示索引。 就像任何清單一樣，值得檢查以確保有要顯示的資料。

接聽 `StartActivityForResult`的傳回值時，必須提供 `OnActivityResult` 方法。

在下列範例中，`textBox` 是用來輸出已聽寫內容的 `TextBox`。 它也可以用來將文字傳遞至某種形式的解譯器，而從該處，應用程式可以將文字和分支與應用程式的另一個部分進行比較。

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

文字轉換語音並不是語音轉換文字的反轉，而是依賴兩個主要元件;要安裝在裝置上的文字轉換語音引擎，以及要安裝的語言。

在大部分的情況下，Android 裝置都會安裝預設的 Google TTS 服務，而且至少會有一種語言。 這是在第一次設定裝置時所建立，而且會根據裝置的所在位置（例如，在德國設定的電話會安裝德文語言，而北美洲的手機則會有美式英文）。

### <a name="step-1---instantiating-texttospeech"></a>步驟 1-具現化 TextToSpeech

`TextToSpeech` 最多可能需要3個參數，第二個是選擇性的（`AppContext`，`IOnInitListener`，`engine`）。 接聽程式可用來系結至服務，並測試引擎是否有任意數目的可用 Android 文字轉換語音引擎。 裝置至少會有 Google 自己的引擎。

### <a name="step-2---finding-the-languages-available"></a>步驟 2-尋找可用的語言

`Java.Util.Locale` 類別包含一個稱為 `GetAvailableLocales()`的實用方法。 這份語音引擎支援的語言清單，可以針對已安裝的語言進行測試。

產生「瞭解」語言的清單是很簡單的事。 一律會有預設語言（使用者第一次設定其裝置時所設定的語言），因此在此範例中，`List<string>` 會將 "Default" 當做第一個參數，並根據 `textToSpeech.IsLanguageAvailable(locale)`的結果填入清單的其餘部分。

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

此程式碼會呼叫[TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) ，以測試特定地區設定的語言套件是否已存在於裝置上。
這個方法會傳回[LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult)，指出通過之地區設定的語言是否可用。 如果 `LanguageAvailableResult` 表示該語言為 `NotSupported`，則表示該語言沒有可用的語音套件（即使是下載）。 如果 `LanguageAvailableResult` 設定為 `MissingData`，則可以下載新的語言套件，如下面的步驟4所述。

### <a name="step-3---setting-the-speed-and-pitch"></a>步驟 3-設定速度和音調

Android 可讓使用者藉由改變 `SpeechRate` 和 `Pitch` （語音的速度和音調）來改變語音的音效。 這會從0到1，而兩者的「正常」語音則是1。

### <a name="step-4---testing-and-loading-new-languages"></a>步驟 4-測試和載入新語言

下載新語言時，會使用 `Intent`來執行。 此意圖的結果會導致叫用[OnActivityResult](xref:Android.App.Activity.OnActivityResult*)方法。 不同于語音轉換文字範例（使用[RecognizerIntent](xref:Android.Speech.RecognizerIntent)做為 `Intent`的 `PutExtra` 參數），測試和載入 `Intent`是以 `Action`為基礎：

- &ndash; [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData)會從平臺 `TextToSpeech` 引擎啟動一個活動，以確認裝置上的語言資源有正確的安裝和可用性。

- &ndash; [TextToSpeech](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData)會啟動一個活動，提示使用者下載所需的語言。

下列程式碼範例說明如何使用這些動作來測試語言資源，並下載新的語言：

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

`TextToSpeech.Engine.ActionCheckTtsData` 測試語言資源的可用性。 當此測試完成時，就會叫用 `OnActivityResult`。 如果需要下載語言資源，`OnActivityResult` 會引發 `TextToSpeech.Engine.ActionInstallTtsData` 動作，以啟動可讓使用者下載所需語言的活動。 請注意，這項 `OnActivityResult` 的執行並不會檢查 `Result` 程式碼，因為在這個簡化的範例中，已經建立了需要下載語言套件的判斷。

`TextToSpeech.Engine.ActionInstallTtsData` 動作會使**GOOGLE TTS 語音資料**活動呈現給使用者，以選擇要下載的語言：

![Google TTS 語音資料活動](speech-images/01-google-tts-voice-data.png)

例如，使用者可能會挑選法文，然後按一下下載圖示來下載法文語音資料：

![下載法文語言的範例](speech-images/02-selecting-french.png)

此資料的安裝會在下載完成後自動執行。

### <a name="step-5---the-ioninitlistener"></a>步驟 5-IOnInitListener

若要讓活動能夠將文字轉換成語音，必須實 `OnInit` 介面方法（這是為 `TextToSpeech` 類別的具現化指定的第二個參數）。 這會初始化接聽程式並測試結果。

接聽程式應該至少測試 `OperationResult.Success` 和 `OperationResult.Failure`。
下列範例只顯示這種情況：

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

在本指南中，我們探討了將文字轉換為語音和語音轉換文字的基本概念，以及如何在您自己的應用程式中包含它們的方法。 雖然它們不涵蓋每個特定案例，但您現在應該已對如何解讀語音、如何安裝新語言，以及如何增加應用程式 inclusivity 的基本瞭解。

## <a name="related-links"></a>相關連結

- [Xamarin. Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [文字轉換語音（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [語音轉換文字（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Android. Speech 命名空間](xref:Android.Speech)
- [Android. 語音命名空間](xref:Android.Speech.Tts)
